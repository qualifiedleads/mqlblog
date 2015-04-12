---
published: false
---

## Uploading conversions via the AdWords API

Utilising the AdWords conversion upload API and connecting it to your tracker/crm/MySQL is not for teh faint-hearted. Not so much for eth programming work involved, but the complexity of security steps and permissions required by Google.
One of our clients recently had no other choice but to use the "conversion upload API". They are buying significant AdWords display traffic, sending it to a mobile-web page that has a Carrier-billing system. The UK version is "PayForIt", the Germand version is 
, etc...
Because of this carrier-billing the usual AdWords conversion tracking tag was unreliable at best. Sometimes triggering conversiosn where there were none, sometimes counting them multiple times. Erratic.

So we had to use the conversion upload feature
You will need:
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
