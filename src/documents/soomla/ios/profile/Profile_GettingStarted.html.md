---
layout: "soomla-content"
image: "Tutorial"
title: "Getting Started"
text: "Get started with ios-profile. Here you can find integration instructions and a basic example of initialization."
position: 1
theme: 'soomla-ios'
collection: 'soomla_ios_profile'
module: 'profile'
platform: 'ios'
---

# Getting Started

## Getting Started

1. Download [ios-profile](http://library.soom.la/fetch/ios-profile/latest?cf=knowledge%20base).

2. The static libs and headers you need are in the zip you downloaded from the link above.

  - Set your project's "Library Search Paths" and "Header Search Paths" to that folder with the `recursive` option.

  - Add `-ObjC -lSoomlaiOSProfile -lSoomlaiOSCore` to the project's "Other Linker Flags".

3. Make sure you have the following frameworks in your application's project: **Security, libsqlite3.0.dylib**.

4. Initialize **Soomla** with a secret of your choise, used to encrypt your user data. (For those who came from older versions, this should be the same as the old "custom secret"):

    ```objectivec
    [Soomla initializeWithSecret:@"[YOUR CUSTOM GAME SECRET HERE]"];
    ```

5. If integrating a virtual economy with the store module, please see [ios-store](/ios/store/Store_GettingStarted) for store setup.

6. Initalize Soomla Profile:

  ``` objectivec
  [[SoomlaProfile getInstance] initialize];
  ```

<div class="info-box">**NOTE:**
  If `-ObjC` flag conflicts with other libs you use in your project, you should remove the `-ObjC` flag from the link flags in Xcode and add `-force_load $(BUILT_PRODUCTS_DIR)/<LIBRARY_NAME>` to `Other Linker Flags` for the following SOOMLA libraries:    
    <ul>
      <li>`libSoomlaiOSCore.a`</li>
      <li>`libSoomlaiOSSProfile.a`</li>      
      <li>`libSoomlaiOSSProfileFacebook.a` (if you use Facebook)</li>
      <li>`libSoomlaiOSSProfileTwitter.a` (if you use Twitter)</li>
      <li>`libSoomlaiOSSProfileGoogle.a` (if you use Google+)</li>
      <li>`libSoomlaiOSSProfileGameCenter.a` (if you use GameCenter)</li>
    </ul>
</div>

  Note that some providers will need initialization parameters (see their sections below), in that case you'll need to supply their parameters here, each with its dictionary:

  ```objectivec
  NSDictionary* providerParams = @{ @([provider]) : @{...}
                                    ... };
    [[SoomlaProfile getInstance] initialize:providerParams];
  ```

  1. **Facebook** - You can provide your custom permission set here, these permissions will be requested from the user on login.

    ```objectivec
    @(FACEBOOK) : @{ @"permissions": @"public_profile,user_friends" }
	```

  <div class="info-box">**NOTE:** You should not request all the possible permissions you'll ever need in your app,
  just request the reasonable minimum. Other permissions will be requested, when they will be needed.
  For instance, if you try to call `updateStatus`, SoomlaProfile will ask for `publish_actions` permission, if your app has not got it.</div>

  2. **Google+** - Please provide **CLIENT ID** from the "API&Auth" -> "Credentials" -> "Client ID for iOS applicatio" section in [Google Developer Console Projects](https://console.developers.google.com/project/), like so:
    ```objectivec
    @(GOOGLE) : @{ @"clientId": @"[CLIENT ID]" }
    ```

   (OPTIONAL) You can supply the `enableGameServices` key in the parameters (with a `BOOL`) value if you would like to use functionality of Google Play Game Services (score submitting, retrieving of leaderboards and scores), like so:

     ```objectivec
     @(GOOGLE): @{ ..., @"enableGameServices": @(YES) },
     ```

  3. **Twitter** - Please provide **Consumer Key** and **Consumer Secret** from the "Keys and Access Tokens" section in [Twitter Apps](https://apps.twitter.com/), like so:
    ```objectivec
    @(TWITTER) : @{ @"consumerKey": @"[YOUR CONSUMER KEY]",
                 @"consumerSecret": @"[YOUR CONSUMER SECRET]" }
    ```

  (OPTIONAL) You can supply the `forceWeb` key in the parameters (with a `BOOL`) value if you would like to force browser-based authorization, like so:

  ```objectivec
  @(TWITTER): @{ ..., @"forceWeb": @(YES) },
  ```

  4. **Common** - There are some settings you can define which applies in all social providers params:

	 `autoLogin` - Setting autoLogin to true will tell Profile to try and login the user automatically to the provider, if the user has already logged in with it in the previous sessions. The default value is `false`.

	``` objectivec
	// Example for FB
	@(FACEBOOK) : @{ @"autoLogin": @(YES) }
	```

<div class="info-box">The following steps should be done according to the target social network.</div>

**ios-profile** is structured to support multiple social networks (Facebook, Twitter, etc.), at the time of writing this the framework only supports Facebook, Twitter and Google+ integration.

### Facebook

Facebook is supported out-of-the-box, you just have to follow the next steps to make it work:

1. Add the Facebook SDK for iOS to the project's Frameworks and make sure your project links to the project

2. Refer to [Getting Started with the Facebook iOS SDK](https://developers.facebook.com/docs/ios/getting-started/) for more information

3. Add `-lSoomlaiOSProfileFacebook` to the project's "Other Linker Flags".

### Twitter

Twitter is supported out-of-the-box, authentication is done either through the signed in Twitter account (iOS 5+) or through web browser (fallback). Follow the next steps to make it work:

1. Create your Twitter app at https://apps.twitter.com/

2. Add a URL scheme to your application:

  - Go to the application's "Info" section in the build target

  - Under "URL Types" add a new URL type

  - In the "URL Schemes" fill in `tw<Your Twitter app consumer key>` (without the braces)

3. Make sure you have the following frameworks in your application's project: **Twitter, Social, Accounts**.

4. Add `-lSoomlaiOSProfileTwitter -lSTTwitter` to the project's "Other Linker Flags".

  **NOTE** that ios-profile uses the [STTWitter](https://github.com/nst/STTwitter) library (v 0.1.5) to support Twitter integration.

### Google Plus

Google Plus is supported out-of-the-box, authentication is done either through the signed in Google Plus account or through web browser (fallback). Follow the next steps to make it work:

1. Follow [Step 1. Creating the Google Developers Console project](https://developers.google.com/+/mobile/ios/getting-started#step_1_creating_the_console_name_project) and create a google+ app for iOS.

  **NOTE:** Set the BUNDLE ID of the google+ app to the bundle identifier of your app.

2. SOOMLA Profile supports [GPGS](https://developers.google.com/games/services/) functionality out-of-the-box also. If you want to use GPGS, please, follow [Steps 3 and 4 (Option 2: Manual Installation) in GPGS Quickstart Guide](https://developers.google.com/games/services/ios/quickstart). Also, there you can find information how to add your own leaderboards.

3. Follow [Add URL schemes to your project](https://developers.google.com/identity/sign-in/ios/start-integrating#add_url_schemes_to_your_project) and add url type to your application to allow browser based authentication.

4. Add the following frameworks to your application:

    * GoogleSignIn.framework (can be downloaded here: https://developers.google.com/identity/sign-in/ios/sdk/)

    * GoogleSignIn.bundle (coming together with GoogleSignIn.framework)

    * GooglePlus.framework (can be downloaded here: https://developers.google.com/+/downloads/)

    * GoogleOpenSource.framework (can be downloaded here: https://developers.google.com/+/downloads/)

    * GooglePlus.bundle (coming together with GooglePlus.framework)

    and, if you want to use GPGS, also include the following frameworks:

    * gpg.framework (can be downloaded here: https://developers.google.com/games/services/downloads/sdks)

    * gpg.bundle (coming together with gpg.framework)

  **NOTE** that ios-profile uses [Google Plus SDK 1.7.1](https://developers.google.com/+/mobile/ios/upgrading-sdk) to support Google Plus integration.

5. Add additional frameworks to your project:

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

    * StoreKit.framework

    * UIKit.framework

    and, if you want to use GPGS, also include the following frameworks:

    * CoreData.framework

    * CoreTelephony.framework

    * QuartzCore.framework

    * libc++.dylib

    * libz.dylib

6. Add `-lSoomlaiOSProfileGoogle` to the project's "Other Linker Flags".

### Game Center

Game Center is supported out-of-the-box, authentication is done through the signed in Game Center account. Follow the next steps to make it work:

1. Follow Apple's guide how to [Incorporate Game Center into Your Game](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/GameKit_Guide/GameCenterOverview/GameCenterOverview.html#//apple_ref/doc/uid/TP40008304-CH5-SW22) (steps 4 and 5).

2. Add `-lSoomlaiOSProfileGameCenter` to the project's "Other Linker Flags".

## Browser-based Authentication

Most social framework SDKs support authentication through your web browser, when the user finishes authenticating through the browser your application will be called dependent on the URL schemes you have defined.

The callback to this process is `openURL` which should be defined in your `AppDelegate`, **ios-profile** provides you with a helper method to handle the `openURL` callback through its providers. Add the following code to your `AppDelegate` to handle this properly:

``` objectivec
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

## Caveats

### Facebook Caveats

1. **Facebook Application** - You must create a Facebook application and use its details in your Profile-based application (with Facebook)

2. **Facebook ID and Display name** - The Facebook application's ID and Name must be used in your application, this information must be added to the application's `plist` file, under `FacebookAppID` (App ID) and `FacebookDisplayName` (Application name)

3. **URL Schemes and openURL** - To support web-based authorization and dialogs the application needs to handle URL schemes (see [here](https://developers.facebook.com/docs/facebook-login/ios/v2.1) for more information):

  - Under the project's info add an entry to `URL Types` and under `URL Schemes` add the string `fbxxxxxxx` the x's should be replaced with your Facebook App ID.

  - See [Browser-based Authentication](#browser-based-authentication)

4. **Facebook Permissions** - Profile will request `publish_actions`, `user_location`, `user_likes` from the user of the application, to test the application please make sure you test with either Admin, Developer or Tester roles.

### Twitter Caveats

1. **Login method returns 401 error** - this could be the result of a few issues:

  - Have you supplied the correct consumer key and secret in `SoomlaProfile` initialization?

  - Have you supplied a `Callback URL` in your Twitter application settings?

### Google Plus Caveats

1. **401. That's an error. Error:invalid_client** - this could be the result of a few issues:

  - Have you supplied the correct client id in `SoomlaProfile` initialization?

  - Does your google+ app BUNDLE ID equal to the Bundle Identifier of your iOS app?

  - Did you add a URL type with identifier and Url Schemes set to your Bundle Identifier?

2. Did you add all required the frameworks?

## Example

Below is a brief example of initializing Profile, logging the user into Facebook, and sharing a story on the user's Facebook wall. To see a full example, check out the [iOS Profile Example](https://github.com/soomla/ios-profile/tree/master/SoomlaiOSProfileExample).

To learn about the different entities and functionality of Profile, see [Main Classes & Operations](/ios/profile/Profile_MainClasses).

<br>
Initialize `SoomlaProfile`.

``` objectivec
[[SoomlaProfile getInstance] initialize];
```

<br>
Log the user into Facebook.

``` objectivec
[[SoomlaProfile getInstance]
  loginWithProvider:FACEBOOK
  andPayload:@""
  andReward:appDelegate.loginReward];
```

<br>
Share a story on the user's Facebook wall.

``` objectivec
[[SoomlaProfile getInstance]
  updateStoryWithProvider:FACEBOOK
  andMessage:@"Message"
  andName:@"Name"
  andCaption:@"Caption"
  andDescription:@"Description"
  andLink:@"https://developers.facebook.com/docs/ios/share/"
  andPicture:@"http://i.imgur.com/g3Qc1HN.png"
  andReward:appDelegate.updateStatusReward];
```
