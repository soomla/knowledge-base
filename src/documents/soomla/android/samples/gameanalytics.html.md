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


<div class="samples-title">Getting started</div>

1. Sign up to <a href="http://www.gameanalytics.com/signup" target="_blank">GameAnalytics</a> for free.

2. Create your account, studio and game.

3. Follow instructions on how to integrate your platform: <br>
    <a href="https://guides.gameanalytics.com/content/sdk?page=unity" target="_blank">Unity</a> |
    <a href="https://guides.gameanalytics.com/content/sdk?page=ios" target="_blank">iOS</a> |
    <a href="https://guides.gameanalytics.com/content/sdk?page=android" target="_blank">Android</a>

4. Integrate SOOMLA Store. Follow all steps in the platform specific getting started guides: <a href="/soomla/unity/store/Store_GettingStarted/" target="_blank">Unity Store</a> | <a href="/soomla/ios/store/Store_GettingStarted/" target="_blank">iOS Store</a> | <a href="/soomla/android/store/Store_GettingStarted/" target="_blank">Android Store</a>

5. Integrate Grow Insights - follow the <a href="/unity/Grow_Insights/" target="_blank">Grow Insights Unity Instructions</a>.
