---
layout: "content"
image: "Bundle"
title: "GrowSpend"
text: "The perfect virtual economy solution for your game. If you want Fraud Protection, cross-device balance SYNC and remote economy configurator then this bundle is for you."
position: 6
theme: 'platforms'
collection: 'unity_grow'
module: 'grow'
platform: 'unity'
---

# GrowSpend - Bundle

## Overview

GrowSpend is the perfect virtual economy solution for your game. If you want Fraud Protection, cross-device balance SYNC and remote economy configurator then this bundle is for you. GrowSpend connects you to GROW - a community-driven data network. Mobile game studios can take advantage of the different GROW products in order to get valuable insights about their games' performance and increase retention and monetization. [Read more...](/university/articles/Grow_About)

GrowSpend includes:

- SOOMLA's open-source module - [Store](/unity/store/Store_GettingStarted)

- [State & Economy Sync](/unity/grow/Grow_Sync)

- [IAP Fraud Protection](/unity/grow/Grow_FraudProtection)

- [Analytics](/university/articles/Grow_Analytics)

- [Whales Report](/university/articles/Grow_WhalesReport)

- [Insights](/unity/grow/Grow_Insights)

**Note:** Cross-device SYNC is using the Profile module which allows your users to login with their social provider. If you want that, [integrate Profile](/unity/profile/Profile_GettingStarted) as well.


## Integrating GrowSpend

### New Game & Configurations

