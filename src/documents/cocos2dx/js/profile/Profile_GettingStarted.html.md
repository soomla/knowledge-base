---
layout: "content"
image: "Tutorial"
title: "Getting Started"
text: "Get started with cocos2dx-profile. Here you can find integration instructions and a basic example of initialization."
position: 1
theme: 'platforms'
collection: 'cocos2djs_profile'
module: 'profile'
lang: 'js' 
platform: 'cocos2dx'
---

#Getting Started

*If you want to develop with C++ sources, refer to the [Working with sources](#working-with-sources) section below.*

<div class="info-box">If you didn't already, clone the Cocos2d-js framework from [here](https://github.com/cocos2d/cocos2d-js), 
or download it from the [Cocos2d-x website](http://www.cocos2d-x.org/download). Make sure the version you clone is 
supported by SOOMLA's cocos2dx-profile (the tag is the version).</div>

<div class="info-box">Profile depends on SOOMLA's Core module. This document assumes that you are new to SOOMLA and have 
not worked with any of the other SOOMLA modules. If this is not the case, and you already have Core & Store, please 
follow these directions only for the Profile module.</div>

##General Instructions

1. Clone [soomla-cocos2dx-core](https://github.com/soomla/soomla-cocos2dx-core) and **cocos2dx-profile** into the `Classes` folder of your project.

    ```
    $ git clone git@github.com:soomla/soomla-cocos2dx-core.git frameworks/runtime-src/Classes/soomla-cocos2dx-core

    $ git clone git@github.com:soomla/cocos2dx-profile.git frameworks/runtime-src/Classes/cocos2dx-profile
    ```

1. We use a [fork](https://github.com/soomla/jansson) of the jansson library for JSON parsing. Clone it there as well.

    ```
    $ git clone git@github.com:soomla/jansson.git external/jansson
    ```

1. Register soomla js-bindings in your `js_module_register.h`:

    ```cpp
    #include "Cocos2dxProfile.h"
    ...
    sc->addRegisterCallback(register_jsb_soomla);
    ```

1. Initialize Native Bridge in your `AppDelegate.cpp` in the method `applicationDidFinishLaunching`:

    ```cpp
    // Bind native bridges
    soomla::CCCoreBridge::getInstance();
    soomla::CCProfileBridge::getInstance();
    ```

1. Copy soomla js-files to your project:

    ```bash
    mkdir script/soomla
    cp frameworks/runtime-src/Classes/soomla-cocos2dx-core/js/* script/soomla/
    cp frameworks/runtime-src/Classes/cocos2dx-profile/js/* script/soomla/
    ```

1. Add them to your `project.json`:

    ```js
  "jsList": [
    "script/soomla/lodash.js",
    "script/soomla/soomla-core.js",
    "script/soomla/soomla-profile.js",
    // your other files
  ]
    ```

1. Initialize `Soomla` and `Soomla.SoomlaProfile` with a `customSecret` and other params:

	``` js
    Soomla.initialize("customSecret");

    var profileParams = {
    };

    Soomla.SoomlaProfile.createShared(profileParams);
	```

	- *Custom Secret* - is an encryption secret you provide that will be used to secure your data.

	<div class="warning-box">Choose the secret wisely. You can't change it after you launch your game!
	Initialize `Soomla.SoomlaProfile` ONLY ONCE when your application loads.</div>

    <div class="warning-box">Initialize `SoomlaProfile` ONLY ONCE when your application loads.</div>


1. Note that some social providers need special parameters to be passed to `SoomlaProfile` in order for them to work:

  a. **Facebook** - You can provide your custom permission set here, these permissions will be requested from the user on login.

	``` js
	    var profileParams = {
          facebook: {
            permissions: 'public_profile,user_friends'
          }
        };
	```
  <div class="info-box">**NOTE:** You should not request all the possible permissions you'll ever need in your app, 
  just request the reasonable minimum. Other permissions will be requested, when they will be needed. 
  For instance, if you try to call `updateStory`, SoomlaProfile will ask for `publish_actions` permission, if your app has not got it.
  </div>
  
  <div class="info-box">**NOTE:** Currently the Android implementation does not work in this way, 
  and you should provide all the needed permissions there. We are working on fixing that.</div>


  b. **Google+** - Please provide Client ID from the "API & Auth, credentials" section like so:

  ``` js
    var profileParams = {
      google: {
        clientId: '[YOUR CLIENT ID]'
      }
    };
  ```

  c. **Twitter** - Please provide a "Consumer Key" and a "Consumer Secret" from the "Keys and Access Tokens" section in [Twitter Apps](https://apps.twitter.com/), like so:

  ``` js
    var profileParams = {
      twitter: {
        consumerKey: '[YOUR CONSUMER KEY]',
        consumerSecret: '[YOUR CONSUMER SECRET]'
      }
    };
  ```

  d. **Common** - There is a common param, supported with all providers: `autoLogin`. Setting this param to `true` makes 
    Soomla to try to log in automatically to this provider, if an user already logged in with it in the previous sessions. 
    The default value is `false`.
  
	``` js
  	    // For instance for FB
	    var profileParams = {
          facebook: {
            autoLogin: true
          }
        };
  	```

1. You'll need to subscribe to profile events to get notified about social network related events. refer to the [Event Handling](/cocos2dx/js/profile/Profile_Events) section for more information.

<br>
<div class="info-box">The next steps are different according to which platform you're using.</div>

###**Instructions for iOS**

In your XCode project, perform the following steps:

1. In order to proceed Soomla needs to know, where your cocos2d-x is. Please, create a symlink with cocos2d-x at the path `frameworks/runtime-src` of the project, which looks at cocos2d-x. It can be something like that:

    ```bash
ln -s <your-cocos2d-js-path>/frameworks/js-bindings/cocos2d-x frameworks/runtime-src/cocos2d-x
    ````

1. Add `jansson` (**frameworks/runtime-src/Classes/jansson/**) to your project (just add it as a source folder, make sure to check "create group").

1. For each of the following XCode projects:

 * `Cocos2dXCore.xcodeproj` (**frameworks/runtime-src/Classes/soomla-cocos2dx-core/**).

 * `Cocos2dXProfile.xcodeproj` (**frameworks/runtime-src/Classes/cocos2dx-profile/**).

    a. Drag the project into your project.

    b. Add its targets to your **Build Phases->Target Dependencies**.

    c. Add its `.a` files to **Build Phases->Link Binary With Libraries**.

 ![alt text](/img/tutorial_img/cocos2dx-profile/iosStep2.png "iOS Integration")

1. Add the following directories to **Build Settings->Header Search Paths** (with `recursive` option):

 - `$(SRCROOT)/../Classes/soomla-cocos2dx-core/Soomla`
 - `$(SRCROOT)/../Classes/soomla-cocos2dx-core/build/ios/headers`
 - `$(SRCROOT)/../Classes/cocos2dx-profile/Soomla`
 - `$(SRCROOT)/../Classes/cocos2dx-profile/build/ios/headers`

 ![alt text](/img/tutorial_img/cocos2dx-profile/headerSP.png "Header search paths")

1. To support browser-based authentication read [here](#browser-based-authentication).

1. Add `-ObjC` to your project **Build Settings->Other Linker Flags**.

	![alt text](/img/tutorial_img/cocos2dx_getting_started/objc.png "Other Linker Flags")

1. Make sure you have the following frameworks linked to your XCode project: **Security, libsqlite3.0.dylib**.

1. Go to **Build Settings->Library Search Paths**, set the library search paths to `$(SRCROOT)/../Classes/cocos2dx-profile/build/ios` with the `recursive` option.

<div class="info-box">The following steps should be done according to the target social network.</div>

###Facebook for iOS

Facebook is supported out-of-the-box, you just have to follow the next steps to make it work:

1. Add the Facebook SDK for iOS to the project's Frameworks and make sure your project links to the project

2. Refer to [Getting Started with the Facebook iOS SDK](https://developers.facebook.com/docs/ios/getting-started/) for more information

3. Add `-lSoomlaiOSProfileFacebook` to your project's **Build Settings->Other Linker Flags**

###Google+ for iOS

Google+ is supported out-of-the-box, authentication is done either through the signed in Google+ account or through the 
web browser (fallback). Follow the next steps to make it work:

1. Click [here](https://console.developers.google.com/project) to create your Google Plus app.

2. Add a URL scheme to your application:

  a. Go to your project's **Info->URL Types**.

  b. Add a new URL type and enter your bundle ID as the identifier and scheme.

4. Make sure you have the following frameworks in your application's project: **GooglePlus, GoogleOpenSource, GooglePlus.bundle**.

5. Add additional frameworks if you still haven't:

    * AddressBook.framework
    * AssetsLibrary.framework
    * Foundation.framework
    * CoreLocation.framework
    * CoreMotion.framework
    * CoreGraphics.framework
    * CoreText.framework
    * MediaPlayer.framework
    * Security.framework
    * SystemConfiguration.framework
    * UIKit.framework

6. Add `-lSoomlaiOSProfileGoogle` to your project's **Build Settings->Other Linker Flags**.

###Twitter for iOS

Twitter is supported out-of-the-box, authentication is done either through the signed in **native** Twitter account 
(iOS 5+) or through web browser (fallback). Follow the next steps to make it work:

1. Click [here](https://apps.twitter.com/) to create your Twitter app.

2. Add a URL scheme to your application:

  a. Go to your project's **Info->URL Types**.

  b. Add a new URL type and enter `tw<Your Twitter app consumer key>` (without the braces) as your scheme.

3. Make sure you have the following frameworks in your application's project: **Twitter, Social, Accounts**.

4. Add `-lSoomlaiOSProfileTwitter -lSTTwitter` to your project's **Build Settings->Other Linker Flags**.

  NOTE: **ios-profile** uses the [STTWitter](https://github.com/nst/STTwitter) library (v 0.1.5) to support Twitter integration.  


**That's it! Now all you have to do is build your XCode project and run your game with cocos2dx-profile.**

> If you use Cocos IDE you'll need to `Build Custom Simulator` for iOS there.

<br>


##Instructions for Android

1. Set COCOS2D_JAVASCRIPT flag for your project changing your `frameworks/runtime-src/proj.android/jni/Application.mk`:
    ```
    APP_CPPFLAGS += -DCOCOS2D_JAVASCRIPT=1
    ```

1. Add "../Classes" to `ndk_module_path`, adding it to the file `frameworks/runtime-src/proj.android/build-cfg.json`

1. Import cocos2dx-profile module into your project's Android.mk by adding the following:

    ```
    # Add these lines along with your other LOCAL_WHOLE_STATIC_LIBRARIES
    LOCAL_WHOLE_STATIC_LIBRARIES += cocos2dx_profile_static

	# Add these lines at the end of the file, along with the other import-module calls
    $(call import-module, cocos2dx-profile)
    ```

2. Add the following jars to your android project's classpath:

  From `frameworks/runtime-src/Classes/soomla-cocos2dx-core/build/android`:
  - SoomlaAndroidCore.jar
  - Cocos2dxAndroidCore.jar
  - square-otto-1.3.2.jar

  From `frameworks/runtime-src/Classes/cocos2dx-profile/build/android`:
  - AndroidProfile.jar
  - Cocos2dxAndroidProfile.jar

4. Update your AndroidManifest.xml to include permissions and the SoomlaApp:

    ``` xml
    <uses-permission android:name="android.permission.INTERNET"/>
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />

    <!-- optional: required for uploadImage from SD card -->
    <uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />

    <application ...
    	       android:name="com.soomla.SoomlaApp">
    	       ...
    </application>
    ```

<div class="info-box">The following steps should be done according to the target social network.

<br>NOTE: All jars for social providers are located at the following path: `frameworks/runtime-src/Classes/cocos2dx-profile/build/android`</div>

###Facebook for Android

Facebook is supported out-of-the-box, you just have to follow the next steps to make it work:

1. Add the following jars from the `build/android` folder:

  - `AndroidProfileFacebook.jar`

  - `simple.facebook-2.1.jar`

2. Import the Facebook SDK for Android into your project and setup all the relevant information (Application ID, etc).

    a. For more information regarding this refer to [Facebook SDK for Android](https://developers.facebook.com/docs/android)

    b. SOOMLA uses [Android Studio](https://developer.android.com/sdk/installing/studio.html), in this case you can extract the Facebook SDK into your project folder and then it's simply a case of importing the `iml` file in the Facebook SDK folder into your project.

3. Make the following changes in `AndroidManifest.xml`:

      ``` xml
      ...

      <application ...
          <activity android:name="com.soomla.profile.social.facebook.SoomlaFacebook$SoomlaFBActivity" android:theme="@android:style/Theme.Translucent.NoTitleBar.Fullscreen">
        </activity>
      </application>
      ```

###Google+ for Android

1. Add the following jars from the [build](https://github.com/soomla/android-profile/tree/master/build) folder:

  - `AndroidProfileGoogle.jar`

2. Follow the steps in [Getting started with GooglePlus API for Android](https://developers.google.com/+/mobile/android/getting-started).

    1. Import the `iml` file of the `google-play-services_lib` project to your project. You can either use the existing `google-play-services_lib` located under social-providers/android-profile-google/libs or create `google-play-services_lib` project by yourself
       as the link above states.

3. Make further changes to `AndroidManifest.xml`:

      ``` xml
      ...

      <application ...
          <activity android:name="com.soomla.profile.social.google.SoomlaGooglePlus$SoomlaGooglePlusActivity" android:theme="@android:style/Theme.Translucent.NoTitleBar.Fullscreen">
        </activity>
      </application>
      ```

4. Add the following permissions in `AndroidManifest.xml`:

    ``` xml
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.GET_ACCOUNTS" />
    <uses-permission android:name="android.permission.USE_CREDENTIALS" />
    ```

###Twitter for Android

Twitter is supported out-of-the-box, authentication is done via web view. Follow the next steps to make it work:

<div class="info-box">SOOMLA uses the [Twitter4J](https://github.com/yusuke/twitter4j) library (v 4.0.2) to support Twitter integration.</div>

1. Add the following jars from the [build](https://github.com/soomla/android-profile/tree/master/build) folder:

  - `AndroidProfileTwitter.jar`

  - `twitter4j-core-4.0.2.jar`

  - `twitter4j-asyc-4.0.2.jar`

2. Click [here](https://apps.twitter.com/) to create your Twitter app.

3. Make the following changes in `AndroidManifest.xml`:

      ```xml
      ...

      <application ...
          <activity android:name="com.soomla.profile.social.twitter.SoomlaTwitter$SoomlaTwitterActivity" android:theme="@android:style/Theme.Translucent.NoTitleBar.Fullscreen">
        </activity>
      </application>
      ```

**That's it! You can begin using cocos2dx-profile in your game.**

> Don't forget to `Build Custom Simulator` for Android, if you use Cocos IDE.

##Working with sources

For those of you who want to contribute code, please use our "sources environment".

1. Fetch submodules of repositories by recursively cloning them:

  ```
  $ git clone --recursive git@github.com:soomla/soomla-cocos2dx-core.git frameworks/runtime-src/Classes/soomla-cocos2dx-core
  $ git clone --recursive git@github.com:soomla/soomla-cocos2dx-core.git frameworks/runtime-src/Classes/cocos2dx-profile
  ```

  or, if you have repositories already cloned, fetch the submodules with this command:

  ```
  $ git submodule update --init --recursive
  ```

  <div class="info=box">**IMPORTANT:** You should run this command in every repository.</div>

2. For iOS: Use a sourced versions of linked projects (`frameworks/runtime-src/Classes/soomla-cocos2dx-core/development/Cocos2dxCoreFromSources.xcodeproj`, `frameworks/runtime-src/Classes/cocos2dx-profile/development/Cocos2dxProfileFromSources.xcodeproj`)

3. For Android: You can use our "sourced" modules for Android Studio (or IntelliJ IDEA) (`frameworks/runtime-src/Classes/soomla-cocos2dx-core/development/Cocos2dxCoreFromSources.iml`, `frameworks/runtime-src/Classes/cocos2dx-profile/development/Cocos2dxProfileFromSources.iml`), just include them in your project.

##Caveats

###Facebook Caveats

####**iOS**

1. **Facebook Application** - You must create a Facebook application and use its details in your Profile-based application (with Facebook)

2. **Facebook ID and Display name** - The Facebook application's ID and Name must be used in your application, this information must be added to the application's `plist` file, under `FacebookAppID` (App ID) and `FacebookDisplayName` (Application name)

3. **URL Schemes and openURL** - To support web-based authorization and dialogs the application needs to handle URL schemes (see [here](https://developers.facebook.com/docs/facebook-login/ios/v2.1) for more information):

  a. Under the project's info add an entry to `URL Types` and under `URL Schemes` add the string `fbxxxxxxx` the x's should be replaced with your Facebook App ID.

  b. See [Browser-based Authentication](#browser-based-authentication)

4. **Facebook Permissions** - Profile will request `publish_actions` from the user of the application, to test the application please make sure you test with either Admin, Developer or Tester roles

####**Android**

1. **Facebook Application** - You must create a Facebook application and use its details in your Profile-based application (with Facebook)

2. **Facebook ID** - The Facebook application's ID must be used in your application, this information should be added to the application's `strings.xml` file, under `fb_app_id` (App ID). In the `AndroidManifest.xml` file add the following:
    ```xml
        <application ...
            <activity android:name="com.facebook.LoginActivity" />
            <meta-data android:name="com.facebook.sdk.ApplicationId" android:value="@string/fb_app_id" />
        </application>
    ```

3. **Facebook Permissions** - Profile will request `publish_actions` from the user of the application, to test the application please make sure you test with either Admin, Developer or Tester roles

###Google+ Caveats

####**iOS**

1. You get the error: **401. That's an error. Error:invalid_client** - this could be the result of:

  a. Have you supplied the correct client ID?

###Twitter Caveats

####**iOS/Android**

1. **Login method returns 401 error** - this could be the result of a few issues:

  a. Have you supplied the correct consumer key and secret SoomlaProfile initialization?

  b. Have you supplied a `Callback URL` in your Twitter application settings?

##Browser-based Authentication

Most social framework SDKs support authentication through your web browser, when the user finishes authenticating through the browser your application will be called dependent on the URL schemes you have defined.

The callback to this process is `openURL` which should be defined in your `AppController`, **ios-profile** provides you with a helper method to handle the `openURL` callback through its providers. Add the following code to your `AppController` to handle this properly:

``` objectivec
#import "SoomlaProfile.h"

...

- (BOOL)application:(UIApplication *)application
            openURL:(NSURL *)url
  sourceApplication:(NSString *)sourceApplication
         annotation:(id)annotation
{
    BOOL urlWasHandled = [[SoomlaProfile getInstance] tryHandleOpenURL:url sourceApplication:sourceApplication annotation:annotation];

    if (urlWasHandled) {
        return urlWasHandled;
    }

    // Profile was unable to handle callback, do some custom handling
    return NO;
}
```

##Example

Below is an example of initializing Profile, logging the user into Facebook, and sharing a story on the user's Facebook wall. To see a full example, please see [cocos2dx-profile-example](https://github.com/soomla/cocos2dx-profile-example). To learn about the different entities and functionality of Profile, see [Main Classes & Operations](/cocos2dx/js/profile/Profile_MainClasses).

<br>

Initialize `Soomla` and `SoomlaProfile`.

``` js
Soomla.initialize("customSecret");

var profileParams = {
};

Soomla.SoomlaProfile.createShared(profileParams);
```

<br>
Log the user into Facebook.

``` js
Soomla.soomlaProfile.login(Soomla.Models.Provider.FACEBOOK);
```

<br>
Share a story on the user's Facebook wall.

``` js
Soomla.soomlaProfile.updateStory(
    Soomla.Models.Provider.FACEBOOK,
    'Check out this great story by SOOMLA!',
    'SOOMLA is 2 years young!',
    'SOOMLA Story',
    'soomla_2_years',
    'http://blog.soom.la/2014/08/congratulations-soomla-is-2-years-young.html',
    'http://blog.soom.la/wp-content/uploads/2014/07/Birthday-bot-300x300.png',
    Soomla.BadgeReward.create({itemId: 'sheriff', name: 'Sheriff'})  
);
```

And that's it! cocos2dx-profile knows how to contact the social provider (Facebook, Twitter, Google+ etc.) and perform social actions with the information you provide.
