---
layout: "content"
image: "Tutorial"
title: "UnityIAP"
text: "Add GROW to your existing project with UnityIAP integration"
position: 3
theme: 'platforms'
collection: 'unity_grow'
module: 'grow'
platform: 'unity'
---

# Integrating Grow with UnityIAP

## Overview

GROW is SOOMLA's flagship, community driven, data network. Mobile game studios can take advantage of the different GROW products in order to get valuable insights about their games' performance and increase retention and monetization. [Read more...](/university/articles/Grow_About)

GROW Includes:

- [Analytics](/university/articles/Grow_Analytics)

- [Whales Report](/university/articles/Grow_WhalesReport)

- [Insights](/unity/grow/Grow_Insights)

## Integrating Grow with UnityIAP

### New Game & Configurations

Go to the [GROW dashboard](http://dashboard.soom.la) and sign up \ login. Upon logging in, you will be directed to the main page of the dashboard. You will need to create a new game in order to start your journey with GROW.

1. In the games screen click on the "+" button to add a new game. If it's your first time in the dashboard, just click on the "+" button underneath the "Create your first game" label in the middle of the screen.

	  ![alt text](/img/tutorial_img/unity_grow/addNewApp.png "Add new app")

 Once you created your game, you'll be redirected to the Integrations page where you can start with any of the GROW integrations. Click on **UnityIAP**. You'll see the instructions screen, you can continue with that or stay here for the extended version.  

2. Download and import the unity package into your Unity project.

	![alt text](/img/tutorial_img/unity_grow/importHighway.png "import")

3. In the menu bar go to **Window > GROW Settings**:

	![alt text](/img/tutorial_img/unity_grow/soomlaSettingsStoreAndHighway.png "SOOMLA Settings")

	**Copy the "Game Key" and "Environment Key"** given to you from the [dashboard](http://dashboard.soom.la) into the fields in the settings pane of the Unity Editor. At this point, you're probably testing your integration and you want to use the **Sandbox** environment key.

	<div class="info-box">The "game" and "environment" keys allow GROW to distinguish between multiple environments of your games. The dashboard pre-generates two fixed environments for your game: **Production** and **Sandbox**. When you decide to publish your game, **make sure to switch the environment key to <u>Production</u>**.  You can always generate more environments:  For example - you can choose to have a playground environment for your game's beta testers which will be isolated from your production environment and will thus prevent analytics data from being mixed between the two.  Another best practice might be to have a separate environment for each version of your game.</div>

	b. Click on the **Add Prefab** button and the prefab will be added to the **current scene**.
	<div class="info-box"> Make sure that you are in your Main Scene when adding the Prefab </div>


<br/>
Now you can build and run your app in order to verify it's Active in the [GROW Dashboard](http://dashboard.soom.la/) UnityIAP integration page, just refresh the page:

![alt text](/img/tutorial_img/unity_grow/ActiveIntegration_UnityIAP.png "UnityIAP Integration")

That's it, you now have GROW integrated with UnityIAP and you can start working with GROW Analytics!
