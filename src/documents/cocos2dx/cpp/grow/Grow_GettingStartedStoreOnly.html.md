---
layout: "content"
image: "Tutorial"
title: "Getting Started (Store only)"
text: "Using cocos2dx-store already? Drop in one more package to seamlessly connect your game to GROW open analytics."
position: 3
theme: 'platforms'
collection: 'cocos2dx_grow'
module: 'grow'
platform: 'cocos2dx'
---

#Getting Started (Store only)

<div class="info-box">This tutorial is for developers who want to use SOOMLA Store and Grow (without using the other modules, Profile and LevelUp). If you already have Store, skip the store & core-related steps, and follow only the highway-related ones.</div>

##Integrate STORE & GROW

Get started with SOOMLA's Grow. Go to the [GROW dashboard](http://dashboard.soom.la) and sign up \ login. Upon logging in, you will be directed to the main page of the dashboard. On the left side panel, you can click on "Demo Game" in order to know what to expect to see once you start using Grow.

<div class="info-box">If you didn't already, clone the Cocos2d-x framework from [here](https://github.com/cocos2d/cocos2d-x), or download it from the [Cocos2d-x website](http://www.cocos2d-x.org/download). Make sure the version you clone is supported by SOOMLA's modules (the tag is the version).</div>

1. Click on the right pointing arrow next to "Demo Game" > "Add New App" and fill in the required fields.

  ![alt text](/img/tutorial_img/unity_grow/addNewApp.png "Add new app")

