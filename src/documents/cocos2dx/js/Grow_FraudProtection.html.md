---
layout: "content"
image: "FraudProtection"
title: "Fraud Protection"
text: "Get started with Grow Fraud Protection."
position: 7
theme: 'platforms'
collection: 'cocos2djs_grow'
module: 'grow'
lang: 'js'
platform: 'cocos2dx'
---

# Fraud Protection

<div class="info-box">General information about Fraud Protection available in this [article](/university/articles/Grow_FraudProtection).</div>

In order to turn on Fraud Protection in cocos2d-js, you need to pass additional params to `storeParams` depending on a
billing provider.

*iOS*

- *SSV* - enables _Fraud Protection_ for iOS.

- *verifyOnServerFailure* - if you use Fraud Protection, optionally you set this param, if you want to get purchases
automatically verified in case of network failures during the verification process. Default value is `false`.

    ``` js
    storeParams.SSV = true;
    storeParams.verifyOnServerFailure = true;
    Soomla.soomlaStore.initialize(assets, storeParams);
	```

*Google Play*

For Google Play you need to get clientId, clientSecret and refreshToken as explained in
[Google Play Purchase Verification](/android/store/Store_GooglePlayVerification) and use them like this:

	``` js
    storeParams.clientId = <YOU_CLIENT_ID>;
    storeParams.clientSecret = <YOUR_CLIENT_SECRET>;
    storeParams.refreshToken = <YOUR_REFRESH_TOKEN>;
    storeParams.verifyOnServerFailure = true;
    Soomla.soomlaStore.initialize(assets, storeParams);
	```
