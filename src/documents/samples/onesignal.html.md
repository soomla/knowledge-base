---
layout: "sample"
image: "onesignal_logo"
title: "OneSignal"
text: "Deliver push notifications triggered by SOOMLA Store events"
position: 10
relates: ["tune", "heroiclabs"]
collection: 'samples'
navicon: "nav-icon-onesignal.png"
backlink: "http://onesignal.com/"
theme: 'samples'
---

# OneSignal Integration

<div>

  <!-- Nav tabs -->
  <ul class="nav nav-tabs nav-tabs-use-case-code sample-tabs" role="tablist">
    <li role="presentation" class="active"><a href="#sample-unity" aria-controls="unity" role="tab" data-toggle="tab">Unity</a></li>
    <li role="presentation"><a href="#sample-cocos2dx" aria-controls="cocos2dx" role="tab" data-toggle="tab">Cocos2d-x v3</a></li>
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
using Soomla;
using Soomla.Store;

//Apply this script to a gameobject in the scene where the store is initialized
public class purchaseTracking : MonoBehaviour {

    void Start() {
        StoreEvents.OnMarketPurchaseStarted += onMarketPurchaseStarted;
    }

    public void onMarketPurchaseStarted(PurchasableVirtualItem pvi) {
        OneSignal.SendTag("StartedPurchase", "true");
    }

}
```
      </pre>

    </div>
    <div role="tabpanel" class="tab-pane" id="sample-cocos2dx">
      <pre>
```

Director::getInstance()->getEventDispatcher()->addCustomEventListener(CCStoreConsts::EVENT_MARKET_PURCHASE_STARTED, CC_CALLBACK_1(Example::onMarketPurchaseStarted, this));

void Example::onMarketPurchaseStarted(EventCustom *event) {
  // DICT_ELEMENT_PURCHASABLE - the PurchasableVirtualItem whose purchase
  //                            operation has just started

  // Read purchase data if you need to.
  // __Dictionary *eventData = (__Dictionary *)event->getUserData();
  // CCPurchasableVirtualItem *purchasable = dynamic_cast<CCPurchasableVirtualItem *>(eventData->objectForKey(CCStoreConsts::DICT_ELEMENT_PURCHASABLE));

  _push->sendTag("StartedPurchase", "true");
}
```
</pre>
    </div>
    <div role="tabpanel" class="tab-pane" id="sample-ios">
              <pre>
```
// observe the event:
[[NSNotificationCenter defaultCenter] addObserver:self
  selector:@selector(marketPurchaseStarted:) name:EVENT_MARKET_PURCHASE_STARTED object:nil];

// your handler:
- (void)marketPurchaseStarted:(NSNotification*)notification {
  // notification's userInfo contains the following keys:
  // DICT_ELEMENT_PURCHASABLE = The item whose purchase process has started

  [OneSignal.defaultClient sendTag:@"StartedPurchase" value:@"true"];
}
```
</pre>
</div>
    <div role="tabpanel" class="tab-pane" id="sample-android">
        <pre>
```
@Subscribe
public void onMarketPurchaseStarted(MarketPurchaseStartedEvent marketPurchaseStartedEvent) {
  OneSignal.sendTag("StoreOpened", "1");
}
```
        </pre>
    </div>
  </div>

</div>

<div class="samples-title">Getting Started</div>

<a title="OneSignal Push Notification Service" href="https://onesignal.com" target="_blank">OneSignal</a> is a free, multi-platform, push notification delivery system for mobile apps. OneSignal supports platforms including iOS, Android, Windows Phone, and Amazon fire devices.

Follow the directions below to see a great use case for how OneSignal can be integrated with SOOMLA Store to increase paying player conversion.

1. Sign up and follow the instructions on the <a title="OneSignal Push Notification Service" href="https://onesignal.com" target="_blank">OneSignal</a> website to set up OneSignal multi-platform push notifications for your app.

2. Add SOOMLA store: Import, drag the prefab, initialize and setup your virtual goods. Here are the <a href="/soomla/unity/store/Store_GettingStarted/" target="_blank">full instructions</a>.

2. Integrate SOOMLA Store.  Follow all steps in the platform specific getting started guides: <br>
    <a href="/soomla/unity/store/Store_GettingStarted/" target="_blank">Unity</a> |
    <a href="/soomla/cocos2dx/cpp/store/Store_GettingStarted/" target="_blank">Cocos2d-x</a> |
    <a href="/soomla/ios/store/Store_GettingStarted/" target="_blank">iOS</a> |
    <a href="/soomla/android/store/Store_GettingStarted/" target="_blank">Android</a> |

3. Add the sample code above into your game.

4. On the OneSignal website, set up a segment that matches users with the tag "StartedPurchase", who have amount spent equal to zero, and who have been inactive for at least 3 hours. This works because OneSignal automatically tracks when a user spends money or hasn't used your app in a while. Next, create an automatic message to send to that segment of players.

5. You're done! Your users will now get a notification after they close the app if they had started making a purchase but never completed it. Go ahead and try out different varieties of this example to boost your user conversion and retention even further.
