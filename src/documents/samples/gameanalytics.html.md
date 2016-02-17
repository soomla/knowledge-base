---
layout: "sample"
image: "gameanalytics_logo"
title: "GameAnalytics"
text: "Report SOOMLA Store & LevelUp in-game events to GameAnalytics"
position: 6
relates: ["heroiclabs", "onesignal"]
collection: 'samples'
navicon: "nav-icon-gameanalytics.png"
backlink: "http://www.gameanalytics.com/"
theme: 'samples'
---

# GameAnalytics Integration

<br>

<div>

  <!-- Nav tabs -->
  <ul class="nav nav-tabs nav-tabs-use-case-code sample-tabs" role="tablist">
    <li role="presentation" class="active"><a href="#sample-unity" aria-controls="unity" role="tab" data-toggle="tab">Unity</a></li>
    <li role="presentation"><a href="#sample-ios" aria-controls="ios" role="tab" data-toggle="tab">iOS</a></li>
    <li role="presentation"><a href="#sample-android" aria-controls="android" role="tab" data-toggle="tab">Android</a></li>
  </ul>

  <!-- Tab panes -->
  <div class="tab-content tab-content-use-case-code">
    <div role="tabpanel" class="tab-pane active" id="sample-unity">
    <pre>
    ```
using UnityEngine;
using Soomla.Store;
using Soomla.Levelup;
using System.Collections.Generic;
using System.Linq;

public enum EPayRank
{
    PayRank0 = 0,
    PayRank1 = 1,
    PayRank2 = 2,
    PayRank3 = 3,
    PayRank4 = 4,
    PayRank5 = 5,
    PayRank6 = 6
}

public class GA_Soomla : MonoBehaviour
{
    private void Start() {

        // Register SOOMLA Store event handlers
        StoreEvents.OnCurrencyBalanceChanged += GA_Soomla.OnCurrencyBalanceChanged;
        StoreEvents.OnMarketPurchase += GA_Soomla.OnMarketPurchase;
        StoreEvents.OnItemPurchased += GA_Soomla.OnItemPurchased;
        StoreEvents.OnGoodUpgrade += GA_Soomla.OnGoodUpgrade;

        // Register SOOMLA LevelUp event handlers
        LevelUpEvents.OnWorldCompleted += GA_Soomla.OnWorldCompleted;
        LevelUpEvents.OnLevelStarted += GA_Soomla.OnLevelStarted;
        LevelUpEvents.OnLevelEnded += GA_Soomla.OnLevelEnded;
        LevelUpEvents.OnMissionCompleted += GA_Soomla.OnMissionCompleted;
        LevelUpEvents.OnGateOpened += GA_Soomla.OnGateOpened;

        // Register SOOMLA Insights event handlers
        HighwayEvents.OnInsightsRefreshFinished += OnSoomlaInsightsRefreshFinished;

        // Initialize SOOMLA Highway & Insights
        // Assumes you've setup GROW
        GrowHighway.Initialize();
        GrowInsights.Initialize();

        // Initialize SOOMLA Store & LevelUp
        // Assumes you've implemented your store assets
        // and an initial world with levels and missions
        SoomlaStore.Initialize (new YourStoreAssetsImplementation ());
        SoomlaLevelUp.Initialize (WORLD);
    }

    #region StoreEvents

    private static void OnCurrencyBalanceChanged(VirtualCurrency virtualCurrency, int balance, int amountAdded) {
        GameAnalytics.NewResourceEvent(
            amountAdded > 0 ? GA_Resource.GAResourceFlowType.GAResourceFlowTypeSource :
                              GA_Resource.GAResourceFlowType.GAResourceFlowTypeSink,
            virtualCurrency.ItemId,
            Mathf.Abs(amountAdded),
            "virtual_currency",
            virtualCurrency.ItemId);
    }

    private static void OnMarketPurchase(PurchasableVirtualItem pvi, string payload, Dictionary<string, string> extra) {
        PurchaseWithMarket purchaseWithMarket = pvi.PurchaseType as PurchaseWithMarket;

        if (purchaseWithMarket != null) {
            MarketItem marketItem = purchaseWithMarket.MarketItem;
            if (Application.platform == RuntimePlatform.IPhonePlayer) {
                GameAnalytics.NewBusinessEvent(
                    marketItem.MarketCurrencyCode,
                    (int)(marketItem.Price * 100.0),
                    pvi.ItemId, pvi.ItemId,
                    "cart",
                    "",
                    true);
            } else if (Application.platform == RuntimePlatform.Android) {
                string receipt;
                string signature;

                if (extra.TryGetValue("originalJson", out receipt) && extra.TryGetValue("signature", out signature)) {
                    GameAnalytics.NewBusinessEvent(
                        marketItem.MarketCurrencyCode,
                        (int)(marketItem.Price * 100.0),
                        pvi.ItemId,
                        pvi.ItemId,
                        "cart",
                        receipt,
                        true);
                }
            }
        }
    }

    private static void OnItemPurchased(PurchasableVirtualItem pvi, string payload) {
        GameAnalytics.NewDesignEvent("Purchased:" + pvi.ItemId);
    }

    private static void OnGoodUpgrade(VirtualGood good, UpgradeVG currentUpgrade) {
        GameAnalytics.NewDesignEvent("Upgrade:" + good.ItemId + ":" + currentUpgrade.ItemId);
    }

    #endregion // StoreEvents

    #region LevelUpEvents

    private static void OnWorldCompleted(World world) {
        GameAnalytics.NewProgressionEvent(
            GA_Progression.GAProgressionStatus.GAProgressionStatusComplete,
            world.ID);
    }

    private static void OnLevelStarted(Level level) {
        World parentWorld = level.ParentWorld;

        if (parentWorld != null) {
            GameAnalytics.NewProgressionEvent(
                GA_Progression.GAProgressionStatus.GAProgressionStatusStart,
                parentWorld.ID,
                level.ID);
        } else {
            GameAnalytics.NewProgressionEvent(
                GA_Progression.GAProgressionStatus.GAProgressionStatusStart,
                level.ID);
        }
    }

    private static void OnLevelEnded(Level level) {
        World parentWorld = level.ParentWorld;

        if (parentWorld != null) {
            GameAnalytics.NewProgressionEvent(
                GA_Progression.GAProgressionStatus.GAProgressionStatusComplete,
                parentWorld.ID,
                level.ID);
        } else {
            GameAnalytics.NewProgressionEvent(
                GA_Progression.GAProgressionStatus.GAProgressionStatusComplete,
                level.ID);
        }
    }

    private static void OnMissionCompleted(Mission mission) {
        World containingWorld = GetWorldContainingMission(mission.ID);
        World parentWorld = containingWorld.ParentWorld;

        if (parentWorld != null) {
            GameAnalytics.NewProgressionEvent(
                GA_Progression.GAProgressionStatus.GAProgressionStatusComplete,
                parentWorld.ID,
                containingWorld.ID,
                mission.ID);
        } else {
            GameAnalytics.NewProgressionEvent(
                GA_Progression.GAProgressionStatus.GAProgressionStatusComplete,
                containingWorld.ID,
                mission.ID);
        }
    }

    private static void OnGateOpened(Gate gate) {
        GameAnalytics.NewDesignEvent("Opened:" + gate.ID);
    }

    #endregion // LevelUpEvents

    #region InsightsEvents

    private static void OnSoomlaInsightsRefreshFinished()
    {
        int payRank = GrowInsights.UserInsights.PayInsights.PayRankByGenre[Genre.<YOUR_GENRE>];

        if (payRank >= 0)
        {
            // Assumes you've setup the following custom dimensions in the GameAnalytics settings in Unity (for CustomDimension01 in this example):
            // CustomDimension01 = { "PayRank0", "PayRank1", "PayRank2", "PayRank3", "PayRank4", "PayRank5", "PayRank6" }
            // Look here for more information: https://github.com/GameAnalytics/GA-SDK-UNITY/wiki/Settings#custom-dimensions

            GameAnalytics.SetCustomDimension01(((EPayRank)payRank).ToString());
            GameAnalytics.NewDesignEvent("PayRank:" + payRank, (float)payRank);
        }
    }

    #endregion // LevelUpEvents

    //
    // Private helper methods
    //

    private static World GetWorldContainingMission(string missionId) {
        Mission mission = (from m in SoomlaLevelUp.InitialWorld.Missions
                           where m.ID == missionId
                           select m).SingleOrDefault();

        if (mission == null) {
            return fetchWorldContainingMission(missionId, SoomlaLevelUp.InitialWorld.InnerWorldsList);
        }
        return SoomlaLevelUp.InitialWorld;
    }

    private static World fetchWorldContainingMission(string missionId, IEnumerable<World> worlds) {
        foreach(World world in worlds) {
            Mission mission = fetchMission(missionId, world.Missions);
            if (mission != null) {
                return world;
            }
            World w = fetchWorldContainingMission(missionId, world.InnerWorldsList);
            if (w != null) {
                return w;
            }
        }

        return null;
    }

    private static Mission fetchMission(string missionId, IEnumerable<Mission> missions) {
        Mission retMission = null;
        foreach(var mission in missions) {
            retMission = fetchMission(missionId, mission);
            if (retMission != null) {
                return retMission;
            }
        }

        return retMission;
    }

    private static Mission fetchMission(string missionId, Mission targetMission) {
        if (targetMission == null) {
            return null;
        }

        if ((targetMission != null) && (targetMission.ID == missionId)) {
            return targetMission;
        }

        Mission result = null;
        Challenge challenge = targetMission as Challenge;

        if (challenge != null) {
            return fetchMission(missionId, challenge.Missions);
        }

        return result;
    }
}
```
    </pre>

  </div>
    <div role="tabpanel" class="tab-pane" id="sample-ios">
      <pre>
