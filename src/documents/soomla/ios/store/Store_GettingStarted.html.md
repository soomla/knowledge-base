---
layout: "soomla-content"
image: "Tutorial"
title: "Getting Started"
text: "Get started with iOS-store. Here you can find a basic example of initialization, economy framework integration, and links to downloads and IAP setup."
position: 1
theme: 'soomla-ios'
collection: 'soomla_ios_store'
module: 'store'
platform: 'ios'
---

# Getting Started

Before doing anything, SOOMLA recommends that you go through Apple's [Selling with In-App Purchase](https://developer.apple.com/appstore/in-app-purchase/index.html).

## Integrate iOS-store (sources)

<div class="info-box">We use ARC! Read about ARC [here](http://www.google.com/url?q=http%3A%2F%2Fen.wikipedia.org%2Fwiki%2FAutomatic_Reference_Counting&sa=D&sntz=1&usg=AFQjCNHaQBd32glc8dP7HSzlvW1RhjInQA).</div>

1. The static libs and headers you need are in the [zip](http://library.soom.la/fetch/ios-store/latest?cf=knowledge%20base) folder.

    - Set your project's "Library Search Paths" and "Header Search Paths" to that folder.

    - Add `-ObjC -lSoomlaiOSStore -lSoomlaiOSCore` to the project's "Other Linker Flags".

2. Make sure you have the following frameworks in your application's project: **Security, libsqlite3.0.dylib, StoreKit**.

3. Initialize `Soomla` with a secret that you chose to encrypt the user data saved in the DB. (For those who came from older versions, this should be the same as the old "custom secret"):

    ``` objectivec
    [Soomla initializeWithSecret:@"[YOUR CUSTOM GAME SECRET HERE]"];
    ```

    <div class="info-box">The secret is your encryption secret for data saved in the DB.</div>

4. Create your own implementation of `IStoreAssets` in order to describe your game's specific assets.

  - For a brief example, see the [example](#example) at the bottom.

  - For a more detailed example, see our [Muffin Rush Example](https://github.com/soomla/ios-store/blob/master/SoomlaiOSStoreExample/SoomlaiOSStoreExample/MuffinRushAssets.m).

5. Initialize `SoomlaStore` with the class you just created:

    ``` objectivec
    [[SoomlaStore getInstance] initializeWithStoreAssets:[[YourStoreAssetsImplementation alloc] init]];
    ```

    <div class="warning-box">Initialize `SoomlaStore` ONLY ONCE when your application loads.</div>

And that's it! You have Storage and in-app purchasing capabilities... ALL-IN-ONE.

<div class="info-box">**NOTE:**
    If `-ObjC` flag conflicts with other libs you use in your project, you should remove the `-ObjC` flag from the link flags in Xcode and add `-force_load $(BUILT_PRODUCTS_DIR)/<LIBRARY_NAME>` to `Other Linker Flags` for the following SOOMLA libraries:
    <ul>
        <li>`libSoomlaiOSCore.a`</li>
        <li>`libSoomlaiOSStore.a`</li>        
    </ul>                                                                                                                                                                                                                                                                     
</div>

## In App Purchasing

SOOMLA provides two ways in which you can let your users purchase items in your game:

 1. **PurchaseWithMarket** is a `PurchaseType` that allows users to purchase a `VirtualItem` via the App Store. These products need to be defined in iTunesConnect.

 2. **PurchaseWithVirtualItem** is a `PurchaseType` that lets your users purchase a `VirtualItem` with some amount of a different `VirtualItem`. *For Example:* Buying 1 Sword with 100 Gems.

In order to define the way your various virtual items (Coins, swords, hats...) are purchased, you'll need to create your implementation of `IStoreAssets` (described above in step 5 of [Getting Started](#getting-started)).

## Example

Create your own implementation of `IStoreAssets`; See the article about [IStoreAssets](/ios/store/Store_IStoreAssets), which includes a code example and explanations.

Then, initialize `SoomlaStore` with your implementation of `IStoreAssets`:

``` objectivec
@implementation AppDelegate
    ...
    - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
    {
        ...
        id<IStoreAssets> storeAssets = [[YourImplementationAssets alloc] init];
        [Soomla initializeWithSecret:@"ChangeMe!!"];
        [[SoomlaStore getInstance] initializeWithStoreAssets:storeAssets];
        ...
    }
    ...
@end
```

When your users buy products, iOS-store knows how to contact the App Store for you and redirect the users to their purchasing system to complete the transaction.

Don't forget to subscribe to events of successful or failed purchases - See [Event Handling](/ios/store/Store_Events).

<div class="info-box">To read about iTunes Connect in-app-purchase setup and integration with SOOMLA see our [iOS IAB tutorial](/ios/store/Store_AppStoreIAB).</div>