Go to the [GROW dashboard](http://dashboard.soom.la) and sign up \ login. Upon logging in, you will be directed to the main page of the dashboard. You will need to create a new game in order to start your journey with GROW.

1. In the games screen click on the "+" button to add a new game. If it's your first time in the dashboard, just click on the "+" button underneath the "Create your first game" label in the middle of the screen.

	  ![alt text](/img/tutorial_img/unity_grow/addNewApp.png "Add new app")

	Once you created your game, you'll be redirected to a quick start process to download any of the GROW bundles. Click on **GrowSpend**. You'll see an instructions screen, you can continue with that or stay here for the extended version.  

2. Double-click on the downloaded Unity package, it'll import all the necessary files into your Unity project.

	![alt text](/img/tutorial_img/unity_grow/importStoreAndHighway.png "import")

	<div class="info-box">Starting from `SOOMLA Unity3D Highway 2.1.0`, SOOMLA changed the location of binaries in `Plugins` directory. If you're updating from a version lower than 2.1.0, please remove the following binaries manually:
        <ul>
          <li>`Assets/Plugins/iOS/libAFNetworking.a`</li>
          <li>`Assets/Plugins/iOS/libSoomlaiOSRoadster.a`</li>
          <li>`Assets/Plugins/iOS/libUnityiOSHighway.a`</li>
	      <li>`Assets/Plugins/iOS/libSoomlaiOSCore.a`</li>
          <li>`Assets/Plugins/iOS/libUnitySoomlaiOSCore.a`</li>
          <li>`Assets/Plugins/Android/UnityAndroidHighway.jar`</li>
          <li>`Assets/Plugins/Android/AndroidViper.jar`</li>          
          <li>`Assets/Plugins/Android/SoomlaAndroidCore.jar`</li>
          <li>`Assets/Plugins/Android/UnitySoomlaAndroidCore.jar`</li>
          <li>`Assets/Plugins/Android/square-otto-1.3.2.jar`</li>
        </ul>		    
        Also, if you're using SOOMLA Unity3D Store, remove the following binaries:
        <ul>      
          <li>`Assets/Plugins/iOS/libSoomlaiOSSStore.a`</li>      
          <li>`Assets/Plugins/iOS/libUnityiOSStore.a`</li>
          <li>`Assets/Plugins/Android/AndroidStore.jar`</li>      
          <li>`Assets/Plugins/Android/UnityAndroidStore.jar`</li>    
          <li>`Assets/Plugins/Android/AndroidStoreAmazon.jar` (if you're using Amazon as billing service)</li>
          <li>`Assets/Plugins/Android/in-app-purchasing-2.0.1.jar` (if you're using Amazon as billing service)</li>
          <li>`Assets/Plugins/Android/AndroidStoreGooglePlay.jar` (if you're using Google Play as billing service)</li>
        </ul>
      </div>


3. ~~Open your earliest loading scene.  Drag the `CoreEvents`, `StoreEvents` and `HighwayEvents` Prefabs from `Assets/Soomla/Prefabs` into the scene. You should see them listed in the "Hierarchy" panel.~~

	<div class="info-box">This step is no longer required starting from: <br>
	Core    v1.2.0 <br>
	Store   v1.9.0 <br>
	Highway v2.1.0</div>

	![alt text](/img/tutorial_img/unity_grow/prefabsStoreAndHighway.png "Prefabs")

4. In the menu bar go to **Window > Soomla > Edit Settings**:

	![alt text](/img/tutorial_img/unity_grow/soomlaSettingsStoreAndHighway.png "SOOMLA Settings")

	a. **Change the value for "Soomla Secret"**: "Soomla Secret" is an encryption secret you provide that will be used to secure your data on the device. **NOTE:** Choose this secret wisely, you can't change it after you launch your game!

	<div class="info-box">Keep the SOOMLA secret in a safe place so you remember it. Changing secrets can cause your users to lose their balances and other important data.</div>

	b. **Copy the "Game Key" and "Environment Key"** given to you from the [dashboard](http://dashboard.soom.la) into the fields in the settings pane of the Unity Editor. At this point, you're probably testing your integration and you want to use the **Sandbox** environment key.

	<div class="info-box">The "game" and "environment" keys allow GROW to distinguish between multiple environments of your games. The dashboard pre-generates two fixed environments for your game: **Production** and **Sandbox**. When you decide to publish your game, **make sure to switch the environment key to <u>Production</u>**.  You can always generate more environments:  For example - you can choose to have a playground environment for your game's beta testers which will be isolated from your production environment and will thus prevent analytics data from being mixed between the two.  Another best practice might be to have a separate environment for each version of your game.</div>

	<img src="/img/tutorial_img/unity_grow/dashboardKeys.png" alt="Game key and Env key" style="border:0;">

	c. If you're building for Android, click on the "Android Settings" option, and choose your billing provider. If you choose Google Play, you need to provide the Public Key, which is given to you from Google.

5. Fraud Protection (<u>RECOMMENDED</u>):

	Fraud Protection is using SOOMLA's validation service to validate the receipt of every purchase made in your game. By using Fraud Protection you also get **Advanced Receipt Verification** to fully protect your game from fraudsters.
	To activate Fraud Protection:

	- In the menu bar go to **Window > Soomla > Edit Settings**.

	- Check the "Receipt Validation" option under the relevant platform (Android - Google Play / iOS).

	- (Google Play only) Fill in the clientId, clientSecret and refreshToken fields by following the instructions posted [here](/android/store/Store_GooglePlayVerification).

### Initialize modules

<div class="info-box">Make sure to initialize each module ONLY ONCE when your application loads, in the `Start()` function of a `MonoBehaviour` and **NOT** in the `Awake()` function. SOOMLA has its own `MonoBehaviour` and it needs to be "Awakened" before you initialize.</div>
<br>
<div class="info-box">The GrowHighway module is the module responsible for connecting your game to the GROW network. In order for it to operate it only needs to be initialized.</div>

1. Initialize Highway and Sync:

	``` cs
	using Grow.Highway;
	using Grow.Sync;

	// Make sure to make this call in your earliest loading scene,
	// and before initializing any other SOOMLA/GROW components
	// i.e. before SoomlaStore.Initialize(...)
	GrowHighway.Initialize();

	// Make sure to make this call AFTER initializing HIGHWAY,
	// and BEFORE initializing STORE
	bool modelSync = true; 	// Remote Economy Management - Synchronizes your game's
                            // economy model between the client and server - enables
                            // you to remotely manage your economy.

	bool stateSync = true; // Synchronizes the users' balances data with the server
                           // and across his other devices.
						   // Must be TRUE in order to use LEADERBOARDS.

	// State sync and Model sync can be enabled/disabled separately.
	GrowSync.Initialize(modelSync, stateSync);
	```

2. Initialize the open-source module - Store (**AFTER** the initialization of Highway and Sync).

	* **Initialize Store:** Create your own implementation of `IStoreAssets` in order to describe your specific game's assets ([example](https://github.com/soomla/unity3d-store/blob/master/Soomla/Assets/Examples/MuffinRush/MuffinRushAssets.cs)). Initialize SoomlaStore with the class you just created:

	``` cs
	SoomlaStore.Initialize(new YourStoreAssetsImplementation());
	```

## Module usage & event handling

The next step is to create your game specific implementation for each of the modules. Use SOOMLA's awesome products to create better in-game economy and user experience.  
In order to be notified about (and handle) SOOMLA-related events, you will also need to create event-handling functions. Refer to the following sections for more information:

- **Store** - With Store you create your in-game virtual economy. It'll allow you to easily setup IAP and safely store your users' balances.  
[API](/unity/store/Store_Model) | [Main classes](/unity/store/Store_MainClasses) | [Events](/unity/store/Store_Events)

- **State & Economy Sync** - Your users want to get their balances, levels and other game state parameters when they switch devices. Now you can let them do it.  
[Events](/unity/grow/Grow_Sync#Events)

## Example

Below is a short example of how to initialize SOOMLA's modules. We suggest you read about the different modules and their entities in SOOMLA's Knowledge Base: [Store](/unity/store/Store_Model) and [State & Economy Sync](/unity/grow/Grow_Sync).

### IStoreAssets

``` cs
public class ExampleAssets : IStoreAssets {

	public int GetVersion() {
		return 0;
	}

	// NOTE: Even if you have no use in one of these functions, you still need to
	// implement them all and just return an empty array.

	public VirtualCurrency[] GetCurrencies() {
		return new VirtualCurrency[]{COIN_CURRENCY};
	}

	public VirtualGood[] GetGoods() {
		return new VirtualGood[] {SHIELD_GOOD, FIVE_SHIELD_GOOD};
	}

	public VirtualCurrencyPack[] GetCurrencyPacks() {
		return new VirtualCurrencyPack[] {TEN_COIN_PACK};
	}

	public VirtualCategory[] GetCategories() {
		return new VirtualCategory[]{GENERAL_CATEGORY};
	}

	/** Virtual Currencies **/
	public static VirtualCurrency COIN_CURRENCY = new VirtualCurrency(
		"Coin currency",                  // Name
		"Collect coins to buy items",     // Description
		"currency_coin"                   // Item ID
		);

	/** Virtual Currency Packs **/
	public static VirtualCurrencyPack TEN_COIN_PACK = new VirtualCurrencyPack(
		"10 Coins",                         // Name
		"This is a 10-coin pack",           // Description
		"coins_10",                         // Item ID
		10,                                 // Number of currencies in the pack
		COIN_CURRENCY.ID,                   // The currency associated with this pack
		new PurchaseWithMarket(             // Purchase type
	                       "[YOUR_COIN_PACK_MARKET_PRODUCT_ID]",    // Product ID
	                       0.99)                           			// Initial price
		);

	/** Virtual Goods **/

	// Shield that can be purchased for 150 coins.
	public static VirtualGood SHIELD_GOOD = new SingleUseVG(
		"Shield",                           // Name
		"Shields you from monsters",        // Description
		"shield_good",                      // Item ID
		new PurchaseWithVirtualItem(        // Purchase type
	                            COIN_CURRENCY.ID,               // Virtual item to pay with
	                            150)                            // Payment amount
		);

	// Pack of 5 shields that can be purchased for $2.99.
	public static VirtualGood FIVE_SHIELD_GOOD = new SingleUsePackVG(
		SHIELD_GOOD.ID,
		5,
		"5 Shields",                        // Name
		"This is a 5-shield pack",          // Description
		"shield_5",                         // Item ID
		new PurchaseWithMarket(             // Purchase type
	                       "[YOUR_SHIELD_MARKET_PRODUCT_ID]",   // Product ID
	                       2.99)                           		// Initial price
		);

	/** Virtual Categories **/

	public static VirtualCategory GENERAL_CATEGORY = new VirtualCategory(
		"General", new List<string>(new string[] {SHIELD_GOOD.ID})
	);
}
```

<br>
### Initialization

``` cs
using Soomla;
using Soomla.Store;
using Grow.Highway;
using Grow.Sync;
using System.Collections.Generic;

public class ExampleWindow : MonoBehaviour {

	//
	// Various event handling methods
	//
	public void onGoodBalanceChanged(VirtualGood good, int balance, int amountAdded) {
		SoomlaUtils.LogDebug("TAG", good.ID + " now has a balance of " + balance);
	}
	public void onGrowSyncInitialized() {
		Debug.Log("GROW Sync has been initialized.");
	}
	public void onModelSyncFinished(IList<string> modules) {
		Debug.Log("Model Sync has finished.");
	}
	public void onStateSyncFinished(IList<string> changedComponents,
	                                IList<string> failedComponents) {
		Debug.Log("State Sync has finished.");
	}

	//
	// Initialize SOOMLA's modules
	//
	void Start () {

		// Setup all event handlers - Make sure to set the event handlers before you initialize
		StoreEvents.OnGoodBalanceChanged += onGoodBalanceChanged;

		HighwayEvents.OnGrowSyncInitialized += onGrowSyncInitialized;
		HighwayEvents.OnModelSyncFinished += onModelSyncFinished;
		HighwayEvents.OnStateSyncFinished += onStateSyncFinished;

		// Make sure to make this call in your earliest loading scene,
		// and before initializing any other SOOMLA/GROW components
		// i.e. before SoomlaStore.Initialize(...)
		GrowHighway.Initialize();

		// Make sure to make this call AFTER initializing HIGHWAY,
		// and BEFORE initializing STORE
		bool modelSync = true; 	// Remote Economy Management - Synchronizes your game's
								// economy model between the client and server - enables
								// you to remotely manage your economy.

		bool stateSync = true; 	// Synchronizes the users' balances data with the server
								// and across his other devices.

		// State sync and Model sync can be enabled/disabled separately.
		GrowSync.Initialize(modelSync, stateSync);

		SoomlaStore.Initialize(new ExampleAssets());

	}

}
```
