---
layout: "content"
image: "Bundle"
title: "GrowSpend"
text: "The perfect virtual economy solution for your game. If you want Fraud Protection, cross-device balance SYNC and remote economy configurator then this bundle is for you."
position: 11
theme: 'platforms'
collection: 'cocos2dx_grow'
module: 'grow'
lang: 'cpp'
platform: 'cocos2dx'
---

# GrowSpend - Bundle

## Overview

GrowSpend is the perfect virtual economy solution for your game. If you want Fraud Protection, cross-device balance SYNC and remote economy configurator then this bundle is for you. GrowSpend connects you to GROW, SOOMLA's flagship - a community-driven data network. Mobile game studios can take advantage of the different GROW products in order to get valuable insights about their games' performance and increase retention and monetization. [Read more...](/cocos2dx/cpp/grow/Grow_About)

GrowSpend includes:

- SOOMLA's open-source module - [Store](/cocos2dx/cpp/store/Store_GettingStarted)

- [State & Economy Sync](/cocos2dx/cpp/grow/Grow_Sync)

- [IAP Fraud Protection](/cocos2dx/cpp/grow/Grow_FraudProtection)

- [Analytics](/cocos2dx/cpp/grow/Grow_Analytics)

- [Whales Report](/cocos2dx/cpp/grow/Grow_WhalesReport)

- [Insights](/cocos2dx/cpp/grow/Grow_Insights)

**Note:** Cross-device SYNC is using the Profile module which allows your users to login with their social provider. If you want that, [integrate Profile](/cocos2dx/cpp/profile/Profile_GettingStarted) as well.

## Integrating GrowSpend

### New Game & Configurations

