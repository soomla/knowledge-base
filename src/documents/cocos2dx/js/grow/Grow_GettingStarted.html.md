---
layout: "content"
image: "Tutorial"
title: "Getting Started"
text: "Get started with GROW open analytics for Cocos2d-x. Includes all of SOOMLA's modules: Core, Store, Profile, LevelUp, and Highway. Learn how to easily integrate all that SOOMLA offers into your game."
position: 2
theme: 'platforms'
collection: 'cocos2djs_grow'
module: 'grow'
lang: 'js' 
platform: 'cocos2dx'
---

#Getting Started

##Overview

Soomla GROW is our flagship community driven analytics dashboard.  Developers using GROW can gain valuable insights 
about their games' performance and compare the data to benchmarks of other games in the GROW community.

**Note:** GROW analytics use all of Soomla's modules: Store, Profile and LevelUp. This document describes how to 
incorporate all of these modules as part of the setup.  You may choose to use only specific modules, however, to benefit 
from the full power of GROW analytics we recommend that you integrate Store, Profile and LevelUp.

##Getting Started

Go to the [GROW dashboard](http://dashboard.soom.la) and sign up \ login. Upon logging in, you will be directed to the 
main page of the dashboard. On the left side panel, you can click on "Demo Game" in order to know what to expect to see 
once you start using Grow.

<div class="info-box">If you didn't already, clone the Cocos2d-x framework from 
[here](https://github.com/cocos2d/cocos2d-x), or download it from the 
[Cocos2d-x website](http://www.cocos2d-x.org/download). Make sure the version you clone is supported by SOOMLA's modules 
(the tag is the version).</div>

1. Click on the right pointing arrow next to "Demo Game" > "Add New App" and fill in the required fields.

  ![alt text](/img/tutorial_img/unity_grow/addNewApp.png "Add new app")

2. Download the SOOMLA Framework. Go to the "Download" window on the left side-panel, or click 
[here](http://dashboard.soom.la/downloads), and choose "Cocos2dx".

3. Download the **GROW Bundle**. (NOTE: The "SOOMLA Bundle" contains the modules Store, Profile, & LevelUp, but does not 
contain Highway, meaning that you will not be able to participate in the data sharing community.)

4. Unzip the all-in-one file. Copy the contents of its extensions directory (soomla-cocos2dx-core, cocos2dx-core, 
cocos2dx-profile, cocos2dx-levelup, cocos2dx-highway) into the extensions directory located at the root of your 
Cocos2d-x framework. Then copy the contents of its external directory (jansson) into the external directory located at 
the root of your Cocos2d-x framework.

1. Register soomla js-bindings in your `js_module_register.h`:

    ```cpp
    #include "Cocos2dxStore.h"
    ...
    sc->addRegisterCallback(register_jsb_soomla);
    ```

1. Initialize Native Bridge in your `AppDelegate.cpp` in the method `applicationDidFinishLaunching`:

    ```cpp
        // Bind native bridges
        soomla::CCCoreBridge::getInstance();
        soomla::CCStoreBridge::getInstance();
        soomla::CCProfileBridge::getInstance();
        soomla::CCLevelUpBridge::getInstance();
        soomla::CCHighwayBridge::getInstance();
    ```

1. Copy soomla js-files to your project:

    ```bash
    mkdir script/soomla
    cp frameworks/runtime-src/Classes/soomla-cocos2dx-core/js/* script/soomla/
    cp frameworks/runtime-src/Classes/cocos2dx-store/js/* script/soomla/
    cp frameworks/runtime-src/Classes/cocos2dx-profile/js/* script/soomla/
    cp frameworks/runtime-src/Classes/cocos2dx-levelup/js/* script/soomla/
    cp frameworks/runtime-src/Classes/cocos2dx-highway/js/* script/soomla/
    ```


1. Initialize `CCSoomla` with a custom secret of your choice (**Custom Secret** is an encryption secret you provide that will be used to secure your data.):

  ```js
  soomla::CCSoomla::initialize("ExampleCustomSecret");
  ```

  <div class="warning-box">Choose this secret wisely, you can't change it after you launch your game!</div>

1. Initialize `SoomlaHighway` with the "Game Key" and "Env Key" given to you in the [dashboard](http://dashboard.soom.la):

  **Copy the "Game Key" and "Environment Key"** given to you from the [dashboard](http://dashboard.soom.la) and 
  initialize `SoomlaHighway` with them. At this point, you're probably testing your integration and you want to use the 
  **Sandbox** environment key.

  Explanation: The "game" and "env" keys allow for your game to distinguish multiple environments for the same game. 
  The dashboard pre-generates two fixed environments for your game: **Production** and **Sandbox**. When you decide to 
  publish your game, make sure to switch the env key to **Production**.  You can always generate more environments.  For example - you can choose to have a playground environment for your game's beta testers which will be isolated from your production environment and will thus prevent analytics data from being mixed between the two.  Another best practice is to have a separate environment for each version of your game.

  ```js
  Soomla.Cocos2dXSoomlaHighway.createShared('yourGameKey', 'yourEnvKey');
  ```

  ![alt text](/img/tutorial_img/cocos_grow/dashboardKeys.png "Keys")

  <div class="warning-box">Initialize `SoomlaHighway` ONLY ONCE when your application loads.</div>
1. Initialize the rest of the SOOMLA modules: `SoomlaStore`, `SoomlaProfile`, and `SoomlaLevelUp`.

    ```js
    // `assets` should implement the `IStoreAssets` interface
    // and should include your entire virtual economy
    var assets = YourImplementationAssets.create();

    var storeParams = {};
    Soomla.soomlaStore.initialize(assets, storeParams);

    var profileParams = {};
    Soomla.soomlaProfile.initialize(profileParams);

    // initialWorld - should be created here and contain all worlds and levels of the game
    // rewards - should contain a list of all rewards that are given through LevelUp
    Soomla.soomlaLevelUp.initialize(initialWorld, rewards);
    ```
<br>
<div class="info-box">The next steps are different according to which native platform you are building for.</div>

###**Instructions for iOS**

In your XCode project, perform the following steps:

1. In order to proceed Soomla needs to know, where your cocos2d-x is. Please, create a symlink with cocos2d-x at the path `frameworks/runtime-src` of the project, which looks at cocos2d-x. It can be something like that:

    ```bash
ln -s <your-cocos2d-js-path>/frameworks/js-bindings/cocos2d-x frameworks/runtime-src/cocos2d-x
    ````

1. Add `jansson` (**frameworks/runtime-src/Classes/jansson/**) to your project (just add it as a source folder, make sure to check "create group").

1. For each of the following XCode projects:

  - `Cocos2dXCore.xcodeproj` (**frameworks/runtime-src/Classes/soomla-cocos2dx-core/**)  
  - `Cocos2dXStore.xcodeproj` (**frameworks/runtime-src/Classes/cocos2dx-store/**)
  - `Cocos2dXProfile.xcodeproj` (**frameworks/runtime-src/Classes/cocos2dx-profile/**)  
  - `Cocos2dXLevelUp.xcodeproj` (**frameworks/runtime-src/Classes/cocos2dx-levelup/**)
  - `Cocos2dXHighway.xcodeproj` (**frameworks/runtime-src/Classes/cocos2dx-highway/**)

  perform the following:

    a. Drag the project into your project.

    b. Add its targets to your **Build Phases->Target Dependencies**.

    c. Add its `.a` files to **Build Phases->Link Binary With Libraries**.

  ![alt text](/img/tutorial_img/cocos_grow/iosStep2.png "iOS Integration")

1. Add the following directories to **Build Settings->Header Search Paths** (with the `recursive` option):

  NOTE: This article assumes you have a `cocos2d` folder under your project folder which either contains the Cocos2d-x framework, or links to to its root folder.

 - `$(SRCROOT)/../Classes/soomla-cocos2dx-core/Soomla`
 - `$(SRCROOT)/../Classes/soomla-cocos2dx-core/build/ios/headers`
 - `$(SRCROOT)/../Classes/cocos2dx-store/Soomla`
 - `$(SRCROOT)/../Classes/cocos2dx-store/build/ios/headers`
 - `$(SRCROOT)/../Classes/cocos2dx-profile/Soomla`
 - `$(SRCROOT)/../Classes/cocos2dx-profile/build/ios/headers`
 - `$(SRCROOT)/../Classes/cocos2dx-levelup/Soomla`
 - `$(SRCROOT)/../Classes/cocos2dx-levelup/build/ios/headers`
 - `$(SRCROOT)/../Classes/cocos2dx-highway/Soomla`
 - `$(SRCROOT)/../Classes/cocos2dx-highway/build/ios/headers`

  ![alt text](/img/tutorial_img/cocos_grow/headerSP.png "Header search paths")

1. Add the AFNetworking dependency to your project:
  - Add the static library (from `frameworks/runtime-src/Classes/cocos2dx-highway/build/ios/libAFNetworking.a`) to **Build Phases->Link Binary With Libraries**.  Achieve this by clicking the + icon, and then "Add Other", and browse for the file.
  - Add `$(SRCROOT)/../Classes/cocos2dx-highway/build/ios/` to **Build Settings->Library Search Paths** (non-recursive)

1. Add the `-ObjC` to the **Build Setting->Other Linker Flags**

  ![alt text](/img/tutorial_img/ios_getting_started/linkerFlags.png "Linker Flags")

1. Make sure you have these 7 Frameworks linked to your XCode project:

  - Security
  - libsqlite3.0.dylib
  - StoreKit
  - CFNetwork
  - libicucore
  - SystemConfguration
  - AdSupport

1. Connect the Profile module to a social network provider:

  - [Facebook](/cocos2dx/js/profile/Profile_GettingStarted#facebook-for-ios)

  - [Google+](/cocos2dx/js/profile/Profile_GettingStarted#google-for-ios)

  - [Twitter](/cocos2dx/js/profile/Profile_GettingStarted#twitter-for-ios)

That's it! 
> Don't forget to `Build Custom Simulator` for iOS, if you use Cocos IDE.


###**Instructions for Android**

1. Import cocos2dx-highway, cocos2dx-store, cocos2dx-profile, and cocos2dx-levelup module into your project's Android.mk by adding the following:

    ```
    # Add these lines along with your other LOCAL_WHOLE_STATIC_LIBRARIES
    LOCAL_WHOLE_STATIC_LIBRARIES += cocos2dx_store_static
    LOCAL_WHOLE_STATIC_LIBRARIES += cocos2dx_profile_static
    LOCAL_WHOLE_STATIC_LIBRARIES += cocos2dx_levelup_static
    LOCAL_WHOLE_STATIC_LIBRARIES += cocos2dx_highway_static

	# Add these lines at the end of the file, along with the other import-module calls
    $(call import-module, cocos2dx-store)  
    $(call import-module, cocos2dx-profile)  
    $(call import-module, cocos2dx-levelup)  
    $(call import-module, cocos2dx-highway)  
    ```

2. Add the following jars to your android project's classpath:

  From `frameworks/runtime-src/Classes/cocos2dx-highway/build/android`
    - AndroidViper.jar
    - Cocos2dxAndroidHighway.jar
    - google-play-services.jar

  From `frameworks/runtime-src/Classes/soomla-cocos2dx-core/build/android`
    - SoomlaAndroidCore.jar
    - Cocos2dxAndroidCore.jar
    - square-otto-1.3.2.jar

  From `frameworks/runtime-src/Classes/cocos2dx-store/build/android`
    - AndroidStore.jar
    - Cocos2dxAndroidStore.jar

  From `frameworks/runtime-src/Classes/cocos2dx-profile/build/android`
    - AndroidProfile.jar
    - Cocos2dxAndroidProfile.jar

  From `frameworks/runtime-src/Classes/cocos2dx-levelup/build/android`
    - AndroidLevelUp.jar
    - Cocos2dxAndroidLevelUp.jar

4. Update your `AndroidManifest.xml`:

  ``` xml
  <uses-permission android:name="android.permission.INTERNET"/>
  <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
  <uses-permission android:name="com.android.vending.BILLING"/>

  <!-- optional: required for uploadImage from SD card -->
  <uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />

  <application
            ...
            android:name="com.soomla.SoomlaApp">
  </application>
  ```

5. Connect the Store module to your desired billing service:

  - [Google Play](/cocos2dx/js/store/Store_GettingStarted#google-play)

  - [Amazon Appstore](/cocos2dx/js/store/Store_GettingStarted#amazon)

6. Connect the Profile module to a social network provider:

  - [Facebook](/cocos2dx/js/profile/Profile_GettingStarted#facebook-for-android)

  - [Google+](/cocos2dx/js/profile/Profile_GettingStarted#google-for-android)

  - [Twitter](/cocos2dx/js/profile/Profile_GettingStarted#twitter-for-android)

That's it! 
> Don't forget to `Build Custom Simulator` for Android, if you use Cocos IDE.


##Back to the Dashboard

Once your app is running, you can go back to the [GROW dashboard](http://dashboard.soom.la) to verify the integration. 
Just refresh the page, and the environments tab should appear (be patient, this step can take a few minutes).

![alt text](/img/tutorial_img/unity_grow/verifyIntegration.png "Verify Integration")

And that's it! You have in-app purchasing, social engagement, and game architecture capabilities at your fingertips.


##Example

Below is a short example of how to initialize SOOMLA's modules. We suggest you read about the different modules and 
their entities in SOOMLA's Knowledge Base: [Store](/cocos2dx/js/store/Store_Model), 
[Profile](/cocos2dx/js/profile/Profile_MainClasses), and [LevelUp](/cocos2dx/js/levelup/Levelup_Model).

###IStoreAssets

```js
/** ExampleAssets (your implementation of CCStoreAssets) **/
  var coinCurrency = Soomla.Models.VirtualCurrency.create({
    itemId: 'coin_currency_ID',               // Item ID
    name: 'Coins'                             // Name
  });

  var tenmuffPack = Soomla.Models.VirtualCurrencyPack.create({
    itemId: 'coins_50',                       // Item ID
    name: '50 Coins',                         // Name
    description: '50 Coin pack',              // Description
    currency_itemId: 'coin_currency_ID',      // The currency associated with this pack 
    currency_amount: 50,                      // Number of currencies in the pack
    purchasableItem: Soomla.Models.PurchaseWithMarket.createWithMarketItem(
      50_COIN_PACK_PRODUCT_ID,                // Product ID
      0.99)                                   // Initial price
  });


  var shieldGood = Soomla.Models.SingleUseVG.create({
    itemId: 'fruit_cake',                     // Item ID
    name: 'Fruit Cake',                       // Name
    description: 'Delicios fruit cake!',      // Description
    purchasableItem: Soomla.Models.PurchaseWithVirtualItem.create({
      pvi_itemId: MUFFIN_CURRENCY_ITEM_ID,
      pvi_amount: 225
    })
  });
```
 <br>

###Initialization

```js
/** World **/
  var mainWorld = Soomla.Models.World.create({
    itemId: 'mainWorld_ID'                      // ID
  });

/** Score **/
var diamondScore = Soomla.Models.VirtualItemScore.create({
	itemId: 'coinScore_ID',                 // ID
	associatedItemId: 'coin_currency_ID'    // Associated item ID
});

/** Gate **/
var gate = Soomla.Models.ScheduleGate.create({
    itemId: 'gate_ID',                          // ID
	schedule: Schedule.createAnyTimeUnLimited() // Schedule
});

/** Mission **/
  var mission = Soomla.Models.BalanceMission.create({
    itemId: 'mission_ID',                       // ID
    name: 'Collect 100 coins',                  // Name
    rewards: rewardsList,                       // Rewards
    associatedItemId: 'coinScore_ID',           // Associated virtual item
    desiredBalance: 100                         // Desired balance
  });
    
/** Levels **/
// Add 5 levels to the main world with the gate, score, and mission templates we just created.
mainWorld.batchAddLevelsWithTemplates(5, gate, score, mission);

/** Set the custom secret **/
Soomla.initialize("ExampleCustomSecret");

/** Initialize Highway **/
Soomla.Cocos2dXSoomlaHighway.createShared('yourGameKey', 'yourEnvKey');

/** Set up and initialize Store, Profile, and LevelUp **/
var assets = ExampleAssets.create();

var storeParams = {
    androidPublicKey: 'ExamplePublicKey'
};

Soomla.soomlaStore.initialize(assets, storeParams);

var profileParams = {};
Soomla.soomlaProfile.initialize(profileParams);

Soomla.soomlaLevelUp.initialize(mainWorld);
```
