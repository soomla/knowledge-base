---
layout: "content"
image: "Tutorial"
title: "Getting Started"
text: "Get started with cocos2dx-store. Here you can find a basic example of initialization, economy framework integration, and links to downloads and IAP setup."
position: 1
theme: 'platforms'
collection: 'cocos2djs_store'
module: 'store'
platform: 'cocos2dx'
---

#Getting Started

##Getting Started

*If you want to develop with C++ sources, refer to the "Working with Sources" section below.*

<div class="info-box">If you didn't already, clone the Cocos2d-js framework from [here](https://github.com/cocos2d/cocos2d-js), or download it from the [Cocos2d-x website](http://www.cocos2d-x.org/download). Make sure the version you clone is supported by SOOMLA's cocos2dx-store (the tag is the version).</div>

1. Clone [soomla-cocos2dx-core](https://github.com/soomla/soomla-cocos2dx-core) and cocos2dx-store into the `Classes` folder of your project.

	```
	$ git clone git@github.com:soomla/soomla-cocos2dx-core.git frameworks/runtime-src/Classes/soomla-cocos2dx-core

	$ git clone git@github.com:soomla/cocos2dx-store.git frameworks/runtime-src/Classes/cocos2dx-store
	```

1. cocos2dx-store uses a [fork](https://github.com/soomla/jansson) of the jansson library for JSON parsing. Clone it there as well.

	```
	$ git clone git@github.com:soomla/jansson.git frameworks/runtime-src/Classes/jansson
	```

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
    ```

1. Copy soomla js-files to your project:

    ```bash
    mkdir script/soomla
    cp frameworks/runtime-src/Classes/soomla-cocos2dx-core/js/* script/soomla/
    cp frameworks/runtime-src/Classes/cocos2dx-store/js/* script/soomla/
    ```

1. Add them to your `project.json`:

    ```js
  "jsList": [
    "script/soomla/lodash.js",
    "script/soomla/soomla-core.js",
    "script/soomla/soomla-store.js",
    // your other files
  ]
    ```

1. Create your own implementation of `Soomla.IStoreAssets` that will represent the assets in your specific game. For a complete example refer to [cocos2dx-js-store-example](https://github.com/soomla/cocos2dx-js-store-example/blob/master/src/MuffinRushAssets.js).

1. Initialize `Soomla` and `Soomla.soomlaStore` with your store assets class, a `customSecret` and other params:

	``` js
    Soomla.initialize("ExampleCustomSecret");

    var assets = new YourImplementationAssets();
    var storeParams = {
      androidPublicKey: "ExamplePublicKey",
      testPurchases: true
    };

    Soomla.soomlaStore.initialize(assets, storeParams);
	```

	- *Custom Secret* - is an encryption secret you provide that will be used to secure your data.

	- *Android Public Key* - is the public key given to you from Google. (iOS doesn't have a public key).

	- *Test Purchases* - allows testing IAP on Google Play. (iOS doesn't have this functionality).

	- *SSV* - enables server-side receipt verification. (Android doesn't have this functionality).

	<div class="warning-box">Choose the secret wisely. You can't change it after you launch your game!
	Initialize `Soomla.soomlaStore` ONLY ONCE when your application loads.</div>

6. You'll need to subscribe to store events to get notified about in-app purchasing related events. refer to the [Event Handling](/cocos2dx/js/store/Store_Events) section for more information.

**The next steps are different according to which platform you're using.**

<br>

###**Instructions for iOS**

In your XCode project, perform the following steps:

1. In order to proceed Soomla needs to know, where your cocos2d-x is. Please, create a symlink with cocos2d-x at the path `frameworks/runtime-src` of the project, which looks at cocos2d-x. It can be something like that:

    ```bash
ln -s <your-cocos2d-js-path>/frameworks/js-bindings/cocos2d-x frameworks/runtime-src/cocos2d-x
    ````

1. Add `jansson` (**frameworks/runtime-src/Classes/jansson/**) to your project (just add it as a source folder, make sure to check "create group").

1. For each of the following XCode projects:

 * `Cocos2dXCore.xcodeproj` (**frameworks/runtime-src/Classes/soomla-cocos2dx-core/**).

 * `Cocos2dXStore.xcodeproj` (**frameworks/runtime-src/Classes/cocos2dx-store/**).

    a. Drag the project into your project.

    b. Add its targets to your **Build Phases->Target Dependencies**.

    c. Add its `.a` files to **Build Phases->Link Binary With Libraries**.

 ![alt text](/img/tutorial_img/cocos2dx_getting_started/iOS_steps1to4.png "iOS Integration")

1. Add the following directories to **Build Settings->Header Search Paths** (with `recursive` option):

 - `$(SRCROOT)/../Classes/soomla-cocos2dx-core/Soomla`

 - `$(SRCROOT)/../Classes/soomla-cocos2dx-core/build/ios/headers`

 - `$(SRCROOT)/../Classes/cocos2dx-store/Soomla`

 - `$(SRCROOT)/../Classes/cocos2dx-store/build/ios/headers`

 ![alt text](/img/tutorial_img/cocos2dx_getting_started/headerSP.png "Header search paths")

1. Add `-ObjC` to your project **Build Settings->Other Linker Flags**.

	![alt text](/img/tutorial_img/cocos2dx_getting_started/objc.png "Other Linker Flags")

1. Make sure you have these 3 Frameworks linked to your XCode project: **Security, libsqlite3.0.dylib, StoreKit**.

**That's it! Now all you have to do is build your XCode project and run your game with cocos2dx-store.**

> If you use Cocos IDE you'll need to `Build Custom Simulator` for iOS there.

<br>
###**Instructions for Android**

1. Set COCOS2D_JAVASCRIPT flag for your project changing your `frameworks/runtime-src/proj.android/jni/Application.mk`:
    ```
    APP_CPPFLAGS += -DCOCOS2D_JAVASCRIPT=1
    ```

1. Add "../Classes" to `ndk_module_path`, adding it to the file `frameworks/runtime-src/proj.android/build-cfg.json`

1. Import cocos2dx-store module into your project's Android.mk by adding the following:

    ```
    # Add these lines along with your other LOCAL_STATIC_LIBRARIES
    LOCAL_WHOLE_STATIC_LIBRARIES += cocos2dx_store_static

	# Add these lines at the end of the file, along with the other import-module calls
    $(call import-module, extensions/cocos2dx-store)  
    ```

1. Add the following jars to your android project's classpath:

    From `frameworks/runtime-src/Classes/soomla-cocos2dx-core/build/android`:

    - SoomlaAndroidCore.jar
    - Cocos2dxAndroidCore.jar
    - square-otto-1.3.2.jar

  From `frameworks/runtime-src/Classes/cocos2dx-store/build/android`:

    - SoomlaAndroidStore.jar
    - Cocos2dxAndroidStore.jar

1. Update your AndroidManifest.xml to include permissions and the SoomlaApp:

    ```xml
    <uses-permission android:name="android.permission.INTERNET"/>
    <uses-permission android:name="com.android.vending.BILLING"/>

    <application ...
    	       android:name="com.soomla.SoomlaApp">
    	       ...
    </application>
    ```

**That's it! You now have storage and in-app purchasing capabilities and you can begin using cocos2dx-store in your game.**

> Don't forget to `Build Custom Simulator` for Android, if you use Cocos IDE.



##**Working with sources**

To integrate cocos2dx-store into your game, follow these steps.

1. **Recursively** clone cocos2dx-core and cocos2dx-store.

    ```
    $ git clone --recursive git@github.com:soomla/soomla-cocos2dx-core.git frameworks/runtime-src/Classes/soomla-cocos2dx-core

    $ git clone --recursive git@github.com:soomla/cocos2dx-store.git frameworks/runtime-src/Classes/cocos2dx-store
    ```

	**OR:** If you have already cloned the repositories, to obtain the submodules, run this command in each repository:

    ```
    $ git submodule update --init --recursive
    ```

2. **For iOS:** Use sourced versions of Linked projects (`frameworks/runtime-src/Classes/soomla-cocos2dx-core/development/Cocos2dxCoreFromSources.xcodeproj`, `frameworks/runtime-src/Classes/cocos2dx-store/development/Cocos2dxStoreFromSources.xcodeproj`)

3. **For Android:** You can use our "sourced" modules for Android Studio (or IntelliJ IDEA) (`frameworks/runtime-src/Classes/soomla-cocos2dx-core/development/Cocos2dxCoreFromSources.iml`, `frameworks/runtime-src/Classes/cocos2dx-store/development/Cocos2dxStoreFromSources.iml`), just include them in your project.



##**Select a Billing Service**

SOOMLA's cocos2dx-store knows how to contact Google Play, Amazon Appstore, or Apple App Store for you and will redirect your users to their purchasing system to complete the transaction.

###Google Play

1. Add `AndroidStoreGooglePlay.jar` from `frameworks/runtime-src/Classes/cocos2dx-store/build/android/billing-services/google-play` to your classpath:

2. Update your AndroidManifest.xml:

	``` xml
	...
	<uses-permission android:name="com.android.vending.BILLING"/>

	<application ...
	 <activity android:name="com.soomla.store.billing.google.GooglePlayIabService$IabActivity"
	            android:theme="@android:style/Theme.Translucent.NoTitleBar.Fullscreen"/>
	 <meta-data android:name="billing.service" android:value="google.GooglePlayIabService"/>
	</application>
	```

3. Read our [tutorial](/android/store/Store_GooglePlayIAB) on how to define your in-app products in Google Play.

4. Start IAB Service in background **(optional)**

    ```js
    // Start Iab Service
    Soomla.soomlaStore.startIabServiceInBg();
    
    // Stop Iab Service
    Soomla.soomlaStore.stopIabServiceInBg();
    ```

    This is not mandatory, your game will work without this, but we recommend it because it enhances performance. The idea here is to preemptively start the in-app billing setup process with Google's servers.

    In many games the user has to navigate into the in-game store, or start a game session in order to reach the point of making purchases. You want the user experience to be fast and smooth and prevent any lag that could be caused by network latency and setup routines you could have done silently in the background.

    <div class="info-box">Don't forget to close the Iab Service when your store is closed.</div>

###Amazon

1. Add `in-app-purchasing-1.0.3.jar` and `AndroidStoreAmazon.jar` from `frameworks/runtime-src/Classes/cocos2dx-store/build/android/billing-services/amazon` to your classpath:

2. Update your manifest:

	``` xml
	<receiver android:name = "com.amazon.inapp.purchasing.ResponseReceiver" >
		<intent-filter>
	    <action android:name = "com.amazon.inapp.purchasing.NOTIFY"
	            android:permission = "com.amazon.inapp.purchasing.Permission.NOTIFY" />
		</intent-filter>
	</receiver>
	<meta-data android:name="billing.service" android:value="amazon.AmazonIabService" />
	```

3. Read our [tutorial](/android/store/Store_AmazonIAB) on how to define your in-app products in the Amazon Appstore.

###Apple App Store

1. Read our [tutorial](/ios/store/Store_AppStoreIAB) on how to define your in-app products in the App Store.

2. Use SOOMLA's iOS Server Side Verification **(Optional)**

	As you probably know, fraud on IAP is pretty common. Hackers can crack their smartphones to think that a purchase was made when the payment isn't actually transferred to you. To help game developers with this issue, we created a verification server that you can use instantly through the framework.

	All you need to do is let cocos2dx-store know that you want to verify purchases. You can do this by passing an extra parameter to `Soomla.soomlaStore`:

	```js
    storeParams.SSV = true;
    Soomla.soomlaStore.initialize(assets, storeParams);
    ```

## Example

Create your own implementation of `Soomla.IStoreAssets`; See the article about `Soomla.IStoreAssets`, which includes a code example and explanations.

Then, initialize `Soomla.soomlaStore` with your implementation of `Soomla.IStoreAssets`:

```js
//In `main.js`:
    Soomla.initialize("ExampleCustomSecret");
    
    // We initialize SoomlaStore before we open the store.
    var assets = new YourImplementationAssets();
    var storeParams = {
      androidPublicKey: "ExamplePublicKey",
      testPurchases: true
    };
    
    // This is the call to initialize SoomlaStore
    Soomla.soomlaStore.initialize(assets, storeParams);
```

And that's it! cocos2dx-store knows how to contact Google Play or the App Store for you and will redirect your users to the purchasing system to complete the transaction. Don't forget to subscribe to store events in order to get notified of successful or failed purchases - See [Event Handling](/cocos2dx/js/store/Store_Events).
