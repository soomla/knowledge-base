---
layout: "sample"
image: "heyzap_logo"
title: "Heyzap"
text: "Show rewarded video ad to earn coins"
position: 9
relates: ["supersonic", "adience", "unity_ads"]
collection: 'samples'
navicon: "nav-icon-heyzap.png"
backlink: "https://www.heyzap.com/"
theme: 'samples'
---

# Unity Ads Integration

<div>

  <!-- Nav tabs -->
  <ul class="nav nav-tabs nav-tabs-use-case-code sample-tabs" role="tablist">
    <li role="presentation" class="active"><a href="#sample-unity" aria-controls="unity" role="tab" data-toggle="tab">Unity</a></li>
  </ul>

  <!-- Tab panes -->
  <div class="tab-content tab-content-use-case-code">
    <div role="tabpanel" class="tab-pane active" id="sample-unity">
      <pre>
```
using UnityEngine;
using System.Collections;
using Soomla.Highway;
using Soomla.Store;

public class Initializer : MonoBehaviour {

	void Awake () {
		// Initialize SOOMLA Highway and Heyzap
		GrowHighway.Initialize();
		HeyzapAds.start("<PUBLISHER ID>", HeyzapAds.FLAG_NO_OPTIONS);
	}

	void Start(){
        // Initialize SOOMLA Store with your Store Assets
        SoomlaStore.Initialize(new YourStoreAssetsImplementation());

		HZIncentivizedAd.fetch();
	}


	HZIncentivizedAd.AdDisplayListener listener = delegate(string adState, string adTag) {
		if (adState.Equals ("incentivized_result_complete")) {

			// The user has watched the entire video and should be given a reward.
			StoreInventory.GiveItem("currency_coins", 100);
		}
	};

	void Update() {
		HZIncentivizedAd.setDisplayListener(listener);
		if (HZIncentivizedAd.isAvailable()) {
			 if (GUILayout.Button("Show rewarded video")) {
			 	HZIncentivizedAd.show();
		 	}
		}
	}
}
```
      </pre>

    </div>
  </div>

</div>


<div class="samples-title">Getting started with the two SDKs</div>

1. Signup for a Heyzap account and claim your app <a href="https://developers.heyzap.com/docs/publishing" target="_blank">(Instructions)</a>.

2. Download and integrate the HeyZap Unity SDK. <a href="https://developers.heyzap.com/docs/unity_sdk_setup_and_requirements" target="_blank">(Instructions)</a>.

3. Integrate SOOMLA Store.  Follow all steps in the Unity Store <a href="/unity/store/store_gettingstarted/" target="_blank">getting started guide</a>.


<div class="samples-title">Additional tips and recommendations</div>

1. Have a look at the <a href="https://github.com/Heyzap/unity-example-app" target="_blank">Heyzap example app</a> for further reference.

2. In the `HZIncentivizedAd.AdDisplayListener` delegate method you can check many more ad states.  See a full list <a href="https://developers.heyzap.com/docs/unity_sdk_advanced#callbacks" target="_blank">here</a>.

3. You can turn Heyzap ads on and off remotely without resubmitting your game by using tags <a href="https://developers.heyzap.com/docs/unity_sdk_advanced#tags" target="_blank">tags</a>.

4. Adding SOOMLA Highway package will allow your users to backup their balances and will give you more tools to analyze their behavior.

<div class="samples-title">Credits</div>

This code sample was contributed by SOOMLA community developer Paul Orac, Co-founder of <a href="http://www.cosmosdigitalstudios.com/" target="_blank">Cosmos Digital Studios</a>.
