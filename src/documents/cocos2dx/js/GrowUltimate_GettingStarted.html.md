---
layout: "content"
image: "Bundle"
title: "GrowUltimate"
text: "The perfect All In One solution for your game. If you want your users to have the perfect experience in your game then this bundle is for you."
position: 14
theme: 'platforms'
collection: 'cocos2djs_grow'
module: 'grow'
lang: 'js'
platform: 'cocos2dx'
---

# GrowUltimate - Bundle

## Overview

GrowUltimate is the perfect All In One solution for your game. If you want your users to have the
perfect experience in your game then this bundle is for you. GrowUltimate connects you to GROW,
SOOMLA's flagship - a community-driven data network. Mobile game studios can take advantage of the
different GROW products in order to get valuable insights about their games' performance and increase
retention and monetization. [Read more...](/university/articles/Grow_About)

GrowUltimate includes:

- SOOMLA's open-source modules - [Store](/cocos2dx/js/store/Store_GettingStarted),
[Profile](/cocos2dx/js/profile/Profile_GettingStarted)
- [State & Economy Sync](/cocos2dx/js/grow/Grow_Sync)
- [IAP Fraud Protection](/cocos2dx/js/grow/Grow_FraudProtection)
- [Analytics](/cocos2dx/js/grow/Grow_Analytics)
- [Whales Report](/cocos2dx/js/grow/Grow_WhalesReport)
- [Insights](/cocos2dx/js/grow/Grow_Insights)

## Integrating GrowUltimate

### New Game & Configurations