```
//
//  AppDelegate.m
//  TestApp
//
//  Copyright (c) 2014 GameAnalytics. All rights reserved.
//

#import "AppDelegate.h"
#import "GameAnalytics.h"
#import "TestAppIAP.h"
#import "GameAnalytics.h"
#import "Soomla.h"
#import "StoreEventHandling.h"
#import "PurchaseWithMarket.h"
#import "PurchasableVirtualItem.h"
#import "MarketItem.h"

@interface AppDelegate ()

@end

@implementation AppDelegate


- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
    [TestAppIAP sharedInstance];

    // Override point for customization after application launch.

    // Configure engine version
    [GameAnalytics configureEngineVersion:@"ios 1.0.0"];

    // Enable log
    [GameAnalytics setEnabledInfoLog:YES];
    [GameAnalytics setEnabledVerboseLog:YES];

    // Configure available virtual currencies and item types
    [GameAnalytics configureAvailableResourceCurrencies:@[@"gems", @"gold"]];
    [GameAnalytics configureAvailableResourceItemTypes:@[@"boost", @"lives"]];

    // Configure available custom dimensions
    [GameAnalytics configureAvailableCustomDimensions01:@[@"ninja", @"samurai"]];
    [GameAnalytics configureAvailableCustomDimensions02:@[@"whale", @"dolphin"]];
    [GameAnalytics configureAvailableCustomDimensions03:@[@"horde", @"alliance"]];

    // Configure build version
    [GameAnalytics configureBuild:@"0.1.0"];

    // Initialize
    [GameAnalytics initializeWithGameKey:@"<game_key>" gameSecret:@"<secret_key>"];

    // Handle SOOMLA events
    [[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(currencyBalanceChanged:) name:EVENT_CURRENCY_BALANCE_CHANGED object:nil];
    [[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(marketPurchased:) name:EVENT_MARKET_PURCHASED object:nil];
    [[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(itemPurchased:) name:EVENT_ITEM_PURCHASED object:nil];
    [[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(goodUpgrade:) name:EVENT_GOOD_UPGRADE object:nil];

    // Initialize SOOMLA Store
    // Assumes you've implemented your store assets.
    [Soomla initializeWithSecret:@"<SECRET_GAME_KEY>"];
    [[SoomlaStore getInstance] initializeWithStoreAssets:[[YourStoreAssetsImplementation alloc] init]];
    return YES;
}

- (void)currencyBalanceChanged:(NSNotification*)notification
{
    NSDictionary* userInfo = [notification userInfo];
    NSNumber* amountAdded = [userInfo objectForKey:DICT_ELEMENT_AMOUNT_ADDED];
    NSString* itemId = [userInfo objectForKey:DICT_ELEMENT_CURRENCY];

    GAResourceFlowType flowType = GAResourceFlowTypeSource;
    if ([amountAdded intValue] <= 0)
    {
        flowType = GAResourceFlowTypeSink;
    }

    [GameAnalytics addResourceEventWithFlowType:flowType currency:@"<virtual_currency_type_goes_here (e.g. Gems, BeamBoosters, Coins)>" amount:amountAdded itemType:@"<item_type_goes_here (e.g. Weapons, IAP, Gameplay, Boosters)>" itemId:itemId];
}

- (void)marketPurchased:(NSNotification*)notification
{
    NSDictionary* userInfo = [notification userInfo];
    PurchasableVirtualItem* purchasableVirtualItem = [userInfo objectForKey:DICT_ELEMENT_PURCHASABLE];
    PurchaseWithMarket* purchaseWithMarket = (PurchaseWithMarket*)[purchasableVirtualItem purchaseType];
    MarketItem* marketItem = [purchaseWithMarket marketItem];
    double price = [marketItem price];
    NSInteger amount = (NSInteger)(price * 100.0);

    [GameAnalytics addBusinessEventWithCurrency:[marketItem marketCurrencyCode] amount:amount itemType:@"<item_type_goes_here (e.g. GoldPacks)>" itemId:[purchasableVirtualItem itemId] cartType:@"<cart_type_goes_here (e.g. The game location of the purchase)>" autoFetchReceipt: YES];
}

- (void)itemPurchased:(NSNotification*)notification
{
    NSDictionary* userInfo = [notification userInfo];
    NSString* itemId = [userInfo objectForKey:DICT_ELEMENT_PURCHASABLE_ID];

    [GameAnalytics addDesignEventWithEventId:[NSString stringWithFormat:@"%@%@", @"Purchased:", itemId]];
}

- (void)goodUpgrade:(NSNotification*)notification
{
    NSDictionary* userInfo = [notification userInfo];
    NSString* itemId = [userInfo objectForKey:DICT_ELEMENT_GOOD];
    NSString* upgradeId = [userInfo objectForKey:DICT_ELEMENT_UpgradeVG];

    [GameAnalytics addDesignEventWithEventId:[NSString stringWithFormat:@"%@%@%@%@", @"Upgrade:", itemId, @":", upgradeId]];
}

- (void)applicationWillResignActive:(UIApplication *)application {
    // Sent when the application is about to move from active to inactive state. This can occur for certain types of temporary interruptions (such as an incoming phone call or SMS message) or when the user quits the application and it begins the transition to the background state.
    // Use this method to pause ongoing tasks, disable timers, and throttle down OpenGL ES frame rates. Games should use this method to pause the game.
}

- (void)applicationDidEnterBackground:(UIApplication *)application {
    // Use this method to release shared resources, save user data, invalidate timers, and store enough application state information to restore your application to its current state in case it is terminated later.
    // If your application supports background execution, this method is called instead of applicationWillTerminate: when the user quits.
}

- (void)applicationWillEnterForeground:(UIApplication *)application {
    // Called as part of the transition from the background to the inactive state; here you can undo many of the changes made on entering the background.
}

- (void)applicationDidBecomeActive:(UIApplication *)application {
    // Restart any tasks that were paused (or not yet started) while the application was inactive. If the application was previously in the background, optionally refresh the user interface.
}

- (void)applicationWillTerminate:(UIApplication *)application {
    // Called when the application is about to terminate. Save data if appropriate. See also applicationDidEnterBackground:.
}

@end
```
      </pre>

    </div>
    <div role="tabpanel" class="tab-pane" id="sample-android">
      <pre>
