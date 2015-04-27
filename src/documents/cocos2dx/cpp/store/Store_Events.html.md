---
layout: "content"
image: "Events"
title: "Events"
text: "Learn how to observe and handle economy events triggered by cocos2dx-store to customize your game-specific behavior."
position: 5
theme: 'platforms'
collection: 'cocos2dx_store'
module: 'store'
platform: 'cocos2dx'
---


# Event Handling

## About

cocos2dx-store allows you to subscribe to events, be notified when they occur, and implement your own application-specific behavior to handle them once they occur.

<div class="info-box">Your game-specific behavior is an addition to the default behavior implemented by SOOMLA. You don't replace SOOMLA's behavior.</div>


## How it Works

Events are triggered when SOOMLA wants to notify you about different things that happen involving Store operations.

For example, when a user starts a Market item purchase, an `EVENT_MARKET_PURCHASE_STARTED` event is fired as a result.


## Observing & Handling Events

SOOMLA uses the Cocos2d-x facilities to dispatch its own custom events.
The names of such events are defined in `CCStoreConsts`, the meta-data of the event is held in a `__Dictionary`. You can subscribe to any event from anywhere in your code.

When handling the event you can extract meta-data from the dictionary using pre-defined keys, which are also defined in `CCStoreConsts`.

### Cocos2d-x v3 Events

** Subscribing **

