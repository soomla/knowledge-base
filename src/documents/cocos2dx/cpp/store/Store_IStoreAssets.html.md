---
layout: "content"
image: "Game"
title: "CCStoreAssets"
text: "To use SOOMLA, you'll need to create your own implementation of CCStoreAssets, an interface that represents your game’s economy."
position: 2
theme: 'platforms'
collection: 'cocos2dx_store'
module: 'store'
platform: 'cocos2dx'
---

# CCStoreAssets

## What is CCStoreAssets?

`CCStoreAssets` is an interface that represents a single game’s economy. When you use SOOMLA to create your game economy, one of the first steps to take is to create your own implementation of `CCStoreAssets`. This is where you declare all of your virtual currencies, virtual currency packs, and virtual goods of all kinds.

### Guidelines

- Each item in `CCStoreAssets` has a unique item ID that is used to identify it in various functions throughout the SDK. Make sure your item IDs do NOT contain periods (.).

- Make sure to implement ALL functions, even if you don't need some of them, just have them return an empty array.

### Example

This is a brief example of `CCStoreAssets`, our [MuffinRush Example](https://github.com/soomla/cocos2dx-store-example/blob/master/Classes/MuffinRushAssets.cpp) contains a more detailed implementation. In the [Functions section](#functions) below, you'll find explanations of the functions in `CCStoreAssets`.

<br>

#### `ExampleAssets.h`

``` cpp
class ExampleAssets: public soomla::CCStoreAssets {

  cocos2d::__Array *mCurrencies;
  cocos2d::__Array *mGoods;
  cocos2d::__Array *mCurrencyPacks;
  cocos2d::__Array *mCategories;

public:

  ExampleAssets():
  mCurrencies(NULL),
  mGoods(NULL),
  mCurrencyPacks(NULL),
  mCategories(NULL)
  {}

  virtual ~ExampleAssets();

  static ExampleAssets *create();

  bool init();

  virtual int getVersion();

  virtual cocos2d::__Array *getCurrencies();

  virtual cocos2d::__Array *getGoods();

  virtual cocos2d::__Array *getCurrencyPacks();

  virtual cocos2d::__Array *getCategories();
};

```

<br>

#### `ExampleAssets.cpp`

``` cpp
...

bool MuffinRushAssets::init() {
  /** Virtual Currencies **/
  CCVirtualCurrency *coinCurrency = CCVirtualCurrency::create(
    __String::create("Coin"),                          // Name
    __String::create("Coin currency"),                 // Description
    __String::create("coin_currency_ID")               // Item ID
  );

  /** Virtual Currency Packs **/

  CCVirtualCurrencyPack *hundCoinPack = CCVirtualCurrencyPack::create(
    __String::create("100 Coins"),                     // Name
    __String::create("100 coin currency units"),       // Description
    __String::create("coins_100_ID"),                  // Item ID
    __Integer::create(100),                            // Number of currencies in the pack
    __String::create("coin_currency_ID"),              // The currency associated with this pack
    CCPurchaseWithMarket::create(                      // Purchase type (with real money $)
      __String::create("coins_100_PROD_ID"),              // Product ID
      __Double::create(1.99)                              // Price (in real money $)
    )
  );

  /** Virtual Goods **/

  CCVirtualGood *shieldGood = CCSingleUseVG::create(
    __String::create("Shield"),                        // Name
    __String::create("Protect yourself from enemies"), // Description
    __String::create("shield_ID"),                     // Item ID
    CCPurchaseWithVirtualItem::create(                 // Purchase type (with virtual currency)
      __String::create("coin_currency_ID"),               // ID of the item used to pay with
      __Integer::create(225)                              // Price (amount in coins)
    )
  );

  CCVirtualGood *noAdsLTVG = CCLifetimeVG::create(
    __String::create("No Ads"),                        // Name
    __String::create("No More Ads!"),                  // Description
    __String::create("no_ads_ID"),                     // Item ID
    CCPurchaseWithMarket::create(                      // Purchase type (with real money $)
      __String::create("no_ads_PROD_ID"),                 // Product ID
      __Double::create(0.99)                              // Price (in real money $)
    )
  );

  /** Virtual Categories **/

  CCVirtualCategory *generalCategory = CCVirtualCategory::create(
    __String::create("General"),
    __Array::create(__String::create("shieldGood_ID"), NULL));

  mCurrencies = __Array::create(coinCurrency, NULL);
  mCurrencies->retain();

  mGoods = __Array::create(shieldGood, noAdsLTVG, NULL);
  mGoods->retain();

  mCurrencyPacks = __Array::create(hundCoinPack, NULL);
  mCurrencyPacks->retain();

  mCategories = __Array::create(generalCategory, NULL);
  mCategories->retain();

  return true;
}

int MuffinRushAssets::getVersion() {
  return 0;
}

cocos2d::__Array *MuffinRushAssets::getCurrencies() {
  return mCurrencies;
}

cocos2d::__Array *MuffinRushAssets::getGoods() {
  return mGoods;
}

cocos2d::__Array *MuffinRushAssets::getCurrencyPacks() {
  return mCurrencyPacks;
}

cocos2d::__Array *MuffinRushAssets::getCategories() {
  return mCategories;
}

...

```

After you’ve implemented your version of `CCStoreAssets`, you’ll need to initialize `CCSoomlaStore` with the implementation you just created (also explained in the [Getting Started](/cocos2dx/cpp/store/Store_GettingStarted) tutorial).

``` cpp
ExampleAssets *assets = ExampleAssets::create();
soomla::CCSoomlaStore::initialize(assets, storeParams);  // storeParams is explained in the
                                                         // Getting Started tutorial
```

`CCStoreAssets` is transformed into metadata in JSON format and saved in a local SQLite database on the device. After that, the economy is always loaded from the database. More about this in [Storage](/cocos2dx/cpp/store/Store_Storage).

## Functions

### `getVersion`

This value will determine if the saved data in the database will be deleted or not. Bump the version every time you want to delete the old data in the DB. If you don't bump this value, you won't be able to see changes you've made to the objects in your implementation of `CCStoreAssets`.

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