Go to the [GROW dashboard](http://dashboard.soom.la) and sign up \ login. Upon logging in, you will be directed to the
main page of the dashboard. You will need to create a new game in order to start your journey with GROW.

<div class="info-box">If you didn't already, clone the Cocos2d-x framework from [
here](https://github.com/cocos2d/cocos2d-x), or download it from the
[Cocos2d-x website](http://www.cocos2d-x.org/download). Make sure the version you clone is supported by SOOMLA's modules
(the tag is the version).</div>

1. In the games screen click on the "+" button to add a new game. If it's your first time in the dashboard, just click
on the "+" button underneath the "Create your first game" label in the middle of the screen.

	  ![alt text](/img/tutorial_img/cocos_grow/addNewApp.png "Add new app")

	Once you created your game, you'll be redirected to a quick start process to download any of the GROW bundles (You
	can also click "Downloads" on the top right corner of the screen). Click on **GrowUltimate**. You'll see an
	instructions screen, you can continue with that or stay here for the extended version.  

2. Unzip the downloaded file and copy its contents into the cocos2d directory located at the root of your Cocos2d-x
project.


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

2. Initialize Native Bridge in your `AppDelegate.cpp` in the method `applicationDidFinishLaunching`:

    ```cpp
        // Bind native bridges
        soomla::CCCoreBridge::getInstance();
        soomla::CCStoreBridge::getInstance();
        soomla::CCProfileBridge::getInstance();
        soomla::CCHighwayBridge::getInstance();
    ```

3. Copy soomla js-files to your project:

    ```bash
    mkdir script/soomla
    cp frameworks/runtime-src/Classes/soomla-cocos2dx-core/js/* script/soomla/
    cp frameworks/runtime-src/Classes/cocos2dx-store/js/* script/soomla/
    cp frameworks/runtime-src/Classes/cocos2dx-profile/js/* script/soomla/
    cp frameworks/runtime-src/Classes/cocos2dx-highway/js/* script/soomla/
    ```

4. Initialize `Soomla` with a custom secret of your choice (**Custom Secret** is an encryption secret you provide that
will be used to secure your data.):

  ```js
  Soomla.initialize('ExampleCustomSecret');
  ```

  <div class="warning-box">Choose this secret wisely, you can't change it after you launch your game!</div>

5. Initialize `GrowHighway` with the "Game Key" and "Env Key" given to you in the [dashboard](http://dashboard.soom.la):

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

6. Initialize Insights and Sync:

	``` js
	// Make sure to make this call AFTER initializing HIGHWAY
    Soomla.GrowInsights.createShared();

	// Make sure to make this call AFTER initializing HIGHWAY,
	// and BEFORE initializing STORE/PROFILE
	var modelSync = true; 	 // Remote Economy Management - Synchronizes your game's
							 // economy model between the client and server - enables
							 // you to remotely manage your economy.

	var stateSync = true;  // Synchronizes the users' balances data with the server
						   // and across his other devices.

	// State sync and Model sync can be enabled/disabled separately.
	Soomla.GrowSync.createShared(modelSync, stateSync);
	```

7. Initialize the rest of the SOOMLA modules: `SoomlaStore` and `SoomlaProfile`.

    ```js
    // `assets` should implement the `IStoreAssets` interface
    // and should include your entire virtual economy
    var assets = YourImplementationAssets.create();

    var storeParams = {};
    Soomla.soomlaStore.initialize(assets, storeParams);

    var profileParams = {};
    Soomla.soomlaProfile.initialize(profileParams);
    ```

<br>
<div class="info-box">The next steps are different according to which native platform you are building for.</div>

### **Instructions for iOS**

In your XCode project, perform the following steps:

1. In order to proceed Soomla needs to know, where your cocos2d-x is. Please, create a symlink with cocos2d-x at the path `frameworks/runtime-src` of the project, which looks at cocos2d-x. It can be something like that:

	```bash
	ln -s <your-cocos2d-js-path>/frameworks/js-bindings/cocos2d-x frameworks/runtime-src/cocos2d-x
	```

2. Add `jansson` (**frameworks/runtime-src/Classes/jansson/**) to your project (just add it as a source folder, make sure to check "create group").

3. For each of the following XCode projects:

  - `Cocos2dXCore.xcodeproj` (**frameworks/runtime-src/Classes/soomla-cocos2dx-core/**)  
  - `Cocos2dXStore.xcodeproj` (**frameworks/runtime-src/Classes/cocos2dx-store/**)
  - `Cocos2dXProfile.xcodeproj` (**frameworks/runtime-src/Classes/cocos2dx-profile/**)  
  - `Cocos2dXHighway.xcodeproj` (**frameworks/runtime-src/Classes/cocos2dx-highway/**)

  perform the following:

    a. Drag the project into your project.

    b. Add its targets to your **Build Phases->Target Dependencies**.

    c. Add its `.a` files to **Build Phases->Link Binary With Libraries**.

  ![alt text](/img/tutorial_img/cocos_grow/iosStep2.png "iOS Integration")

4. Add the following directories to **Build Settings->Header Search Paths** (with the `recursive` option):

  NOTE: This article assumes you have a `cocos2d` folder under your project folder which either contains the Cocos2d-x framework, or links to to its root folder.

 - `$(SRCROOT)/../Classes/soomla-cocos2dx-core/Soomla`
 - `$(SRCROOT)/../Classes/soomla-cocos2dx-core/build/ios/headers`
 - `$(SRCROOT)/../Classes/cocos2dx-store/Soomla`
 - `$(SRCROOT)/../Classes/cocos2dx-profile/Soomla`
 - `$(SRCROOT)/../Classes/cocos2dx-profile/build/ios/headers`
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

7. Connect the Profile module to a social network provider:

  - [Facebook](/cocos2dx/js/profile/Profile_GettingStarted#facebook-for-ios)

  - [Google+](/cocos2dx/js/profile/Profile_GettingStarted#google-for-ios)

  - [Twitter](/cocos2dx/js/profile/Profile_GettingStarted#twitter-for-ios)

That's it! Now all you have to do is build your XCode project and run your game.

### **Instructions for Android**

1. Import cocos2dx-highway, cocos2dx-store and cocos2dx-profile modules into your project's Android.mk by adding the following:

    ```
    LOCAL_STATIC_LIBRARIES += cocos2dx_store_static
    LOCAL_STATIC_LIBRARIES += cocos2dx_profile_static
    LOCAL_STATIC_LIBRARIES += cocos2dx_highway_static
    # add these lines along with your other LOCAL_STATIC_LIBRARIES

    $(call import-module, cocos2dx-store)
    $(call import-module, cocos2dx-profile)
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

  From `frameworks/runtime-src/Classes/cocos2dx-profile/build/android`

    - AndroidProfile.jar

    - Cocos2dxAndroidProfile.jar

3. Update your `AndroidManifest.xml`:

  ``` xml
  <uses-permission android:name="android.permission.INTERNET"/>
  <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
  <uses-permission android:name="com.android.vending.BILLING"/>

  <!-- optional: required for uploadImage from SD card -->
  <uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
  
  ```

4. Connect the Store module to your desired billing service:

  - [Google Play](/cocos2dx/js/store/Store_GettingStarted#google-play)

  - [Amazon Appstore](/cocos2dx/js/store/Store_GettingStarted#amazon)

5. Connect the Profile module to a social network provider:

  - [Facebook](/cocos2dx/js/profile/Profile_GettingStarted#facebook-for-android)

  - [Google+](/cocos2dx/js/profile/Profile_GettingStarted#google-for-android)

  - [Twitter](/cocos2dx/js/profile/Profile_GettingStarted#twitter-for-android)

That's it! Don't forget to run the **build_native.py** script so that SOOMLA sources will be built with cocos2d-x.

## Module usage & event handling

The next step is to create your game specific implementation for each of the modules. Use SOOMLA's awesome products to
create better in-game economy, social interactions, game design and user experience.  
In order to be notified about (and handle) SOOMLA-related events, you will also need to create event-handling functions.
Refer to the following sections for more information:

- **Store** - With Store you create your in-game virtual economy. It'll allow you to easily setup IAP and safely store
your users' balances.  
[API](/cocos2dx/js/store/Store_Model) | [Main classes](/cocos2dx/js/store/Store_MainClasses) | [Events](/cocos2dx/js/store/Store_Events)

- **Profile** - This module will make your life extremely easy when it comes to connecting your users to Social Networks.  
[API](/cocos2dx/js/profile/Profile_MainClasses) | [Events](/cocos2dx/js/profile/Profile_Events)

- **Insights** - Getting in-game information about your users in real-time used to be a dream. Now it's here. Insights
will tell you things about your users (as seen in other games) inside the code so you can take actions when it matters.
This is the power of the GROW data network.  
[API](/cocos2dx/js/grow/Grow_Insights#MainClasses&Methods) | [Events](/cocos2dx/js/grow/Grow_Insights#Events)

- **State & Economy Sync** - Your users want to get their balances, levels and other game state parameters when they
switch devices. Now you can let them do it.  
[Events](/cocos2dx/js/grow/Grow_Sync#Events)


## Example

Below is a short example of how to initialize SOOMLA's modules. We suggest you read about the different modules and
their entities in SOOMLA's Knowledge Base: [Store](/cocos2dx/js/store/Store_Model), [Profile](/cocos2dx/js/profile/Profile_MainClasses), [State & Economy Sync](/cocos2dx/js/grow/Grow_Sync) and [Insights](/cocos2dx/js/grow/Grow_Insights).

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

``` js

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

// State sync and Model sync can be enabled/disabled separately.
Soomla.GrowSync.createShared(modelSync, stateSync);

** Set up and initialize Store and Profile **/
var assets = ExampleAssets.create();

var storeParams = {
    androidPublicKey: 'ExamplePublicKey'
};

Soomla.soomlaStore.initialize(assets, storeParams);

var profileParams = {};
Soomla.soomlaProfile.initialize(profileParams);
```
