---
layout: "content"
image: "Bundle"
title: "GrowSoomla"
text: "The perfect solution for your game if you have already integrated any of the SOOMLA open-source modules into it. If you just want to get Analytics, Whales Report and Grow Insights then this bundle is for you."
position: 7
theme: 'platforms'
collection: 'unity_grow'
module: 'grow'
platform: 'unity'
---

# GrowSoomla - Bundle

## Overview

GrowSoomla is the perfect solution for your game if you have already integrated any of the SOOMLA open-source modules into it. If you just want to get Analytics Whales Report and Grow Insights then this bundle is for you. GrowSoomla connects you to GROW - a community-driven data network. Mobile game studios can take advantage of the different GROW products in order to get valuable insights about their games' performance and increase retention and monetization. [Read more...](/university/articles/Grow_About)

GrowSoomla includes:

- [Analytics](/university/articles/Grow_Analytics)

- [Whales Report](/university/articles/Grow_WhalesReport)

- [Insights](/unity/grow/Grow_Insights)

## Integrating GrowSoomla

### New Game & Configurations

Go to the [GROW dashboard](http://dashboard.soom.la) and sign up \ login. Upon logging in, you will be directed to the main page of the dashboard. You will need to create a new game in order to start your journey with GROW.

1. In the games screen click on the "+" button to add a new game. If it's your first time in the dashboard, just click on the "+" button underneath the "Create your first game" label in the middle of the screen.

	  ![alt text](/img/tutorial_img/unity_grow/addNewApp.png "Add new app")

	Once you created your game, you'll be redirected to the Integrations page where you can start with any of the GROW integrations. Click on **GrowSoomla**. You'll see the instructions screen, you can continue with that or stay here for the extended version.  

2. Double-click on the downloaded Unity package, it'll import all the necessary files into your Unity project.

	![alt text](/img/tutorial_img/unity_grow/importHighway.png "import")

	<div class="info-box">Starting from `SOOMLA Unity3D Highway 2.1.0`, SOOMLA changed the location of binaries in `Plugins` directory. If you're updating from a version lower than 2.1.0, please remove the following binaries manually:
        <ul>
          <li>`Assets/Plugins/iOS/libAFNetworking.a`</li>
          <li>`Assets/Plugins/iOS/libSoomlaiOSRoadster.a`</li>
          <li>`Assets/Plugins/iOS/libUnityiOSHighway.a`</li>
          <li>`Assets/Plugins/Android/UnityAndroidHighway.jar`</li>
          <li>`Assets/Plugins/Android/AndroidViper.jar`</li>          
        </ul>		    
      </div>

3. ~~Open your earliest loading scene.  Drag the `HighwayEvents` Prefab from `Assets/Soomla/Prefabs` into the scene. You should see it listed in the "Hierarchy" panel.~~

	<div class="info-box">This step is no longer required starting from: <br>
	Core    v1.2.0 <br>
	Highway v2.1.0</div>

	![alt text](/img/tutorial_img/unity_grow/prefabsStoreAndHighway.png "Prefabs")

4. In the menu bar go to **Window > Soomla > Edit Settings**:

	![alt text](/img/tutorial_img/unity_grow/soomlaSettingsStoreAndHighway.png "SOOMLA Settings")

	**Copy the "Game Key" and "Environment Key"** given to you from the [dashboard](http://dashboard.soom.la) into the fields in the settings pane of the Unity Editor. At this point, you're probably testing your integration and you want to use the **Sandbox** environment key.

	<div class="info-box">The "game" and "environment" keys allow GROW to distinguish between multiple environments of your games. The dashboard pre-generates two fixed environments for your game: **Production** and **Sandbox**. When you decide to publish your game, **make sure to switch the environment key to <u>Production</u>**.  You can always generate more environments:  For example - you can choose to have a playground environment for your game's beta testers which will be isolated from your production environment and will thus prevent analytics data from being mixed between the two.  Another best practice might be to have a separate environment for each version of your game.</div>


### Initialize modules

<div class="info-box">Make sure to initialize each module ONLY ONCE when your application loads, in the `Start()` function of a `MonoBehaviour` and **NOT** in the `Awake()` function. SOOMLA has its own `MonoBehaviour` and it needs to be "Awakened" before you initialize.</div>
<br>
<div class="info-box">The GrowHighway module is the module responsible for connecting your game to the GROW network. In order for it to operate it only needs to be initialized.</div>

1. Initialize Highway and Insights:

	``` cs
	using Grow.Highway;
	using Grow.Insights;

	// Make sure to make this call in your earliest loading scene,
	// and before initializing any other SOOMLA/GROW components
	// i.e. before SoomlaStore.Initialize(...)
	GrowHighway.Initialize();

	// Make sure to make this call AFTER initializing HIGHWAY
    GrowInsights.Initialize();
	```

2. Make sure that in your current implementation you initialize the open-source modules (Store/Profile) **AFTER** the initialization of Highway.

## Module usage & event handling

The next step is to create your game specific implementation for each of the modules. Use SOOMLA's awesome products to create better in-game economy, social interactions, game design and user experience.  
In order to be notified about (and handle) SOOMLA-related events, you will also need to create event-handling functions. Refer to the following sections for more information:

 - **Insights** - Getting in-game information about your users in real-time used to be a dream. Now it's here. Insights will tell you things about your users (as seen in other games) inside the code so you can take actions when it matters. This is the power of the GROW data network.  
[API](/unity/grow/Grow_Insights#MainClasses&Methods) | [Events](/unity/grow/Grow_Insights#Events)

## Example

Below is a short example of how to initialize SOOMLA's modules. We suggest you read about the different modules and their entities in SOOMLA's Knowledge Base:  [Insights](/unity/grow/Grow_Insights).

### Initialization

``` cs
using Soomla;
using Grow.Highway;
using Grow.Insights;

public class ExampleWindow : MonoBehaviour {

	//
	// Various event handling methods
	//
	public void onGrowInsightsInitialized () {
		Debug.Log("Grow insights has been initialized.");
	}
	public void onInsightsRefreshFinished (){
		if (GrowInsights.UserInsights.PayInsights.PayRankByGenre[Genre.Educational] > 3) {
			// ... Do stuff according to your business plan ...
		}
	}

	//
	// Initialize SOOMLA's modules
	//
	void Start () {

		// Setup all event handlers - Make sure to set the event handlers before you initialize
		HighwayEvents.OnGrowInsightsInitialized += onGrowInsightsInitialized;
		HighwayEvents.OnInsightsRefreshFinished += onInsightsRefreshFinished;

		// Make sure to make this call in your earliest loading scene,
		// and before initializing any other SOOMLA/GROW components
		// i.e. before SoomlaStore.Initialize(...)
		GrowHighway.Initialize();

		// Make sure to make this call AFTER initializing HIGHWAY
		GrowInsights.Initialize();

        // Initialize the other SOOMLA modules you're using here
	}
}
```
