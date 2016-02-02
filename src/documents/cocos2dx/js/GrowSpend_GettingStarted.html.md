---
layout: "content"
image: "Bundle"
title: "GrowSpend"
text: "The perfect virtual economy solution for your game. If you want Fraud Protection, cross-device balance SYNC and remote economy configurator then this bundle is for you."
position: 11
theme: 'platforms'
collection: 'cocos2djs_grow'
module: 'grow'
lang: 'js'
platform: 'cocos2dx'
---

# GrowSpend - Bundle

## Overview

GrowSpend is the perfect virtual economy solution for your game. If you want Fraud Protection, cross-device balance SYNC
and remote economy configurator then this bundle is for you. GrowSpend connects you to GROW, SOOMLA's flagship - a
community-driven data network. Mobile game studios can take advantage of the different GROW products in order to get
valuable insights about their games' performance and increase retention and monetization.
[Read more...](/cocos2dx/js/grow/Grow_About)

GrowSpend includes:

- SOOMLA's open-source module - [Store](/cocos2dx/js/store/Store_GettingStarted)

- [State & Economy Sync](/cocos2dx/js/grow/Grow_Sync)

- [IAP Fraud Protection](/cocos2dx/js/grow/Grow_FraudProtection)

- [Analytics](/cocos2dx/js/grow/Grow_Analytics)

- [Whales Report](/cocos2dx/js/grow/Grow_WhalesReport)

- [Insights](/cocos2dx/js/grow/Grow_Insights)

**Note:** Cross-device SYNC is using the Profile module which allows your users to login with their social provider. If
you want that, [integrate Profile](/cocos2dx/js/profile/Profile_GettingStarted) as well.

## Integrating GrowSpend

### New Game & Configurations

