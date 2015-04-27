---
layout: "content"
image: "Events"
title: "Events"
text: "Learn how to observe and handle economy events triggered by cocos2dx-store to customize your game-specific behavior."
position: 5
theme: 'platforms'
collection: 'cocos2djs_store'
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

SOOMLA uses the common way of dispatching events used in Cocos2d-x.
The names of such events are defined in `Soomla.StoreConsts`, the data of the event are passes as arguments to the handlers. You can subscribe to any event from anywhere in your code.

** Subscribing **

Subscribe to events calling `Soomla.addHandler(eventName, handler, target)`, 
where `handler` - is a function that will be called when event is fired, and `target` - is "thisArg" used in that call. 

```js
Soomla.addHandler(Soomla.StoreConsts.EVENT_ITEM_PURCHASED, this.onItemPurchased, this);
```

** Handling **

Handle the event through your own custom function:

```js
onItemPurchased: function (purchasableVirtualItem) {
  // your code is here
}
```


## Store Events

Below we provide a list of all events in Store.

### EVENT_SOOMLA_STORE_INITIALIZED

This event is triggered when the Soomla Store module is initialized and ready.

```js
Soomla.addHandler(Soomla.StoreConsts.EVENT_SOOMLA_STORE_INITIALIZED, this.onSoomlaStoreInitialized, this);

this.onSoomlaStoreInitialized = function () {
  // ... your game specific implementation here ...
}
```

**NOTE:** If you want to observe the `EVENT_SOOMLA_STORE_INITIALIZED` event you have to set up the listener before you initialize `SoomlaStore`. So, put the following code:

```js
Soomla.addHandler(Soomla.StoreConsts.EVENT_SOOMLA_STORE_INITIALIZED, this.onSoomlaStoreInitialized, this);
```

before

```js
Soomla.SoomlaStore.initialize(assets, storeParams);
```

### EVENT_CURRENCY_BALANCE_CHANGED

This event is triggered when the balance of a specific currency has changed.

```js
Soomla.addHandler(Soomla.StoreConsts.EVENT_SOOMLA_STORE_INITIALIZED, this.onCurrencyBalanceChanged, this);

this.onCurrencyBalanceChanged = function (virtualCurrency, balance, amountAdded) {
    // virtualCurrency    - the currency whose balance was changed
    // balance            - the balance of the currency after the change
    // amountAdded        - the amount that was added to the currency balance
    //    (in case the number of currencies was removed this will be a negative value)

    // ... your game specific implementation here ...
}
```

### EVENT_GOOD_BALANCE_CHANGED

This event is triggered when the balance of a specific virtual good has changed.

```js
Soomla.addHandler(Soomla.StoreConsts.EVENT_GOOD_BALANCE_CHANGED, this.onGoodBalanceChanged, this);

this.onGoodBalanceChanged = function (virtualGood, balance, amountAdded) {
    // virtualGood        - the virtual good whose balance was changed
    // balance            - the balance of the good after the change
    // amountAdded        - the amount that was added to the good balance
    //    (in case the number of goods was removed this will be a negative value)

    // ... your game specific implementation here ...
}
```

### EVENT_MARKET_PURCHASE_STARTED

This event is triggered when a market purchase operation has started.

```js
Soomla.addHandler(Soomla.StoreConsts.EVENT_MARKET_PURCHASE_STARTED, this.onMarketPurchaseStarted, this);

this.onMarketPurchaseStarted = function (purchasableVirtualItem) {
    // purchasableVirtualItem   - the PurchasableVirtualItem whose purchase operation has just started

    // ... your game specific implementation here ...
}
```

### EVENT_MARKET_PURCHASE

This event is triggered when a market purchase operation has completed successfully.

