---
layout: "content"
image: "Tutorial"
title: "SDKBOX"
text: "Get started with integrating GROW Analytics, Whales Report and Insights for Cocos2d-x with SDKBOX. Doesn't Include any of SOOMLA's opensource modules, only Highway and GrowInsights."
position: 1
theme: 'platforms'
collection: 'cocos2dx_grow'
module: 'grow'
lang: 'cpp'
platform: 'cocos2dx'
---

# Get GROW from SDKBOX

## Overview

GROW is SOOMLA's flagship, community-driven, data network. Mobile game studios can take advantage of the different GROW products in order to get valuable insights about their games' performance and increase retention and monetization.

Analytics, Whales Report and GrowInsights are provided for free to users of the GROW network. GrowInsights is an SDK provided with the GROW packages and Whales Report are automatically sent to the emails of users with live & active games.

**Note:** This document is for users of SDKBOX who don't want to integrate SOOMLA's opensource modules into their game.

## Getting Started

Go to the [GROW dashboard](http://dashboard.soom.la) and sign up \ login. Upon logging in, you will be directed to the main page of the dashboard. You will need to create a new game in order to start your journey with GROW.

1. In the games screen click on the "+" button to add a new game. If it's your first time in the dashboard, just click on the "+" button underneath the "Create your first game" label in the middle of the screen.

	  ![alt text](/img/tutorial_img/cocos_grow/addNewApp.png "Add new app")

	* Once you created your game, you'll be redirected to a quick start process to download any of the GROW bundles. Click on any bundle to see your game's envKey and gameKey (You can ignore other installation instructions on the dashboard as you're only using SDKBOX).

2. Install `soomla-grow` package on SDKBOX as explained [here](http://sdkbox-doc.github.io/en/plugins/soomlagrow/v3-cpp/).

3. Once you finished installation, add "Game Key" and "Env Key" from the GROW dashboard to SDKBOX's configuration json:

  <img src="/img/tutorial_img/cocos_grow/dashboardKeys.png" alt="Game key and Env key" style="border:0;">

  > You can always find the keys in the Game Settings Screens on the `environments` section.

  ```
  "ios" :
  {
    "soomlaGrow":{
                "gameKey":"0cbc07e3-0f0c-4b68-bb0c-061c1b5fb553",
                "envKey":"8b865add-4541-4db1-be18-f6c7e5e00564"
            }
  }
  "android" :
  {
    "soomlaGrow":{
                "gameKey":"0cbc07e3-0f0c-4b68-bb0c-061c1b5fb553",
                "envKey":"8b865add-4541-4db1-be18-f6c7e5e00564"
            }
  }
  ```

  > you're probably testing your integration and you want to use the **Sandbox** environment key for starters.

  <div class="info-box">The *game* and *environment* keys allow for your game to distinguish multiple environments for the same game. The dashboard pre-generates two fixed environments for your game: **Production** and **Sandbox**. When you decide to publish your game, make sure to switch the env key to **Production**.  You can always generate more environments.  For example - you can choose to have a playground environment for your game's beta testers which will be isolated from your production environment and will thus prevent analytics data from being mixed between the two.  Another best practice is to have a separate environment for each version of your game.</div>


## Back to the Dashboard

Once your app is running, you can go back to the [GROW dashboard](http://dashboard.soom.la) to verify the integration. Just refresh the page, and the environments tab should appear (be patient, this step can take a few minutes).

![alt text](/img/tutorial_img/unity_grow/verifyIntegration.png "Verify Integration")

And that's it! Whales Report, GrowInsights and GROW Analytics integrated with SDKBOX.
