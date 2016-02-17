---
layout: "soomla-content"
image: "Events"
title: "Events"
text: "Learn how to observe and handle economy events triggered by android-store to customize your game-specific behavior."
position: 5
theme: 'soomla'
collection: 'soomla_android_store'
module: 'store'
platform: 'android'
---

# Event Handling

## About

SOOMLA's android-store allows you to subscribe to events, be notified when they occur, and implement your own application-specific behavior to handle them once they occur.

<div class="info-box">Your game-specific behavior is an addition to the default behavior implemented by SOOMLA. You don't replace SOOMLA's behavior.</div>

## How it Works

Events are triggered when SOOMLA wants to notify you about different things that happen involving Store operations.

For example, when a user purchases an item using virtual currency, his/her currency balance changes, causing a `CurrencyBalanceChangedEvent` to be fired.

## Observing & Handling Events

For event handling, SOOMLA uses Square's great open-source project [Otto](http://square.github.io/otto/). In order to be notified of profile related events, you can register for specific events and create your game-specific behavior to handle them.

**In order to register for & handle events:**

1. In the class that should receive the event, create your event handler function with the annotation '@Subscribe'.

  For example:

  ``` java
  @Subscribe
  public void onCurrencyBalanceChangedEvent(
        CurrencyBalanceChangedEvent currencyBalanceChangedEvent) {
    // ... your game specific implementation here ...
  }
  ```

2. You'll also have to register your **event handler class** in the event bus:

  ``` java
  BusProvider.getInstance().register(this);
  ```

  and unregister when needed:

  ``` java
  BusProvider.getInstance().unregister(this);
  ```

  <div class="info-box">If your class is an Activity, register in 'onResume' and unregister in 'onPause'.</div>

## Store Events