Subscribe to events through the Cocos2d-x  [`EventDispatcher`](http://www.cocos2d-x.org/wiki/EventDispatcher_Mechanism):

```cpp
cocos2d::Director::getInstance()->getEventDispatcher()->addCustomEventListener(soomla::CCStoreConsts::EVENT_ITEM_PURCHASED, CC_CALLBACK_1(ExampleScene::onItemPurchased, this));
```

** Handling **

Handle the event through your own custom function:

```cpp
void ExampleScene::onItemPurchased(cocos2d::EventCustom *event) {
  cocos2d::__Dictionary *eventData = (cocos2d::__Dictionary *)event->getUserData();
  // ... get meta-data information from eventData
}
```


### Cocos2d-x v2 Events

** Subscribing **

Subscribe to events through the Cocos2d-x `CCNotificationCenter`:

```cpp
cocos2d::CCNotificationCenter::sharedNotificationCenter()->addObserver(this, callfuncO_selector(ExampleScene::onItemPurchased), soomla::CCStoreConsts::EVENT_ITEM_PURCHASED, NULL);
```

** Handling **

Handle the event through your own custom function:

```cpp
void ExampleScene::onItemPurchased(cocos2d::CCDictionary *eventData) {
  // ... get meta-data information from eventData
}
```

## Store Events

Below we provide a list of all events in Store, their handling examples are written for v3, but it's easy to convert them to v2 dialect, see how above.

### EVENT_SOOMLA_STORE_INITIALIZED

This event is triggered when the Soomla Store module is initialized and ready.

```cpp
Director::getInstance()->getEventDispatcher()->addCustomEventListener(CCStoreConsts::EVENT_SOOMLA_STORE_INITIALIZED, CC_CALLBACK_1(Example::onSoomlaStoreInitialized, this));

void Example::onSoomlaStoreInitialized(EventCustom *event) {
  // ... your game specific implementation here ...
}
```

**NOTE:** If you want to observe the `EVENT_SOOMLA_STORE_INITIALIZED` event you have to set up the listener before you initialize `CCSoomlaStore`. So, put the following code:

```cpp
Director::getInstance()->getEventDispatcher()->addCustomEventListener(CCStoreConsts::EVENT_SOOMLA_STORE_INITIALIZED, CC_CALLBACK_1(Example::onSoomlaStoreInitialized, this));
```

before

```cpp
soomla::CCSoomlaStore::initialize(assets, storeParams);
```

### EVENT_CURRENCY_BALANCE_CHANGED

This event is triggered when the balance of a specific currency has changed.

```cpp
Director::getInstance()->getEventDispatcher()->addCustomEventListener(CCStoreConsts::EVENT_CURRENCY_BALANCE_CHANGED, CC_CALLBACK_1(Example::onCurrencyBalanceChanged, this));

void Example::onCurrencyBalanceChanged(EventCustom *event) {
    // DICT_ELEMENT_CURRENCY      - the currency whose balance was changed
    // DICT_ELEMENT_BALANCE       - the balance of the currency after the change
    // DICT_ELEMENT_AMOUNT_ADDED  - the amount that was added to the currency balance
    //    (in case the number of currencies was removed this will be a negative value)
    __Dictionary *eventData = (__Dictionary *)event->getUserData();
    CCVirtualCurrency *virtualCurrency = dynamic_cast<CCVirtualCurrency *>(eventData->objectForKey(CCStoreConsts::DICT_ELEMENT_CURRENCY));
    __Integer *balance = dynamic_cast<__Integer *>(eventData->objectForKey(CCStoreConsts::DICT_ELEMENT_BALANCE));
    __Integer *amountAdded = dynamic_cast<__Integer *>(eventData->objectForKey(CCStoreConsts::DICT_ELEMENT_AMOUNT_ADDED));

    // ... your game specific implementation here ...
}
```

### EVENT_GOOD_BALANCE_CHANGED

This event is triggered when the balance of a specific virtual good has changed.

```cpp
Director::getInstance()->getEventDispatcher()->addCustomEventListener(CCStoreConsts::EVENT_GOOD_BALANCE_CHANGED, CC_CALLBACK_1(Example::onGoodBalanceChanged, this));

void Example::onGoodBalanceChanged(EventCustom *event) {
  // DICT_ELEMENT_GOOD          - the virtual good whose balance was changed
  // DICT_ELEMENT_BALANCE       - the balance of the good after the change
  // DICT_ELEMENT_AMOUNT_ADDED  - the amount that was added to the good balance
  //    (in case the number of goods was removed this will be a negative value)

  __Dictionary *eventData = (__Dictionary *)event->getUserData();
  CCVirtualGood *good = dynamic_cast<CCVirtualGood *>(eventData->objectForKey(CCStoreConsts::DICT_ELEMENT_GOOD));
  __Integer *balance = dynamic_cast<__Integer *>(eventData->objectForKey(CCStoreConsts::DICT_ELEMENT_BALANCE));
  __Integer *amountAdded = dynamic_cast<__Integer *>(eventData->objectForKey(CCStoreConsts::DICT_ELEMENT_AMOUNT_ADDED));

  // ... your game specific implementation here ...
}
```

### EVENT_MARKET_PURCHASE_STARTED

This event is triggered when a market purchase operation has started.

```cpp
Director::getInstance()->getEventDispatcher()->addCustomEventListener(CCStoreConsts::EVENT_MARKET_PURCHASE_STARTED, CC_CALLBACK_1(Example::onMarketPurchaseStarted, this));

void Example::onMarketPurchaseStarted(EventCustom *event) {
  // DICT_ELEMENT_PURCHASABLE - the PurchasableVirtualItem whose purchase
  //                            operation has just started

  __Dictionary *eventData = (__Dictionary *)event->getUserData();
  CCPurchasableVirtualItem *purchasable = dynamic_cast<CCPurchasableVirtualItem *>(eventData->objectForKey(CCStoreConsts::DICT_ELEMENT_PURCHASABLE));

  // ... your game specific implementation here ...
}
```

### EVENT_MARKET_PURCHASE

This event is triggered when a market purchase operation has completed successfully.

```cpp
Director::getInstance()->getEventDispatcher()->addCustomEventListener(CCStoreConsts::EVENT_MARKET_PURCHASE, CC_CALLBACK_1(Example::onMarketPurchase, this));

void Example::onMarketPurchase(EventCustom *event) {
  // DICT_ELEMENT_PURCHASABLE       - the PurchasableVirtualItem that was just purchased
  // DICT_ELEMENT_TOKEN             - The purchase token
  // DICT_ELEMENT_DEVELOPERPAYLOAD  - a text that you can give when you initiate the
  //    purchase operation and you want to receive back upon completion
  // Android only:
  // DICT_ELEMENT_ORIGINAL_JSON     - Original JSON of the purchase (Google Only)
  // DICT_ELEMENT_SIGNATURE         - Purchase signature (Google Only)
  // DICT_ELEMENT_USER_ID           - The purchasing user ID (Amazon Only)

  __Dictionary *eventData = (__Dictionary *)event->getUserData();
  CCPurchasableVirtualItem *purchasable = dynamic_cast<CCPurchasableVirtualItem *>(eventData->objectForKey(CCStoreConsts::DICT_ELEMENT_PURCHASABLE));
  __String *token = dynamic_cast<__String *>(eventData->objectForKey(CCStoreConsts::DICT_ELEMENT_TOKEN));
  __String *payload = dynamic_cast<__String *>(eventData->objectForKey(CCStoreConsts::DICT_ELEMENT_DEVELOPERPAYLOAD));

  // Android only
  __String *originalJSON = dynamic_cast<__String *>(eventData->objectForKey(CCStoreConsts::DICT_ELEMENT_ORIGINAL_JSON));
  __String *signature = dynamic_cast<__String *>(eventData->objectForKey(CCStoreConsts::DICT_ELEMENT_SIGNATURE));
  __String *userId = dynamic_cast<__String *>(eventData->objectForKey(CCStoreConsts::DICT_ELEMENT_USER_ID));

  // ... your game specific implementation here ...
}
```

### EVENT_MARKET_PURCHASE_CANCELED

This event is triggered when a market purchase operation has been cancelled by the user.

```cpp
Director::getInstance()->getEventDispatcher()->addCustomEventListener(CCStoreConsts::EVENT_MARKET_PURCHASE_CANCELED, CC_CALLBACK_1(Example::onMarketPurchaseCancelled, this));

void Example::onMarketPurchaseCancelled(EventCustom *event) {
  // DICT_ELEMENT_PURCHASABLE - the PurchasableVirtualItem whose purchase operation
  //                            was cancelled

  __Dictionary *eventData = (__Dictionary *)event->getUserData();
  CCPurchasableVirtualItem *purchasable = dynamic_cast<CCPurchasableVirtualItem *>(eventData->objectForKey(CCStoreConsts::DICT_ELEMENT_PURCHASABLE));

  // ... your game specific implementation here ...
}
```

### EVENT_MARKET_PURCHASE_VERIFICATION

This event is triggered when a market purchase verification process has started.

```cpp
Director::getInstance()->getEventDispatcher()->addCustomEventListener(CCStoreConsts::EVENT_MARKET_PURCHASE_VERIFICATION, CC_CALLBACK_1(Example::onMarketPurchaseVerification, this));

void Example::onMarketPurchaseVerification(EventCustom *event) {
  // DICT_ELEMENT_PURCHASABLE - The PurchasableVirtualItem item whose purchase is
  //                            being verified

  __Dictionary *eventData = (__Dictionary *)event->getUserData();
  CCPurchasableVirtualItem *purchasable = dynamic_cast<CCPurchasableVirtualItem *>(eventData->objectForKey(CCStoreConsts::DICT_ELEMENT_PURCHASABLE));

  // ... your game specific implementation here ...
}
```

### EVENT_MARKET_ITEMS_REFRESH_STARTED

This event is triggered when a refresh market items operation has started.

```cpp
Director::getInstance()->getEventDispatcher()->addCustomEventListener(CCStoreConsts::EVENT_MARKET_ITEMS_REFRESH_STARTED, CC_CALLBACK_1(Example::onMarketItemsRefreshStarted, this));

void Example::onMarketItemsRefreshStarted(EventCustom *event) {
  // ... your game specific implementation here ...
}
```

### EVENT_MARKET_ITEMS_REFRESHED

This event is triggered when a refresh market items operation has finished.

```cpp
Director::getInstance()->getEventDispatcher()->addCustomEventListener(CCStoreConsts::EVENT_MARKET_ITEMS_REFRESHED, CC_CALLBACK_1(Example::onMarketItemsRefreshed, this));

void Example::onMarketItemsRefreshed(EventCustom *event) {
  // DICT_ELEMENT_MARKET_ITEMS - the list of Market items that was fetched from the Market

  __Dictionary *eventData = (__Dictionary *)event->getUserData();
  __Array *marketItems = dynamic_cast<__Array *>(eventData->objectForKey(CCStoreConsts::DICT_ELEMENT_MARKET_ITEMS));

  // ... your game specific implementation here ...
}
```

### EVENT_MARKET_ITEMS_REFRESH_FAILED

This event is triggered when a market item refreshed process has failed.

```cpp
Director::getInstance()->getEventDispatcher()->addCustomEventListener(CCStoreConsts::EVENT_MARKET_ITEMS_REFRESH_FAILED, CC_CALLBACK_1(Example::onMarketItemsRefreshFailed, this));

void Example::onMarketItemsRefreshFailed(EventCustom *event) {
  // DICT_ELEMENT_ERROR_MESSAGE - The error which caused the failure

  __Dictionary *eventData = (__Dictionary *)(event->getUserData());
  __String *errorMessage = dynamic_cast<__String *>(eventData->objectForKey(CCStoreConsts::DICT_ELEMENT_ERROR_MESSAGE));

  // ... your game specific implementation here ...
}
```

### EVENT_RESTORE_TRANSACTION_STARTED

This event is triggered when a restore transactions operation has started.

```cpp
Director::getInstance()->getEventDispatcher()->addCustomEventListener(CCStoreConsts::EVENT_RESTORE_TRANSACTION_STARTED, CC_CALLBACK_1(Example::onMarketItemsRefreshStarted, this));

void Example::onMarketItemsRefreshStarted(EventCustom *event) {
  // ... your game specific implementation here ...
}
```

### EVENT_RESTORE_TRANSACTION_FINISHED

This event is triggered when a restore transactions operation has completed successfully.

```cpp
Director::getInstance()->getEventDispatcher()->addCustomEventListener(CCStoreConsts::EVENT_RESTORE_TRANSACTION_FINISHED, CC_CALLBACK_1(Example::onRestoreTransactionsFinished, this));

void Example::onRestoreTransactionsFinished(EventCustom *event) {
  // DICT_ELEMENT_SUCCESS - true if the restore transactions operation has succeeded

  __Dictionary *eventData = (__Dictionary *)event->getUserData();
  __Bool *success = dynamic_cast<__Bool *>(eventData->objectForKey(CCStoreConsts::DICT_ELEMENT_SUCCESS));

  // ... your game specific implementation here ...
}
```

### EVENT_ITEM_PURCHASE_STARTED

This event is triggered when an item purchase operation has started.

```cpp
Director::getInstance()->getEventDispatcher()->addCustomEventListener(CCStoreConsts::EVENT_ITEM_PURCHASE_STARTED, CC_CALLBACK_1(Example::onItemPurchaseStarted, this));

void Example::onItemPurchaseStarted(EventCustom *event) {
  // DICT_ELEMENT_PURCHASABLE - the PurchasableVirtualItem whose purchase operation has
  //                            just started

  __Dictionary *eventData = (__Dictionary *)event->getUserData();
  CCPurchasableVirtualItem *purchasable = dynamic_cast<CCPurchasableVirtualItem *>(eventData->objectForKey(CCStoreConsts::DICT_ELEMENT_PURCHASABLE));

  // ... your game specific implementation here ...
}
```

### EVENT_ITEM_PURCHASED

This event is triggered when an item purchase operation has completed successfully.

```cpp
Director::getInstance()->getEventDispatcher()->addCustomEventListener(CCStoreConsts::EVENT_ITEM_PURCHASED, CC_CALLBACK_1(Example::onItemPurchased, this));

void Example::onItemPurchased(EventCustom *event) {
  // DICT_ELEMENT_PURCHASABLE       - the PurchasableVirtualItem that was just purchased
  // DICT_ELEMENT_DEVELOPERPAYLOAD  - a text that you can give when you initiate the purchase
  //    operation and you want to receive back upon completion

  __Dictionary *eventData = (__Dictionary *)event->getUserData();
  CCPurchasableVirtualItem *purchasable = dynamic_cast<CCPurchasableVirtualItem *>(eventData->objectForKey(CCStoreConsts::DICT_ELEMENT_PURCHASABLE));
  __String *payload = dynamic_cast<__String *>(eventData->objectForKey(CCStoreConsts::DICT_ELEMENT_DEVELOPERPAYLOAD));

  // ... your game specific implementation here ...
}
```

### EVENT_GOOD_EQUIPPED

This event is triggered when a virtual good equipping operation has been completed successfully.

```cpp
Director::getInstance()->getEventDispatcher()->addCustomEventListener(CCStoreConsts::EVENT_GOOD_EQUIPPED, CC_CALLBACK_1(Example::onGoodEquipped, this));

void Example::onGoodEquipped(EventCustom *event) {
  // DICT_ELEMENT_EQUIPPABLEVG - the virtual good that was just equipped

  __Dictionary *eventData = (__Dictionary *)event->getUserData();
  CCEquippableVG *equippable = dynamic_cast<CCEquippableVG *>(eventData->objectForKey(CCStoreConsts::DICT_ELEMENT_EQUIPPABLEVG));

  // ... your game specific implementation here ...
}
```

### EVENT_GOOD_UNEQUIPPED

This event is triggered when a virtual good un-equipping operation has been completed successfully.

```cpp
Director::getInstance()->getEventDispatcher()->addCustomEventListener(CCStoreConsts::EVENT_GOOD_UNEQUIPPED, CC_CALLBACK_1(Example::onGoodUnEquipped, this));

void Example::onGoodUnEquipped(EventCustom *event) {
  // DICT_ELEMENT_EQUIPPABLEVG - the virtual good that was just unequipped

  __Dictionary *eventData = (__Dictionary *)event->getUserData();
  CCEquippableVG *equippable = dynamic_cast<CCEquippableVG *>(eventData->objectForKey(CCStoreConsts::DICT_ELEMENT_EQUIPPABLEVG));

  // ... your game specific implementation here ...
}
```

### EVENT_GOOD_UPGRADE

This event is triggered when a virtual good upgrading operation has been completed successfully.

```cpp
Director::getInstance()->getEventDispatcher()->addCustomEventListener(CCStoreConsts::EVENT_GOOD_UPGRADE, CC_CALLBACK_1(Example::onGoodUpgrade, this));

void Example::onGoodUpgrade(EventCustom *event) {
  // DICT_ELEMENT_GOOD      - the virtual good that was just upgraded
  // DICT_ELEMENT_UPGRADEVG - the upgrade after the operation completed

  __Dictionary *eventData = (__Dictionary *)event->getUserData();
  CCVirtualGood *good = dynamic_cast<CCVirtualGood *>(eventData->objectForKey(CCStoreConsts::DICT_ELEMENT_GOOD));
  CCUpgradeVG *upgrade = dynamic_cast<CCUpgradeVG *>(eventData->objectForKey(CCStoreConsts::DICT_ELEMENT_UPGRADEVG));

  // ... your game specific implementation here ...
}
```

### EVENT_BILLING_SUPPORTED

This event is triggered when the billing service is initialized and ready.

```cpp
Director::getInstance()->getEventDispatcher()->addCustomEventListener(CCStoreConsts::EVENT_BILLING_SUPPORTED, CC_CALLBACK_1(Example::onBillingSupported, this));

void Example::onBillingSupported(EventCustom *event) {
  // ... your game specific implementation here ...
}
```

### EVENT_BILLING_NOT_SUPPORTED

This event is triggered when the billing service fails to initialize.

```cpp
Director::getInstance()->getEventDispatcher()->addCustomEventListener(CCStoreConsts::EVENT_BILLING_NOT_SUPPORTED, CC_CALLBACK_1(Example::onBillingNotSupported, this));

void Example::onBillingNotSupported(EventCustom *event) {
  // ... your game specific implementation here ...
}
```

### EVENT_UNEXPECTED_ERROR_IN_STORE

This event is triggered an unexpected error occurs in the Store.

```cpp
Director::getInstance()->getEventDispatcher()->addCustomEventListener(CCStoreConsts::EVENT_UNEXPECTED_ERROR_IN_STORE, CC_CALLBACK_1(Example::onUnexpectedErrorInStore, this));

void Example::onUnexpectedErrorInStore(EventCustom *event) {
  // DICT_ELEMENT_ERROR_MESSAGE - the description of the error

  __Dictionary *eventData = (__Dictionary *)event->getUserData();
  __String *errorMessage = dynamic_cast<__String *>(eventData->objectForKey(CCStoreConsts::DICT_ELEMENT_ERROR_MESSAGE));

  // ... your game specific implementation here ...
}
```

### Android Specific Events

#### EVENT_IAB_SERVICE_STARTED

This event is triggered when the in-app billing service is started.

```cpp
Director::getInstance()->getEventDispatcher()->addCustomEventListener(CCStoreConsts::EVENT_IAB_SERVICE_STARTED, CC_CALLBACK_1(Example::onIabServiceStarted, this));

void Example::onIabServiceStarted(EventCustom *event) {
  // ... your game specific implementation here ...
}
```

#### EVENT_IAB_SERVICE_STOPPED

This event is triggered when the in-app billing service is stopped.

```cpp
Director::getInstance()->getEventDispatcher()->addCustomEventListener(CCStoreConsts::EVENT_IAB_SERVICE_STOPPED, CC_CALLBACK_1(Example::onIabServiceStopped, this));

void Example::onIabServiceStopped(EventCustom *event) {
  // ... your game specific implementation here ...
}
```

#### EVENT_MARKET_REFUND

This event is triggered when a market refund operation has been completed successfully.

```cpp
Director::getInstance()->getEventDispatcher()->addCustomEventListener(CCStoreConsts::EVENT_MARKET_REFUND, CC_CALLBACK_1(Example::onMarketRefund, this));

void Example::onMarketRefund(EventCustom *event) {
  // DICT_ELEMENT_PURCHASABLE - the PurchasableVirtualItem to refund

  __Dictionary *eventData = (__Dictionary *)event->getUserData();
  CCPurchasableVirtualItem *purchasable = dynamic_cast<CCPurchasableVirtualItem *>(eventData->objectForKey(CCStoreConsts::DICT_ELEMENT_PURCHASABLE));

  // ... your game specific implementation here ...
}
```
