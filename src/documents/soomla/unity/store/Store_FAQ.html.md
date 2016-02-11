---
layout: "soomla-content"
image: "Tutorial"
title: "FAQ"
text: "Frequently asked questions about unity3d-store"
position: 9
theme: 'soomla-unity'
collection: 'soomla_unity_store'
module: 'store'
platform: 'unity'
---

# **FAQ**

## Integration

**iOS: What libraries do I need in my project?**

After you download and import the unity3d-store package, you should see the following static libraries under `Assets/Plugins/iOS`:

- libUnitySoomlaiOSCore.a

- libUnityiOSStore.a

- libSoomlaIOSStore.a

- libSoomlaIOSCore.a

**Android: What jars do I need in my project?**

After you download and import the unity3d-store package, you should see the following jars under `Assets/Plugins/Android`:

- AndroidStore.jar

- AndroidStoreGooglePlay.jar

- SoomlaAndroidCore.jar

- UnityAndroidStore.jar

- UnitySoomlaAndroidCore.jar

- square-otto-1.3.2.jar

---

**Can I test unity3d-store in the Unity editor or only on device?**

Currently unity3d-store can only be tested on a device. In the near future it will be available for testing in the Unity editor.

---

**What is the purpose of the "Soomla Secret"?**

The Soomla Secret is a special string of your choice that the framework uses for encrypting end device data. The rationale here is that you want all the users' balances to be stored securely to prevent malicious users from hacking the numbers.

**NOTE:** You should choose this secret wisely and never change it! If you try to change it after you launch your game, old data from the database will become unavailable, meaning that your users will lose their balances.

---

**The prices of the products I want to sell should be set in iTunes Connect, Google Play, etc. Then why does Soomla expect me to supply a price in the code?**

All SOOMLA items require you to provide price by design. The purpose of this is to support displaying of prices in situations of no internet connectivity where you can't query iTunes Connect / Google Play.

---

**To build my project in the Unity editor, should I use "Build & Run" or "Build"?**

You should click on "Build". The reason for this is that Soomla has a post-build script that needs to run after the build, and if you click on “Build and run” you're not giving that script a chance.

**NOTE:** Before you click on "Build", you'll need to switch to whatever platform you want to run. To avoid problems, after clicking on "Switch platform", you must wait for the little circle at the bottom right corner to disappear.

---

**How can I test the products that I want to sell in Google Play?**

In testing mode, you should use [Google's test product IDs](http://developer.android.com/google/play/billing/billing_testing.html#billing-testing-static). Please notice that you should use "android.test.purchased" for all the products you want to successfully test, and that you can use this same product ID for more than one product. Once you are done with testing mode you can define your actual product IDs, as explained [here](docs/platforms/android/googleplayiab/).

---

## General

**What's the difference between "item IDs" and "product IDs" that Soomla requires and can they have the same value?**

Item IDs are internal IDs that SOOMLA uses to identify ALL entities in the economy. Product IDs are IDs you need to provide for products that you want to sell in the market (Google Play, App Store, etc.). These product IDs need to correspond exactly to what you define in the market.

Yes, a product with an item ID and a product ID can have the same value if you wish.

---

**I have a `LifetimeVG` defined in my game that is available for purchase in the market. Why does this item still appear after I restart my app?**

`LifetimeVG`s that are defined in the market, are defined as non-consumable products. This means that once the user purchases this item, the market saves it for them forever.

The only way to delete this kind of item is to refund it in the market.

---

**How can I test purchases of `LifetimeVG`s that are defined in Google Play?**

For testing purposes, you will need to purchase the `LifetimeVG` and then return it in order to repurchase.

Because of the characteristics of `LifetimeVG`s (kept for him/her forever in the Market), the only way to purchase the same item a second time, is to refund the first purchase.

Instructions for how to refund items in Google Play:

1. Visit your merchant account through Google's Developer Console (there's a link under the "Financial Reports" tab).

2. Refund the test item. See Google's [Refund an order](https://support.google.com/wallet/business/answer/2741495?hl=en) instructions for how to do this.

3. Call `StoreInventory.takeItem("lifetimeVG_ID", 1)` to clear your local inventory.

Note that Google Play caches purchase statuses locally, so you may have to wait a few minutes for the cancellation to actually take place. If you don't want to wait, a faster option is to clear the cache of the "Google Play Store" app and your app manually, to force the non-consumable item refresh.

---

**If I make changes to the products I have defined in iTunes Connect or Google Play, how can I see the updated information in my code?**

Use the function `RefreshMarketItemDetails` to see the current details of your IAP products. For example, if you update the price of a product in iTunes Connect (or Google Play), you will be able to see the new price after refreshing market item details. See more [here](https://github.com/soomla/unity3d-store/blob/master/Soomla/Assets/Plugins/Soomla/Store/SoomlaStore.cs).

---


## Common Errors

**After cloning unity3d-store from Github, I get the following error: "The type or namespace name "name" could not be found. Are you missing a using directive or an assembly reference?"**

This error usually means that you did not *recursively* clone the project, and therefore you are missing the submodules. The correct way to clone is: `git clone --recursive git@github.com:soomla/unity3d-store.git`

---

**I am using unity3d-store v1.5.1, Soomla Highway, and Storefront. I get the error "Assets/Soomla/Editor/SoomlaSettingsEditor.cs ... The imported type `SoomSettings' is defined multiple times"**

You need to use the unity3d-store version supplied in the dashboard [docs](http://soom.la/docs/#unity-getting-started) and NOT the latest one from Github. The storefront is supported only up to version 1.4.4 of unity3d-store, because the open source project is progressing faster than the storefront.

---

**I am getting the error "Can’t run the java command. Add your JDK folder to the PATH environment variable". What does this mean?**

If you are using Windows, this is a known bug in Windows that you can just ignore and everything will work fine.

---

**When I try to test a purchase after publishing my app on Google Play I fet the error message: "The item you were attempting to purchase couldn't be found".**

You need to define your item's product ID exactly the same in Google Play developer console and in your code. If not, you will get this error.

The other case where you will get such an error, is when you haven't waited long enough after publishing your game on Google Play. After publishing the game (to alpha or beta) you must wait a few hours (about 1-2 hours) until you can test your in-app products.
