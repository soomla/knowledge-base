---
layout: "sample"
image: "gameanalytics_logo"
title: "GAMEANALYTICS"
text: "Show rewarded video to earn coins"
position: 5
relates: ["gameup"]
collection: 'samples'
theme: 'samples'
---

# GameAnalytics + SOOMLA

## Use Case: report in-game events to analytics

<br>

<div>

  <!-- Nav tabs -->
  <ul class="nav nav-tabs nav-tabs-use-case-code sample-tabs" role="tablist">
    <li role="presentation" class="active"><a href="#sample-unity" aria-controls="unity" role="tab" data-toggle="tab">Unity</a></li>
  </ul>

  <!-- Tab panes -->
  <div class="tab-content tab-content-use-case-code">
    <div role="tabpanel" class="tab-pane active" id="sample-unity">
      <pre>
        <code class="cs">
using UnityEngine;
using Soomla.Store;
using Soomla.Levelup;
using System.Collections.Generic;
using System.Linq;

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
        </code>
      </pre>

    </div>
  </div>

</div>


<div class="samples-title">Getting started</div>

1. Download and install the GameAnalytics Unity SDK. [(Instructions)](https://github.com/GameAnalytics/GA-SDK-UNITY/wiki/Download%20and%20Installation).

2. Sign up for a GameAnalytics account, login and create a new studio and game through our Unity plugin. [(Instructions)](https://github.com/GameAnalytics/GA-SDK-UNITY/wiki/Sign%20up%20and%20login)

3. Configure the GameAnalytics settings in Unity. [(Instructions)](https://github.com/GameAnalytics/GA-SDK-UNITY/wiki/Settings)

4. Create a GameAnalytics game object in your Unity scene. [(Instructions)](https://github.com/GameAnalytics/GA-SDK-UNITY/wiki/GameAnalytics%20object)

5. Integrate SOOMLA Store and LevelUp.  Follow all steps in the platform specific getting started guides: <br>
    <a href="/unity/store/store_gettingstarted/" target="_blank">Unity Store</a> |
    <a href="/unity/levelup/levelup_gettingstarted/" target="_blank">Unity LevelUp</a>
