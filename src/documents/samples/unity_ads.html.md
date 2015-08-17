---
layout: "sample"
image: "unityads_logo"
title: "Unity Ads"
text: "Show rewarded ad to earn coins"
position: 9
relates: ["giftgaming", "supersonic", "tune"]
collection: 'samples'
navicon: "nav-icon-unityads.png"
backlink: "http://unityads.unity3d.com/"
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
using UnityEngine.UI;
using UnityEngine.Advertisements;
using Soomla;
using Soomla.Store;

//Just apply this script to an UI Button
public class freeCoins : MonoBehaviour {
    public string COIN_ID = "coin_ID";
    Button btn;
    // Use this for initialization
    void Start () {
        btn = GetComponent<Button>();
        Advertisement.Initialize ("YOUR_UNITYADS_GAMEID");
        Advertisement.allowPrecache = true;
    }

    void LateUpdate()
    {
        if (Advertisement.isReady() && Advertisement.isSupported==true)
        { btn.interactable = true;} else { btn.interactable = false;}
    }

    //UI Button action
    public void ShowAds()
    {
        ShowOptions options = new ShowOptions();
        options.pause=true;
        options.resultCallback=HandleShowResult;
        Advertisement.Show(null,options);
    }
    void HandleShowResult(ShowResult result)
    {
        //Give 200 Coins if the user watched the Ads Completely
        if(result==ShowResult.Finished)
        {
            StoreInventory.GiveItem(COIN_ID, 200);
        }
    }
}
```
      </pre>

    </div>
  </div>

</div>


<div class="samples-title">Getting started with the two SDKs</div>

1. If you are already using Unity to develop the game, getting started with Unity Ads is very simple. Import the package, drag the prefab and you are good to go. Here are the <a href="https://unityads.unity3d.com/help/Documentation%20for%20Publishers/GettingStarted" target="_blank">full instructions</a>.

2. Integrate SOOMLA Store and. See <a href="/unity/store/store_gettingstarted/" target="_blank">Full instructions</a>.

<div class="samples-title">Additional tips and recommendations</div>

1. Combine 2 or 3 Video ad networks for increased coverage. Working with a single provider will often lead to inventory problems where `UnityAdsCampaignsAvailable();` returns empty.

2. Adding SOOMLA Highway package will allow your users to backup their balances and will give you more tools to analyze their behavior.
