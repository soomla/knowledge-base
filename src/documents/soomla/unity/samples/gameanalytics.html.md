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

    private static void OnSoomlaInsightsRefreshFinished(InsightsRefreshFinishedEvent evnt)
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


<div class="samples-title">Getting started</div>

1. Sign up to <a href="http://www.gameanalytics.com/signup" target="_blank">GameAnalytics</a> for free.

2. Create your account, studio and game.

3. Follow instructions on how to integrate your platform: <br>
    <a href="https://guides.gameanalytics.com/content/sdk?page=unity" target="_blank">Unity</a> |
    <a href="https://guides.gameanalytics.com/content/sdk?page=ios" target="_blank">iOS</a> |
    <a href="https://guides.gameanalytics.com/content/sdk?page=android" target="_blank">Android</a>

4. Integrate SOOMLA Store. Follow all steps in the platform specific getting started guides: <a href="/soomla/unity/store/Store_GettingStarted/" target="_blank">Unity Store</a> | <a href="/soomla/ios/store/Store_GettingStarted/" target="_blank">iOS Store</a> | <a href="/soomla/android/store/Store_GettingStarted/" target="_blank">Android Store</a>

5. Integrate Grow Insights - follow the <a href="/unity/Grow_Insights/" target="_blank">Grow Insights Unity Instructions</a>.
