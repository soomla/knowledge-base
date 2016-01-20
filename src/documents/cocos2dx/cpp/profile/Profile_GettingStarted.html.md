---
layout: "content"
image: "Tutorial"
title: "Getting Started"
text: "Get started with cocos2dx-profile. Here you can find integration instructions and a basic example of initialization."
position: 1
theme: 'platforms'
collection: 'cocos2dx_profile'
module: 'profile'
lang: 'cpp'
platform: 'cocos2dx'
---

# Getting Started

*If you want to develop with sources, refer to the [Working with sources](#working-with-sources) section below*.

<div class="info-box">Profile depends on SOOMLA's Core module. This document assumes that you are new to SOOMLA and have not worked with any of the other SOOMLA modules. If this is not the case, and you already have Core & Store, please follow these directions only for the Profile module.</div>

## General Instructions

1. If you didn't already, clone the Cocos2d-x framework from [here](https://github.com/cocos2d/cocos2d-x), or download it from the [Cocos2d-x website](http://www.cocos2d-x.org/download). Make sure the version you clone is supported by cocos2dx-profile (the tag is the version).

2. Clone [soomla-cocos2dx-core](https://github.com/soomla/soomla-cocos2dx-core) and **cocos2dx-profile** into the `extensions` directory located at the root of your Cocos2d-x framework:

    ```
    $ git clone git@github.com:soomla/soomla-cocos2dx-core.git extensions/soomla-cocos2dx-core

    $ git clone git@github.com:soomla/cocos2dx-profile.git extensions/cocos2dx-profile
    ```

3. We use a [fork](https://github.com/soomla/jansson) of the jansson library for JSON parsing, clone our fork into the `external` directory at the root of your framework:

    ```
    $ git clone git@github.com:soomla/jansson.git external/jansson
    ```
4. Make sure to include the `Cocos2dxProfile.h` header whenever you use any of the **cocos2dx-profile** methods:

  ``` cpp
  #include "Cocos2dxProfile.h"
  ```

5. Initialize `CCSoomla` with a `customSecret` and `CCSoomlaProfile` with its params. **Custom Secret** is an encryption secret you provide that will be used to secure your data. Choose this secret wisely, you can't change it after you launch your game!

  ``` cpp
  soomla::CCSoomla::initialize("customSecret");
  ```

  ``` cpp      
  soomla::CCSoomlaProfileConfigBuilder *profileConfig 
      = soomla::CCSoomlaProfileConfigBuilder::create();
  
  soomla::CCSoomlaProfile::initialize(profileConfig->build());  
  ```

    <div class="warning-box">Initialize `CCSoomlaProfile` ONLY ONCE when your application loads.</div>

6. Note that some social providers need special parameters to be passed to `CCSoomlaProfile` in order for them to work:

  a. **Facebook** - You can provide your custom permission set here, these permissions will be requested from the user on login.

	``` cpp
	profileConfig
	  ->addSocialProviderConfiguration(soomla::CCSoomlaFacebookConfigBuilder::create()
      ->setPermissions("public_profile,user_friends"));    
	```
  <div class="info-box">**NOTE:** You should not request all the possible permissions you'll ever need in your app,
  just request the reasonable minimum. Other permissions will be requested, when they will be needed.
  For instance, if you try to call `updateStatus`, SoomlaProfile will ask for `publish_actions` permission, if your app has not got it.
  </div>

  <div class="info-box">**NOTE:** Currently the Android implementation does not work in this way,
  and you should provide all the needed permissions there. We are working on fixing that.</div>


  b. **Google+** - Please provide Client ID from the "API & Auth, credentials" section like so:

  ``` cpp
  profileConfig
    ->addSocialProviderConfiguration(soomla::CCSoomlaGooglePlusConfigBuilder::create()
      ->setClientId("[YOUR CLIENT ID]"));  
  ```

  c. **Twitter** - Please provide a "Consumer Key" and a "Consumer Secret" from the "Keys and Access Tokens" section in [Twitter Apps](https://apps.twitter.com/), like so:

  ``` cpp
  profileConfig
    ->addSocialProviderConfiguration(soomla::CCSoomlaTwitterConfigBuilder::create()
      ->setConsumerKey("[YOUR CONSUMER KEY]")
      ->setConsumerSecret("[YOUR CONSUMER SECRET]"));   
  ```

  d. **Common** - There are some settings you can define which applies in all social providers params:
	- `autoLogin` - Setting autoLogin to true will tell Profile to try and login the user automatically to the provider, if the user has already logged in with it in the previous sessions. The default value is `false`.

  ``` cpp
  // For instance for FB
  profileConfig->addSocialProviderConfiguration(soomla::CCSoomlaFacebookConfigBuilder::create()             
      ->enableAutoLogin(true));
  ```

7. You'll need to subscribe to profile events to get notified about social network related events. refer to the [Event Handling](/cocos2dx/cpp/profile/Profile_Events) section for more information.

<br>
<div class="info-box">The next steps are different according to which platform you're using.</div>

## Instructions for iOS

In your XCode project, perform the following steps:

1. Add `jansson` (**external/jansson/**) to your project (just add it as a source folder, make sure to check "create group").

2. For each of the following XCode projects:

  * `Cocos2dXCore.xcodeproj` (**extensions/soomla-cocos2dx-core/**).

  * `Cocos2dXProfile.xcodeproj` (**extensions/cocos2dx-profile/**).

    Perform the following:

    a. Drag the project into your project.

    b. Add its targets to your **Build Phases->Target Dependencies**.

    c. Add the Products (\*.a) of the project to **Build Phases->Link Binary With Libraries**.

  ![alt text](/img/tutorial_img/cocos2dx-profile/iosStep2.png "iOS Integration")

3. Add the following directories to **Build Settings->Header Search Paths** (with `recursive` option):
 - `$(SRCROOT)/../cocos2d/extensions/soomla-cocos2dx-core/Soomla`
 
 - `$(SRCROOT)/../cocos2d/extensions/soomla-cocos2dx-core/build/ios/headers`
 
 - `$(SRCROOT)/../cocos2d/extensions/cocos2dx-profile/Soomla`
 
 - `$(SRCROOT)/../cocos2d/extensions/cocos2dx-profile/build/ios/headers`

 ![alt text](/img/tutorial_img/cocos2dx-profile/headerSP.png "Header search paths")

4. To support browser-based authentication read [here](#browser-based-authentication).

5. Make sure you have the following frameworks linked to your XCode project: **Security, libsqlite3.0.dylib**.

6. Go to **Build Settings->Library Search Paths**, set the library search paths to `extensions/cocos2dx-profile/build/ios` with the `recursive` option.

<div class="info-box">The following steps should be done according to the target social network.</div>

### Facebook for iOS

Facebook is supported out-of-the-box, you just have to follow the next steps to make it work:

1. Add the Facebook SDK for iOS to the project's Frameworks and make sure your project links to the project

2. Refer to [Getting Started with the Facebook iOS SDK](https://developers.facebook.com/docs/ios/getting-started/) for more information

3. Add `-lSoomlaiOSProfileFacebook` to your project's **Build Settings->Other Linker Flags**

### Google+ for iOS

Google+ is supported out-of-the-box, authentication is done either through the signed in Google+ account or through the web browser (fallback). Follow the next steps to make it work:

1. Click [here](https://console.developers.google.com/project) to create your Google Plus app.

2. SOOMLA Profile supports [GPGS](https://developers.google.com/games/services/) functionality out-of-the-box also. If you want to use GPGS, please, follow [Steps 3 and 4 (Option 2: Manual Installation) in GPGS Quickstart Guide](https://developers.google.com/games/services/ios/quickstart). Also, there you can find information how to add your own leaderboards.

3. Follow [Add URL schemes to your project](https://developers.google.com/identity/sign-in/ios/start-integrating#add_url_schemes_to_your_project) and add url type to your application to allow browser based authentication.

4. Make sure you have the following frameworks in your application's project: **GooglePlus, GoogleOpenSource, GooglePlus.bundle** and, if you want to use GPGS, also include the following frameworks: **gpg.framework, gpg.bundle**.

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
    
    and, if you want to use GPGS, also include the following frameworks:
                
    * CoreData.framework
       
    * CoreTelephony.framework
                    
    * QuartzCore.framework
                
    * libc++.dylib
        
    * libz.dylib

6. Add `-lSoomlaiOSProfileGoogle` to your project's **Build Settings->Other Linker Flags**.

### Twitter for iOS

Twitter is supported out-of-the-box, authentication is done either through the signed in **native** Twitter account (iOS 5+) or through web browser (fallback). Follow the next steps to make it work:

1. Click [here](https://apps.twitter.com/) to create your Twitter app.

2. Add a URL scheme to your application:

  a. Go to your project's **Info->URL Types**.

  b. Add a new URL type and enter `tw<Your Twitter app consumer key>` (without the braces) as your scheme.

3. Make sure you have the following frameworks in your application's project: **Twitter, Social, Accounts**.

4. Add `-lSoomlaiOSProfileTwitter -lSTTwitter` to your project's **Build Settings->Other Linker Flags**.

  NOTE: **ios-profile** uses the [STTWitter](https://github.com/nst/STTwitter) library (v 0.1.5) to support Twitter integration.
    
### Game Center

Game Center is supported out-of-the-box, authentication is done through the signed in Game Center account. Follow the next steps to make it work:

1. Follow Apple's guide how to [Incorporate Game Center into Your Game](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/GameKit_Guide/GameCenterOverview/GameCenterOverview.html#//apple_ref/doc/uid/TP40008304-CH5-SW22) (steps 4 and 5).

2. Add `-lSoomlaiOSProfileGameCenter` to your project's **Build Settings->Other Linker Flags**.

<div class="info-box">This social provider is for iOS only.</div>

That's it! Now all you have to do is build your XCode project and run your game with cocos2dx-profile.

<div class="info-box">**FOR CORRECT iOS USAGE:** <br/>
	**1.** If you are building your app under Windows, you have to have iTunes installed since the SOOMLA postprocessing is expecting a utility that exists in OS X and is installed with iTunes in Windows.                                          
  **2.** If `-ObjC` flag conflicts with other libs you use in your project, you should remove the `-ObjC` flag from the link flags in Xcode and add `-force_load $(BUILT_PRODUCTS_DIR)/<LIBRARY_NAME>` to `Other Linker Flags` for the following SOOMLA libraries:
  <ul>
    <li>`libSoomlaiOSCore.a`</li>    
    <li>`libSoomlaiOSSProfile.a`</li>
    <li>`libCocos2dXiOSCore.a`</li>
    <li>`libCocos2dXiOSProfile.a`</li>    
    <li>`libSoomlaiOSSProfileFacebook.a` (if you use Facebook)</li>
    <li>`libSoomlaiOSSProfileTwitter.a` (if you use Twitter)</li>
    <li>`libSoomlaiOSSProfileGoogle.a` (if you use Google+)</li>      
  </ul>
</div>


## Instructions for Android

1. Import cocos2dx-profile module into your project's Android.mk by adding the following:

    ```
    LOCAL_WHOLE_STATIC_LIBRARIES += cocos2dx_profile_static # add this line along with your other LOCAL_WHOLE_STATIC_LIBRARIES

    $(call import-module, extensions/cocos2dx-profile) # add this line at the end of the file, along with the other import-module calls
    ```

2. Add the following jars to your android project's classpath:

  From `extensions/soomla-cocos2dx-core/build/android`:
  
  - SoomlaAndroidCore.jar
  
  - Cocos2dxAndroidCore.jar
  
  - square-otto-1.3.2.jar

  From `extensions/cocos2dx-profile/build/android`:
  
  - AndroidProfile.jar
  
  - Cocos2dxAndroidProfile.jar

4. Update your AndroidManifest.xml to include permissions:

    ``` xml
    <uses-permission android:name="android.permission.INTERNET"/>
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />

    <!-- optional: required for uploadImage from SD card -->
    <uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
    
    ```

<div class="info-box">The following steps should be done according to the target social network.

<br>NOTE: All jars for social providers are located at the following path: `extensions/cocos2dx-profile/build/android`</div>

### Facebook for Android

Facebook is supported out-of-the-box, you just have to follow the next steps to make it work:

1. Add the following jars from the `build/android` folder:

  - `AndroidProfileFacebook.jar`

  - `simple-fb-4.0.3.jar`
  
  - `gson-1.7.2.jar`

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

### Google+ for Android

1. Add the following jars from the [zip](http://library.soom.la/fetch/android-store/latest?cf=knowledge%20base):

  - `AndroidProfileGoogle.jar`

2. Follow the steps in [Getting started with GooglePlus API for Android](https://developers.google.com/+/mobile/android/getting-started).

	  Import the `iml` file of the `google-play-services_lib` project to your project. You can either use the existing `google-play-services_lib` located under social-providers/android-profile-google/libs or create `google-play-services_lib` project by yourself as the link above states.

3. SOOMLA Profile supports [GPGS](https://developers.google.com/games/services/) functionality out-of-the-box also. If you want to use GPGS, please, follow [Steps 2 and 3 in GPGS Quickstart Guide](https://developers.google.com/games/services/android/quickstart#step_2_set_up_the_game_in_the_dev_console). Also, there you can find information how to add your own leaderboards.

4. Make further changes to `AndroidManifest.xml`:

      ``` xml
      ...

      <application ...
          <activity android:name="com.soomla.profile.social.google.SoomlaGooglePlus$SoomlaGooglePlusActivity" android:theme="@android:style/Theme.Translucent.NoTitleBar.Fullscreen">
        </activity>
      </application>
      ```

5. Add the following permissions in `AndroidManifest.xml`:

    ``` xml
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.GET_ACCOUNTS" />
    <uses-permission android:name="android.permission.USE_CREDENTIALS" />
    ```

### Twitter for Android

Twitter is supported out-of-the-box, authentication is done via web view. Follow the next steps to make it work:

<div class="info-box">SOOMLA uses the [Twitter4J](https://github.com/yusuke/twitter4j) library (v 4.0.2) to support Twitter integration.</div>

1. Add the following jars from the [zip](http://library.soom.la/fetch/android-store/latest?cf=knowledge%20base) folder:

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

That's it! Don't forget to run the **build_native.sh** script so cocos2dx-profile sources will be built with cocos2d-x.

## Working with sources

For those of you who want to contribute code, please use our "sources environment".

1. Fetch submodules of repositories by recursively cloning them:

  ```
  $ git clone --recursive git@github.com:soomla/soomla-cocos2dx-core.git extensions/soomla-cocos2dx-core

  $ git clone --recursive git@github.com:soomla/cocos2dx-profile.git extensions/cocos2dx-profile
  ```

  or, if you have repositories already cloned, fetch the submodules with this command:

  ```
  $ git submodule update --init --recursive
  ```

  <div class="info=box">**IMPORTANT:** You should run this command in every repository.</div>

2. For iOS: Use a sourced versions of linked projects (`extensions/soomla-cocos2dx-core/development/Cocos2dxCoreFromSources.xcodeproj`, `extensions/cocos2dx-profile/development/Cocos2dxProfileFromSources.xcodeproj`)

3. For Android: You can use our "sourced" modules for Android Studio (or IntelliJ IDEA) (`extensions/soomla-cocos2dx-core/development/Cocos2dxCoreFromSources.iml`, `extensions/cocos2dx-profile/development/Cocos2dxProfileFromSources.iml`), just include them in your project.

### Facebook Caveats

#### **iOS**

1. **Facebook Application** - You must create a Facebook application and use its details in your Profile-based application (with Facebook)

2. **Facebook ID and Display name** - The Facebook application's ID and Name must be used in your application, this information must be added to the application's `plist` file, under `FacebookAppID` (App ID) and `FacebookDisplayName` (Application name)

3. **URL Schemes and openURL** - To support web-based authorization and dialogs the application needs to handle URL schemes (see [here](https://developers.facebook.com/docs/facebook-login/ios/v2.1) for more information):

  a. Under the project's info add an entry to `URL Types` and under `URL Schemes` add the string `fbxxxxxxx` the x's should be replaced with your Facebook App ID.

  b. See [Browser-based Authentication](#browser-based-authentication)

4. **Facebook Permissions** - Profile will request `publish_actions`, `user_location`, `user_likes` from the user of the application, to test the application please make sure you test with either Admin, Developer or Tester roles

#### **Android**

1. **Facebook Application** - You must create a Facebook application and use its details in your Profile-based application (with Facebook)

2. **Facebook ID** - The Facebook application's ID must be used in your application, this information should be added to the application's `strings.xml` file, under `fb_app_id` (App ID). In the `AndroidManifest.xml` file add the following:
    ```xml
        <application ...
            <activity android:name="com.facebook.LoginActivity" />
            <meta-data android:name="com.facebook.sdk.ApplicationId" android:value="@string/fb_app_id" />
        </application>
    ```

3. **Facebook Permissions** - Profile will request `publish_actions`, `user_location`, `user_likes` from the user of the application, to test the application please make sure you test with either Admin, Developer or Tester roles

### Google+ Caveats

#### **iOS**

1. You get the error: **401. That's an error. Error:invalid_client** - this could be the result of:

  a. Have you supplied the correct client ID?

### Twitter Caveats

#### **iOS/Android**

1. **Login method returns 401 error** - this could be the result of a few issues:

  a. Have you supplied the correct consumer key and secret SoomlaProfile initialization?

  b. Have you supplied a `Callback URL` in your Twitter application settings?

## Browser-based Authentication

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

## Example

Below is an example of initializing Profile, logging the user into Facebook, and sharing a story on the user's Facebook wall. To see a full example, please see [cocos2dx-profile-example](https://github.com/soomla/cocos2dx-profile-example). To learn about the different entities and functionality of Profile, see [Main Classes & Operations](/cocos2dx/cpp/profile/Profile_MainClasses).

<br>

Initialize `CCSoomla` and `CCSoomlaProfile`.

``` cpp
soomla::CCSoomla::initialize("customSecret");

...

__Dictionary *profileParams = __Dictionary::create();
soomla::CCSoomlaProfile::initialize(profileParams);
```

<br>
Log the user into Facebook.

``` cpp
soomla::CCSoomlaProfile::getInstance()->login(soomla::FACEBOOK, &profileError);
```

<br>
Share a story on the user's Facebook wall.

``` cpp
soomla::CCSoomlaProfile::getInstance()->updateStory(
    soomla::FACEBOOK,
    "Check out this great story by SOOMLA!",
    "SOOMLA is 2 years young!",
    "SOOMLA Story",
    "soomla_2_years",
    "http://blog.soom.la/2014/08/congratulations-soomla-is-2-years-young.html",
    "http://blog.soom.la/wp-content/uploads/2014/07/Birthday-bot-300x300.png",
    soomla::CCBadgeReward::create("sheriff", "Sheriff"), &profileError  
);
```

And that's it! cocos2dx-profile knows how to contact the social provider (Facebook, Twitter, Google+ etc.) and perform social actions with the information you provide.
