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
    <div role="tabpanel" class="tab-pane active" id="sample-android">
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


<div class="samples-title">Getting started</div>

1. Sign up to Supersonic <a href="
https://www.supersonic.com/lp/roam/lp_m1/?utm_source=Soomla%20Promotion&utm_medium=Soomla%20Knowledge%20Center&utm_campaign=SDK%20Integration%20Article" target="_blank">here</a>.

2. <a href="https://www.supersonicads.com/partners/login" target="_blank">Login</a> to the Supersonic Dashboard and <a href="http://developers.supersonic.com/hc/en-us/articles/200772061-Adding-Your-App" target="_blank">add your application</a>.

3. Follow the integration instructions for your platform: <br>
    <a href="http://developers.supersonic.com/hc/en-us/articles/201527091-Getting-started-with-the-Supersonic-Unity-Plugin-" target="_blank">Unity</a> |
    <a href="http://developers.supersonic.com/hc/en-us/articles/201328522-Getting-Started-with-the-Supersonic-iOS-SDK" target="_blank">iOS</a> |
    <a href="http://developers.supersonic.com/hc/en-us/articles/201481051-Getting-Started-with-the-Supersonic-Android-SDK" target="_blank">Android</a>

4. Integrate SOOMLA Store and LevelUp.  Follow all steps in the platform specific getting started guides: <br>
    <a href="/soomla/unity/store/Store_GettingStarted/" target="_blank">Unity Store</a> |
    <a href="/unity/levelup/levelup_gettingstarted/" target="_blank">Unity LevelUp</a> |
    <a href="/soomla/android/store/store_gettingstarted/" target="_blank">Android Store</a> |
    <a href="/soomla/ios/store/store_gettingstarted/" target="_blank">iOS Store</a>


<div class="samples-title">Additional tips and recommendations</div>

1. Combine 2 or 3 Video ad networks for increased coverage.<br> Working with a single provider could lead to inventory problems where there are no ads available.

2. Adding <a href="/unity/grow_gettingstarted/" target="_blank">GROW</a> will allow your users to backup their balances and will give you more tools to analyze their behavior.
