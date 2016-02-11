---
layout: "soomla-content"
image: "Events"
title: "Events"
text: "Learn how to observe and handle economy events triggered by ios-store to customize your game-specific behavior."
position: 5
theme: 'soomla-ios'
collection: 'soomla_ios_store'
module: 'store'
platform: 'ios'
---

# Event Handling

## About

SOOMLA's iOS-store allows you to subscribe to events, be notified when they occur, and implement your own application-specific behavior to handle them once they occur.

<div class="info-box">Your game-specific behavior is an addition to the default behavior implemented by SOOMLA. You don't replace SOOMLA's behavior.</div>

## How it Works

Events are triggered when SOOMLA wants to notify you about different things that happen involving Store operations.

For example, when a user purchases an item using virtual currency, his/her currency balance changes, causing a `CurrencyBalanceChangedEvent` to be fired.

## Observing & Handling Events

In order to observe store events you need to import `StoreEventHandling.h` and then you can add a notification to `NSNotificationCenter`:

``` objectivec
[[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(yourCustomSelector:)
  name:EVENT_ITEM_PURCHASED object:nil];
```

OR, you can observe all events with the same selector by calling:

``` objectivec
[EventHandling observeAllEventsWithObserver:self withSelector:@selector(yourCustomSelector:)];
```

Handle the events with your game-specific behavior. These handler functions will be called once the observer gets notified that an event has been triggered.

``` objectivec
- (void)yourCustomSelector:(NSNotification*)notification {
  // handle accordingly
}
```

## Store Events

### STORE INITIALIZED  

The event `EVENT_SOOMLASTORE_INIT` is triggered when the Soomla Store module is initialized and ready.

``` objectivec
// observe the event:
[[NSNotificationCenter defaultCenter] addObserver:self
  selector:@selector(soomlaStoreInitialized:) name:EVENT_SOOMLASTORE_INIT object:nil];

// your handler:
- (void)soomlaStoreInitialized:(NSNotification*)notification {
  // ... your game specific implementation here ...
}
```

### CURRENCY BALANCE CHANGED

The event `EVENT_CURRENCY_BALANCE_CHANGED` is triggered when the balance of a specific currency has changed.

``` objectivec
// observe the event:
[[NSNotificationCenter defaultCenter] addObserver:self
  selector:@selector(currencyBalanceChanged:) name:EVENT_CURRENCY_BALANCE_CHANGED object:nil];

// your handler:
- (void)currencyBalanceChanged:(NSNotification*)notification {
  // notification's userInfo contains the following keys:
  // DICT_ELEMENT_BALANCE      = The balance after the change
  // DICT_ELEMENT_CURRENCY     = The item ID of the currency whose balance was changed
  // DICT_ELEMENT_AMOUNT_ADDED = The amount added to the currency's balance

  // ... your game specific implementation here ...
}
```

### GOOD BALANCE CHANGED

The event `EVENT_GOOD_BALANCE_CHANGED` is triggered when the balance of a specific virtual good has changed.

``` objectivec
// observe the event:
[[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(goodBalanceChanged:)
  name:EVENT_GOOD_BALANCE_CHANGED object:nil];

// your handler:
- (void)goodBalanceChanged:(NSNotification*)notification {
  // notification's userInfo contains the following keys:
  // DICT_ELEMENT_BALANCE      = The balance after the change
  // DICT_ELEMENT_GOOD         = The item ID of the good whose balance was changed
  // DICT_ELEMENT_AMOUNT_ADDED = The amount added to the good's balance

  // ... your game specific implementation here ...
}
```

### MARKET PURCHASE STARTED

The event `EVENT_MARKET_PURCHASE_STARTED` is triggered when a market purchase operation has started.

``` objectivec
// observe the event:
[[NSNotificationCenter defaultCenter] addObserver:self
  selector:@selector(marketPurchaseStarted:) name:EVENT_MARKET_PURCHASE_STARTED object:nil];

// your handler:
- (void)marketPurchaseStarted:(NSNotification*)notification {
  // notification's userInfo contains the following keys:
  // DICT_ELEMENT_PURCHASABLE = The item whose purchase process has started

  // ... your game specific implementation here ...
}
```

### MARKET PURCHASE

The event `EVENT_MARKET_PURCHASED` is triggered when a market purchase operation has completed successfully.