Go to the [GROW dashboard](http://dashboard.soom.la) and sign up \ login. Upon logging in, you will be directed to the main page of the dashboard. You will need to create a new game in order to start your journey with GROW.

<div class="info-box">If you didn't already, clone the Cocos2d-x framework from [here](https://github.com/cocos2d/cocos2d-x), or download it from the [Cocos2d-x website](http://www.cocos2d-x.org/download). Make sure the version you clone is supported by SOOMLA's modules (the tag is the version).</div>

1. In the games screen click on the "+" button to add a new game. If it's your first time in the dashboard, just click on the "+" button underneath the "Create your first game" label in the middle of the screen.

	  ![alt text](/img/tutorial_img/cocos_grow/addNewApp.png "Add new app")

	* Once you created your game, you'll be redirected to download  **GrowSpend**. You'll see an instructions screen, you can continue with that or stay here for the extended version.  

2. Unzip the downloaded file and copy its contents into the cocos2d directory located at the root of your Cocos2d-x project.


### Initialize modules

<div class="info-box">Make sure to initialize each module ONLY ONCE when your application loads.</div>
<br>
<div class="info-box">The CCGrowHighway module is the module responsible for connecting your game to the GROW network. In order for it to operate it only needs to be initialized.</div>

1. In your `AppDelegate.cpp` include `Cocos2dxHighway.h`:

  ```cpp
  #include "Cocos2dxHighway.h"
  ```

2. Initialize `CCSoomla` with a custom secret of your choice (**Custom Secret** is an encryption secret you provide that will be used to secure your data.):

  ```cpp
  soomla::CCSoomla::initialize("ExampleCustomSecret");
  ```

<div class="warning-box">Choose this secret wisely, you can't change it after you launch your game!</div>

* **Fraud Protection (RECOMMENDED):**
3. Initialize `CCGrowHighway` with the "Game Key" and "Env Key" given to you in the [dashboard](http://dashboard.soom.la):

  **Copy the "Game Key" and "Environment Key"** given to you from the [dashboard](http://dashboard.soom.la) and initialize `CCGrowHighway` with them. At this point, you're probably testing your integration and you want to use the **Sandbox** environment key.

  Explanation: The "game" and "env" keys allow for your game to distinguish multiple environments for the same game. The dashboard pre-generates two fixed environments for your game: **Production** and **Sandbox**. When you decide to publish your game, make sure to switch the env key to **Production**.  You can always generate more environments.  For example - you can choose to have a playground environment for your game's beta testers which will be isolated from your production environment and will thus prevent analytics data from being mixed between the two.  Another best practice is to have a separate environment for each version of your game.

  ``` cpp
  // Make sure to make this call in your AppDelegate's
  // applicationDidFinishLaunching method, and before
  // initializing any other SOOMLA/GROW components
  // i.e. before CCSoomlaStore::initialize(...)
  grow::CCGrowHighway::initShared(
      __String::create("yourGameKey"),
      __String::create("yourEnvKey"));
  ```

  <img src="/img/tutorial_img/cocos_grow/dashboardKeys.png" alt="Game key and Env key" style="border:0;">

4. Initialize Sync:

	``` cpp
	// Make sure to make this call AFTER initializing HIGHWAY,
	// and BEFORE initializing STORE/PROFILE
	bool modelSync = true; 	// Remote Economy Management - Synchronizes your game's
                             // economy model between the client and server - enables
                             // you to remotely manage your economy.

	bool stateSync = true; // Synchronizes the users' balances data with the server
                           // and across his other devices.
						   // Must be TRUE in order to use LEADERBOARDS.

	// State sync and Model sync can be enabled/disabled separately.
	grow::CCGrowSync::initShared(modelSync, stateSync);
	```

5. Initialize `CCSoomlaStore`:

    ```cpp
    // `YourImplementationAssets` should implement the `CCStoreAssets` interface
    // and should include your entire virtual economy
    YourImplementationAssets *assets = YourImplementationAssets::create();

    __Dictionary *storeParams = __Dictionary::create();
    soomla::CCSoomlaStore::initialize(assets, storeParams);
    ```

<br>
<div class="info-box">The next steps are different according to which native platform you are building for.</div>

### **Instructions for iOS**

In your XCode project, perform the following steps:

1. Add `jansson` (**external/jansson/**) to your project (just add it as a source folder, make sure to check "create group").

2. For each of the following XCode projects:

  - `Cocos2dXHighway.xcodeproj` (**extensions/cocos2dx-highway/**)

  - `Cocos2dXCore.xcodeproj` (**extensions/soomla-cocos2dx-core/**)  

  - `Cocos2dXStore.xcodeproj` (**extensions/cocos2dx-store/**)

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

 - `$(SRCROOT)/../cocos2d/extensions/cocos2dx-highway/Soomla`

  ![alt text](/img/tutorial_img/cocos_grow/headerSP.png "Header search paths")

5. Add the `-ObjC` to the **Build Setting->Other Linker Flags**

  ![alt text](/img/tutorial_img/ios_getting_started/linkerFlags.png "Linker Flags")

6. Make sure you have these 10 Frameworks linked to your XCode project:

  - Security

  - libsqlite3.0.dylib
  
  - libz.dylib

  - StoreKit

  - CFNetwork

  - libicucore

  - SystemConfiguration

  - AdSupport

  - MediaPlayer

  - GameController

That's it! Now all you have to do is build your XCode project and run your game.

### **Instructions for Android**

1. Import cocos2dx-highway, cocos2dx-store, modules into your project's Android.mk by adding the following:

    ```
    LOCAL_STATIC_LIBRARIES += cocos2dx_store_static
    LOCAL_STATIC_LIBRARIES += cocos2dx_highway_static
    # add these lines along with your other LOCAL_STATIC_LIBRARIES

    $(call import-module, extensions/cocos2dx-store)
    $(call import-module, extensions/cocos2dx-highway)
    # add these lines at the end of the file, along with the other import-module calls
    ```

2. Add the following jars to your android project's classpath (or into its libs dir):

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

4. Update your `AndroidManifest.xml`:

  ``` xml
  <uses-permission android:name="android.permission.INTERNET"/>
  <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
  <uses-permission android:name="com.android.vending.BILLING"/>
  
  ```

5. Connect the Store module to your desired billing service:

  - [Google Play](/cocos2dx/cpp/store/Store_GettingStarted#google-play)

  - [Amazon Appstore](/cocos2dx/cpp/store/Store_GettingStarted#amazon)

That's it! Don't forget to run the **build_native.py** script so that SOOMLA sources will be built with cocos2d-x.

## Module usage & event handling

The next step is to create your game specific implementation for each of the modules. Use SOOMLA's awesome products to create better in-game economy and user experience.  
In order to be notified about (and handle) SOOMLA-related events, you will also need to create event-handling functions. Refer to the following sections for more information:

- **Store** - With Store you create your in-game virtual economy. It'll allow you to easily setup IAP and safely store your users' balances.  
[API](/cocos2dx/cpp/store/Store_Model) | [Main classes](/cocos2dx/cpp/store/Store_MainClasses) | [Events](/cocos2dx/cpp/store/Store_Events)

- **State & Economy Sync** - Your users want to get their balances, levels and other game state parameters when they switch devices. Now you can let them do it.  
[Events](/cocos2dx/cpp/grow/Grow_Sync#Events)

## Example

Below is a short example of how to initialize SOOMLA's modules. We suggest you read about the different modules and their entities in SOOMLA's Knowledge Base: [Store](/cocos2dx/cpp/store/Store_Model) and [State & Economy Sync](/cocos2dx/cpp/grow/Grow_Sync).

### CCStoreAssets

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

### Initialization

```cpp
using namespace grow;

bool AppDelegate::applicationDidFinishLaunching() {
	Director::getInstance()->getEventDispatcher()->addCustomEventListener(CCHighwayConsts::EVENT_GROW_SYNC_INITIALIZED, CC_CALLBACK_1(Example::onGrowSyncInitialized, this));
	Director::getInstance()->getEventDispatcher()->addCustomEventListener(CCHighwayConsts::EVENT_STATE_SYNC_FINISHED, CC_CALLBACK_1(Example::onStateSyncFinished, this));
	Director::getInstance()->getEventDispatcher()->addCustomEventListener(CCHighwayConsts::EVENT_MODEL_SYNC_FINISHED, CC_CALLBACK_1(Example::onModelSyncFinished, this));

	soomla::CCSoomla::initialize("ExampleCustomSecret");

	// Make sure to make this call in your AppDelegate's
	// applicationDidFinishLaunching method, and before
	// initializing any other SOOMLA/GROW components
	// i.e. before CCSoomlaStore::initialize(...)
	grow::CCGrowHighway::initShared(__String::create("yourGameKey"),
									__String::create("yourEnvKey"));

	// Make sure to make this call AFTER initializing HIGHWAY,
	// and BEFORE initializing STORE/PROFILE
	bool modelSync = true; 	// Remote Economy Management - Synchronizes your game's
							 // economy model between the client and server - enables
							 // you to remotely manage your economy.

	bool stateSync = true; // Synchronizes the users' balances data with the server
						   // and across his other devices.
						   // Must be TRUE in order to use LEADERBOARDS.

	// State sync and Model sync can be enabled/disabled separately.
	grow::CCGrowSync::initShared(modelSync, stateSync);

	/** Set up and initialize Store and Profile **/
	ExampleAssets *assets = ExampleAssets::create();

	__Dictionary *storeParams = __Dictionary::create();
	storeParams->setObject(__String::create("ExamplePublicKey"), "androidPublicKey");

	soomla::CCSoomlaStore::initialize(assets, storeParams);
}

void Example::onGrowSyncInitialized(EventCustom *event) {
	cocos2d::log("GROW Sync has been initialized.");
}

void Example::onModelSyncFinished(EventCustom *event) {
    __Dictionary *eventData = (__Dictionary *)event->getUserData();
    __Array *changedComponents = dynamic_cast<__Array *>(eventData->objectForKey(CCHighwayConsts::DICT_ELEMENT_MODEL_CHANGED_COMPONENTS));
	cocos2d::log("Model Sync has finished.");
}

void Example::onStateSyncFinished(EventCustom *event) {
    __Dictionary *eventData = (__Dictionary *)event->getUserData();
    __Array *changedComponents = dynamic_cast<__Array *>(eventData->objectForKey(CCHighwayConsts::DICT_ELEMENT_STATE_CHANGED_COMPONENTS));
    __Array *failedComponents = dynamic_cast<__Array *>(eventData->objectForKey(CCHighwayConsts::DICT_ELEMENT_STATE_FAILED_COMPONENTS));
	cocos2d::log("State Sync has finished.");
}
```
