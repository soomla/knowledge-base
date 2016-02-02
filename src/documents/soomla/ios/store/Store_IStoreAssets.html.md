---
layout: "content"
image: "Game"
title: "IStoreAssets"
text: "To use SOOMLA, you'll need to create your own implementation of IStoreAssets, an interface that represents your game’s economy."
position: 2
theme: 'platforms'
collection: 'soomla_ios_store'
module: 'store'
platform: 'ios'
---

# IStoreAssets

## What is IStoreAssets?

`IStoreAssets` is an interface that represents a single game’s economy. When you use SOOMLA to create your game economy, one of the first steps to take is to create your own implementation of `IStoreAssets`. This is where you declare all of your virtual currencies, virtual currency packs, and virtual goods of all kinds.

### Guidelines

- Each item in `IStoreAssets` has a unique item ID that is used to identify it in various functions throughout the SDK. Make sure your item IDs do NOT contain periods (.).

- Make sure to implement ALL functions, even if you don't need some of them, just have them return an empty array.

- SOOMLA Store initialization will be stopped if some of methods in `IStoreAssets` will return null-pointer reference. Even if you don't need some of them, just have them return an empty array.

### Example

This is a brief example of `IStoreAssets`, our [MuffinRush Example](https://github.com/soomla/ios-store/blob/master/SoomlaiOSStoreExample/SoomlaiOSStoreExample/MuffinRushAssets.m) contains a more detailed implementation. In the [Functions section](#functions) below, you'll find explanations of the functions in `IStoreAssets`.

<br>

#### `ExampleAssets.h`

``` objectivec
@interface ExampleAssets : NSObject <IStoreAssets>{

}

@end
```

<br>

#### `ExampleAssets.m`

``` objectivec
@implementation ExampleAssets

+ (void)initialize {

  /** Virtual Currencies **/

  COIN_CURRENCY = [[VirtualCurrency alloc]
    initWithName:@"Coin"
    andDescription:@"Coin currency"
    andItemId:@"coin_currency_ID"];

  /** Virtual Currency Packs **/

  // This good needs to be made available in the App Store, because its purchase type is with
  // real money $$$.
  HUND_COIN_PACK = [[VirtualCurrencyPack alloc]
    initWithName:@"100 Coins"
    andDescription:@"Pack of 100 coins"
    andItemId:@"coins_100_ID"
    andCurrencyAmount:100
    andCurrency:@"coin_currency_ID"
    andPurchaseType:[[PurchaseWithMarket alloc]
      initWithMarketItem:[[MarketItem alloc]
        initWithProductId:@"coins_100_PROD_ID"
        andConsumable:kConsumable
        andPrice:1.99]]];

  /** Virtual Goods **/

  // A shield that can be purchased for 225 coins.
  SHIELD_GOOD = [[SingleUseVG alloc]
    initWithName:@"Shield"
    andDescription:@"Protect yourself from enemies"
    andItemId:@"shield_ID"
    andPurchaseType:[[PurchaseWithVirtualItem alloc]
      initWithVirtualItem:@"coin_currency_ID"
      andAmount:225]];

  // NOTE: Create non-consumable items using LifeTimeVG with PurchaseType of PurchaseWithMarket.
  NO_ADS_LTVG = [[LifetimeVG alloc]
    initWithName:@"No Ads"
    andDescription:@"No More Ads!"
    andItemId:@"no_ads_ID"
    andPurchaseType:[[PurchaseWithMarket alloc]
      initWithProductId:@"no_ads_PROD_ID"
      andPrice:0.99]];

  /** Virtual Categories **/

  GENERAL_CATEGORY  = [[VirtualCategory alloc]
    initWithName:@"Muffins"
    andGoodsItemIds:@[SHIELD_GOOD.itemId]];

}

- (int)getVersion {
  return 0;
}

- (NSArray*)virtualCurrencies{
  return @[COIN_CURRENCY];
}

- (NSArray*)virtualGoods{
  return @[SHIELD_GOOD, NO_ADS_LTVG];
}

- (NSArray*)virtualCurrencyPacks{
  return @[HUND_COIN_PACK];
}

- (NSArray*)virtualCategories{
  return @[GENERAL_CATEGORY];
}

@end
```

After you’ve implemented your version of `IStoreAssets`, you’ll need to initialize `SoomlaStore` with the implementation you just created (also explained in the [Getting Started](/ios/store/Store_GettingStarted) tutorial).

``` objectivec
[[SoomlaStore getInstance] initializeWithStoreAssets:[[YourImplementationAssets alloc] init]];
```

`IStoreAssets` is transformed into metadata in JSON format and saved in a local SQLite database on the device. After that, the economy is always loaded from the database. More about this in [Storage](/ios/store/Store_Storage).

## Functions

### `getVersion`

This value will determine if the saved data in the database will be deleted or not. Bump the version every time you want to delete the old data in the DB. If you don't bump this value, you won't be able to see changes you've made to the objects in your implementation of `IStoreAssets`.

For example: If you previously created a `VirtualGood` with the name "Hat" and you published your application, the name "Hat” will be saved in all of your users' databases. If you want to change the name to "Green_hat" then you'll also have to bump the version (increase the number that the `getVersion` function returns). Now the new "Green_hat" name will replace the old one.

### `getCurrencies`

Create an instance of all your desired virtual currencies. For every `VirtualCurrency`, you'll have to provide: name, description and item ID. Use this functions to access your game's currencies.

### `getCurrencyPacks`

Create an instance of all your desired `VirtualCurrencyPack`s. For every `VirtualCurrencyPack`, you'll have to provide: name, description, item ID, purchase type, currency amount (the amount of currencies in the pack), and currency item ID (the item ID of the associated currency). Use this functions to access your game's currency packs.

### `getGoods`

Create an instance of all your desired `VirtualGood`s. For every good, you'll have to provide: name, description, item ID and purchase type. Use this function to access your game's virtual goods.

### `getCategories`

Create an instance of all your desired virtual categories. For every `VirtualCategory`, you'll have to provide: name and goods item IDs (list of item IDs of the `VirtualGood`s in this category). Use this function to access your game's categories.

<div class="info-box">If you don't want to categorize your `VirtualGood`s, just add one 'GENERAL' `VirtualCategory`.</div>
