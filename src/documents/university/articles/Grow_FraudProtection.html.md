---
layout: "content"
image: "FraudProtection"
title: "Fraud Protection"
text: "SOOMLA's Fraud Protection service is protecting you from IAP fraudsters. This document will explain how to integrate the service into your game and how it works."
position: 7
theme: 'platforms'
collection: 'university_articles'
module: 'articles'
platform: 'university'
---

# Fraud Protection

## Introduction

There are dozens of ways for hacking games, and they can be generally divided into 2 categories:

* File Overwriting - Hackers search games for important files and variables containing the current game score, currency
  balance, and level progression. And change values to their benefit.

* Fake in-game purchases - Special hacking applications perform fake communications with the game server.

IAP Fraud in mobile games is a serious issue. If you release your game without any mechanism to detect and prevent IAP fraud then you probably don't really care about your game enough to make it a good one. There are many reasons Fraud can hurt your game, for example:

* Fraudulent players can mess up your analytics.

* Fraudulent players can make other (paying or viral) users leave b/c they can never go up on the leaderboard.

And more ...

## Encrypted Storage

Developers very often save their game data in plain text files. Such files are easily found by hackers, who can then change
their contents to their liking, and control the game data as they wish.

There is a simple and robust solution for this - encryption. Using a good encryption algorithm to encrypt your data
will make it almost impossible for a hacker to decrypt it.

SOOMLA's solutions use encrypted storage and keep your games safe from this kind of hacking.
You have probably already noticed the "Soomla Secret" setting, when you set up SOOMLA libraries. It is up to you to define a key
as strong as possible, and keep it secret to prevent your games from being hacked.

## Server Side Verification

When an in-app purchase is made in your game, the game connects to the App Store or Google Play server in order to confirm that the transaction
was completed successfully. After getting confirmation from the server the user is given the purchased virtual goods.

Hacking tools intercept such requests to the App Store or Google Play and emulate their behavior, send a confirmation back for every request made
by your game. As result the hacker would get virtual goods for free.

The best way to handle this type of hacking tools is by using a private server to verify these transactions. When a user makes an in-app purchase
an electronic receipt is generated, saved and sent back to the game. The game can then send the receipt to the private server and the server can verify
this receipt with Google or Apple servers to confirm that the receipt is valid. The response is sent back to the game and only then will the user get the purchased virtual goods.

Most games do not use a server, because the gameplay happens on the client side and there is no need for it.
Implementing and maintaining a server takes a lot of effort, and creating one solely for the purpose of receipt verification seems a bit excessive.
That's why some games skip the server side verification, and this hole in security is extensively used by hackers.

Good news! Today you do not need such a server at all. SOOMLA provides a receipt verification service with the added value of checking for suspicious activity.

## Fraud protection on our platform

The way to configure Fraud protection depends on a platform you use:

* [Unity3d](/unity/Grow_FraudProtection.html)

* [Cocos2d-x](/cocos2dx/cpp/Grow_FraudProtection.html)

* [Cocos2d-js](/cocos2dx/js/Grow_FraudProtection.html)

* [Android](/soomla/android/store/Grow_FraudProtection.html)

* [iOS](/soomla/ios/store/Grow_FraudProtection.html)
