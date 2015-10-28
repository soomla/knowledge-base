---
layout: "sample"
image: "adience_logo"
title: "Adience Mediation"
text: "Report SOOMLA Store purchase events to Adience Mediation"
position: 13
relates: []
collection: 'samples'
navicon: ""
backlink: "http://www.adience.com/"
theme: 'samples'
---

# Adience Mediation Integration

<br>

<div>

  <!-- Nav tabs -->
  <ul class="nav nav-tabs nav-tabs-use-case-code sample-tabs" role="tablist">
    <li role="presentation" class="active"><a href="#sample-unity" aria-controls="unity" role="tab" data-toggle="tab">Unity</a></li>
    <li role="presentation"><a href="#sample-android" aria-controls="android" role="tab" data-toggle="tab">Android</a></li>
  </ul>

  <!-- Tab panes -->
  <div class="tab-content tab-content-use-case-code">
    <div role="tabpanel" class="tab-pane active" id="sample-unity">
    <pre>
    ```
using UnityEngine;
using AdBoost;
using Soomla.Store;
using System.Collections.Generic;
using System.Linq;

public class MyBehaviour : MonoBehaviour
{
    private void Start() {

        // Register SOOMLA Store event handlers
        StoreEvents.OnMarketPurchase += MyBehaviour.OnMarketPurchase;

        // Initialize SOOMLA Store
        // Assumes you've implemented your store assets
        SoomlaStore.Initialize(new YourStoreAssetsImplementation());
    }

    private static void OnMarketPurchase(PurchasableVirtualItem pvi, string payload, Dictionary<string, string> extra) {
        PurchaseWithMarket purchaseWithMarket = pvi.PurchaseType as PurchaseWithMarket;

        if (purchaseWithMarket != null) {
            MarketItem marketItem = purchaseWithMarket.MarketItem;
            if (Application.platform == RuntimePlatform.Android) {
                EventTracker tracker = AdBoostUnity.getEventTracker();
                tracker.sendRevenueEvent(marketItem.MarketCurrencyCode, marketItem.Price, pvi.ItemId);
            }
        }
    }

```
    </pre>

  </div>
    <div role="tabpanel" class="tab-pane" id="sample-android">
      <pre>
```
package com.mycompany.myapp;

import android.app.Activity;
import android.os.Bundle;
import android.view.Menu;
import android.view.MenuItem;

import com.adience.adboost.AdBoost;
import com.adience.sdk.EventTracker;
import com.soomla.BusProvider;
import com.soomla.Soomla;
import com.soomla.store.domain.MarketItem;
import com.soomla.store.domain.PurchasableVirtualItem;
import com.soomla.store.events.MarketPurchaseEvent;
import com.soomla.store.purchaseTypes.PurchaseWithMarket;
import com.squareup.otto.Subscribe;

import java.util.HashMap;


public class MainActivity extends Activity
{

	@Override
	protected void onCreate(Bundle savedInstanceState)
	{
		super.onCreate(savedInstanceState);
		setContentView(R.layout.activity_main);
		AdBoost.appStarted();

        // Register this instance to receive SOOMLA events.
        BusProvider.getInstance().register(this);

        // Initialize SOOMLA Store
        // Assumes you've implemented your store assets.
		Soomla.initialize("<GAME SECRET GOES HERE>");
        SoomlaStore.getInstance().initialize(new YourStoreAssetsImplementation());
	}

	@Subscribe
	public void onMarketPurchase(MarketPurchaseEvent marketPurchaseEvent)
	{
		PurchasableVirtualItem pvi = marketPurchaseEvent.PurchasableVirtualItem;
		PurchaseWithMarket purchaseWithMarket =
		    PurchaseWithMarket.class.isInstance(pvi.getPurchaseType()) ?
		    (PurchaseWithMarket)pvi.getPurchaseType() : null;

		if(purchaseWithMarket != null)
		{
			MarketItem marketItem = purchaseWithMarket.getMarketItem();
			HashMap<String, String> extraInfo = marketPurchaseEvent.ExtraInfo;
            EventTracker tracker = AdBoost.getEventTracker();
    		tracker.sendRevenueEvent(marketItem.getMarketCurrencyCode(), marketItem.getPrice(), pvi.getItemId());
		}
	}

}
```
      </pre>
    </div>
  </div>
</div>


<div class="samples-title">Getting started</div>

1. Sign up to <a href="http://www.adience.com/" target="_blank">Adience Mediation</a> for free.

2. Create your account, studio and game.

3. Follow instructions on how to <a href="https://github.com/adience-code/adboost-demo/wiki" target="_blank">integrate your platform</a>.

4. Integrate SOOMLA Store. Follow all steps in the platform specific getting started guides: <a href="http://know.soom.la/unity/store/store_gettingstarted/" target="_blank">Unity Store</a> | <a href="http://know.soom.la/ios/store/store_gettingstarted/" target="_blank">iOS Store</a> | <a href="http://know.soom.la/android/store/store_gettingstarted/" target="_blank">Android Store</a>

