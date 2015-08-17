---
layout: "sample"
image: "supersonic_logo"
title: "Supersonic"
text: "Show rewarded video / offer wall to earn coins"
position: 7
relates: ["giftgaming", "fyber", "unity_ads"]
collection: 'samples'
navicon: "nav-icon-supersonic.png"
featured: true
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

  <!-- Nav tabs -->
  <ul class="nav nav-tabs nav-tabs-use-case-code sample-tabs" role="tablist">
    <li role="presentation" class="active"><a href="#sample-unity" aria-controls="unity" role="tab" data-toggle="tab">Unity (Rewarded Ads + Interstitial)</a></li>
    <li role="presentation"><a href="#sample-unity-insufficient-funds" aria-controls="unityinsuff" role="tab" data-toggle="tab">Unity (Insufficient Funds)</a></li>
    <li role="presentation"><a href="#sample-ios" aria-controls="iod" role="tab" data-toggle="tab">iOS</a></li>
    <li role="presentation"><a href="#sample-android" aria-controls="android" role="tab" data-toggle="tab">Android</a></li>
  </ul>

  <!-- Tab panes -->
  <div class="tab-content tab-content-use-case-code">
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
    <div role="tabpanel" class="tab-pane" id="sample-ios">
      <pre>
```
#import "ViewController.h"

@implementation ViewController

    NSString *soomlaSecret = @"YOUR_SOOMLA_SECRET";
    NSString *SUPERSONIC_APP_KEY = @"YOUR_SUPERSONIC_APPKEY";
    NSString *userId = @"UNIQUE_USER_ID";

    - (void)viewDidLoad {
        [super viewDidLoad];

        // Initialize SOOMLA
        // Assumes you've implemented your store assets
        [Soomla initializeWithSecret:soomlaSecret];
        id<IStoreAssets> storeAssets = [[YourImplementationAssets alloc] init];
        [[SoomlaStore getInstance] initializeWithStoreAssets:[[storeAssets alloc] init]];

        self.rewardedVideoBtn.enabled = false;
        self.offerwallBtn.enabled = false;

        // Initialize Supersonic
        [[Supersonic sharedInstance] setLogDelegate:self];
        [[Supersonic sharedInstance] setRVDelegate:self];
        [[Supersonic sharedInstance] setOWDelegate:self];
        [SUSupersonicAdsConfiguration getConfiguration].useClientSideCallbacks = [NSNumber numberWithInt:1];
        [[Supersonic sharedInstance] initOWWithAppKey:SUPERSONIC_APP_KEY withUserId:userId];
        [[Supersonic sharedInstance] initRVWithAppKey:SUPERSONIC_APP_KEY withUserId:userId];
    }

    - (IBAction)rewardedVideoBtn:(id)sender {
        [[Supersonic sharedInstance] showRV];
    }

    - (IBAction)offerwallBtn:(id)sender {
        [[Supersonic sharedInstance] showOW];
    }

    - (IBAction)buyShieldBtn:(id)sender {
        @try {
            [StoreInventory buyItemWithItemId:@"shield_good"];
        }
        @catch (InsufficientFundsException *exception) {
            // Not enough coins to buy a shield, show the user some free options to earn more coins
            if([[Supersonic sharedInstance] isAdAvailable])
                [[Supersonic sharedInstance] showRV];
            else if ([[Supersonic sharedInstance] isOWAvailable])
                [[Supersonic sharedInstance] showOW];
        }

    }

    - (void)supersonicRVAdRewarded:(NSInteger)amount{
        [StoreInventory giveAmount:amount ofItem:@"coins_currency"];
    }

    - (BOOL)supersonicOWDidReceiveCredit:(NSDictionary *)creditInfo {
        [StoreInventory giveAmount:[[creditInfo valueForKey:@"credits"] intValue]
                            ofItem:@"coins_currency"];
        // Return true if you successfully rewarded the user
        return true;
    }

    - (void)supersonicOWInitSuccess{
        self.offerwallBtn.enabled = true;
    }
    - (void)supersonicOWInitFailedWithError:(NSError *)error {
        self.offerwallBtn.enabled = false;
    }
    - (void)supersonicRVAdAvailabilityChanged:(BOOL)hasAvailableAds {
        self.rewardedVideoBtn.enabled = hasAvailableAds;
    }

@end
```
      </pre>
    </div>
    <div role="tabpanel" class="tab-pane" id="sample-android">
      <pre>
