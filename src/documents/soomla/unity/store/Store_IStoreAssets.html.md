---
layout: "soomla-content"
image: "Game"
title: "IStoreAssets"
text: "To use SOOMLA, you'll need to create your own implementation of IStoreAssets, an interface that represents your game’s economy."
position: 2
theme: 'soomla-unity'
collection: 'soomla_unity_store'
module: 'store'
platform: 'unity'
---

# IStoreAssets

## What is IStoreAssets?

`IStoreAssets` is an interface that represents a single game’s economy. When you use SOOMLA to create your game economy, one of the first steps to take is to create your own implementation of `IStoreAssets`. This is where you declare all of your virtual currencies, virtual currency packs, and virtual goods of all kinds.

### Guidelines

- Each item in `IStoreAssets` has a unique item ID that is used to identify it in various functions throughout the SDK. Make sure your item IDs do NOT contain periods (.).

- Make sure to implement ALL functions, even if you don't need some of them, just have them return an empty array.

- SOOMLA Store initialization will be stopped if some of methods in `IStoreAssets` will return null-pointer reference. Even if you don't need some of them, just have them return an empty array.

### Example

This is a brief example of `IStoreAssets`, our [MuffinRush Example](https://github.com/soomla/unity3d-store/blob/master/Soomla/Assets/Examples/MuffinRush/MuffinRushAssets.cs) contains a more detailed implementation. In the [Functions section](#functions) below, you'll find explanations of the functions in `IStoreAssets`.

``` cs
public class ExampleAssets : IStoreAssets{

  ...

  public int GetVersion() {
    return 0;
  }

  // NOTE: Even if you have no use in one of these functions, you still need to
  // implement them all and just return an empty array.

  public VirtualCurrency[] GetCurrencies() {
    return new VirtualCurrency[]{COIN_CURRENCY};
  }

  public VirtualGood[] GetGoods() {
    return new VirtualGood[] {SHIELD_GOOD, NO_ADS_LTVG};
  }

  public VirtualCurrencyPack[] GetCurrencyPacks() {
    return new VirtualCurrencyPack[] {HUND_COIN_PACK};
  }

  public VirtualCategory[] GetCategories() {
    return new VirtualCategory[]{GENERAL_CATEGORY};
  }

  /** Virtual Currencies **/

  public static VirtualCurrency COIN_CURRENCY = new VirtualCurrency(
    "Coin",                               // Name
    "Coin currency",                      // Description
    "coin_currency_ID"                    // Item ID
  );

  /** Virtual Currency Packs **/

  public static VirtualCurrencyPack HUND_COIN_PACK = new VirtualCurrencyPack(
    "100 Coins",                          // Name
    "100 coin currency units",            // Description
    "coins_100_ID",                       // Item ID
    100,                                  // Number of currencies in the pack
    "coin_currency_ID",                   // ID of the currency associated with this pack
    new PurchaseWithMarket(               // Purchase type (with real money $$$)
      "coins_100_PROD_ID",                   // Product ID
      1.99                                   // Price (in real money $$$)
    )
  );

  /** Virtual Goods **/

  public static VirtualGood SHIELD_GOOD = new SingleUseVG(
    "Shield",                             // Name
    "Protect yourself from enemies",      // Description
    "shield_ID",                          // Item ID
    new PurchaseWithVirtualItem(          // Purchase type (with virtual currency)
     "coin_currency_ID",                     // ID of the item used to pay with
      225                                    // Price (amount of coins)
    )
  );

  // NOTE: Create non-consumable items using LifeTimeVG with PurchaseType of PurchaseWithMarket.
  public static VirtualGood NO_ADS_LTVG = new LifetimeVG(
    "No Ads",                             // Name
    "No More Ads!",                       // Description
    "no_ads_ID",                          // Item ID
    new PurchaseWithMarket(               // Purchase type (with real money $$$)
      "no_ads_PROD_ID",                      // Product ID
      0.99                                   // Price (in real money $$$)
    )
  );

  /** Virtual Categories **/

  public static VirtualCategory GENERAL_CATEGORY = new VirtualCategory(
    "General", new List<string>(new string[] {SHIELD_GOOD.ItemId})
  );

}
```

After you’ve implemented your version of `IStoreAssets`, you’ll need to initialize `SoomlaStore` with the implementation you just created (also explained in the [Getting Started](/unity/store/Store_GettingStarted) tutorial).

``` cs
SoomlaStore.Initialize(new ExampleAssets());
```

`IStoreAssets` is transformed into metadata in JSON format and saved in a local SQLite database on the device. After that, the economy is always loaded from the database. More about this in [Storage](/unity/store/Store_Storage).

## Functions

### `GetVersion`

This value will determine if the saved data in the database will be deleted or not. Bump the version every time you want to delete the old data in the DB. If you don't bump this value, you won't be able to see changes you've made to the objects in your implementation of `IStoreAssets`.

For example: If you previously created a `VirtualGood` with the name "Hat" and you published your application, the name "Hat” will be saved in all of your users' databases. If you want to change the name to "Green_hat" then you'll also have to bump the version (increase the number that the `GetVersion` function returns). Now the new "Green_hat" name will replace the old one.

### `GetCurrencies`

Create an instance of all your desired virtual currencies. For every `VirtualCurrency`, you'll have to provide: name, description and item ID. Use this functions to access your game's currencies.

### `GetCurrencyPacks`

Create an instance of all your desired `VirtualCurrencyPack`s. For every `VirtualCurrencyPack`, you'll have to provide: name, description, item ID, purchase type, currency amount (the amount of currencies in the pack), and currency item ID (the item ID of the associated currency). Use this functions to access your game's currency packs.

### `GetGoods`

Create an instance of all your desired `VirtualGood`s. For every good, you'll have to provide: name, description, item ID and purchase type. Use this function to access your game's virtual goods.

### `GetCategories`

Create an instance of all your desired virtual categories. For every `VirtualCategory`, you'll have to provide: name and goods item IDs (list of item IDs of the `VirtualGood`s in this category). Use this function to access your game's categories.

<div class="info-box">If you don't want to categorize your `VirtualGood`s, just add one 'GENERAL' `VirtualCategory`.</div>