```js
Soomla.addHandler(Soomla.StoreConsts.EVENT_MARKET_PURCHASE, this.onMarketPurchase, this);

this.onMarketPurchase = function (purchasableVirtualItem, token, payload) {
    // purchasableVirtualItem   - the PurchasableVirtualItem whose purchase operation has just started
    // token                    - The purchase token
    // payload                  - a text that you can give when you initiate the purchase operation and you want to receive back upon completion

    // ... your game specific implementation here ...
}
```

### EVENT_MARKET_PURCHASE_CANCELED

This event is triggered when a market purchase operation has been cancelled by the user.

```js
Soomla.addHandler(Soomla.StoreConsts.EVENT_MARKET_PURCHASE_CANCELED, this.onMarketPurchaseCancelled, this);

this.onMarketPurchaseCancelled = function (purchasableVirtualItem) {
    // purchasableVirtualItem   - the PurchasableVirtualItem whose purchase operation was cancelled

    // ... your game specific implementation here ...
}
```

### EVENT_MARKET_PURCHASE_VERIFICATION

This event is triggered when a market purchase verification process has started.

```js
Soomla.addHandler(Soomla.StoreConsts.EVENT_MARKET_PURCHASE_VERIFICATION, this.onMarketPurchaseVerification, this);

this.onMarketPurchaseVerification = function (purchasableVirtualItem) {
    // purchasableVirtualItem   - the PurchasableVirtualItem whose purchase is being verified

    // ... your game specific implementation here ...
}
```

### EVENT_MARKET_ITEMS_REFRESH_STARTED

This event is triggered when a refresh market items operation has started.

```js
Soomla.addHandler(Soomla.StoreConsts.EVENT_MARKET_ITEMS_REFRESH_STARTED, this.onMarketItemsRefreshStarted, this);

this.onMarketItemsRefreshStarted = function () {
    // ... your game specific implementation here ...
}
```

### EVENT_MARKET_ITEMS_REFRESHED

This event is triggered when a refresh market items operation has finished.

```js
Soomla.addHandler(Soomla.StoreConsts.EVENT_MARKET_ITEMS_REFRESHED, this.onMarketItemsRefreshed, this);

this.onMarketItemsRefreshed = function (marketItems) {
    // marketItems   - the list of Market items that was fetched from the Market

    // ... your game specific implementation here ...
}
```

### EVENT_MARKET_ITEMS_REFRESH_FAILED

This event is triggered when a market item refreshed process has failed.

```js
Soomla.addHandler(Soomla.StoreConsts.EVENT_MARKET_ITEMS_REFRESH_FAILED, this.onMarketItemsRefreshFailed, this);

this.onMarketItemsRefreshFailed = function (errorMessage) {
    // errorMessage   - The error which caused the failure

    // ... your game specific implementation here ...
}
```

### EVENT_RESTORE_TRANSACTION_STARTED

This event is triggered when a restore transactions operation has started.

```js
Soomla.addHandler(Soomla.StoreConsts.EVENT_RESTORE_TRANSACTION_STARTED, this.onMarketItemsRefreshStarted, this);

this.onMarketItemsRefreshStarted = function () {
    // ... your game specific implementation here ...
}
```

### EVENT_RESTORE_TRANSACTION_FINISHED

This event is triggered when a restore transactions operation has completed successfully.

```js
Soomla.addHandler(Soomla.StoreConsts.EVENT_RESTORE_TRANSACTION_FINISHED, this.onRestoreTransactionsFinished, this);

this.onRestoreTransactionsFinished = function (success) {
    // success   - true if the restore transactions operation has succeeded

    // ... your game specific implementation here ...
}
```

### EVENT_ITEM_PURCHASE_STARTED

This event is triggered when an item purchase operation has started.

```js
Soomla.addHandler(Soomla.StoreConsts.EVENT_ITEM_PURCHASE_STARTED, this.onItemPurchaseStarted, this);

this.onItemPurchaseStarted = function (purchasableVirtualItem) {
    // purchasableVirtualItem   - the PurchasableVirtualItem whose purchase operation has just started

    // ... your game specific implementation here ...
}
```

### EVENT_ITEM_PURCHASED