``` objectivec
// observe the event:
[[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(marketPurchased:)
  name:EVENT_MARKET_PURCHASED object:nil];

// your handler:
- (void)marketPurchased:(NSNotification*)notification {
  // notification's userInfo contains the following keys:
  // DICT_ELEMENT_PURCHASABLE      = The item that was purchased
  // DICT_ELEMENT_RECEIPT          = URL to receipt of the purchase
  // DICT_ELEMENT_EXTRA_INFO       = A dictionary of extra information associated with the in-app billing purchase.
  //                                  possible keys are: 'receiptUrl', 'transactionIdentifier', 'receiptBase64', 'transactionDate', 'originalTransactionDate', 'originalTransactionIdentifier'

  // ... your game specific implementation here ...
}
```

### MARKET PURCHASE VERIFIED

The event `EVENT_MARKET_PURCHASE_VERIF` is triggered when a market purchase operation is verified.

``` objectivec
// observe the event:
[[NSNotificationCenter defaultCenter] addObserver:self
  selector:@selector(marketPurchaseVerified:) name:EVENT_MARKET_PURCHASE_VERIF object:nil];

// your handler:
- (void)marketPurchaseVerified:(NSNotification*)notification {
  // notification's userInfo contains the following keys:
  // DICT_ELEMENT_PURCHASABLE = The item whose purchase is being verified
  // DICT_ELEMENT_VERIFIED    = True if the market purchase verification operation has succeeded
  // DICT_ELEMENT_TRANSACTION = Transaction of the market purchase being verified

  // ... your game specific implementation here ...
}
```

### MARKET PURCHASE DEFERRED

The event `EVENT_MARKET_PURCHASE_DEFERRED` is triggered when a market purchase operation has been deferred using "Ask To Buy" feature.

``` objectivec
// observe the event:
[[NSNotificationCenter defaultCenter] addObserver:self
  selector:@selector(marketPurchaseDeferred:) name:EVENT_MARKET_PURCHASE_DEFERRED object:nil];

// your handler:
- (void)marketPurchaseDeferred:(NSNotification*)notification {
  // notification's userInfo contains the following keys:
  // DICT_ELEMENT_PURCHASABLE = The item whose purchase is being deferred
  // DICT_ELEMENT_DEVELOPERPAYLOAD = Text that you can provide when the item is purchased and
  //                                 receive back upon purchase completion

  // ... your game specific implementation here ...
}
```



### MARKET ITEMS REFRESH STARTED

The event `EVENT_MARKET_ITEMS_REFRESH_STARTED` is triggered when a refresh market items operation has started.

``` objectivec
// observe the event:
[[NSNotificationCenter defaultCenter] addObserver:self
  selector:@selector(marketItemsRefreshStarted:)
  name:EVENT_MARKET_ITEMS_REFRESH_STARTED object:nil];

// your handler:
- (void)marketItemsRefreshStarted:(NSNotification*)notification {
  // ... your game specific implementation here ...
}
```

### MARKET ITEMS REFRESH FINISHED

The event `EVENT_MARKET_ITEMS_REFRESH_FINISHED` is triggered when a refresh market items operation has finished.

``` objectivec
// observe the event:
[[NSNotificationCenter defaultCenter] addObserver:self
  selector:@selector(marketItemsRefreshFinished:)
  name:EVENT_MARKET_ITEMS_REFRESH_FINISHED object:nil];

// your handler:
- (void)marketItemsRefreshFinished:(NSNotification*)notification {
  // notification's userInfo contains the following keys:
  // DICT_ELEMENT_MARKET_ITEMS = List of MarketItems that was fetched from Apple

  // ... your game specific implementation here ...
}
```

### RESTORE TRANSACTIONS STARTED

The event `EVENT_RESTORE_TRANSACTIONS_STARTED` is triggered when the Soomla Store module is initialized and ready.

``` objectivec
// observe the event:
[[NSNotificationCenter defaultCenter] addObserver:self
  selector:@selector(restoreTransactionsStarted:)
  name:EVENT_RESTORE_TRANSACTIONS_STARTED object:nil];

// your handler:
- (void)restoreTransactionsStarted:(NSNotification*)notification {
  // ... your game specific implementation here ...
}
```

### RESTORE TRANSACTIONS FINISHED

The event `EVENT_RESTORE_TRANSACTIONS_FINISHED` is triggered when a restore transactions operation has completed successfully.

``` objectivec
// observe the event:
[[NSNotificationCenter defaultCenter] addObserver:self
  selector:@selector(restoreTransactionsFinished:)
  name:EVENT_RESTORE_TRANSACTIONS_FINISHED object:nil];

// your handler:
- (void)restoreTransactionsFinished:(NSNotification*)notification {
  // notification's userInfo contains the following keys:
  // DICT_ELEMENT_SUCCESS = True if the restore transactions operation finished successfully

  // ... your game specific implementation here ...
}
```

### ITEM PURCHASED STARTED

