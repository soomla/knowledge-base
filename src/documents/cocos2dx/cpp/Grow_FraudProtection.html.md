---
layout: "content"
image: "FraudProtection"
title: "Fraud Protection"
text: "Get started with Grow Fraud Protection."
position: 7
theme: 'platforms'
collection: 'cocos2dx_grow'
module: 'grow'
lang: 'cpp'
platform: 'cocos2dx'
---

# Fraud Protection

<div class="info-box">General information about Fraud Protection available in this [article](/university/articles/Grow_FraudProtection).</div>

In order to turn on Fraud Protection in cocos2d-x, you need to pass additional params to `storeParams` depending on a
billing provider.

*iOS*

- *SSV* - enables _Fraud Protection_ for iOS.

- *verifyOnServerFailure* - if you use Fraud Protection, optionally you set this param, if you want to get purchases
automatically verified in case of network failures during the verification process. Default value is `false`.

    ``` cpp
    storeParams->setObject(__Bool::create(true), "SSV");
	storeParams->setObject(__Bool::create(true), "verifyOnServerFailure");
	soomla::CCStoreService::initShared(assets, storeParams);
	```

*Google Play*

For Google Play you need to get clientId, clientSecret and refreshToken as explained in
[Google Play Purchase Verification](/android/store/Store_GooglePlayVerification) and use them like this:

	``` cpp
	storeParams->setObject(__String::create(<YOU_CLIENT_ID>), "clientId");
	storeParams->setObject(__String::create(<YOUR_CLIENT_SECRET>), "clientSecret");
	storeParams->setObject(__String::create(<YOUR_REFRESH_TOKEN>), "refreshToken");
	storeParams->setObject(__Bool::create(true), "verifyOnServerFailure");
	soomla::CCStoreService::initShared(assets, storeParams);
	```
