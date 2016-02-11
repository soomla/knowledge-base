---
layout: "soomla-content"
image: "InAppPurchase"
title: "Create Your Own Billing Plugin"
text: "Not all Android users pay with Google Play. Learn how to create new billing plugins for android-store to allow alternative payment methods."
position: 8
theme: 'soomla-unity'
collection: 'soomla_unity_store'
module: 'store'
platform: 'unity'
---

# Make your In App Billing Service available on unity3d-store

## About this document

This article is related to [Creating a billing service for android-store](/soomla/android/store/Store_CreateBillingPlugins). Please go over that document first and then come back to read this one.

Writing a billing service is nice but if you don't make it available to developers on their actual game engines it'll be pretty hard to use. In this article we'll describe how to make your android-store billing service available to unity3d-store developers.

We created a mechanism which makes it easy to extend SOOMLA on Unity. unity3d-store uses that mechanism so the SOOMLA Store module will be easily available to developers through the Settings panel and background configurations. To add your billing service to unity3d-store, you'll need to just plug into the existing configuration through unity3d-store's existing code.

## Directions

**Fork** unity3d-store and clone it to your local machine. Open the unity.scene from Assets folder and start making the necessary changes for your billing service:

1. Copy the jar file you built for your billing service on Android to a specific folder under Assets/Soomla/compilations/android-billing-services/...

2. Open [StoreSettings.cs](https://github.com/soomla/unity3d-store/blob/master/Soomla/Assets/Plugins/Soomla/Store/Config/) and add the code to support your billing service. You'll need to do 3 things in this file:

    - Write the code that'll draw the UI associated with your billing service into the SOOMLA Settings panel. See this [example](https://github.com/soomla/unity3d-store/blob/master/Soomla/Assets/Plugins/Soomla/Store/Config/StoreSettings.cs#L119).

    - Add fields that'll save the user input and will determine if your billing service is the one that the user has selected in the UI. See [AmazonBP](https://github.com/soomla/unity3d-store/blob/master/Soomla/Assets/Plugins/Soomla/Store/Config/StoreSettings.cs#L314).

    - Write code that’ll copy the jars related with your billing services from the android-billing-services folder to the Plugins/Android folder whenever your billing service is selected. Remove them when it's unselected. See this [example](https://github.com/soomla/unity3d-store/blob/master/Soomla/Assets/Plugins/Soomla/Store/Config/StoreSettings.cs#L111).

3. Open [StoreManifestTools.cs](https://github.com/soomla/unity3d-store/blob/master/Soomla/Assets/Plugins/Soomla/Store/Config/StoreManifestTools.cs) and add the code that'll do the necessary changes to AndroidManifest.xml so your service will run properly.

**Test** your work. Make sure that the right jars are in Plugins/Android and the AndroidManifest.xml looks ok when your billing service is selected and when other services are selected (and when nothing is selected).

**Finished?** Great! Now pull-request your work and wait for it to be merged into the master branch of the main unity3d-store repo. When it gets in, your billing service will also appear in the official Unity project SOOMLA releases for unity3d-store.
