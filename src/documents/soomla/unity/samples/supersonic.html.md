---
layout: "sample"
image: "supersonic_logo"
title: "Supersonic"
text: "Show rewarded video / offer wall to earn coins"
position: 11
relates: ["fyber", "adience", "heyzap"]
collection: 'samples'
navicon: "nav-icon-supersonic.png"
backlink: "http://www.supersonic.com/"
theme: 'samples'
---

# Supersonic Integration

<div class="samples-title">This article shows multiple integration samples:</div>

* Reward coins on RewardedVideo/OfferWall ads completion.

* Show an Interstitial ad when a level ends (Unity only).

* Offer users to watch a video or complete an offer to receive virtual currency when they have insufficient funds (coins) to purchase an item.

<br>

<div>

    <div role="tabpanel" class="tab-pane active" id="sample-unity">
      <pre>
```
using UnityEngine;
using System.Collections;
using System.Collections.Generic;
using Soomla;
using Soomla.Store;
using Soomla.Profile;
using Soomla.Levelup;

public class MainGame : MonoBehaviour {

    // Variables to indicate Supersonic initialization and available ad inventory
    bool isRewardedVideoReady = false;
    bool isOfferwallReady = false;

    const string SUPERSONIC_APPKEY = "YOUR_APP_KEY";
    string userId = "UNIQUE_USER_ID";

    void Start () {

        // Initialize SOOMLA
        // Assumes you've implemented your store assets and an initial world
        SoomlaStore.Initialize (new YourStoreAssetsImplementation ());
        SoomlaProfile.Initialize ();
        SoomlaLevelUp.Initialize (WORLD);

        // Initialize Supersonic
        Supersonic.Agent.start ();

        // Enable client side reward events for Offerwall
        SupersonicConfig.Instance.setClientSideCallbacks (true);
        Supersonic.Agent.initInterstitial (SUPERSONIC_APPKEY, userId);
        Supersonic.Agent.initRewardedVideo (SUPERSONIC_APPKEY, userId);
        Supersonic.Agent.initOfferwall(SUPERSONIC_APPKEY, userId);
    }

    void OnGUI(){
        // Disable the show RewardedVideo button if no videos are available
        if (!isRewardedVideoReady)
            GUI.enabled = false;
        if(GUILayout.Button("Earn coins by watching a video!"))
            Supersonic.Agent.showRewardedVideo();
        GUI.enabled = true;

        // Disable the show Offerwall button if no videos are available
        if (!isOfferwallReady)
            GUI.enabled = false;
        if(GUILayout.Button("Earn coins by completing offers!")){
            Supersonic.Agent.showOfferwall();
        }
        GUI.enabled = true;
    }

    void OnEnable(){
        LevelUpEvents.OnLevelEnded += onLevelEnded;
        SupersonicEvents.onVideoAvailabilityChangedEvent += onRVAvailability;
        SupersonicEvents.onRewardedVideoAdRewardedEvent += onRVRewarded;
        SupersonicEvents.onOfferwallInitSuccessEvent += onOWInitSuccess;
        SupersonicEvents.onOfferwallInitFailEvent += onOWInitFailed;
        SupersonicEvents.onOfferwallAdCreditedEvent += onOWCredited;
    }

    void onLevelEnded(Level level){
        // A good place to show an Interstitial ad is when the level ends
        if (Supersonic.Agent.isInterstitialAdAvailalbe ())
            Supersonic.Agent.showInterstitial ();
    }

    void onRVAvailability(bool available){
        isRewardedVideoReady = available;
    }

    void onRVRewarded (int amount){
        // Give the user the amount of coins rewarded from watching the video
        StoreInventory.GiveItem (StoreAssets.COIN_CURRENCY.ItemId, amount);
    }

    void onOWInitSuccess(){
        isOfferwallReady = true;
    }

    void onOWInitFailed(SupersonicError error){
        isOfferwallReady = false;
    }

    void onOWCredited (Dictionary<string, object> rewardInfo){
        int credits = int.Parse(rewardInfo["credits"].ToString());
        StoreInventory.GiveItem (StoreAssets.COIN_CURRENCY.ItemId, credits);
    }

}
```
      </pre>

    </div>
    <div role="tabpanel" class="tab-pane" id="sample-unity-insufficient-funds">
      <pre>
