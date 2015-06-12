---
layout: "content"
image: "Tutorial"
title: "Getting Started (Store only)"
text: "Using cocos2dx-store already? Drop in one more package to seamlessly connect your game to GROW open analytics."
position: 3
theme: 'platforms'
collection: 'cocos2djs_grow'
module: 'grow'
lang: 'js' 
platform: 'cocos2dx'
---

#Getting Started (Store only)

<div class="info-box">This tutorial is for developers who want to use SOOMLA Store and Grow (without using the other 
modules, Profile and LevelUp). If you already have Store, skip the store & core-related steps, and follow only the 
highway-related ones.</div>

##Integrate STORE & GROW

Get started with SOOMLA's Grow. Go to the [GROW dashboard](http://dashboard.soom.la) and sign up \ login. Upon logging 
in, you will be directed to the main page of the dashboard. On the left side panel, you can click on "Demo Game" in 
order to know what to expect to see once you start using Grow.

<div class="info-box">If you didn't already, clone the Cocos2d-x framework from 
[here](https://github.com/cocos2d/cocos2d-x), or download it from the 
[Cocos2d-x website](http://www.cocos2d-x.org/download). Make sure the version you clone is supported by SOOMLA's modules 
(the tag is the version).</div>

1. Click on the right pointing arrow next to "Demo Game" > "Add New App" and fill in the required fields.

  ![alt text](/img/tutorial_img/unity_grow/addNewApp.png "Add new app")

2. Download [soomla-cocos2dx-core](http://library.soom.la/fetch/cocos2dx-core/latest?cf=knowledge%20base), 
[cocos2dx-store](http://library.soom.la/fetch/cocos2dx-store/latest?cf=knowledge%20base) and 
[cocos2dx-store](http://library.soom.la/fetch/cocos2dx-highway/latest?cf=knowledge%20base) into your Cocos2d-x root 
framework and unzip them.  You should have Core, Store and Highway placed in the `extensions` and the Jansson dependency 
(a JSON parsing library) placed in `external`.

  ![](/img/tutorial_img/cocos_grow/folder_structure.png "Folder Structure")

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

3. Create your own implementation of `IStoreAssets` that will represent the assets in your specific game. For a brief 
example, refer to the example below. For more details refer to the cocos2dx-store 
[Getting Started](/cocos2dx/js/store/Store_GettingStarted).

5. Initialize `Soomla` and `SoomlaStore`. Subscribe to any store events, refer to the 
[Event Handling](/cocos2dx/js/store/Store_Events) section for more information:

    ``` js
  soomla::CCSoomla::initialize("ExampleCustomSecret");

    var assets = YourImplementationAssets.create();

    var storeParams = {
      androidPublicKey: 'ExamplePublicKey',
      testPurchases: true,
      SSV: true
    };
    Soomla.soomlaStore.initialize(assets, storeParams);
    ```

    - *Custom Secret* - is an encryption secret you provide that will be used to secure your data.

    - *Android Public Key* - is the public key given to you from Google. (iOS doesn't have a public key).

    - *Test Purchases* - allows testing IAP on Google Play. (iOS doesn't have this functionality).

    - *SSV* - enables server-side receipt verification. (Android doesn't have this functionality).

    <div class="warning-box">Choose the secret wisely. You can't change it after you launch your game!
  	Initialize `SoomlaStore` ONLY ONCE when your application loads.</div>

7. Initialize `CCSoomlaHighway` with the "Game Key" and "Env Key" given to you in the [dashboard](http://dashboard.soom.la).

  ``` js
  Soomla.Cocos2dXSoomlaHighway.createShared('yourGameKey', 'yourEnvKey');
  ```

  ![alt text](/img/tutorial_img/cocos_grow/dashboardKeys.png "Keys")

  <div class="info-box">Note: SOOMLA modules should be initialized in this order: core, highway, store
  <br>Or in other words - `Soomla`, `SoomlaHighway` `SoomlaStore`</div>

8. According to your target platform (iOS or Android) go over the platform specific instructions below.  Once your app 
is running, you can go back to the [GROW dashboard](http://dashboard.soom.la) to verify the integration. Just refresh 
the page, and the environments tab should appear (be patient, this step can take a few minutes).

  ![alt text](/img/tutorial_img/unity_grow/verifyIntegration.png "Verify Integration")

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

  - `Cocos2dXHighway.xcodeproj` (**frameworks/runtime-src/Classes/cocos2dx-highway/**)
  - `Cocos2dXCore.xcodeproj` (**frameworks/runtime-src/Classes/soomla-cocos2dx-core/**)  
  - `Cocos2dXStore.xcodeproj` (**frameworks/runtime-src/Classes/cocos2dx-store/**)

  perform the following:

  - Drag the project into your project
  - Add its targets to your **Build Phases->Target Dependencies**
  - Add the Products (\*.a) of the project to **Build Phases->Link Binary With Libraries**.

  ![alt text](/img/tutorial_img/cocos_grow/iosStep2SO.png "iOS Integration")

3. Add the following directories to **Build Settings->Header Search Paths** (with the `recursive` option):

  NOTE: This article assumes you have a `cocos2d` folder under your project folder which either contains the Cocos2d-x framework, or links to to its root folder.

  - `$(SRCROOT)/../Classes/soomla-cocos2dx-core/Soomla`
  - `$(SRCROOT)/../Classes/soomla-cocos2dx-core/build/ios/headers`
  - `$(SRCROOT)/../Classes/cocos2dx-store/Soomla`
  - `$(SRCROOT)/../Classes/cocos2dx-store/build/ios/headers`
  - `$(SRCROOT)/../Classes/cocos2dx-highway/Soomla`
  - `$(SRCROOT)/../Classes/cocos2dx-highway/build/ios/headers`

  ![alt text](/img/tutorial_img/cocos_grow/headersSO.png "Header search paths")

4. Add the AFNetworking dependency:

  - Add the static library (from `frameworks/runtime-src/Classes/cocos2dx-highway/build/ios/libAFNetworking.a`) to 
  **Build Phases->Link Binary With Libraries**.  Achieve this by clicking the + icon, and then "Add Other", and browse 
  for the file.
  - Add `$(SRCROOT)/../Classes/cocos2dx-highway/build/ios/` to **Build Settings->Library Search Paths** (non-recursive)

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

  ![alt text](/img/tutorial_img/cocos_grow/ios_frameworks.png "Link With Binaries")

That's it!
> Don't forget to `Build Custom Simulator` for iOS, if you use Cocos IDE.


###**Instructions for Android**

1. Import cocos2dx-highway and cocos2dx-store module into your project's Android.mk by adding the following:

    ```
    # Add these lines along with your other LOCAL_WHOLE_STATIC_LIBRARIES
    LOCAL_WHOLE_STATIC_LIBRARIES += cocos2dx_store_static
    LOCAL_WHOLE_STATIC_LIBRARIES += cocos2dx_highway_static

	# Add these lines at the end of the file, along with the other import-module calls
    $(call import-module, cocos2dx-store)  
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

3. Update your `AndroidManifest.xml`:

  ``` xml
  <uses-permission android:name="android.permission.INTERNET"/>
  <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
  <uses-permission android:name="com.android.vending.BILLING"/>

  <application
            ...
            android:name="com.soomla.SoomlaApp">
  </application>
  ```

4. Connect the Store module to your desired billing service:

  - [Google Play](/cocos2dx/js/store/Store_GettingStarted#google-play)

  - [Amazon Appstore](/cocos2dx/js/store/Store_GettingStarted#amazon-appstore)

That's it! 
> Don't forget to `Build Custom Simulator` for Android, if you use Cocos IDE.


##Example

Below is a short example of how to initialize SOOMLA's modules. We suggest you read about the different modules and their entities in SOOMLA's [Knowledge Base](/cocos2dx).

``` js
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

``` js
/** Set the custom secret **/
Soomla.initialize("ExampleCustomSecret");

/** Initialize Highway **/
Soomla.Cocos2dXSoomlaHighway.createShared('yourGameKey', 'yourEnvKey');

/** Set up and initialize Store **/
var assets = ExampleAssets.create();

var storeParams = {
    androidPublicKey: 'ExamplePublicKey'
};

Soomla.soomlaStore.initialize(assets, storeParams);
```
