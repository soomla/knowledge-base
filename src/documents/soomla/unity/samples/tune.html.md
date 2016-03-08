---
layout: "sample"
image: "tune_logo"
title: "TUNE"
text: "Measure SOOMLA in-game events to see which ad networks and publishers send the most engaged users"
position: 12
relates: ["giftgaming", "supersonic", "unity_ads"]
collection: 'samples'
navicon: "nav-icon-tune.png"
backlink: "http://www.tune.com/"
theme: 'samples'
---

# TUNE Integration

<div class="samples-title">This article shows multiple integration samples:</div>

* Measure SOOMLA Store purchase events to see which ad networks and publishers are sending you valuable users making in-app purchases

* Measure SOOMLA Profile social network logins to see which ad networks and publishers are sending you social users, and which social network they login to.

* Measure SOOMLA LevelUp events to see which ad networks and publishers are sending you engaged users that make more in-game progress.

* Measure SOOMLA Insights events to see which ad networks and publishers are sending you paying users.

<br>


The biggest hurdle for marketing a mobile app is integrating SDKs for every ad network and publisher you want to work with. With <a href="http://www.tune.com" target="_blank">TUNE</a>, you never have to integrate another advertising SDK. Not only have we integrated with mobile ad networks and publishers, but you can easily pass conversion information to any third party partner you choose.

The <a href="https://developers.mobileapptracking.com/mobile-sdks/" target="_blank">TUNE SDK</a> provides application session and event logging functionality. To begin measuring sessions and installs, initiate the `measureSession` method. You can then rely on TUNE to log in-app events (such as purchases, game levels, and any other user engagement).

This document will show you how to measure events from all SOOMLA modules - Store, Profile, LevelUp, and Insights - so you can identify which ad networks and publishers send you the most valuable users.

<div>
    <div role="tabpanel" class="tab-pane active" id="sample-unity-store">
      <pre>
```
using UnityEngine;
using System.Collections;
using System.Collections.Generic;
using MATSDK;
using Soomla.Store;

public class TuneSoomlaStoreScript : MonoBehaviour {
    void Start () {
        // Initialize TUNE SDK
        MATBinding.Init("tune_advertiser_id", "tune_conversion_key");
        // Measure initial app open
        MATBinding.MeasureSession();

        // Listen for Soomla OnMarketPurchase event
        StoreEvents.OnMarketPurchase += onMarketPurchase;

        // Initialize Soomla Store with your Store Assets
        SoomlaStore.Initialize(new YourStoreAssetsImplementation());
    }

    public void onMarketPurchase(PurchasableVirtualItem pvi, string payload,
            Dictionary<string, string> extras) {

        // On purchase complete, set purchase info and measure purchase in TUNE
        double revenue = 0;
        string currency = "";
        MATItem[] items = new MATItem[] {};
        PurchaseType type = pvi.PurchaseType;
        if (type is PurchaseWithMarket) {
            MarketItem item = ((PurchaseWithMarket)type).MarketItem;
            revenue = item.MarketPriceMicros / 1000000;
            currency = item.MarketCurrencyCode;

            // Create event item to store purchase item data
            MATItem matItem = new MATItem();
            matItem.name = item.MarketTitle;
            matItem.attribute1 = item.ProductId;

            // Add event item to MATItem array in order to pass to TUNE SDK
            items[items.Length] = matItem;
        }

        // Get order ID and receipt data for purchase validation
        string receipt = "";
        string receiptSignature = "";
        string orderId = "";
#if UNITY_ANDROID
        extras.TryGetValue("originalJson", out receipt);
        extras.TryGetValue("signature", out receiptSignature);
        extras.TryGetValue("orderId", out orderId);
#elif UNITY_IOS
        extras.TryGetValue("receiptBase64", out receipt);
        extras.TryGetValue("transactionIdentifier", out orderId);
#endif

        // Create a MATEvent with this purchase data
        MATEvent purchaseEvent = new MATEvent("purchase");
        purchaseEvent.revenue = revenue;
        purchaseEvent.currencyCode = currency;
        purchaseEvent.advertiserRefId = orderId;
        purchaseEvent.receipt = receipt;
        purchaseEvent.receiptSignature = receiptSignature;

        // Set event item if it exists
        if (items.Length != 0) {
            purchaseEvent.eventItems = items;
        }
        // Measure "purchase" event
        MATBinding.MeasureEvent(purchaseEvent);
    }
}
```
      </pre>
    </div>
    <div role="tabpanel" class="tab-pane" id="sample-unity-profile">
      <pre>
```
using UnityEngine;
using System.Collections;
using MATSDK;
using Soomla.Profile;

public class TuneSoomlaProfileScript : MonoBehaviour {
    void Start () {
        // Initialize TUNE SDK
        MATBinding.Init("tune_advertiser_id", "tune_conversion_key");
        // Measure initial app open
        MATBinding.MeasureSession();

        // Listen for Soomla OnLoginFinished event
        ProfileEvents.OnLoginFinished += onLoginFinished;

        // Initialize Soomla Profile
        SoomlaProfile.Initialize();
    }

    void OnApplicationPause(bool pauseStatus) {
        if (!pauseStatus) {
            // Measure app resumes from background
            MATBinding.MeasureSession();
        }
    }

    // Set user ID and measure login event upon login finished
    public void onLoginFinished(UserProfile userProfileJson, string payload) {
        Provider provider = userProfileJson.Provider;
        string userId = userProfileJson.ProfileId;

        // Set different user IDs in TUNE SDK based on provider
        if (provider == Provider.FACEBOOK) {
            MATBinding.SetFacebookUserId(userId);
        } else if (provider == Provider.GOOGLE) {
            MATBinding.SetGoogleUserId(userId);
        } else if (provider == Provider.TWITTER) {
            MATBinding.SetTwitterUserId(userId);
        } else {
            MATBinding.SetUserId(userId);
        }
        // Measure a login event for this user ID
        MATBinding.MeasureEvent("login");
    }
}
```
      </pre>
    </div>
    <div role="tabpanel" class="tab-pane" id="sample-unity-levelup">
      <pre>
