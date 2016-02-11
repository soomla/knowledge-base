---
layout: "soomla-content"
image: "Game"
title: "IStoreAssets"
text: "To use SOOMLA, you'll need to create your own implementation of IStoreAssets, an \"interface\" that represents your game’s economy."
position: 2
theme: 'soomla-cocos2dx'
collection: 'soomla_cocos2djs_store'
module: 'store'
lang: 'js'
platform: 'cocos2dx'
---

# IStoreAssets

## What is IStoreAssets?

`IStoreAssets` is an "interface" that represents a single game’s economy. When you use SOOMLA to create your game economy, one of the first steps to take is to create your own implementation of `IStoreAssets`. This is where you declare all of your virtual currencies, virtual currency packs, and virtual goods of all kinds.

### Guidelines

- Each item in `IStoreAssets` has a unique item ID that is used to identify it in various functions throughout the SDK. Make sure your item IDs do NOT contain periods (.).

- Make sure to implement ALL functions, even if you don't need some of them, just have them return an empty array.

- SOOMLA Store initialization will be stopped if some of methods in `IStoreAssets` will return null-pointer reference. Even if you don't need some of them, just have them return an empty array.

### Example

This is a brief example of `IStoreAssets`, our [MuffinRush Example](https://github.com/soomla/cocos2dx-js-store-example/blob/master/src/MuffinRushAssets.js) contains a more detailed implementation. In the [Fields section](#fields) below, you'll find explanations of the fields in `IStoreAssets`.

``` js
function ExampleAssets {
  /** Virtual Currencies **/
  var coinCurrency = Soomla.Models.VirtualCurrency.create({
      name: 'Coin',
      description: 'Coin currency',
      itemId: 'coin_currency_ID'
    });

  /** Virtual Currency Packs **/
  var hundCoinPack = Soomla.Models.VirtualCurrencyPack.create({
    name: "100 Coins",
    description: "100 coin currency units",
    itemId: "coins_100_ID",
    currency_amount: 100,
    currency_itemId: 'coin_currency_ID',
    purchasableItem: Soomla.Models.PurchaseWithMarket.createWithMarketItem('coins_100_PROD_ID', 1.99)
  });

  /** Virtual Goods **/
  var shieldGood = Soomla.Models.SingleUseVG.create({
    name: "Shield",
    description: "Protect yourself from enemies",
    itemId: "shield_ID",
    purchasableItem: Soomla.Models.PurchaseWithVirtualItem.create({
      pvi_itemId: 'coin_currency_ID',
      pvi_amount: 225
    })
  });

  var noAdsLTVG = Soomla.Models.LifetimeVG.create({
    name: 'No Ads',
    description: 'No More Ads!',
    itemId: 'no_ads_ID',
    purchasableItem: Soomla.Models.PurchaseWithVirtualItem.create({
      pvi_itemId: 'no_ads_PROD_ID',
      pvi_amount: 0.99
    })
  });


  /** Virtual Categories **/
  var generalCategory = Soomla.Models.VirtualCategory.create({
    name: 'General',
    goods_itemIds: [
     'shieldGood_ID'
    ]
  });

  var muffinRushAssets = Soomla.IStoreAssets.create({
    categories: [generalCategory],
    currencies: [coinCurrency],
    currencyPacks: [hundCoinPack],
    goods: {
      singleUse: [shieldGood],
      lifetime: [noAdsLTVG],
      equippable: [],
      goodUpgrades: [],
      goodPacks: []
    },
    version: 1
  });
};

```

<br>

After you’ve implemented your version of `IStoreAssets`, you’ll need to initialize `Soomla.SoomlaStore` with the implementation you just created (also explained in the [Getting Started](/cocos2dx/js/store/Store_GettingStarted) tutorial).

``` js
    var assets = ExampleAssets();
    Soomla.soomlaStore.initialize(assets, storeParams);     // storeParams is explained in the
                                                            // Getting Started tutorial
```

`IStoreAssets` is transformed into metadata in JSON format and saved in a local SQLite database on the device. After that, the economy is always loaded from the database. More about this in [Storage](/cocos2dx/js/store/Store_Storage).

## Fields

### `version`

This value will determine if the saved data in the database will be deleted or not. Bump the version every time you want to delete the old data in the DB. If you don't bump this value, you won't be able to see changes you've made to the objects in your implementation of `IStoreAssets`.

For example: If you previously created a `VirtualGood` with the name "Hat" and you published your application, the name "Hat” will be saved in all of your users' databases. If you want to change the name to "Green_hat" then you'll also have to bump the version (increase the number of the `version`). Now the new "Green_hat" name will replace the old one.

### `currencies`

Create an instance of all your desired virtual currencies. For every `VirtualCurrency`, you'll have to provide: name, description and item ID. Use this field to access your game's currencies.

### `currencyPacks`

Create an instance of all your desired `VirtualCurrencyPack`s. For every `VirtualCurrencyPack`, you'll have to provide: name, description, item ID, purchase type, currency amount (the amount of currencies in the pack), and currency item ID (the item ID of the associated currency). Use this field to access your game's currency packs.

### `goods`

Create an instance of all your desired `VirtualGood`s. For every good, you'll have to provide: name, description, item ID and purchase type.
Goods are divided into their types (`singleUse`, `lifetime`, `equippable`, `goodUpgrades`, `goodPacks`). You should use appropriate array for every your good. Use this field to access your game's virtual goods.

### `categories`

Create an instance of all your desired virtual categories. For every `VirtualCategory`, you'll have to provide: name and goods item IDs (list of item IDs of the `VirtualGood`s in this category). Use this field to access your game's categories.

<div class="info-box">If you don't want to categorize your `VirtualGood`s, just add one 'GENERAL' `VirtualCategory`.</div>
