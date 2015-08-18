---
layout: "content"
image: "Tutorial"
title: "Getting Started"
text: "Get started with android-profile. Here you can find integration instructions and a basic example of initialization."
position: 1
theme: 'platforms'
collection: 'android_profile'
module: 'profile'
platform: 'android'
---

#Getting Started

##Getting Started

1. Download [android-profile](http://library.soom.la/fetch/android-profile/latest?cf=github).

2. From the downloaded zip, add the following jars to your project.

	- `SoomlaAndroidCore.jar`

	- `AndroidProfile.jar`

	- `square-otto-1.3.2.jar`

3. Make the following changes to your AndroidManifest.xml:

	Set `SoomlaApp` as the main Application by placing it in the `application` tag:

	``` xml
	<application ...
	             android:name="com.soomla.SoomlaApp">
	```

4. Initialize `Soomla` with a "Soomla Secret", a secret of your choice used to encrypt your users' data. (If you used an older version of android-store, this should be the same as the old "custom secret"):

	``` java
	Soomla.initialize("[YOUR CUSTOM GAME SECRET HERE]");
	```

5. Initialize `SoomlaProfile`:

	``` java
	SoomlaProfile.getInstance().initialize();
	```

  **NOTE** that some social providers need special parameters in initialization - supply them like so:

	``` java
	HashMap<IProvider.Provider, HashMap<String, String>> providerParams = new
	            HashMap<IProvider.Provider, HashMap<String, String>>();

	// Fill in the HashMap according to social providers

	SoomlaProfile.getInstance().initialize(providerParams);
	```

  a. **Facebook** - You can provide your custom permission set here.

	``` java
	HashMap<String, String> facebookParams = new HashMap<String, String>();
	facebookParams.put("permissions", "public_profile,user_friends");
	providerParams.put(IProvider.Provider.FACEBOOK, facebookParams);

	SoomlaProfile.getInstance().initialize(providerParams);
	```

  b. **Google+** - No special parameters needed

  c. **Twitter** - Please provide **Consumer Key** and **Consumer Secret** from the "Keys and Access Tokens" section in [Twitter Apps](https://apps.twitter.com/), like so:

	``` java
	HashMap<String, String> twitterParams = new HashMap<String, String>();
	twitterParams.put("consumerKey", "[YOUR CONSUMER KEY]");
	twitterParams.put("consumerSecret", "[YOUR CONSUMER SECRET]");
	providerParams.put(IProvider.Provider.TWITTER, twitterParams);

	SoomlaProfile.getInstance().initialize(providerParams);
	```

  d. **Common** - There are some settings you can define which applies in all social providers params:

	 `autoLogin` - Setting autoLogin to true will tell Profile to try and login the user automatically to the provider, if the user has already logged in with it in the previous sessions. The default value is `false`.

	``` java
  // Example for FB
  HashMap<String, String> facebookParams = new HashMap<String, String>();
  facebookParams.put("autoLogin", Boolean.toString(true));
  providerParams.put(IProvider.Provider.FACEBOOK, facebookParams);
  ```

6. If integrating a virtual economy with the store module, please see [android-store](/android/store/Store_GettingStarted) for store setup.

<div class="info-box">The following steps should be done according to the target social network.</div>

### Facebook

Facebook is supported out-of-the-box, you just have to follow the next steps to make it work:

1. From the downloaded zip, Add the following jars to your project.

	- `AndroidProfileFacebook.jar`
	- `simple.facebook-2.1.jar`

2. Import the Facebook SDK for Android into your project and setup all the relevant information (Application ID, etc).

    - For more information regarding this refer to [Facebook SDK for Android](https://developers.facebook.com/docs/android)

    - SOOMLA uses [Android Studio](https://developer.android.com/sdk/installing/studio.html), in this case you can extract the Facebook SDK into your project folder and then it's simply a case of importing the `iml` file in the Facebook SDK folder into your project
3. Make the following changes in `AndroidManifest.xml`:

	```xml
	...
	<application ...
	    <activity android:name="com.soomla.profile.social.facebook.SoomlaFacebook$SoomlaFBActivity" android:theme="@android:style/Theme.Translucent.NoTitleBar.Fullscreen">
	  </activity>
	</application>
	```

### Twitter

Twitter is supported out-of-the-box, authentication is done via web view. Follow the next steps to make it work:

> **android-profile** uses the [Twitter4J](https://github.com/yusuke/twitter4j) library (v 4.0.2) to support Twitter integration

1. From the downloaded zip, Add the following jars to your project.

	- `AndroidProfileTwitter.jar`

	- `twitter4j-core-4.0.2.jar`

	- `twitter4j-asyc-4.0.2.jar`

2. Create your Twitter app at https://apps.twitter.com/

3. Make the following changes in `AndroidManifest.xml`:

	```xml
	...
	<application ...
	    <activity android:name="com.soomla.profile.social.twitter.SoomlaTwitter$SoomlaTwitterActivity" android:theme="@android:style/Theme.Translucent.NoTitleBar.Fullscreen">
	  </activity>
	</application>
	```

### Google Plus

1. From the downloaded zip, Add the `AndroidProfileGoogle.jar` jar to your project.

2. Follow [Step 1: Enable the Google+ API](https://developers.google.com/+/mobile/android/getting-started#step_1_enable_the_google_api) and create a google+ app for Android.

    > **Note:** Set the PACKAGE NAME of your google+ app to the value the package defined in your `AndroidManifest.xml`.

3. Import `google-play-services_lib` project as module dependency to your project.

    > **Note:** You can either download/copy the existing `google-play-services_lib` project located under [google social provider libs](https://github.com/soomla/android-profile/tree/master/social-providers/android-profile-google/libs) folder or [create one yourself](https://developers.google.com/+/mobile/android/getting-started#step_2_configure_your_eclipse_project).

4. Add `SoomlaGooglePlusActivity` to `AndroidManifest.xml` as following:

	```xml
	...
	<application ...
	    <activity android:name="com.soomla.profile.social.google.SoomlaGooglePlus$SoomlaGooglePlusActivity" android:theme="@android:style/Theme.Translucent.NoTitleBar.Fullscreen">
	  </activity>
	</application>
	```

5. Add the following permissions in `AndroidManifest.xml`:

	```xml
	<uses-permission android:name="android.permission.INTERNET" />
	<uses-permission android:name="android.permission.GET_ACCOUNTS" />
	<uses-permission android:name="android.permission.USE_CREDENTIALS" />
	```

## Facebook Caveats

1. **Facebook Application** - You must create a Facebook application and use its details in your Profile-based application (with Facebook)

2. **Facebook ID** - The Facebook application's ID must be used in your application, this information should be added to the application's `strings.xml` file, under `fb_app_id` (App ID). In the `AndroidManifest.xml` file add the following:

	```xml
	<application ...
	    <activity android:name="com.facebook.LoginActivity" />
	    <meta-data android:name="com.facebook.sdk.ApplicationId" android:value="@string/fb_app_id" />
	</application>
	```

3. **Facebook Permissions** - Profile will request `publish_actions` from the user of the application, to test the application please make sure you test with either Admin, Developer or Tester roles

## Twitter Caveats

1. **Login method returns 401 error** - this could be the result of a few issues:

	- Have you supplied the correct consumer key and secret SoomlaProfile initialization?

	- Have you supplied a `Callback URL` in your Twitter application settings?

## Google Plus Caveats

1. Did you set the PACKAGE NAME of your google+ app is the same as the package name in `AndroidManifest.xml`?

2. Did you set the CERTIFICATE FINGERPRINT (SHA1) of your google+ app is the same as your debug.keystore or release keystore SHA1?

3. Did you add google-play-services_lib as a dependency to your project?

## Example

Below is a brief example of initializing Profile, logging the user into Facebook, and sharing a story on the user's Facebook wall. To see a full example, check out the [Muffin Rush Example](https://github.com/soomla/android-profile/tree/master/SoomlaAndroidExample/src/com/soomla/example).

To learn about the different entities and functionality of Profile, see [Main Classes & Operations](/android/profile/Profile_MainClasses).

<br>
Initialize `SoomlaProfile`.

``` java
SoomlaProfile.getInstance().initialize();
```

<br>
Log the user into Facebook.

``` java
SoomlaProfile.getInstance().login(
	this,                                     // Activity
	IProvider.Provider.TWITTER                // Social Provider
	someReward                                // Reward
);
```

<br>
Share a story on the user's Facebook wall.

``` java
SoomlaProfile.getInstance().updateStory(
	IProvider.Provider.FACEBOOK                // Social Provider
	"Check out this great story by SOOMLA!",   // Message
	"SOOMLA is 2 years young!",                // Name
	"SOOMLA is GROWing",                       // Caption
	"soomla_2_years",                          // Desc
	"http://blog.soom.la",                     // Link
	"http://blog.soom.la.../soombot.png",      // Image
	new BadgeReward("sheriff", "Sheriff")      // Reward
);
```