```
using UnityEngine;
using System.Collections;
using MATSDK;
using Soomla.Levelup;

public class TuneSoomlaLevelUpScript : MonoBehaviour {
    void Start () {
        // Initialize TUNE SDK
        MATBinding.Init("tune_advertiser_id", "tune_conversion_key");
        // Measure initial app open
        MATBinding.MeasureSession();

        // Listen for Soomla OnLevelStarted event
        LevelUpEvents.OnLevelStarted += onLevelStarted;

        // Create an example World with Level
        World world = new World("exampleWorld");
        Level lvl1 = new Level("Level 1");
        world.AddInnerWorld(lvl1);

        // Initialize Soomla LevelUp with the example world containing a level
        SoomlaLevelUp.Initialize(world);

        // Start the level, will trigger OnLevelStarted event
        lvl1.Start();
    }

    void OnApplicationPause(bool pauseStatus) {
        if (!pauseStatus) {
            // Measure app resumes from background
            MATBinding.MeasureSession();
        }
    }

    // Set level ID and measure a level achieved event upon level start
    public void onLevelStarted(Level level) {

        // Create a MATEvent for level_achieved with the level ID
        MATEvent levelEvent = new MATEvent("level_achieved");
        levelEvent.contentId = level.ID;

        // Measure "level_achieved" event for this level ID
        MATBinding.MeasureEvent (levelEvent);
    }
}
```
      </pre>
    </div>
    <div role="tabpanel" class="tab-pane" id="sample-unity-insights">
      <pre>
```
using UnityEngine;
using System.Collections;
using MATSDK;
using Soomla.Highway;
using Soomla.Insights;

public class TuneSoomlaInsightsScript : MonoBehaviour {
    void Start () {
        // Initialize TUNE SDK
        MATBinding.Init("tune_advertiser_id", "tune_conversion_key");
        // Measure initial app open
        MATBinding.MeasureSession();

        // Add event listeners - Make sure to set the event handlers before you initialize
        HighwayEvents.OnGrowInsightsInitialized += OnSoomlaInsightsInitialized;
        HighwayEvents.OnInsightsRefreshFinished += OnSoomlaInsightsRefreshFinished;

        // Initialize GrowHighway
        GrowHighway.Initialize();

        // Initialize GrowInsights
        GrowInsights.Initialize();
    }

    void OnApplicationPause(bool pauseStatus) {
        if (!pauseStatus) {
            // Measure app resumes from background
            MATBinding.MeasureSession();
        }
    }

    void OnSoomlaInsightsInitialized (GrowInsightsInitializedEvent evnt) {
        Debug.Log("Soomla insights has been initialized.");
    }

    void OnSoomlaInsightsRefreshFinished (InsightsRefreshFinishedEvent evnt){
        if (GrowInsights.UserInsights.PayInsights.PayRankByGenre[Genre.Educational] > 0) {
            // Set in TUNE SDK that user is a paying user for a given genre
            // which will be sent with future events
            MATBinding.SetPayingUser(true);
        }
    }
}
```
      </pre>
    </div>

</div>

<div class="samples-title">Getting started</div>


1. <a href="https://platform.mobileapptracking.com/#!/advertiser" target="_blank">Sign up</a> with TUNE to get started on your home for attribution and analytics.

2. Download and integrate the <a href="https://developers.mobileapptracking.com/mobile-sdks/" target="_blank">TUNE SDK</a>.

3. Integrate SOOMLA Store.  Follow all steps in the platform specific getting started guides: <br>
    <a href="/unity/" target="_blank">Unity</a> |
    <a href="/ios/" target="_blank">iOS</a> |
    <a href="/android/" target="_blank">Android</a>

4. To use Grow Insights, follow the <a href="/unity/Grow_Insights/" target="_blank">Grow Insights Unity Instructions</a>.


<div class="samples-title">Sample projects</div>

For sample projects of TUNE + SOOMLA integration, please see in GitHub:

<a href="https://github.com/john-gu/TuneSoomlaUnity" target="_blank">Unity</a> |
<a href="https://github.com/john-gu/TuneSoomlaIOS" target="_blank">iOS</a> |
<a href="https://github.com/john-gu/TuneSoomlaAndroid" target="_blank">Android</a>


<div class="samples-title">Support</div>

* For more information on SDK implementation and events to measure, please visit our <a href="https://developers.mobileapptracking.com/mobile-sdks/" target="_blank">documentation</a>.

* If you have further questions, please feel free to contact us at [support@mobileapptracking.com](mailto:support@mobileapptracking.com).