The event `EVENT_ITEM_PURCHASE_STARTED` is triggered when an item purchase operation has started.

``` objectivec
// observe the event:
[[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(itemPurchaseStarted:)
  name:EVENT_ITEM_PURCHASE_STARTED object:nil];

// your handler:
- (void)itemPurchaseStarted:(NSNotification*)notification {
  // notification's userInfo contains the following keys:
  // DICT_ELEMENT_PURCHASABLE_ID = The item ID of the item whose purchase operation has started

  // ... your game specific implementation here ...
}
```

### ITEM PURCHASED

The event `EVENT_ITEM_PURCHASED` is triggered when an item purchase operation has completed successfully.

``` objectivec
// observe the event:
[[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(itemPurchased:)
  name:EVENT_ITEM_PURCHASED object:nil];

// your handler:
- (void)itemPurchased:(NSNotification*)notification {
  // notification's userInfo contains the following keys:
  // DICT_ELEMENT_PURCHASABLE_ID   = The item ID of the item that was purchased
  // DICT_ELEMENT_DEVELOPERPAYLOAD = Text that you can provide when the item is purchased and
  //                                 receive back upon purchase completion

  // ... your game specific implementation here ...
}
```

### GOOD EQUIPPED

The event `EVENT_GOOD_EQUIPPED` is triggered when a virtual good equipping operation has been completed successfully.

``` objectivec
// observe the event:
[[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(goodEquipped:)
  name:EVENT_GOOD_EQUIPPED object:nil];

// your handler:
- (void)goodEquipped:(NSNotification*)notification {
  // notification's userInfo contains the following keys:
  // DICT_ELEMENT_EquippableVG = The item ID of the good being equipped

  // ... your game specific implementation here ...
}
```

### GOOD UNEQUIPPED

The event `EVENT_GOOD_UNEQUIPPED` is triggered when a virtual good un-equipping operation has been completed successfully.

``` objectivec
// observe the event:
[[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(goodUnequipped:)
  name:EVENT_GOOD_UNEQUIPPED object:nil];

// your handler:
- (void)goodUnequipped:(NSNotification*)notification {
  // notification's userInfo contains the following keys:
  // DICT_ELEMENT_EquippableVG = The item ID of the good being unequipped

  // ... your game specific implementation here ...
}
```

### GOOD UPGRADE

The event `EVENT_GOOD_UPGRADE` is triggered when a virtual good upgrading operation has been completed successfully.

``` objectivec
// observe the event:
[[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(goodUpgrade:)
  name:EVENT_GOOD_UPGRADE object:nil];

// your handler:
- (void)goodUpgrade:(NSNotification*)notification {
  // notification's userInfo contains the following keys:
  // DICT_ELEMENT_GOOD = The item ID of the good being upgraded
  // DICT_ELEMENT_UpgradeVG = The item ID of the UPGRADE good itself

  // ... your game specific implementation here ...
}
```

### BILLING SUPPORTED

The event `EVENT_BILLING_SUPPORTED` is triggered when the billing service is initialized and ready.

``` objectivec
// observe the event:
[[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(billingSupported:)
  name:EVENT_BILLING_SUPPORTED object:nil];

// your handler:
- (void)billingSupported:(NSNotification*)notification {
  // ... your game specific implementation here ...
}
```

### BILLING NOT SUPPORTED

The event `EVENT_BILLING_NOT_SUPPORTED` is triggered when the billing service fails to initialize.

``` objectivec
// observe the event:
[[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(billingNotSupported:)
  name:EVENT_BILLING_NOT_SUPPORTED object:nil];

// your handler:
- (void)billingNotSupported:(NSNotification*)notification {
  // ... your game specific implementation here ...
}
```

### UNEXPECTED STORE ERROR

The event `EVENT_UNEXPECTED_ERROR_IN_STORE` is triggered an unexpected error occurs in the Store.

``` objectivec
// observe the event:
[[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(unexpectedStoreError:)
  name:EVENT_UNEXPECTED_ERROR_IN_STORE object:nil];

// your handler:
- (void)unexpectedStoreError:(NSNotification*)notification {
  // notification's userInfo contains the following keys:
  // DICT_ELEMENT_ERROR_CODE = Numeric error code
  //    ERR_VERIFICATION_FAIL - something is going wrong while SOOMLA tried to verify purchase.
  //    ERR_VERIFICATION_TIMEOUT - app didn't receive validation response from server in time. Please, try again later.
  //    ERR_PURCHASE_FAIL - something is going wrong while SOOMLA tried to make purchase.
  //    ERR_GENERAL - other types of error. See details in app logs.

  // ... your game specific implementation here ...
}
```
