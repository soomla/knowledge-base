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

In order to turn on Fraud Protection in cocos2d-x, you need to enable additional params in your Store config depending on a
billing provider.

*iOS*

- calling of *activateFraudProtection* enables fraud protection on iOS.

- *verifyOnServerFailure* - if you use Fraud Protection, optionally you set this param, if you want to get purchases
automatically verified in case of network failures during the verification process. Default value is `false`.

    ``` cpp
    bool verifyOnFailure = true;
    soomla::CCSoomlaStoreConfigBuilder *storeConfig
       = soomla::CCSoomlaStoreConfigBuilder::create();
    storeConfig
      ->setIosConfiguration(soomla::CCSoomlaStoreIosConfigBuilder::create()
      ->activateFraudProtection(verifyOnFailure));
	soomla::CCStoreService::initShared(assets, storeConfig->build());
	```

*Google Play*

For Google Play you need to get clientId, clientSecret and refreshToken as explained in
[Google Play Purchase Verification](/soomla/android/store/Store_GooglePlayVerification) and use them like this:

	``` cpp
	 bool verifyOnFailure = true;
    soomla::CCSoomlaStoreConfigBuilder *storeConfig
       = soomla::CCSoomlaStoreConfigBuilder::create();
    storeConfig
     ->setGpConfiguration(soomla::CCSoomlaStoreGpConfigBuilder::create()
       ->setAndroidPublicKey("ExamplePublicKey")
       ->setTestPurchases(true)
       ->activateFraudProtection("clientId", "clientSecret", "refreshToken", verifyOnFailure));
	soomla::CCStoreService::initShared(assets, storeConfig->build());
	```