```
package com.supersonic.soomlasample;
import android.app.Activity;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import com.soomla.Soomla;
import com.soomla.store.SoomlaStore;
import com.soomla.store.StoreInventory;
import com.soomla.store.exceptions.InsufficientFundsException;
import com.soomla.store.exceptions.VirtualItemNotFoundException;
import com.supersonic.adapters.supersonicads.SupersonicConfig;
import com.supersonic.mediationsdk.logger.SupersonicError;
import com.supersonic.mediationsdk.sdk.OfferwallListener;
import com.supersonic.mediationsdk.sdk.RewardedVideoListener;
import com.supersonic.mediationsdk.sdk.Supersonic;
import com.supersonic.mediationsdk.sdk.SupersonicFactory;

public class MainActivity extends Activity implements RewardedVideoListener, OfferwallListener {

    final static String SOOMLA_SECRET = "YOUR_SOOMLA_SECRET";
    final static String SUPERSONIC_APPKEY = "YOUR_SUPERSONIC_APP_KEY";
    static String userId = "APP_USER_ID";

    Button showRewardedVideoButton;
    Button showOfferwallButton;
    Button buyShieldButton;

    private Supersonic supersonicAgent;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        // Initialize SOOMLA
        // Assumes you've implemented your store assets and an initial world
        Soomla.initialize(SOOMLA_SECRET);
        SoomlaStore.getInstance().initialize(new YourStoreAssetsImplementation());

        // Initialize Supersonic
        supersonicAgent = SupersonicFactory.getInstance();
        SupersonicConfig.getConfigObj().setClientSideCallbacks(true);
        supersonicAgent.setRewardedVideoListener(this);
        supersonicAgent.setOfferwallListener(this);
        supersonicAgent.initRewardedVideo(this, SUPERSONIC_APPKEY, userId);
        supersonicAgent.initOfferwall(this, SUPERSONIC_APPKEY, userId);

        View.OnClickListener btnOnClick = new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                switch(v.getId()){
                    case R.id.buy_shield_btn:
                        try {
                            StoreInventory.buy(StoreAssets.SHIELD_GOOD.getItemId());
                        }
                        // The users does not have enough coins to buy a shield,
                        // therefore we should offer him some free choices to receive coins.
                        catch(InsufficientFundsException exception)
                        {
                            if(supersonicAgent.isRewardedVideoAvailable())
                                supersonicAgent.showRewardedVideo();
                            else if(supersonicAgent.isOfferwallAvailable())
                                supersonicAgent.showOfferwall();
                        }
                        break;
                    case R.id.show_rv_btn:
                        supersonicAgent.showRewardedVideo();
                        break;
                    case R.id.show_ow_btn:
                        supersonicAgent.showOfferwall();
                        break;
                }
            }
        };
        buyShieldButton = (Button)findViewById(R.id.buy_shield_btn);
        showRewardedVideoButton = (Button)findViewById(R.id.show_rv_btn);
        showOfferwallButton = (Button)findViewById(R.id.show_ow_btn);
        showRewardedVideoButton.setEnabled(false);
        showOfferwallButton.setEnabled(false);
        buyShieldButton.setOnClickListener(btnOnClick);
        showRewardedVideoButton.setOnClickListener(btnOnClick);
        showOfferwallButton.setOnClickListener(btnOnClick);
    }

    public void onVideoAvailabilityChanged(final boolean isAvailable) {
        showRewardedVideoButton.setEnabled(isAvailable);
    }

    public void onOfferwallInitSuccess() {
        showOfferwallButton.setEnabled(true);
    }

    public void onOfferwallInitFail(SupersonicError supersonicError) {
        showOfferwallButton.setEnabled(false);
    }

    public void onRewardedVideoAdRewarded(int amount) {
        try {
            StoreInventory.giveVirtualItem(StoreAssets.COIN_CURRENCY.getItemId(), amount);
        }
        catch(VirtualItemNotFoundException exception){}
    }

    public boolean onOfferwallAdCredited(int credits, int totalCredits, boolean totalCreditsFlag) {
        try {
            StoreInventory.giveVirtualItem(StoreAssets.COIN_CURRENCY.getItemId(), credits);
        }
        catch(VirtualItemNotFoundException exception){ return false; }
        // Return true if you successfully credited the user.
        return true;
    }

    protected void onResume() {
        super.onResume();
        if (supersonicAgent != null) {
            supersonicAgent.onResume (this);
        }
    }

    protected void onPause() {
        super.onPause();
        if (supersonicAgent != null) {
            supersonicAgent.onPause(this);
        }
    }

    public void onRewardedVideoInitSuccess() {}
    public void onRewardedVideoInitFail(SupersonicError supersonicError) {}
    public void onRewardedVideoAdOpened() {}
    public void onRewardedVideoAdClosed() {}
    public void onVideoStart() {}
    public void onVideoEnd() {}
    public void onOfferwallOpened() {}
    public void onOfferwallShowFail(SupersonicError supersonicError) {}
    public void onGetOfferwallCreditsFail(SupersonicError supersonicError) {}
    public void onOfferwallClosed() {}

}
```
      </pre>
    </div>
  </div>

</div>


<div class="samples-title">Getting started</div>

1. Sign up to Supersonic <a href="https://www.supersonicads.com/partners/signup?cid=oso_part_soomla" target="_blank">here</a>.

2. <a href="https://www.supersonicads.com/partners/login" target="_blank">Login</a> to the Supersonic Dashboard and <a href="http://developers.supersonic.com/hc/en-us/articles/200772061-Adding-Your-App" target="_blank">add your application</a>.

3. Follow the integration instructions for your platform: <br>
    <a href="http://developers.supersonic.com/hc/en-us/articles/201527091-Getting-started-with-the-Supersonic-Unity-Plugin-" target="_blank">Unity</a> |
    <a href="http://developers.supersonic.com/hc/en-us/articles/201328522-Getting-Started-with-the-Supersonic-iOS-SDK" target="_blank">iOS</a> |
    <a href="http://developers.supersonic.com/hc/en-us/articles/201481051-Getting-Started-with-the-Supersonic-Android-SDK" target="_blank">Android</a>

4. Integrate SOOMLA Store and LevelUp.  Follow all steps in the platform specific getting started guides: <br>
    <a href="/unity/store/store_gettingstarted/" target="_blank">Unity Store</a> |
    <a href="/unity/levelup/levelup_gettingstarted/" target="_blank">Unity LevelUp</a> |
    <a href="/android/store/store_gettingstarted/" target="_blank">Android Store</a> |
    <a href="/ios/store/store_gettingstarted/" target="_blank">iOS Store</a>


<div class="samples-title">Additional tips and recommendations</div>

1. Combine 2 or 3 Video ad networks for increased coverage.<br> Working with a single provider could lead to inventory problems where there are no ads available.

2. Adding <a href="/unity/grow/grow_gettingstarted/" target="_blank">SOOMLA GROW</a> will allow your users to backup their balances and will give you more tools to analyze their behavior.