Below is a list of all the events in SOOMLA Store and an example of how to observe & handle them. See a full [event handler example](https://github.com/soomla/android-profile/blob/master/SoomlaAndroidExample/src/com/soomla/example/ExampleEventHandler.java).

### SoomlaStoreInitializedEvent

This event is triggered when the Soomla Store module is initialized and ready.

``` java
@Subscribe
public void onSoomlaStoreInitialized(SoomlaStoreInitializedEvent soomlaStoreInitializedEvent) {
  // ... your game specific implementation here ...
}
```

<div class="warning-box">NOTE: Make sure to initialize the event handler for `SoomlaStoreInitializedEvent` before initializing SoomlaStore.</div>

### CurrencyBalanceChangedEvent

This event is triggered when the balance of a specific currency has changed.

``` java
@Subscribe
public void onCurrencyBalanceChanged(CurrencyBalanceChangedEvent currencyBalanceChangedEvent) {
  // currencyBalanceChangedEvent contains:
  //   itemId - the currency whose balance has changed
  //   balance - the currency's balance after the change
  //   amountAdded - the change in balance that caused the event to be triggered

  // ... your game specific implementation here ...
}
```

### GoodBalanceChangedEvent

This event is triggered when the balance of a specific virtual good has changed.

``` java
@Subscribe
public void onGoodBalanceChanged(GoodBalanceChangedEvent goodBalanceChangedEvent) {
  // goodBalanceChangedEvent contains:
  //   itemId - the good whose balance has changed
  //   balance - the good's balance after the change
  //   amountAdded - the change in balance that caused the event to be triggered

  // ... your game specific implementation here ...
}
```

### MarketPurchaseStartedEvent

This event is triggered when a market purchase operation has started.

``` java
@Subscribe
public void onMarketPurchaseStarted(MarketPurchaseStartedEvent marketPurchaseStartedEvent) {
  // marketPurchaseStartedEvent contains:
  //   purchasableVirtualItem - the item whose purchase process has started

  // ... your game specific implementation here ...
}
```

### MarketPurchaseEvent

This event is triggered when a market purchase operation has completed successfully.

``` java
@Subscribe
public void onMarketPurchase(MarketPurchaseEvent marketPurchaseEvent) {
  // marketPurchaseEvent contains:
  //   PurchasableVirtualItem - the item that was purchased
  //   Payload - text that you can provide when you initiate the purchase operation
  //             and receive back upon completion
  //   ExtraInfo - a hash of extra information associated with the in-app billing purchase.
  //          possible keys are: 'token', 'orderId', 'originalJson', 'signature', 'userId'

  // ... your game specific implementation here ...
}
```

### MarketPurchaseCancelledEvent

This event is triggered when a market purchase operation has been cancelled by the user.

``` java
@Subscribe
public void onMarketPurchaseCancelled(MarketPurchaseCancelledEvent marketPurchaseCancelledEvent) {
  // marketPurchaseCancelledEvent contains:
  //   purchasableVirtualItem - the item whose purchase was cancelled

  // ... your game specific implementation here ...
}
```

### MarketRefundEvent

This event is triggered when a market refund operation has been completed successfully.

``` java
@Subscribe
public void onMarketRefund(MarketRefundEvent marketRefundEvent) {
  // marketRefundEvent contains:
  //   purchasableVirtualItem - the item whose purchase was refunded
  //   payload - text that you can provide when you initiate the refund operation
  //             and receive back upon completion

  // ... your game specific implementation here ...
}
```

### MarketItemsRefreshStartedEvent

This event is triggered when a refresh market items operation has started.

``` java
@Subscribe
public void onMarketItemsRefreshStarted(
      MarketItemsRefreshStartedEvent marketItemsRefreshStartedEvent) {
  // ... your game specific implementation here ...
}
```

### MarketItemsRefreshFinishedEvent

This event is triggered when a refresh market items operation has finished.

``` java
@Subscribe
public void onMarketItemsRefreshFinished(
      MarketItemsRefreshFinishedEvent marketItemsRefreshFinishedEvent) {
  // marketItemsRefreshFinishedEvent contains:
  //   List<MarketItem> marketItems - a list of MarketItems that was fetched from the market
  //                                  (Google Play, Amazon, etc...)

  // ... your game specific implementation here ...
}
```

### RestoreTransactionsStartedEvent

This event is triggered when the Soomla Store module is initialized and ready.

``` java
@Subscribe
public void onRestoreTransactionsStarted(
      RestoreTransactionsStartedEvent restoreTransactionsStartedEvent) {
  // ... your game specific implementation here ...
}
```

### RestoreTransactionsFinishedEvent

This event is triggered when a restore transactions operation has completed successfully.

``` java
@Subscribe
public void onRestoreTransactionsFinished(
      RestoreTransactionsFinishedEvent restoreTransactionsFinishedEvent) {
  // restoreTransactionsFinishedEvent contains:
  //   success - true if the RestoreTransactions operation finished successfully

  // ... your game specific implementation here ...
}
```

### ItemPurchaseStartedEvent

This event is triggered when an item purchase operation has started.

``` java
@Subscribe
public void onItemPurchaseStarted(ItemPurchaseStartedEvent itemPurchaseStartedEvent) {
  // itemPurchaseStartedEvent contains:
  //   itemId - ID of the item whose purchase process has started

  // ... your game specific implementation here ...
}
```

### ItemPurchasedEvent

This event is triggered when an item purchase operation has completed successfully.

``` java
@Subscribe
public void onItemPurchased(ItemPurchasedEvent itemPurchasedEvent) {
  // itemPurchasedEvent contains:
  //   itemId - ID of the item that was purchased
  //   payload - text that you can provide when you initiate the purchase operation
  //             and receive back upon completion

  // ... your game specific implementation here ...
}
```

### GoodEquippedEvent

This event is triggered when a virtual good equipping operation has been completed successfully.

``` java
@Subscribe
public void onGoodEquipped(GoodEquippedEvent goodEquippedEvent) {
  // goodEquippedEvent contains:
  //   itemId - ID of the good that was equipped

  // ... your game specific implementation here ...
}
```

### GoodUnEquippedEvent

This event is triggered when a virtual good un-equipping operation has been completed successfully.

``` java
@Subscribe
public void onGoodUnEquipped(GoodUnEquippedEvent goodUnEquippedEvent) {
  // goodUnEquippedEvent contains:
  //   itemId - ID of the good that was unequipped

  // ... your game specific implementation here ...
}
```

### GoodUpgradeEvent

This event is triggered when a virtual good upgrading operation has been completed successfully.

``` java
@Subscribe
public void onGoodUpgrade(GoodUpgradeEvent goodUpgradeEvent) {
  // goodUpgradeEvent contains:
  //   itemId - ID of the good that was upgraded
  //   upgradeVGItemId - details about the upgrade

  // ... your game specific implementation here ...
}
```

### BillingSupportedEvent

This event is triggered when the billing service is initialized and ready.

``` java
@Subscribe
public void onBillingSupported(BillingSupportedEvent billingSupportedEvent) {
  // ... your game specific implementation here ...
}
```

### BillingNotSupportedEvent

This event is triggered when the billing service fails to initialize.

``` java
@Subscribe
public void onBillingNotSupported(BillingNotSupportedEvent billingNotSupportedEvent) {
  // ... your game specific implementation here ...
}
```

### UnexpectedStoreErrorEvent

This event is triggered an unexpected error occurs in the Store.

``` java
@Subscribe
public void onUnexpectedStoreError(UnexpectedStoreErrorEvent unexpectedStoreErrorEvent) {
  // unexpectedStoreErrorEvent contains:
  // errorCode - code of the error:  
  //    VERIFICATION_FAIL - something is going wrong while SOOMLA tried to verify purchase.
  //    VERIFICATION_TIMEOUT - app didn't receive validation response from server in time. Please, try again later.
  //    PURCHASE_FAIL - something is going wrong while SOOMLA tried to make purchase.
  //    GENERAL - other types of error. See details in app logs.

  // ... your game specific implementation here ...
}
```

### IabServiceStartedEvent

This event is triggered when the in-app billing service is started.

``` java
@Subscribe
public void onIabServiceStarted(IabServiceStartedEvent iabServiceStartedEvent) {
  // ... your game specific implementation here ...
}
```

### IabServiceStoppedEvent

This event is triggered when the in-app billing service is stopped.

``` java
@Subscribe
public void onIabServiceStopped(IabServiceStoppedEvent iabServiceStoppedEvent) {
  // ... your game specific implementation here ...
}
```