```
package com.gameanalytics.dummy;

import android.support.v7.app.ActionBarActivity;
import android.os.Bundle;
import android.view.Menu;
import android.view.MenuItem;

import com.gameanalytics.sdk.GAErrorSeverity;
import com.gameanalytics.sdk.GAPlatform;
import com.gameanalytics.sdk.GAProgressionStatus;
import com.gameanalytics.sdk.GAResourceFlowType;
import com.gameanalytics.sdk.GameAnalytics;
import com.gameanalytics.sdk.StringVector;
import com.soomla.BusProvider;
import com.soomla.Soomla;
import com.soomla.store.domain.MarketItem;
import com.soomla.store.domain.PurchasableVirtualItem;
import com.soomla.store.events.CurrencyBalanceChangedEvent;
import com.soomla.store.events.GoodUpgradeEvent;
import com.soomla.store.events.ItemPurchasedEvent;
import com.soomla.store.events.MarketPurchaseEvent;
import com.soomla.store.purchaseTypes.PurchaseWithMarket;
import com.squareup.otto.Subscribe;

import java.util.HashMap;


public class MainActivity extends ActionBarActivity
{

	@Override
	protected void onCreate(Bundle savedInstanceState)
	{
		super.onCreate(savedInstanceState);
		setContentView(R.layout.activity_main);
		initializeGameAnalytics();

        // Register this instance to receive SOOMLA events.
        BusProvider.getInstance().register(this);

        // Initialize SOOMLA Store
        // Assumes you've implemented your store assets.
		Soomla.initialize("<GAME SECRET GOES HERE>");
        SoomlaStore.getInstance().initialize(new YourStoreAssetsImplementation());
	}

	@Subscribe
	public void onCurrencyBalanceChanged(CurrencyBalanceChangedEvent currencyBalanceChangedEvent)
	{
		int amountAdded = currencyBalanceChangedEvent.getAmountAdded();

		GameAnalytics.addResourceEventWithFlowType(
		    amountAdded > 0 ?
		        GAResourceFlowType.GAResourceFlowTypeSource :
		        GAResourceFlowType.GAResourceFlowTypeSink,
            "<virtual_currency_type_goes_here (e.g. Gems, BeamBoosters, Coins)>",
            Math.abs(amountAdded),
            "<item_type_goes_here (e.g. Weapons, IAP, Gameplay, Boosters)>",
            currencyBalanceChangedEvent.getCurrencyItemId());
	}

	@Subscribe
	public void onMarketPurchase(MarketPurchaseEvent marketPurchaseEvent)
	{
		PurchasableVirtualItem purchasableVirtualItem = marketPurchaseEvent.PurchasableVirtualItem;
		PurchaseWithMarket purchaseWithMarket =
		    PurchaseWithMarket.class.isInstance(purchasableVirtualItem.getPurchaseType()) ?
		    (PurchaseWithMarket)purchasableVirtualItem.getPurchaseType() : null;

		if(purchaseWithMarket != null)
		{
			MarketItem marketItem = purchaseWithMarket.getMarketItem();
			HashMap<String, String> extraInfo = marketPurchaseEvent.ExtraInfo;

			if(extraInfo.containsKey("originalJson") && extraInfo.containsKey("signature"))
			{
				String receipt = extraInfo.get("originalJson");
				String signature = extraInfo.get("signature");

				GameAnalytics.addBusinessEventWithCurrency(
				    marketItem.getMarketCurrencyCode(),
				    (int)(marketItem.getPrice() * 100.0),
				    purchasableVirtualItem.getItemId(),
				    "<cart_type_goes_here (e.g. The game location of the purchase)>",
                    receipt, "google_play", signature);
			}
		}
	}

	@Subscribe
	public void onItemPurchased(ItemPurchasedEvent itemPurchasedEvent)
	{
		GameAnalytics.addDesignEventWithEventId("Purchased:" + itemPurchasedEvent.getItemId());
	}

	@Subscribe
	public void onGoodUpgrade(GoodUpgradeEvent goodUpgradeEvent)
	{
		GameAnalytics.addDesignEventWithEventId("Upgrade:" + goodUpgradeEvent.getGoodItemId() + ":" + goodUpgradeEvent.getCurrentUpgrade());
	}

	private void initializeGameAnalytics()
	{
		GAPlatform.initializeWithContext(this.getApplicationContext());

		// Configure engine version
		GameAnalytics.configureEngineVersion("android 1.0.0");

		// Enable log
		GameAnalytics.setEnabledInfoLog(true);
		GameAnalytics.setEnabledVerboseLog(true);

		// Configure available virtual currencies and item types
		StringVector currencies = new StringVector();
		currencies.add("gems");
		currencies.add("gold");
		GameAnalytics.configureAvailableResourceCurrencies(currencies);

		StringVector itemTypes = new StringVector();
		itemTypes.add("boost");
		itemTypes.add("lives");
		GameAnalytics.configureAvailableResourceItemTypes(itemTypes);

		// Configure available custom dimensions
		StringVector customDimension01 = new StringVector();
		customDimension01.add("ninja");
		customDimension01.add("samurai");
		GameAnalytics.configureAvailableCustomDimensions01(customDimension01);

		StringVector customDimension02 = new StringVector();
		customDimension02.add("whale");
		customDimension02.add("dolphin");
		GameAnalytics.configureAvailableCustomDimensions02(customDimension02);

		StringVector customDimension03 = new StringVector();
		customDimension03.add("horde");
		customDimension03.add("alliance");
		GameAnalytics.configureAvailableCustomDimensions03(customDimension03);

		// Configure build version
		GameAnalytics.configureBuild("0.1.0");

		// Initialize
		GameAnalytics.initializeWithGameKey("<game_key>", "<secret_key>");
	}
}
```
      </pre>
    </div>
  </div>
</div>


<div class="samples-title">Getting started</div>

1. Sign up to <a href="http://www.gameanalytics.com/signup" target="_blank">GameAnalytics</a> for free.

2. Create your account, studio and game.

3. Follow instructions on how to integrate your platform: <br>
    <a href="https://guides.gameanalytics.com/content/sdk?page=unity" target="_blank">Unity</a> |
    <a href="https://guides.gameanalytics.com/content/sdk?page=ios" target="_blank">iOS</a> |
    <a href="https://guides.gameanalytics.com/content/sdk?page=android" target="_blank">Android</a>

4. Integrate SOOMLA Store. Follow all steps in the platform specific getting started guides: <a href="/soomla/unity/store/Store_GettingStarted/" target="_blank">Unity Store</a> | <a href="/soomla/ios/store/Store_GettingStarted/" target="_blank">iOS Store</a> | <a href="/soomla/android/store/Store_GettingStarted/" target="_blank">Android Store</a>

5. Integrate Grow Insights - follow the <a href="/unity/Grow_Insights/" target="_blank">Grow Insights Unity Instructions</a>.
