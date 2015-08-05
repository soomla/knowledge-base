---
layout: "content"
image: "Tutorial"
title: "Getting Started"
text: "Get started with unity3d-profile. Here you can find integration instructions and a basic example of initialization."
position: 1
theme: 'platforms'
collection: 'unity_profile'
module: 'profile'
platform: 'unity'
---

#Getting Started

<div class="info-box">Profile depends on SOOMLA's Core module. This document assumes that you are new to SOOMLA and have not worked with any of the other SOOMLA modules. If this is not the case, and you already have Core & Store, please follow these directions only for the Profile module.</div>

##Getting Started

1. Download and import:

	a. [soomla-unity3d-core.unitypackage](https://github.com/soomla/unity3d-profile/blob/master/soomla-unity3d-core.unitypackage)

	b. [unity3d-profile.unitypackage](http://bit.ly/1sUDdG0)

	If you want to use Store-related rewards you'll need to go over the instructions of [unity3d-store](https://github.com/soomla/unity3d-store).

2. Drag the `CoreEvents` and `ProfileEvents` Prefabs from `Assets/Soomla/Prefabs` into your scene. You should see them listed in the "Hierarchy" panel. **IMPORTANT:** This step MUST be done for unity3d-profile to work properly!

	![alt text](/img/tutorial_img/unity-profile/prefabs.png "Prefabs")

3. Go to the menu bar, under **Window > Soomla > Edit Settings**:

	a. Change the value for **Soomla Secret**. "Soomla Secret" is an encryption secret you provide that will be used to secure your data. Choose this secret wisely, you can't change it after you launch your game! (NOTE: If you used unity3d-store versions before v1.5.2 this secret MUST be the same as "Custom Secret".)

	b. Select the **Social Platform** that you want to integrate with. The current available social platforms are Facebook, Twitter & Google+.

	![alt text](/img/tutorial_img/unity-profile/soomlaSettings.png "Soomla Settings")

4. Initialize `SoomlaProfile`:

	``` cs
	SoomlaProfile.Initialize();
	```

	<div class="warning-box">IMPORTANT NOTES:<br>

	Initialize `SoomlaProfile` ONLY ONCE when your application loads.<br>

	Initialize `SoomlaProfile` in the `Start()` function of a `MonoBehaviour` and **NOT** in the `Awake()` function. SOOMLA has its own `MonoBehaviour` and it needs to be "Awakened" before you initialize.<br>

5. Call all the social functions you can from the `SoomlaProfile` class (not from the social provider class). Otherwise, you won't be able to work with SOOMLA correctly. You can still call functions from the social provider, such as the `FB` class, but only those that are not provided by `SoomlaProfile`.

6. You'll need event handlers in order to be notified about in-app purchasing-related events and social-related events. Refer to the [Event Handling](/unity/profile/Profile_Events) document for more information.

And that's it! unity3d-profile knows how to contact the social provider (Facebook, Twitter, Google+ etc.) and perform social actions with the information you provide.

<div class="info-box">The following steps should be done according to the target social network.</div>

###Facebook

1. Download and import the official [Facebook SDK](https://developers.facebook.com/docs/unity/getting-started/canvas).

2. Follow Facebook's integration process.

<div class="warning-box">You don't need to initialize FB. SoomlaProfile will initialize it for you. (Do **not** call `FB.Init()`).</div>

3. Create an empty folder named `Facebook` under `Assets/Plugins`.

4. Move the `Scripts` folder from `Assets/Facebook` to `Assets/Plugins/Facebook` - SOOMLA works from the `Plugins` folder so that it'll be available to UnityScript developers. This is why you need to move `Facebook` into `Plugins` as well.

  <div class="info-box">**NOTE:** When working with Unity version > 4.5.0 (targeting iOS) please follow these extra steps:<br>

  1. Edit the file `Assets/Facebook/Editor/iOS/fixup.projmods`<br>

  2. Under `headerpaths` change `Facebook/Scripts` to `Plugins/Facebook/Scripts`</div>

5. In the menu bar of the Unity editor go to **Window > Soomla > Edit Settings** and set up "Login Permissions" you want to request from FB on login.

  <div class="info-box">**NOTE:** You should not request all the possible permissions you'll ever need in your app, 
  just request the reasonable minimum. Other permissions will be requested, when they will be needed. 
  For instance, if you try to call `UpdateStory`, SoomlaProfile will ask for `publish_actions` permission, if your app has not got it.</div>


###Google+

####**Targeting iOS**:

1. Follow [Step 1. Creating the Google Developers Console project](https://developers.google.com/+/mobile/ios/getting-started#step_1_creating_the_console_name_project) and create a Google+ app for iOS. Set the BUNDLE ID of your Google+ app to the Bundle Identifier of your Unity3d app.

2. In the menu bar of the Unity editor go to **Window > Soomla > Edit Settings**, toggle the "google" check box and fill the "Client Id" text box with "CLIENT ID" value of your Google+ app.

####**Targeting Android**:
1. Follow [Step 1: Enable the Google+ API](https://developers.google.com/+/mobile/android/getting-started#step_1_enable_the_google_api) and create a Google+ app for Android. Set the PACKAGE NAME of your Google+ app to the value of "Bundle Identifier" of your Unity3d app.

	<div class="info-box">To create a custom keystore file with Unity3d, navigate to **Player Settings->Publishing Settings** and click "Create New Keystore". In your Google+ app page, navigate to **API & Auth->Credentials** and update the value of "CERTIFICATE FINGERPRINT (SHA1)" with the SHA-1 of your new keystore file.</div>

2. Navigate to **Window > Soomla > Edit Settings** and toggle "google" check box (ignore the Client Id text box).

3. Navigate to "Publishing Settings" and browse for your keystore file (debug.keystore/custom keystore).

###Twitter

Twitter is supported out-of-the-box, authentication is done either through the signed in Twitter account (iOS 5+) or through web browser (fallback). Follow the next steps to make it work:

1. Create your Twitter app at https://apps.twitter.com/

2. In the menu bar of the Unity editor go to **Window > Soomla > Edit Settings** and toggle the "twitter" check box. Then fill in "Consumer Key" and "Consumer Secret".

	**NOTE:** "Consumer Key" and "Consumer Secret" are located under "Keys and Access Tokens" of your twitter app.
	
###Common

There is a common option, supported with all providers: `autoLogin`. Setting this option makes Soomla to try to log 
in automatically to this provider, if an user already logged in with it in the previous sessions. 

##Caveats

###Facebook Caveats

####**iOS**

1. **Facebook Application** - You must create a Facebook application and use its details in your Profile-based application (with Facebook).

2. **Facebook Permissions** - Profile will request `publish_actions` from the user of the application, to test the application please make sure you test with either Admin, Developer or Tester roles.

####**Android**

1. **Facebook Application** - You must create a Facebook application and use its details in your Profile-based application (with Facebook).

2. **Facebook Permissions** - Profile will request `publish_actions` from the user of the application, to test the application please make sure you test with either Admin, Developer or Tester roles

###Twitter Caveats

1. Have you enabled twitter in **Widow->Soomla->Edit Settings**?

2. Did you supply the correct Consumer Key and Secret?

###Google Plus Caveats

1. Have you enabled google in **Window > Soomla > Edit Settings**?

2. Have you supplied the correct Client Id (when targeting iOS)?

3. Did you sign your Unity3d app with keystore file with SHA-1 identical to "CERTIFICATE FINGERPRINT (SHA1)" of your Google+ app?


##Example

Below is a brief example of initializing Profile, logging the user into Facebook, and sharing a story on the user's Facebook wall. To see a full example, check out the [Muffin Rush Example](https://github.com/soomla/unity3d-profile/tree/master/Soomla/Assets/Examples/MuffinRush) that comes with unity3d-profile when you download it.

To learn about the different entities and functionality of Profile, see [Main Classes & Operations](/unity/profile/Profile_MainClasses).

<br>
Initialize `SoomlaProfile`.

``` cs
SoomlaProfile.Initialize();
```

<br>
Log the user into Facebook.

``` cs
SoomlaProfile.Login(
	Provider.FACEBOOK,                        // Social Provider
	new BadgeReward("loggedIn", "Logged In!") // Reward
);
```

<br>
Share a story on the user's Facebook wall.

``` cs
SoomlaProfile.UpdateStory(
	"Check out this great story by SOOMLA!",   // Message
	"SOOMLA is 2 years young!",                // Name
	"SOOMLA is GROWing",                       // Caption
	"soomla_2_years",                          // Desc
	"http://blog.soom.la",                     // Link
	"http://blog.soom.la.../soombot.png",      // Image
	new BadgeReward("sheriff", "Sheriff")      // Reward
);
```