2. Download [soomla-cocos2dx-core](http://library.soom.la/fetch/cocos2dx-core/latest?cf=knowledge%20base), [cocos2dx-store](http://library.soom.la/fetch/cocos2dx-store/latest?cf=knowledge%20base) and [cocos2dx-store](http://library.soom.la/fetch/cocos2dx-highway/latest?cf=knowledge%20base) into your Cocos2d-x root framework and unzip them.  You should have Core, Store and Highway placed in the `extensions` and the Jansson dependency (a JSON parsing library) placed in `external`.

  ![](/img/tutorial_img/cocos_grow/folder_structure.png "Folder Structure")

3. Create your own implementation of `CCStoreAssets` that will represent the assets in your specific game. For a brief example, refer to the example below. For more details refer to the cocos2dx-store [Getting Started](/cocos2dx/cpp/store/Store_GettingStarted).

4. Make sure to include the `Cocos2dxStore.h` header whenever you use any of the *cocos2dx-store* functions:

    ```cpp
    #include "Cocos2dxStore.h"
    ```

5. Initialize `CCSoomla` and `CCSoomlaStore`. Subscribe to any store events, refer to the [Event Handling](/cocos2dx/cpp/store/Store_Events) section for more information:

    ``` cpp
    soomla::CCSoomla::initialize("ExampleCustomSecret");

    YourImplementationAssets *assets = YourImplementationAssets::create();

    __Dictionary *storeParams = __Dictionary::create();
     storeParams->setObject(__String::create("ExamplePublicKey"), "androidPublicKey");
     storeParams->setObject(__Bool::create(true), "testPurchases");
     storeParams->setObject(__Bool::create(true), "SSV");

    soomla::CCSoomlaStore::initialize(assets, storeParams);
    ```

    - *Custom Secret* - is an encryption secret you provide that will be used to secure your data.

    - *Android Public Key* - is the public key given to you from Google. (iOS doesn't have a public key).

    - *Test Purchases* - allows testing IAP on Google Play. (iOS doesn't have this functionality).

    - *SSV* - enables server-side receipt verification. (Android doesn't have this functionality).

    <div class="warning-box">Choose the secret wisely. You can't change it after you launch your game!
  	Initialize `CCSoomlaStore` ONLY ONCE when your application loads.</div>

6. In your `AppDelegate.cpp` include `Cocos2dxHighway.h`:

    ```cpp
    #include "Cocos2dxHighway.h"
    ```

7. Initialize `CCSoomlaHighway` with the "Game Key" and "Env Key" given to you in the [dashboard](http://dashboard.soom.la).

  ``` cpp
  soomla::CCSoomlaHighway::initShared(
      __String::create("yourGameKey"),
      __String::create("yourEnvKey"));
  ```

  ![alt text](/img/tutorial_img/cocos_grow/dashboardKeys.png "Keys")

  <div class="info-box">Note: SOOMLA modules should be initialized in this order: core, highway, store
  <br>Or in other words - `CCSoomla`, `CCSoomlaHighway` `CCSoomlaStore`</div>

8. According to your target platform (iOS or Android) go over the platform specific instructions below.  Once your app is running, you can go back to the [GROW dashboard](http://dashboard.soom.la) to verify the integration. Just refresh the page, and the environments tab should appear (be patient, this step can take a few minutes).

  ![alt text](/img/tutorial_img/unity_grow/verifyIntegration.png "Verify Integration")

<br>
<div class="info-box">The next steps are different according to which native platform you are building for.</div>

###**Instructions for iOS**

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

  ![alt text](/img/tutorial_img/cocos_grow/iosStep2SO.png "iOS Integration")

3. Add the following directories to **Build Settings->Header Search Paths** (with the `recursive` option):

  NOTE: This article assumes you have a `cocos2d` folder under your project folder which either contains the Cocos2d-x framework, or links to to its root folder.

  - `$(SRCROOT)/../cocos2d/extensions/soomla-cocos2dx-core/Soomla`
  - `$(SRCROOT)/../cocos2d/extensions/soomla-cocos2dx-core/build/ios/headers`
  - `$(SRCROOT)/../cocos2d/extensions/cocos2dx-store/Soomla`
  - `$(SRCROOT)/../cocos2d/extensions/cocos2dx-highway/Soomla`

  ![alt text](/img/tutorial_img/cocos_grow/headersSO.png "Header search paths")

4. Add the AFNetworking dependency:

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

  ![alt text](/img/tutorial_img/cocos_grow/ios_frameworks.png "Link With Binaries")

That's it! Now all you have to do is build your XCode project and run your game.

###**Instructions for Android**

1. Import cocos2dx-highway and cocos2dx-store module into your project's Android.mk by adding the following:

    ```
    LOCAL_WHOLE_STATIC_LIBRARIES += cocos2dx_highway_static
    # add this line along with your other LOCAL_WHOLE_STATIC_LIBRARIES

    $(call import-module, extensions/cocos2dx-highway)
    # add this line at the end of the file, along with the other import-module calls

    LOCAL_WHOLE_STATIC_LIBRARIES += cocos2dx_store_static
    # add this line along with your other LOCAL_WHOLE_STATIC_LIBRARIES

    $(call import-module, extensions/cocos2dx-store)
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

  - [Google Play](/cocos2dx/cpp/store/Store_GettingStarted#google-play)

  - [Amazon Appstore](/cocos2dx/cpp/store/Store_GettingStarted#amazon-appstore)

That's it! Don't forget to run the **build_native.py** script so that SOOMLA sources will be built with cocos2d-x.

##Example

Below is a short example of how to initialize SOOMLA's modules. We suggest you read about the different modules and their entities in SOOMLA's [Knowledge Base](/cocos2dx).

``` cpp
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

``` cpp
/** Set the custom secret **/
soomla::CCSoomla::initialize("ExampleCustomSecret");

/** Initialize Highway **/
soomla::CCSoomlaHighway::initShared(__String::create("yourGameKey"),
                                    __String::create("yourEnvKey"));

ExampleAssets *assets = ExampleAssets::create();

__Dictionary *storeParams = __Dictionary::create();
storeParams->setObject(__String::create("ExamplePublicKey"), "androidPublicKey");

soomla::CCSoomlaStore::initialize(assets, storeParams);
```