This event is triggered when an item purchase operation has completed successfully.

```js
Soomla.addHandler(Soomla.StoreConsts.EVENT_ITEM_PURCHASED, this.onItemPurchased, this);

this.onItemPurchased = function (purchasableVirtualItem) {
    // purchasableVirtualItem   - the PurchasableVirtualItem that was just purchased

    // ... your game specific implementation here ...
}
```

### EVENT_GOOD_EQUIPPED

This event is triggered when a virtual good equipping operation has been completed successfully.

```js
Soomla.addHandler(Soomla.StoreConsts.EVENT_GOOD_EQUIPPED, this.onGoodEquipped, this);

this.onGoodEquipped = function (equippableVG) {
    // equippableVG   - the virtual good that was just equipped

    // ... your game specific implementation here ...
}
```

### EVENT_GOOD_UNEQUIPPED

This event is triggered when a virtual good un-equipping operation has been completed successfully.

```js
Soomla.addHandler(Soomla.StoreConsts.EVENT_GOOD_UNEQUIPPED, this.onGoodUnEquipped, this);

this.onGoodUnEquipped = function (equippableVG) {
    // equippableVG   - the virtual good that was just unequipped

    // ... your game specific implementation here ...
}
```

### EVENT_GOOD_UPGRADE

This event is triggered when a virtual good upgrading operation has been completed successfully.

```js
Soomla.addHandler(Soomla.StoreConsts.EVENT_GOOD_UPGRADE, this.onGoodUpgrade, this);

this.onGoodUpgrade = function (virtualGood, upgradeVG) {
    // virtualGood  - the virtual good that was just upgraded
    // upgradeVG    - the upgrade after the operation completed

    // ... your game specific implementation here ...
}
```

### EVENT_BILLING_SUPPORTED

This event is triggered when the billing service is initialized and ready.

```js
Soomla.addHandler(Soomla.StoreConsts.EVENT_BILLING_SUPPORTED, this.onBillingSupported, this);

this.onBillingSupported = function () {
    // ... your game specific implementation here ...
}
```

### EVENT_BILLING_NOT_SUPPORTED

This event is triggered when the billing service fails to initialize.

```js
Soomla.addHandler(Soomla.StoreConsts.EVENT_BILLING_NOT_SUPPORTED, this.onBillingNotSupported, this);

this.onBillingNotSupported = function () {
    // ... your game specific implementation here ...
}
```

### EVENT_UNEXPECTED_ERROR_IN_STORE

This event is triggered an unexpected error occurs in the Store.

```js
Soomla.addHandler(Soomla.StoreConsts.EVENT_UNEXPECTED_ERROR_IN_STORE, this.onUnexpectedErrorInStore, this);

this.onUnexpectedErrorInStore = function () {
    // ... your game specific implementation here ...
}
```

### Android Specific Events

#### EVENT_IAB_SERVICE_STARTED

This event is triggered when the in-app billing service is started.

```js
Soomla.addHandler(Soomla.StoreConsts.EVENT_IAB_SERVICE_STARTED, this.onIabServiceStarted, this);

this.onIabServiceStarted = function () {
    // ... your game specific implementation here ...
}
```

#### EVENT_IAB_SERVICE_STOPPED

This event is triggered when the in-app billing service is stopped.

```js
Soomla.addHandler(Soomla.StoreConsts.EVENT_IAB_SERVICE_STOPPED, this.onIabServiceStarted, this);

this.onIabServiceStopped = function () {
    // ... your game specific implementation here ...
}
```

#### EVENT_MARKET_REFUND

This event is triggered when a market refund operation has been completed successfully.

```js
Soomla.addHandler(Soomla.StoreConsts.EVENT_MARKET_REFUND, this.onMarketRefund, this);

this.onMarketRefund = function (purchasableVirtualItem) {
    // purchasableVirtualItem  - the PurchasableVirtualItem to refund

    // ... your game specific implementation here ...
}
```