Go to the [GROW dashboard](http://dashboard.soom.la) and sign up \ login. Upon logging in, you will be directed to the
main page of the dashboard. You will need to create a new game in order to start your journey with GROW.

<div class="info-box">If you didn't already, clone the Cocos2d-x framework from
[here](https://github.com/cocos2d/cocos2d-x), or download it from the [Cocos2d-x website](http://www.cocos2d-x.org/download).
Make sure the version you clone is supported by SOOMLA's modules (the tag is the version).</div>

1. In the games screen click on the "+" button to add a new game. If it's your first time in the dashboard, just click
on the "+" button underneath the "Create your first game" label in the middle of the screen.

	  ![alt text](/img/tutorial_img/unity_grow/addNewApp.png "Add new app")

	* Once you created your game, you'll be redirected to download **GrowSpend**. You'll see an 
	instructions screen, you can continue with that or stay here for the extended version.  

2. Unzip the downloaded file and copy its contents into the cocos2d directory located at the root of your Cocos2d-x project.


### Initialize modules

<div class="info-box">Make sure to initialize each module ONLY ONCE when your application loads.</div>
<br>
<div class="info-box">The GrowHighway module is the module responsible for connecting your game to the GROW network.
In order for it to operate it only needs to be initialized.</div>


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
        soomla::CCHighwayBridge::getInstance();
    ```

1. Copy soomla js-files to your project:

    ```bash
    mkdir script/soomla
    cp frameworks/runtime-src/Classes/soomla-cocos2dx-core/js/* script/soomla/
    cp frameworks/runtime-src/Classes/cocos2dx-store/js/* script/soomla/
    cp frameworks/runtime-src/Classes/cocos2dx-highway/js/* script/soomla/
    ```


2. Initialize `Soomla` with a custom secret of your choice (**Custom Secret** is an encryption secret you provide that
will be used to secure your data.):

  ```js
  Soomla.initialize('ExampleCustomSecret');
  ```

  <div class="warning-box">Choose this secret wisely, you can't change it after you launch your game!</div>

* **Fraud Protection (RECOMMENDED):**

3. Initialize `GrowHighway` with the "Game Key" and "Env Key" given to you in the [dashboard](http://dashboard.soom.la):

  **Copy the "Game Key" and "Environment Key"** given to you from the [dashboard](http://dashboard.soom.la) and initialize
  `GrowHighway` with them. At this point, you're probably testing your integration and you want to use the **Sandbox**
  environment key.

  Explanation: The "game" and "env" keys allow for your game to distinguish multiple environments for the same game.
  The dashboard pre-generates two fixed environments for your game: **Production** and **Sandbox**. When you decide to
   publish your game, make sure to switch the env key to **Production**.  You can always generate more environments.  
   For example - you can choose to have a playground environment for your game's beta testers which will be isolated
   from your production environment and will thus prevent analytics data from being mixed between the two.  Another best
   practice is to have a separate environment for each version of your game.

  ```js
    // Make sure to make this call before
    // initializing any other SOOMLA/GROW components
    // i.e. before Soomla.soomlaStore.initialize(...)
    Soomla.GrowHighway.createShared('yourGameKey', 'yourEnvKey');
  ```

  ![alt text](/img/tutorial_img/cocos_grow/dashboardKeys.png "Keys")

4. Initialize Sync:

	``` js
	// Make sure to make this call AFTER initializing HIGHWAY,
	// and BEFORE initializing STORE/PROFILE
	var modelSync = true; 	 // Remote Economy Management - Synchronizes your game's
							 // economy model between the client and server - enables
							 // you to remotely manage your economy.

	var stateSync = true;  // Synchronizes the users' balances data with the server
						   // and across his other devices.
						   // Must be TRUE in order to use LEADERBOARDS.

	// State sync and Model sync can be enabled/disabled separately.
	Soomla.GrowSync.createShared(modelSync, stateSync);

	// LEADERBOARDS requires no initialization,
	// but it depends on SYNC initialization with stateSync=true
	```

5. Initialize `SoomlaStore`:

    ```js
    // `assets` should implement the `IStoreAssets` interface
    // and should include your entire virtual economy
    var assets = YourImplementationAssets.create();

    var storeParams = {};
    Soomla.soomlaStore.initialize(assets, storeParams);
    ```

<br>
<div class="info-box">The next steps are different according to which native platform you are building for.</div>

### **Instructions for iOS**

In your XCode project, perform the following steps:

1. In order to proceed Soomla needs to know, where your cocos2d-x is. Please, create a symlink with cocos2d-x at the path `frameworks/runtime-src` of the project, which looks at cocos2d-x. It can be something like that:

    ```bash
ln -s <your-cocos2d-js-path>/frameworks/js-bindings/cocos2d-x frameworks/runtime-src/cocos2d-x
    ````

1. Add `jansson` (**frameworks/runtime-src/Classes/jansson/**) to your project (just add it as a source folder, make sure to check "create group").

1. For each of the following XCode projects:

  - `Cocos2dXCore.xcodeproj` (**frameworks/runtime-src/Classes/soomla-cocos2dx-core/**)  

  - `Cocos2dXStore.xcodeproj` (**frameworks/runtime-src/Classes/cocos2dx-store/**)

  - `Cocos2dXHighway.xcodeproj` (**frameworks/runtime-src/Classes/cocos2dx-highway/**)


  perform the following:

    a. Drag the project into your project.

    b. Add its targets to your **Build Phases->Target Dependencies**.

    c. Add its `.a` files to **Build Phases->Link Binary With Libraries**.

  ![alt text](/img/tutorial_img/cocos_grow/iosStep2.png "iOS Integration")

3. Add the following directories to **Build Settings->Header Search Paths** (with the `recursive` option):

  NOTE: This article assumes you have a `cocos2d` folder under your project folder which either contains the Cocos2d-x framework, or links to to its root folder.

 - `$(SRCROOT)/../Classes/soomla-cocos2dx-core/Soomla`

 - `$(SRCROOT)/../Classes/soomla-cocos2dx-core/build/ios/headers`

 - `$(SRCROOT)/../Classes/cocos2dx-store/Soomla`

 - `$(SRCROOT)/../Classes/cocos2dx-highway/Soomla`

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

    $(call import-module, cocos2dx-store)
    $(call import-module, cocos2dx-highway)
    # add these lines at the end of the file, along with the other import-module calls
    ```

2. Add the following jars to your android project's classpath (or into its libs dir):

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

4. Update your `AndroidManifest.xml`:

  ``` xml
  <uses-permission android:name="android.permission.INTERNET"/>
  <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
  <uses-permission android:name="com.android.vending.BILLING"/>
  
  ```

5. Connect the Store module to your desired billing service:

  - [Google Play](/cocos2dx/js/store/Store_GettingStarted#google-play)

  - [Amazon Appstore](/cocos2dx/js/store/Store_GettingStarted#amazon)

That's it! Don't forget to run the **build_native.py** script so that SOOMLA sources will be built with cocos2d-x.

## Module usage & event handling

The next step is to create your game specific implementation for each of the modules. Use SOOMLA's awesome products to
create better in-game economy and user experience.  
In order to be notified about (and handle) SOOMLA-related events, you will also need to create event-handling functions.
Refer to the following sections for more information:

- **Store** - With Store you create your in-game virtual economy. It'll allow you to easily setup IAP and safely store
your users' balances.  
[API](/cocos2dx/js/store/Store_Model) | [Main classes](/cocos2dx/js/store/Store_MainClasses) | [Events](/cocos2dx/js/store/Store_Events)

- **State & Economy Sync** - Your users want to get their balances, levels and other game state parameters when they
switch devices. Now you can let them do it.  
[Events](/cocos2dx/js/grow/Grow_Sync#Events)

## Example

Below is a short example of how to initialize SOOMLA's modules. We suggest you read about the different modules and
their entities in SOOMLA's Knowledge Base: [Store](/cocos2dx/js/store/Store_Model) and
[State & Economy Sync](/cocos2dx/js/grow/Grow_Sync).

### IStoreAssets

```js
/** ExampleAssets (your implementation of IStoreAssets) **/
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

### Initialization

```js
Soomla.addHandler(Soomla.Models.HighwayConsts.EVENT_GROW_SYNC_INITIALIZED, this.onGrowSyncInitialized, this);
Soomla.addHandler(Soomla.Models.ProfileConsts.EVENT_STATE_SYNC_FINISHED, this.onStateSyncFinished, this);
Soomla.addHandler(Soomla.Models.ProfileConsts.EVENT_MODEL_SYNC_FINISHED, this.onModelSyncFinished, this);

/** Set the custom secret **/
Soomla.initialize("ExampleCustomSecret");

/** Initialize Highway **/
// Make sure to make this call before
// initializing any other SOOMLA/GROW components
// i.e. before Soomla.soomlaStore.initialize(...)
Soomla.GrowHighway.createShared('yourGameKey', 'yourEnvKey');

// Make sure to make this call AFTER initializing HIGHWAY,
// and BEFORE initializing STORE/PROFILE
var modelSync = true; 	 // Remote Economy Management - Synchronizes your game's
                         // economy model between the client and server - enables
                         // you to remotely manage your economy.

var stateSync = true;  // Synchronizes the users' balances data with the server
                       // and across his other devices.
                       // Must be TRUE in order to use LEADERBOARDS.

// State sync and Model sync can be enabled/disabled separately.
Soomla.GrowSync.createShared(modelSync, stateSync);

/** Set up and initialize Store and Profile **/
var assets = ExampleAssets.create();

var storeParams = {
    androidPublicKey: 'ExamplePublicKey'
};

Soomla.soomlaStore.initialize(assets, storeParams);


this.onGrowSyncInitialized = function() {
	// GROW Sync has been initialized.
}

this.onModelSyncFinished = function(changedComponents) {
	// Model Sync has finished.
}

this.onStateSyncFinished = function(changedComponents, failedComponents) {
	// State Sync has finished.
}
```
