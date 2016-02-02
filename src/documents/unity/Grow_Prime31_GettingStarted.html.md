---
layout: "content"
image: "Tutorial"
title: "Prime31"
text: "Get started with integrating GROW Analytics, Whales Report and Insights for Unity3D with Prime31. Doesn't Include any of SOOMLA's opensource modules, only Highway and GrowInsights."
position: 1
theme: 'platforms'
collection: 'unity_grow'
module: 'grow'
platform: 'unity'
---

# Get GROW with Prime31

## Overview

GROW is SOOMLA's flagship, community-driven, data network. Mobile game studios can take advantage of the different GROW products in order to get valuable insights about their games' performance and increase retention and monetization.

Analytics, Whales Report and GrowInsights are provided for free to users of the GROW network. GrowInsights is an SDK provided with the GROW packages and Whales Report are automatically sent to the emails of users with live & active games.

**Note:** This document is for users of Prime31 who installed the IAP plugin and don't want to integrate SOOMLA's opensource modules into their game.

## Getting Started

Go to the [GROW dashboard](http://dashboard.soom.la) and sign up \ login. Upon logging in, you will be directed to the main page of the dashboard. You will need to create a new game in order to start your journey with GROW.

> This integration works with Prime31's IAP Plugin v2.8+ on Android OR v2.6+ on iOS.

1. In the games screen click on the "+" button to add a new game. If it's your first time in the dashboard, just click on the "+" button underneath the "Create your first game" label in the middle of the screen.

	  ![alt text](/img/tutorial_img/unity_grow/addNewApp.png "Add new app")

	<div class="info-box">Once you created your game, you'll be redirected to a quick start process for integration. Click on the Prime31 box to see your game's envKey and gameKey.</div>

2. Download and Import the GROW Lite unitypackage from [here](http://library.soom.la/fetch/unity3d-soomla-grow-lite/latest?cf=kb).

3. Once you finished importing the package, add "Game Key" and "Env Key" from the GROW dashboard to your Unity3d Project:

  <img src="/img/tutorial_img/unity_grow/dashboardKeys.png" alt="Game key and Env key" style="border:0;">

  > The keys are on the Quickstart page on the dashboard but you can always find them in the Game Settings Screens on the `Environments` section.

	Add gameKey and envKey in the info.plist editor screen so it'll be applied to your **iOS** configuration:  
  ![alt text](/img/tutorial_img/unity_grow/info_plist_editor.png "Keys")

	Also, Add gameKey and envKey in AndroidManifest.xml so it'll be applied to your **Android** configuration:  
  ```
	<meta-data android:name="SoomlaGameKey" android:value="[YOUR gameKey]" />
	<meta-data android:name="SoomlaEnvKey" android:value="[YOUR envKey]" />
	```

	> **You should use the same envKey and gameKey for Android and iOS**  
	<br>
  > You're probably testing your integration and you want to use the **Sandbox** environment key for starters.

  <div class="info-box">The *game* and *environment* keys allow for your game to distinguish multiple environments for the same game. The dashboard pre-generates two fixed environments for your game: **Production** and **Sandbox**. When you decide to publish your game, make sure to switch the env key to **Production**.  You can always generate more environments.  For example - you can choose to have a playground environment for your game's beta testers which will be isolated from your production environment and will thus prevent analytics data from being mixed between the two.  Another best practice is to have a separate environment for each version of your game.</div>


## Back to the Dashboard

Once your app is running, you can go back to the [GROW dashboard](http://dashboard.soom.la) to verify the integration. Just refresh the page, and the environments tab should appear (be patient, this step can take a few minutes).

![alt text](/img/tutorial_img/unity_grow/verifyIntegration.png "Verify Integration")

And that's it! Whales Report, GrowInsights and GROW Analytics integrated with Prime31.
