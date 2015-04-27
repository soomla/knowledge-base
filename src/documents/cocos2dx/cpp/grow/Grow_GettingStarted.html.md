---
layout: "content"
image: "Tutorial"
title: "Getting Started"
text: "Get started with GROW open analytics for Cocos2d-x. Includes all of SOOMLA's modules: Core, Store, Profile, LevelUp, and Highway. Learn how to easily integrate all that SOOMLA offers into your game."
position: 2
theme: 'platforms'
collection: 'cocos2dx_grow'
module: 'grow'
platform: 'cocos2dx'
---

#Getting Started

##Overview

Soomla GROW is our flagship community driven analytics dashboard.  Developers using GROW can gain valuable insights about their games' performance and compare the data to benchmarks of other games in the GROW community.

**Note:** GROW analytics use all of Soomla's modules: Store, Profile and LevelUp. This document describes how to incorporate all of these modules as part of the setup.  You may choose to use only specific modules, however, to benefit from the full power of GROW analytics we recommend that you integrate Store, Profile and LevelUp.

##Getting Started

Go to the [GROW dashboard](http://dashboard.soom.la) and sign up \ login. Upon logging in, you will be directed to the main page of the dashboard. On the left side panel, you can click on "Demo Game" in order to know what to expect to see once you start using Grow.

<div class="info-box">If you didn't already, clone the Cocos2d-x framework from [here](https://github.com/cocos2d/cocos2d-x), or download it from the [Cocos2d-x website](http://www.cocos2d-x.org/download). Make sure the version you clone is supported by SOOMLA's modules (the tag is the version).</div>

1. Click on the right pointing arrow next to "Demo Game" > "Add New App" and fill in the required fields.

  ![alt text](/img/tutorial_img/unity_grow/addNewApp.png "Add new app")

2. Download the SOOMLA Framework. Go to the "Download" window on the left side-panel, or click [here](http://dashboard.soom.la/downloads), and choose "Cocos2dx".

3. Download the **GROW Bundle**. (NOTE: The "SOOMLA Bundle" contains the modules Store, Profile, & LevelUp, but does not contain Highway, meaning that you will not be able to participate in the data sharing community.)

4. Unzip the all-in-one file. Copy the contents of its extensions directory (soomla-cocos2dx-core, cocos2dx-core, cocos2dx-profile, cocos2dx-levelup, cocos2dx-highway) into the extensions directory located at the root of your Cocos2d-x framework. Then copy the contents of its external directory (jansson) into the external directory located at the root of your Cocos2d-x framework.

5. In your `AppDelegate.cpp` include `Cocos2dxHighway.h`:

  ```cpp
  #include "Cocos2dxHighway.h"
  ```

6. Initialize `CCSoomla` with a custom secret of your choice (**Custom Secret** is an encryption secret you provide that will be used to secure your data.):

  ```cpp
  soomla::CCSoomla::initialize("ExampleCustomSecret");
  ```

  <div class="warning-box">Choose this secret wisely, you can't change it after you launch your game!</div>

7. Initialize `CCSoomlaHighway` with the "Game Key" and "Env Key" given to you in the [dashboard](http://dashboard.soom.la):

  **Copy the "Game Key" and "Environment Key"** given to you from the [dashboard](http://dashboard.soom.la) and initialize `CCSoomlaHighway` with them. At this point, you're probably testing your integration and you want to use the **Sandbox** environment key.

  Explanation: The "game" and "env" keys allow for your game to distinguish multiple environments for the same game. The dashboard pre-generates two fixed environments for your game: **Production** and **Sandbox**. When you decide to publish your game, make sure to switch the env key to **Production**.  You can always generate more environments.  For example - you can choose to have a playground environment for your game's beta testers which will be isolated from your production environment and will thus prevent analytics data from being mixed between the two.  Another best practice is to have a separate environment for each version of your game.

  ```cpp
  CCSoomlaHighway::initShared(
        __String::create("yourGameKey"),
        __String::create("yourEnvKey"));
  ```

  ![alt text](/img/tutorial_img/cocos_grow/dashboardKeys.png "Keys")

  <div class="warning-box">Initialize `CCSoomlaHighway` ONLY ONCE when your application loads.</div>
8. Make sure to include the relevant headers:

    ```cpp
    #include "Cocos2dxStore.h"

    #include "Cocos2dxProfile.h"

    #include "Cocos2dxLevelUp.h"
    ```

9. Initialize the rest of the SOOMLA modules: `CCSoomlaStore`, `CCSoomlaProfile`, and `CCSoomlaLevelUp`.

    ```cpp
    // `assets` should implement the `CCStoreAssets` interface
    // and should include your entire virtual economy
    YourImplementationAssets *assets = YourImplementationAssets::create();

    __Dictionary *storeParams = __Dictionary::create();
    soomla::CCSoomlaStore::initialize(assets, storeParams);

    __Dictionary *profileParams = __Dictionary::create();
    soomla::CCSoomlaProfile::initialize(profileParams);

    // initialWorld - should be created here and contain all worlds and levels of the game
    // rewards - should contain a list of all rewards that are given through LevelUp
    soomla::CCSoomlaLevelUp::getInstance()->initialize(initialWorld, rewards);
    ```

<br>
<div class="info-box">The next steps are different according to which native platform you are building for.</div>

###**Instructions for iOS**

In your XCode project, perform the following steps:

1. Add `jansson` (**external/jansson/**) to your project (just add it as a source folder, make sure to check "create group").

2. For each of the following XCode projects:

  - `Cocos2dXHighway.xcodeproj` (**extensions/cocos2dx-highway/**)
  - `Cocos2dXCore.xcodeproj` (**extensions/soomla-cocos2dx-core/**)  
  - `Cocos2dXStore.xcodeproj` (**extensions/cocos2dx-store/**)
  - `Cocos2dXProfile.xcodeproj` (**extensions/soomla-cocos2dx-profile/**)  
  - `Cocos2dXLevelUp.xcodeproj` (**extensions/cocos2dx-levelup/**)

  perform the following:

  - Drag the project into your project
  - Add its targets to your **Build Phases->Target Dependencies**
  - Add the Products (\*.a) of the project to **Build Phases->Link Binary With Libraries**.

  ![alt text](/img/tutorial_img/cocos_grow/iosStep2.png "iOS Integration")

3. Add the following directories to **Build Settings->Header Search Paths** (with the `recursive` option):

  NOTE: This article assumes you have a `cocos2d` folder under your project folder which either contains the Cocos2d-x framework, or links to to its root folder.

 - `$(SRCROOT)/../cocos2d/extensions/soomla-cocos2dx-core/Soomla`
 - `$(SRCROOT)/../cocos2d/extensions/soomla-cocos2dx-core/build/ios/headers`
 - `$(SRCROOT)/../cocos2d/extensions/cocos2dx-store/Soomla`
 - `$(SRCROOT)/../cocos2d/extensions/cocos2dx-profile/Soomla`
 - `$(SRCROOT)/../cocos2d/extensions/cocos2dx-profile/build/ios/headers`
 - `$(SRCROOT)/../cocos2d/extensions/cocos2dx-levelup/Soomla`
 - `$(SRCROOT)/../cocos2d/extensions/cocos2dx-highway/Soomla`

  ![alt text](/img/tutorial_img/cocos_grow/headerSP.png "Header search paths")

4. Add the AFNetworking dependency to your project:
  - Add the static library (from `extensions/cocos2dx-highway/build/ios/libAFNetworking.a`) to **Build Phases->Link Binary With Libraries**.  Achieve this by clicking the + icon, and then "Add Other", and browse for the file.
  - Add `$(SRCROOT)/../cocos2d/extensions/cocos2dx-highway/build/ios/` to **Build Settings->Library Search Paths** (non-recursive)

5. Add the `-ObjC` to the **Build Setting->Other Linker Flags**

  ![alt text](/img/tutorial_img/ios_getting_started/linkerFlags.png "Linker Flags")

6. Make sure you have these 7 Frameworks linked to your XCode project:

  - Security
  - libsqlite3.0.dylib
  - StoreKit
  - CFNetwork
  - libicucore
  - SystemConfguration
  - AdSupport

7. Connect the Profile module to a social network provider:

  - [Facebook](/cocos2dx/cpp/profile/Profile_GettingStarted#facebook-for-ios)

  - [Google+](/cocos2dx/cpp/profile/Profile_GettingStarted#google-for-ios)

  - [Twitter](/cocos2dx/cpp/profile/Profile_GettingStarted#twitter-for-ios)

That's it! Now all you have to do is build your XCode project and run your game.

###**Instructions for Android**

1. Import cocos2dx-highway, cocos2dx-store, cocos2dx-profile, and cocos2dx-levelup module into your project's Android.mk by adding the following:

    ```
    LOCAL_WHOLE_STATIC_LIBRARIES += cocos2dx_store_static
    # add this line along with your other LOCAL_WHOLE_STATIC_LIBRARIES

    $(call import-module, extensions/cocos2dx-store)
    # add this line at the end of the file, along with the other import-module calls

    LOCAL_WHOLE_STATIC_LIBRARIES += cocos2dx_profile_static
    # add this line along with your other LOCAL_WHOLE_STATIC_LIBRARIES

    $(call import-module, extensions/cocos2dx-profile)
    # add this line at the end of the file, along with the other import-module calls

    LOCAL_WHOLE_STATIC_LIBRARIES += cocos2dx_levelup_static
    # add this line along with your other LOCAL_WHOLE_STATIC_LIBRARIES

    $(call import-module, extensions/cocos2dx-levelup)
    # add this line at the end of the file, along with the other import-module calls

    LOCAL_WHOLE_STATIC_LIBRARIES += cocos2dx_highway_static
    # add this line along with your other LOCAL_WHOLE_STATIC_LIBRARIES

    $(call import-module, extensions/cocos2dx-highway)
    # add this line at the end of the file, along with the other import-module calls
    ```

2. Add the following jars to your android project's classpath:

  From `extensions/cocos2dx-highway/build/android`

    - AndroidViper.jar

    - Cocos2dxAndroidHighway.jar

    - google-play-services.jar

  From `extensions/soomla-cocos2dx-core/build/android`

    - SoomlaAndroidCore.jar

    - Cocos2dxAndroidCore.jar

    - square-otto-1.3.2.jar

  From `extensions/cocos2dx-store/build/android`

    - AndroidStore.jar

    - Cocos2dxAndroidStore.jar

  From `extensions/cocos2dx-profile/build/android`

    - AndroidProfile.jar

    - Cocos2dxAndroidProfile.jar

  From `extensions/cocos2dx-levelup/build/android`

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

  - [Google Play](/cocos2dx/cpp/store/Store_GettingStarted#google-play)

  - [Amazon Appstore](/cocos2dx/cpp/store/Store_GettingStarted#amazon)

6. Connect the Profile module to a social network provider:

  - [Facebook](/cocos2dx/cpp/profile/Profile_GettingStarted#facebook-for-android)

  - [Google+](/cocos2dx/cpp/profile/Profile_GettingStarted#google-for-android)

  - [Twitter](/cocos2dx/cpp/profile/Profile_GettingStarted#twitter-for-android)

That's it! Don't forget to run the **build_native.py** script so that SOOMLA sources will be built with cocos2d-x.



##Back to the Dashboard

Once your app is running, you can go back to the [GROW dashboard](http://dashboard.soom.la) to verify the integration. Just refresh the page, and the environments tab should appear (be patient, this step can take a few minutes).

![alt text](/img/tutorial_img/unity_grow/verifyIntegration.png "Verify Integration")

And that's it! You have in-app purchasing, social engagement, and game architecture capabilities at your fingertips.


##Example

Below is a short example of how to initialize SOOMLA's modules. We suggest you read about the different modules and their entities in SOOMLA's Knowledge Base: [Store](/cocos2dx/cpp/store/Store_Model), [Profile](/cocos2dx/cpp/profile/Profile_MainClasses), and [LevelUp](/cocos2dx/cpp/levelup/Levelup_Model).

###CCStoreAssets

```cpp
/** ExampleAssets (your implementation of CCStoreAssets) **/
CCVirtualCurrency *coinCurrency = CCVirtualCurrency::create(
  CCString::create("Coins"),
  CCString::create(""),
  CCString::create("coin_currency_ID")
);

CCVirtualCurrencyPack *tenmuffPack = CCVirtualCurrencyPack::create(
  CCString::create("50 Coins"),                 // Name
  CCString::create("50 Coin pack"),             // Description
  CCString::create("coins_50"),                 // Item ID
  CCInteger::create(50),                        // Number of currencies in the pack
  CCString::create("coin_currency_ID"),         // Currency associated with this pack
  CCPurchaseWithMarket::create(                 // Purchase type
    CCString::create(50_COIN_PACK_PRODUCT_ID),
    CCDouble::create(0.99))
);

CCVirtualGood *shieldGood = CCSingleUseVG::create(
  CCString::create("Fruit Cake"),               // Name
  CCString::create("Delicios fruit cake!"),     // Description
  CCString::create("fruit_cake"),               // Item ID
  CCPurchaseWithVirtualItem::create(            // Purchase type
    CCString::create(MUFFIN_CURRENCY_ITEM_ID),
    CCInteger::create(225))
);

```
 <br>

###Initialization

```cpp
/** World **/
CCWorld* mainWorld = CCWorld::create(
  CCString::create("mainWorld_ID")
);

/** Score **/
CCScore *coinScore = CCVirtualItemScore::create(
  CCString::create("coinScore_ID"),
  CCString::create("coin_currency_ID")
);

/** Gate **/
CCGate *gate = CCScheduleGate::create(
  CCString::create("gate_ID"),
  CCSchedule::createAnyTimeUnLimited()
);

/** Mission **/
CCMission *mission = CCBalanceMission::create(
  CCString::create("mission_ID"),
  CCString::create("Collect 100 coins"),
  rewardsList,
  CCString::create("coinScore_ID"),
  CCInteger::create(100)
);

/** Levels **/
// Add 5 levels to the main world with the gate, score, and mission templates we just created.
mainWorld->batchAddLevelsWithTemplates(5, gate, score, mission);

/** Set the custom secret **/
soomla::CCSoomla::initialize("ExampleCustomSecret");

/** Initialize Highway **/
soomla::CCSoomlaHighway::initShared(__String::create("yourGameKey"),
                                    __String::create("yourEnvKey"));

/** Set up and initialize Core, Store, Profile, and LevelUp **/
ExampleAssets *assets = ExampleAssets::create();

__Dictionary *storeParams = __Dictionary::create();
storeParams->setObject(__String::create("ExamplePublicKey"), "androidPublicKey");

soomla::CCSoomlaStore::initialize(assets, storeParams);

__Dictionary *profileParams = __Dictionary::create();
soomla::CCSoomlaProfile::initialize(profileParams);

soomla::CCSoomlaLevelUp::getInstance()->initialize(mainWorld, NULL);
```
