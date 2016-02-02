---
layout: "content"
image: "FraudProtection"
title: "Fraud Protection"
text: "Get started with Grow Fraud Protection."
position: 18
theme: 'platforms'
collection: 'unity_grow'
module: 'grow'
platform: 'unity'
---

# Fraud Protection

<div class="info-box">General information about Fraud Protection available in this [article](/university/articles/Grow_FraudProtection).</div>

In case you want to turn on _Fraud Protection_ you need to get clientId, clientSecret and refreshToken as explained in 
[Google Play Purchase Verification](/android/store/Store_GooglePlayVerification) and use them like this:   

  ``` java
      GooglePlayIabService.getInstance().configVerifyPurchases(new HashMap<String, Object>() {{
          put("clientId", <YOU_CLIENT_ID>);
          put("clientSecret", <YOUR_CLIENT_SECRET>);
          put("refreshToken", <YOUR_REFRESH_TOKEN>);
      }});
  ```

  >  Optionally you can turn on `verifyOnServerFailure` if you want to get purchases automatically verified in case of network failures during the verification process:
  >
  > ``` java
  > GooglePlayIabService.getInstance().verifyOnServerFailure = true;
  > ```

