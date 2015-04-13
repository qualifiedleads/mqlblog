---
published: false
---

## Uploading conversions via the AdWords API

Utilising the AdWords conversion upload API and connecting it to your tracker/crm/MySQL is not for the faint-hearted. Not so much for the programming work involved, but the complexity of security steps and permissions required by Google.
One of our clients recently had no other choice but to use the "conversion upload API". They are buying significant AdWords display traffic, sending it to a mobile-web page that has a Carrier-billing system. The UK version is "PayForIt", in every country it's another billing supplier.
Because of this carrier-billing the usual AdWords conversion tracking tag was unreliable at best. Sometimes triggering conversions where there were none, sometimes counting them multiple times. Erratic.

Initially we had left out conversion tracking inside the AdWords UI altogether, and just relied on the tracking platform to do our stats & slicing-dicing.

As a test I started using the conversion upload to see if we could somehow get to use  CPA bidding inside AdWords.

The client was dismissive, and AdWords support said that becuase this was an RTB feature it would not work.

I decided to try it anyway, as it would enable easier optimisation of the creatives: it's much easier to SEE the banner, and have the CTR next to it, than looking at a CSV with a code fro a specific creative. You look up the real outliers in the data-sets, but it really killse the enthusiasm for optimisation when dealing with hundreds of creatives all listed by a code like "hg6QImsjiInnj87_009".
Ugh..

Lo-and-behold, after 15 conversions uploaded to a campaign, suddenly the CPA bidding became available. Even though AdWords support had said it would not!

Within a week that campaign had tripled in volume, with improved CPA.

The client woke up.
It was very clear that this was an optimisation to implement across the boards.

However pretty soon I was downloading/converting/uploading dozens of CSV's per day. All the campaigns that had the 'conversion optimiser' activated and was running n target CPA's were running beautifully. But so many CSV's - ugh. It became unworkable. 

So we had no choice but to opt for the 'conversion upload API'.
Little did we know what was in store for us.

I will attempt to spare you the suffering we went through. Let's just say that the documentation is confusing at best. At worst, just conflicting. Let me make it brief for you.
## You will need:
+ MCC access
+ a Test MCC account
+ a written documentation of your proposed API
+ a signed agreement of Terms for API usage
+ a google cloud app
+ credit card details filled in, in the MCC

Links you might need:
https://services.google.com/fb/forms/newtoken/

https://services.google.com/fb/forms/apicontact/

https://developers.google.com/adwords/api/docs/guides/importing-conversions

https://secure.echosign.com/public/hostedForm?formid=8XCUEEIX3A4256

Links that may help when you get stuck:
