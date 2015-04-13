---
layout: post
title: Airpush reporting API
excerpt: "Extending the 7-day Airpush conversion optimiser"
categories: articles
tags: [code, airpush, CRO]
comments: true
share: true
---

Connect to the Airpush reporting API to collect conversion data Biggest problem with the Airpush platform is that you only have 7 days of conversion data in the optimiser. If a change you make more than 7 days ago affects your campaign you have no way of retracing your steps.[^1] This code is the base for connecting to their reporting API so you collect it in your own SQL db.[^2]

[^1]: <http://www.airpush.com/advertiser-apis/index.php/Airpush_Conversion_API_Documentation#WAP_Conversions>
[^2]: <http://www.airpush.com/advertiser-apis/index.php/Airpush_Conversion_API_Documentation#WAP_Conversions>

### The basic APP code

Fork the [repo from GitHub](https://github.com/TapTapMobile/airpush_conversion_api) and help us create a better version.

{% highlight php %}
{% raw %}
<?php 
	set_time_limit(0);
	
	$campids=array();
	$dateyest ="2014-10-27";
	
	function dbq($q){
		$dbLink = new mysqli("HOSTNAME","USERNAME","PASSWORD","DBNAME" );
		if ($dbLink->connect_error) {
			die('Connect Error, '. $dbLink->connect_errno . ': ' . $dbLink->connect_error);
		}
		$r = $dbLink->query($q);
		$dbLink->close();
		return $r;
	}
	
	function apicall($url){
		$ch = curl_init();
		curl_setopt_array(
			$ch, array( 
			CURLOPT_URL => $url,
			CURLOPT_RETURNTRANSFER => true
		));
		$o = curl_exec($ch);
		curl_close($ch);
		return json_decode($o);
	}
	
	function getSetMultiBid($campaignId){
		$xbidout = apicall('http://openapi.airpush.com/getMultibidsList?apikey=XXXXXXXXXXXXXXXXXX&multibids='.urlencode('{"campaignId": "'.$campaignId.'"}'));
		foreach($xbidout as $x){
			if(isset($x->bid_percentage) && isset($x->app_id)){
				$bidp= (100 + ($x->bid_percentage))/100;
				$q=dbq("select * from campaignappdailydata where campaignid=".$campaignId." and AppID=".$x->app_id." and Date >=".$x->TX_TIMESTAMP);
				if($q->num_rows){
					while($row = mysqli_fetch_array($q)){
						$qbid = dbq("select * from campaigndaily where campaignid = '".$campaignId."' and Date = '".$row['Date']."'");
						$rw = mysqli_fetch_assoc($qbid);
						print_r($rw);
						$bid = round($rw['currentBid']*$bidp,3);
						//echo $bid."<br>";
						$qbidupdate = "UPDATE campaignappdailydata SET Bid='".$bid."' WHERE id='".$row['id']."'";
						dbq($qbidupdate);
					}
				}
			}			
		}
	}
	
	$x = apicall('http://openapi.airpush.com/getCampaignsByUserId?apikey=XXXXXXXXXXXXXXXXXXXX'); 
	
	foreach ($x as $k => $i){
		$flagset=0;
		$campaignstatus = $i->campaignstatus;
		$campaigncheck=dbq("SELECT * FROM campaigns WHERE campaignid = ".$i->campaignid);
		$ccheck=$campaigncheck->num_rows;
		if($ccheck == 0){
			$qy = "INSERT INTO campaigns (campaignid,campaigncategoryid,campaignname,dailybudget,startdate,enddate,ppm,ppmbid,budgetspent,campaignstatus,createdate) VALUES ('".$i->campaignid."','".$i->campaigncategoryid."','".$i->campaignname."','".$i->dailybudget."','".$i->startdate."','".$i->enddate."','".$i->ppm."','".$i->ppmbid."','".$i->budgetspent."','".$i->campaignstatus."','".$i->createdate."')";
			dbq($qy);
		}else{
			$qy = "UPDATE campaigns SET dailybudget='".$i->dailybudget."',enddate='".$i->enddate."',ppm='".$i->ppm."',ppmbid='".$i->ppmbid."',budgetspent='".$i->budgetspent."',campaignstatus='".$i->campaignstatus."' WHERE campaignid='".$i->campaignid."'";
			dbq($qy);
		}
		
		if($campaignstatus != "Draft"){
			$xout = apicall('http://openapi.airpush.com/getCampaignReports?apikey=XXXXXXXXXXXXXXXXXXXXXXXXXX&startDate='.$dateyest.'&endDate='.$dateyest.'&campaignIds='.$i->campaignid);
			$n = $xout->campaign_data[0];
			$q = "INSERT INTO campaigndaily (`campaignid`, `campaignstatus`, `dailybudget`, `currentBid`, `impression`, `clicks`, `Spent`, `conversion`, `avgCPC`, `ctr`, `conversionrate`, `CPA`, `Date`) VALUES ('".$n->campaignid."','".$n->campaignstatus."','".$n->dailybudget."','".$n->currentBid."','".$n->impression."','".$n->clicks."','".$n->Spent."','".$n->conversion."','".$n->avgCPC."','".$n->ctr."','".$n->conversionrate."','".$n->CPA."','".$n->Date."')";
			dbq($q);
			$campids[$n->campaignid] = $n->currentBid;
		}
	}

	foreach($campids as $id => $bid){
		$flagset=0;
		
		$xappout = apicall('http://openapi.airpush.com/getCampaignOptimizerData?apikey=XXXXXXXXXXXXXXXXXXXXXXXXXX&startDate='.$dateyest.'&endDate='.$dateyest.'&campaignIds='.$id.'&reportType=application&limit=1000');

		if(isset($xappout->status)){
			if($xappout->status == 3020){
				sleep(60);
				$xappout = apicall('http://openapi.airpush.com/getCampaignOptimizerData?apikey=XXXXXXXXXXXXXXXXXXXXXXXXXX&startDate='.$dateyest.'&endDate='.$dateyest.'&campaignIds='.$id.'&reportType=application&limit=1000');
			}
		}		
		
		if(!empty($xappout->$id)){
			foreach ($xappout->$id as $ai){
				$qapp="INSERT INTO `campaignappdailydata` (`campaignid`, `AppID`, `publisher`, `push`, `clicks`, `ctr`, `cpa`, `conversion`, `conversionrate`, `cpc`, `Bid`, `Date`) VALUES ('".$id."','".$ai->app."', '".$ai->publisher."', '".$ai->push."', '".$ai->click."', '".$ai->ctr."', '".$ai->cpa."', '".$ai->conversion."', '".$ai->conversionrate."', '".$ai->cpc."', '".$bid."', '".$dateyest."')";
				dbq($qapp);
				$flagset=1;
			}
		}
		
		if($flagset==1){
			getSetMultiBid($id);
		}

	}
?>
{% endraw %}
{% endhighlight %}






### Git' it

Fork the [repo from GitHub](https://github.com/TapTapMobile/airpush_conversion_api) and help us create a better version.

