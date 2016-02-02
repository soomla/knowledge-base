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

In order to turn on Fraud Protection in iOS, use the following params:   

- *VERIFY_PURCHASES* - enables _Fraud Protection_ for iOS.

- *VERIFY_ON_ITUNES_FAILURE* - if you use Fraud Protection, optionally you set this param, if you want to get purchases 
automatically verified in case of network failures during the verification process. Default value is `NO`. 

```objc
    VERIFY_PURCHASES = YES;
    VERIFY_ON_ITUNES_FAILURE = YES;
```