```
using UnityEngine;
using System.Collections;
using System.Collections.Generic;
using Soomla;
using Soomla.Store;
using Soomla.Profile;
using Soomla.Levelup;

public class MainGame : MonoBehaviour {

    // Variables to indicate Supersonic initialization and available ad inventory
    bool isRewardedVideoReady = false;
    bool isOfferwallReady = false;

    const string SUPERSONIC_APPKEY = "YOUR_SUPERSONIC_APPKEY";
    string userId = "UNIQUE_USER_ID";

    void Start () {

        // Initialize SOOMLA
        // Assumes you've implemented your store assets and an initial world
        SoomlaStore.Initialize (new YourStoreAssetsImplementation ());

        // Initialize Supersonic
        Supersonic.Agent.start ();

        // Enable client side reward events for Offerwall
        SupersonicConfig.Instance.setClientSideCallbacks (true);
        Supersonic.Agent.initRewardedVideo (SUPERSONIC_APPKEY, userId);
        Supersonic.Agent.initOfferwall(SUPERSONIC_APPKEY, userId);
    }

    void OnGUI(){
        if (GUILayout.Button ("Buy shield for 100 coins")) {
            try{
                StoreInventory.BuyItem(StoreAssets.SHIELD_GOOD.ItemId);
            }
            catch(InsufficientFundsException exception){
                // The user does not have enough coins to buy the shield.
                // We would usually like to show a dialog that suggests
                // the user to complete an offer or watch a video.
                if(isRewardedVideoReady)
                    Supersonic.Agent.showRewardedVideo();
                else if(isOfferwallReady){
                    Supersonic.Agent.showOfferwall();
                }
            }
        }
    }

    void OnEnable(){
        SupersonicEvents.onVideoAvailabilityChangedEvent += onRVAvailability;
        SupersonicEvents.onRewardedVideoAdRewardedEvent += onRVRewarded;
        SupersonicEvents.onOfferwallInitSuccessEvent += onOWInitSuccess;
        SupersonicEvents.onOfferwallInitFailEvent += onOWInitFailed;
        SupersonicEvents.onOfferwallAdCreditedEvent += onOWCredited;
    }

    void onRVAvailability(bool available){
        isRewardedVideoReady = available;
    }

    void onRVRewarded (int amount){
        // Give the user the amount of coins rewarded from watching the video
        StoreInventory.GiveItem (StoreAssets.COIN_CURRENCY.ItemId, amount);
    }

    void onOWInitSuccess(){
        isOfferwallReady = true;
    }

    void onOWInitFailed(SupersonicError error){
        isOfferwallReady = false;
    }

    void onOWCredited (Dictionary<string, object> rewardInfo){
        int credits = int.Parse(rewardInfo["credits"].ToString());
        StoreInventory.GiveItem (StoreAssets.COIN_CURRENCY.ItemId, credits);
    }

}
```
      </pre>
    </div>
</div>


<div class="samples-title">Getting started</div>

1. Sign up to Supersonic <a href="
https://www.supersonic.com/lp/roam/lp_m1/?utm_source=Soomla%20Promotion&utm_medium=Soomla%20Knowledge%20Center&utm_campaign=SDK%20Integration%20Article" target="_blank">here</a>.

2. <a href="https://www.supersonicads.com/partners/login" target="_blank">Login</a> to the Supersonic Dashboard and <a href="http://developers.supersonic.com/hc/en-us/articles/200772061-Adding-Your-App" target="_blank">add your application</a>.

3. Follow the integration instructions for your platform: <br>
    <a href="http://developers.supersonic.com/hc/en-us/articles/201527091-Getting-started-with-the-Supersonic-Unity-Plugin-" target="_blank">Unity</a> |
    <a href="http://developers.supersonic.com/hc/en-us/articles/201328522-Getting-Started-with-the-Supersonic-iOS-SDK" target="_blank">iOS</a> |
    <a href="http://developers.supersonic.com/hc/en-us/articles/201481051-Getting-Started-with-the-Supersonic-Android-SDK" target="_blank">Android</a>

4. Integrate SOOMLA Store.  Follow all steps in the platform specific getting started guides: <br>
    <a href="/soomla/unity/store/Store_GettingStarted/" target="_blank">Unity Store</a> |
    <a href="/soomla/android/store/Store_GettingStarted/" target="_blank">Android Store</a> |
    <a href="/soomla/ios/store/Store_GettingStarted/" target="_blank">iOS Store</a>


<div class="samples-title">Additional tips and recommendations</div>

1. Combine 2 or 3 Video ad networks for increased coverage.<br> Working with a single provider could lead to inventory problems where there are no ads available.

2. Adding <a href="/university/articles/Grow_About/" target="_blank">GROW</a> will allow your users to backup their balances and will give you more tools to analyze their behavior.
