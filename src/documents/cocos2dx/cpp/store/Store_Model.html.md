---
layout: "content"
image: "Modeling"
title: "Economy Model & API"
text: "Every game economy can be based on SOOMLA's economy model. Learn the building blocks of creating a virtual economy with currencies and virtual goods."
position: 3
theme: 'platforms'
collection: 'cocos2dx_store'
module: 'store'
platform: 'cocos2dx'
---

#Economy Model & API

SOOMLA's cocos2dx-store provides a complete data model implementation for virtual economies. Every game economy has currencies, packs of currencies that can be sold, and items that can be sold either for money or in exchange for other items. And these are just the very basics, of course. This tutorial contains descriptions of each entity in the economy model, along with examples.

![alt text](/img/tutorial_img/soomla_diagrams/EconomyModel.png "Soomla Economy Model")

## Virtual Items

Almost every entity in your virtual economy will be a Virtual Item. There are many types of Virtual Items and you can select the ones that fit your needs. Each one of the various types extends the class `VirtualItem` and adds its own behavior.

Almost all `VirtualItems` are `PurchasableVirtualItems`. Among other features, all Virtual items have 2 functions to help you easily interact with them: `give` and `take`. Preferably, you should use the two methods provided in `StoreInventory` for these purposes, called `giveItem` and `takeItem`. Use these functions to give or take from your users a specific amount of a specific Virtual Item.

Use `giveItem` when you want to give your user something and get nothing in return. (If you want to give something and get something in return, you need to use `buyItem`). Use `takeItem` when you want to take something from your user, for example in the case of a refund.

Every virtual item has an ID, a unique string that we use to identify the different items.

## PurchaseTypes

As stated above, almost all Virtual Items are purchasable, or as we call them, `PurchasableVirtualItem`s. Purchase types are used to indicate whether an item will be purchased with money or with other virtual items: there are 2 different purchase types described below.

<div class="info-box">In the examples below the declarations of purchase types are shown as a part of `PurchasableVirtualItem` declarations, because this is the most common use of purchase types.</div>

###PurchaseWithMarket <a href="https://github.com/soomla/cocos2dx-store/blob/master/Soomla/PurchaseTypes/CCPurchaseWithMarket.h" target="_blank"><img class="link-icon" src="/img/tutorial_img/linkImg.png"></a>

This kind of `PurchaseType` should be attached to items that you want to make available for purchase in the Market (App Store, Google Play Store, etc..) for real money. When you create an instance of `PurchaseWithMarket`, you need to define the associated `VirtualItem` in the Market.

**NOTE:** The product ID you define in your implementation of `CCStoreAssets` should be the same as you define in the Market.

####**For Example**
Suppose that in your game, you offer a “No-Ads” feature for $1.99 in the Market. The code below shows how you need to declare the `PurchaseType` parameter of your “No-Ads” item.

``` cpp
CCLifetimeVG *noAds = CCLifetimeVG::create(
    ...
    CCPurchaseWithMarket::createWithMarketItem(             // Purchase type
        CCMarketItem::create(
            __String::create(NO_ADDS_PRODUCT_ID),           // Product ID
            __Integer::create(CCMarketItem::NONCONSUMABLE), // Product type
            __Double::create(1.99))                         // Initial price
));
```

There is another way to define this purchase type:

``` cpp
CCVirtualCurrencyPack *thousandmuffPack = CCVirtualCurrencyPack::create(
    ...
    CCPurchaseWithMarket::create(                           // Purchase type
        __String::create(THOUSANDMUFF_PACK_PRODUCT_ID),     // Product ID
        __Double::create(8.99))                             // Initial price
);
```

For more info on how to declare your items in the Market, see one of our tutorials on In-app Billing:

- For Android: [Google Play IAB](/android/store/Store_GooglePlayIAB) or [Amazon IAB](/android/store/Store_AmazonIAB)

- For iOS: [App Store IAB](/ios/store/Store_AppStoreIAB)

### PurchaseWithVirtualItem <a href="https://github.com/soomla/cocos2dx-store/blob/master/Soomla/PurchaseTypes/CCPurchaseWithVirtualItem.h" target="_blank"><img class="link-icon" src="/img/tutorial_img/linkImg.png"></a>

Any item with purchase type `PurchaseWithVirtualItem` can be purchased with any `VirtualItem`, like a sort of trade. When creating an instance of `PurchaseWithVirtualItem`, you need to provide the ID of the virtual item that you want to be paid with and the amount of that virtual item.

####**For Example**
Suppose that in your game, you offer a chocolate cake that can be bought by paying 250 “Muffins”. The item being purchased is a `chocolateCakeGood`, the item (virtual currency) to pay with is “Muffin”, and the amount is 250.

``` cpp
// A chocolate cake good can be purchased with 250 muffin currency units
CCVirtualGood *chocolateCakeGood = CCSingleUseVG::create(
    ...
    CCPurchaseWithVirtualItem::create(
        __String::create("muffinCurrency_ID"),              // Target item ID
        __Integer::create(250))                             // Initial price
);
```

## Virtual Currencies

### VirtualCurrency <a href="https://github.com/soomla/cocos2dx-store/blob/master/Soomla/domain/virtualCurrencies/CCVirtualCurrency.h" target="_blank"><img class="link-icon" src="/img/tutorial_img/linkImg.png"></a>

Every game that has an economy has at least one `VirtualCurrency`. `VirtualCurrency` is NOT a `PurchasableVirtualItem`. This is because in game stores, you never buy just a single "Gold Coin" or a "Muffin", but rather you buy a pack of them. Your users will be able to buy packs of your game’s `VirtualCurrency` by using `VirtualCurrencyPack` (explained later in this document). If for some reason you *do* want to sell a single currency you can do so by providing a `VirtualCurrencyPack` with an amount of 1.

####**How to define**

``` cpp

CCVirtualCurrency *muffinCurrency = CCVirtualCurrency::create(
    __String::create("Muffins"),                            // Name
    __String::create(""),                                   // Description
    __String::create("muffinCurrency_ID")                   // Item ID
);
```

####How to use

A `VirtualCurrency` by itself is not very useful, because it cannot be sold individually. To sell currency, you need to use a VirtualCurrencyPack (see section below).

``` cpp
//Use VirtualCurrency when defining VirtualCurrencyPacks:

CCVirtualCurrencyPack *tenmuffPack = CCVirtualCurrencyPack::create(
    __String::create("10 Muffins"),                         // Name
    __String::create("Test refund of an item"),             // Description
    __String::create("muffins_10"),                         // Item ID
    __Integer::create(10),                                  // Number of currency units
    __String::create("muffinCurrency_ID"),                  // Associated currency
    CCPurchaseWithMarket::create(__String::create(          // Purchase type
        __String::create("tenMuff_prodID"),                 // Product ID
        __Double::create(0.99))                             // Initial price
);
```

**Give:**

Give a VirtualCurrency and get nothing in return.
This is useful if you’d like to give your users some amount of currency to begin with when they first download your game.

``` cpp
CCError *soomlaError = NULL;

// If this is the first time playing, give the user an initial balance of 1000 muffins.
CCStoreInventory::sharedStoreInventory()->giveItem("currency_coin", 1000, &soomlaError);
```

####**Get the balance**

Get the balance of a specific VirtualCurrency.

``` cpp
CCError *soomlaError = NULL;

CCStoreInventory::sharedStoreInventory()->getItemBalance("currency_muffin", &soomlaError);
```

### VirtualCurrencyPack <a href="https://github.com/soomla/cocos2dx-store/blob/master/Soomla/domain/virtualCurrencies/CCVirtualCurrencyPack.h" target="_blank"><img class="link-icon" src="/img/tutorial_img/linkImg.png"></a>

As mentioned above, in game stores you never buy just a "Gold Coin" or a "Muffin", you always buy a pack of the game's `VirtualCurrency`. This class represents exactly that: a pack of `VirtualCurrency`. Use this class to define various currency packs in your game.

####**How to define**

``` cpp
CCVirtualCurrencyPack *fiftymuffPack = CCVirtualCurrencyPack::create(
    __String::create("50 Muffins"),                         // Name
    __String::create("Test cancellation of an item"),       // Description
    __String::create("muffins_50"),                         // Item id
    __Integer::create(50),                                  // Number of currency units
    __String::create("muffinCurrency_ID"),                  // Associated currency
    CCPurchaseWithMarket::create(__String::create(          // Purchase type
        __String::create("fiftyMuff_prodID"),               // Target item ID
        __Double::create(1.99))                             // Initial price
);
```

####**How to use**

**Buy:**

When your user buys a VirtualCurrencyPack of 50 muffins, his/her muffin currency balance will be increased by 50, and the payment will be deducted.

``` cpp
CCError *soomlaError = NULL;

CCStoreInventory::sharedStoreInventory()->buyItem("muffins_50", &soomlaError);
```

**Give:**

Give your users a 50-muffin pack for free. This is useful if you’d like to give your users a currency_pack to begin with when they first download your game.

``` cpp
CCError *soomlaError = NULL;

CCStoreInventory::sharedStoreInventory()->giveItem("muffins_50", 1, &soomlaError);
```

**Take:**

This function simply deducts the user’s balance. In case of a refund request, it is your responsibility to give the user back whatever he/she paid.

Take back the 50-muffin pack that the user owns:

``` cpp
CCError *soomlaError = NULL;

CCStoreInventory::sharedStoreInventory()->takeItem("muffins_50", 1, &soomlaError);
```

####**Get the balance**

VirtualCurrencyPacks do not have a balance of their own in the database. When a user purchases a VirtualCurrencyPack, the balance of the associated VirtualCurrency is increased.

``` cpp
CCError *soomlaError = NULL;

CCStoreInventory::sharedStoreInventory()->getItemBalance("currency_muffin", &soomlaError);
```

## Virtual Goods

Every virtual good is a `PurchasableVirtualItem`. You can buy it with other `VirtualItem`s, or in the market with money. Virtual goods are the heart of every virtual economy. These are the game objects you’re going to want to sell in your game's store.

Virtual goods need to be declared in your implementation of `IStoreAssets`.

Every virtual good belongs to one of the following groups:

1. Single Use

2. Single Use Pack

3. Lifetime

4. Equippables

5. Upgradables

Below are detailed descriptions of each category.

### SingleUseVG <a href="https://github.com/soomla/cocos2dx-store/blob/master/Soomla/domain/virtualGoods/CCSingleUseVG.h" target="_blank"><img class="link-icon" src="/img/tutorial_img/linkImg.png"></a>

The most basic and common kind of a `VirtualGood` is a `SingleUseVG`. `SingleUseVG`s can be purchase by your users multiple times. No limits!

**The SingleUseVG's characteristics are:**

- Can be purchased an unlimited number of times.

- Has a balance that is saved in the database. Its balance goes up when you "give" it or "buy" it. The balance goes down when you “take” it (for example in the case of a refund).

####**How to define**

``` cpp
CCVirtualGood *fruitCakeGood = CCSingleUseVG::create(
    __String::create("Fruit Cake"),                         // Name
    __String::create("Tasty cake"),                         // Description
    __String::create("fruit_cake"),                         // Item id
    CCPurchaseWithVirtualItem::create(__String::create(     // Purchase type
        "muffinCurrency_ID"),                               // Target item ID
        __Integer::create(225))                             // Initial price
);
```

####**How to use**

**Buy:**

When your user buys a `SingleUseVG`, for example “fruit_cake”, his/her “fruit_cake” balance will be increased by 1, and the payment will be deducted.

``` cpp
CCError *soomlaError = NULL;

CCStoreInventory::sharedStoreInventory()->buyItem("fruit_cake", &soomlaError);
```

**Give:**

Gives your user the given amount of the `SingleUseVG` with the given itemId(“fruit_cake” in our example) for free. This is useful if you’d like to give your users a `SingleUseVG` to start with when they first download your game.

``` cpp
CCError *soomlaError = NULL;

CCStoreInventory::sharedStoreInventory()->giveItem("fruit_cake", 1, &soomlaError);
```

**Take:**

This function simply deducts the user’s balance. In case of a refund request, it is your responsibility to give the user back whatever he/she paid.

``` cpp
CCError *soomlaError = NULL;

CCStoreInventory::sharedStoreInventory()->takeItem("fruit_cake", 1, &soomlaError);
```

####**Get the balance**

Get the balance of a specific `SingleUseVG`.

``` cpp
CCError *soomlaError = NULL;

CCStoreInventory::sharedStoreInventory()->getItemBalance("fruit_cake", &soomlaError);
```

### SingleUsePackVG <a href="https://github.com/soomla/cocos2dx-store/blob/master/Soomla/domain/virtualGoods/CCSingleUsePackVG.h" target="_blank"><img class="link-icon" src="/img/tutorial_img/linkImg.png"></a>

Sometimes, you'll want to to sell packs of `SingleUseVG`s. To support these cases, we've created `SingleUsePackVG`.

**The SingleUsePackVG's characteristics are:**

- Can be purchased an unlimited number of times.

- Doesn't have a balance in the database. The `SingleUseVG` that's associated with this pack has its own balance. When your users buy a `SingleUsePackVG`, the balance of the associated `SingleUseVG` goes up in the amount you defined for the pack.

**For Example:**
Suppose you offer a `SingleUsePackVG` of “5 fruit cakes”. The `SingleUseVG` that’s associated with this Pack is "fruit_cake". When your user buys a “5 fruit cake" Pack, the balance of fruit_cake is increased by 5 in the storage.

####**How to define**

``` cpp
CCVirtualGood *fiveFruitcakeGoods = CCSingleUsePackVG::create(
    __String::create("fruit_cake"),                         // Item ID of associated good
    __Integer::create(5),                                  // Amount of goods in pack
    __String::create("5 Fruitcakes"),                      // Name
    __String::create("Pack of 5 fruitcakes"),               // Description
    __String::create("fruitcake_5"),                       // Item ID
    CCPurchaseWithVirtualItem::create(                      // Purchase type
        __String::create("muffinCurrency_ID"),
        __Integer::create(1750)));
```

####**How to use**

The explanations for buying, giving, and taking are the same as those in SingleUseVG.

**Buy:**

``` cpp
CCError *soomlaError = NULL;

CCStoreInventory::sharedStoreInventory()->buyItem("fruitcake_5", &soomlaError);
```

**Give:**

``` cpp
CCError *soomlaError = NULL;

CCStoreInventory::sharedStoreInventory()->giveItem("fruitcake_5", 1, &soomlaError);
```

**Take:**

```
CCError *soomlaError = NULL;

CCStoreInventory::sharedStoreInventory()->takeItem("fruitcake_5", 1, &soomlaError);
```

####**Get the balance**

SingleUsePackVGs do not have a balance of their own in the database. When a user buys a SingleUsePackVG, the balance of the associated `SingleUseVG` is increased. After buying a pack of 5 fruit cakes, your user’s fruit cake balance should be increased by 5.

Query the balance of the virtual good with item ID “fruit_cake”:

``` cpp
CCError *soomlaError = NULL;

CCStoreInventory::sharedStoreInventory()->getItemBalance("fruit_cake", &soomlaError);
```

### LifetimeVG <a href="https://github.com/soomla/cocos2dx-store/blob/master/Soomla/domain/virtualGoods/CCLifetimeVG.h" target="_blank"><img class="link-icon" src="/img/tutorial_img/linkImg.png"></a>

A `LifetimeVG` is a `VirtualGood` that can be bought once and is kept forever.

**The LifetimeVG's characteristics are:**

- Can only be purchased once.

- Your users can't have more than one of this item. In other words, they can have either 0 or 1 of this item at any given time.

If you declare a `LifetimeVG` with a purchase type of `PurchaseWithMarket`, it represents a non-consumable item in the market, i.e., it cannot be consumed and is owned by the user forever. (The Market saves this information forever)

However, notice that if you declare a `LifetimeVG` with a purchase type of `PurchaseWithVirtualItem`, the user will own the `LifetimeVG` **as long as the local storage of the game has NOT been deleted** (i.e. the version has been updated, or the game was deleted and re-downloaded, etc..).

####**For Example**

``` cs
// A blue car that is purchased with virtual coins.
CCVirtualGood *BLUE_CAR =
    CCLifetimeVG::create(__String::create("Blue Car"),      // Name
    __String::create("This car is yours as long as the game's storage doesn't change!"),
    __String::create("blue_car"),                           // Item ID
    CCPurchaseWithVirtualItem::create(                      // Purchase type
        __String::create("coinCurrency_ID"),
        __Integer::create(3000)));

// A red car that is purchased with money in the market.
CCVirtualGood *RED_CAR =
    CCLifetimeVG::create(__String::create("Red Car"),       // Name
    __String::create("This car is yours FOREVER!!!"),       // Description
    __String::create("red_car"),                            // Item ID
    CCPurchaseWithMarket::create(                           // Purchase type
            __String::create("redCar_ProdID"),
            __Double::create(4.99)));
```

Let's say a user purchases both cars. Even if the game's local storage is deleted, the user will still own the red car and will receive it upon `refreshInventory` process. However, the user will not own the blue car any longer.

<div class="info-box">IMPORTANT: When defining a `LifetimeVG` in the App Store (iOS), you MUST define its type as a Non-Consumable! For more information see our guide for [defining IAP products in the App Store](/ios/store/Store_AppStoreIAB).</div>

<br>

####**How to define**

``` cpp
CCVirtualGood *marriageGood =
    CCLifetimeVG::create(__String::create("Marriage"),      // Name
    __String::create("This is a LIFETIME thing."),          // Description
    __String::create("marriage"),                           // Item ID
    CCPurchaseWithVirtualItem::create(                      // Purchase type
        __String::create("muffinCurrency_ID"),
        __Integer::create(100)));
```

####**How to use**

**Buy:**

Buying a `LifetimeVG` means that the user will now own the item for the rest of time, unless the game developer decides to explicitly take away the item from the user. Lifetime goods can be bought only once.

``` cpp
CCError *soomlaError = NULL;

CCStoreInventory::sharedStoreInventory()->buyItem("marriage", &soomlaError);
```

**Give:**

Give a `LifetimeVG` and get nothing in return. This is useful if you’d like to give your users a `LifetimeVG` when they first download your game.

```cpp
CCError *soomlaError = NULL;

CCStoreInventory::sharedStoreInventory()->giveItem("marriage", 1, &soomlaError);
```

**Take:**

This function simply deducts the user’s balance. In case of a refund request, it is your responsibility to give the user back whatever he/she paid.

```cpp
CCError *soomlaError = NULL;

CCStoreInventory::sharedStoreInventory()->takeItem("marriage", 1, &soomlaError);
```

####**Check ownership**

``` cpp
//If the balance is greater than 0, the user owns this LifetimeVG.

CCError *soomlaError = NULL;

CCStoreInventory::sharedStoreInventory()->getItemBalance("marriage", &soomlaError);
```

### EquippableVG <a href="https://github.com/soomla/cocos2dx-store/blob/master/Soomla/domain/virtualGoods/CCEquippableVG.h" target="_blank"><img class="link-icon" src="/img/tutorial_img/linkImg.png"></a>

An `EquippableVG` is a special type of `LifetimeVG`. In addition to the fact that an `EquippableVG` can be purchased once, it can also be equipped by your users. Equipping means that the user decides to currently use a specific `EquippableVG`.

**The EquippableVG's characteristics are:**

- Can be purchased only once.

- Can be equipped by the user.

- Inherits the definition of `LifetimeVG`.

**There are 3 equipping models:**

- `LOCAL` - The current `EquippableVG`'s equipping status doesn't affect any other `EquippableVG`.

- `CATEGORY` - In the containing category, if this `EquippableVG` is equipped, all other `EquippableVG`s are unequipped.

- `GLOBAL` - In the whole game, if this `EquippableVG` is equipped, all other `EquippableVG`s are unequipped.

####**How to define**

In this example we're defining 2 characters, George and Kramer as `CATEGORY` equippable goods. This means the user can own both characters but can play only as one at a time.

``` cpp
// Character "Kramer" can be purchased for 350 Muffins.
CCVirtualGood *georgeGood = CCEquippableVG::create(
    __Integer::create(CCEquippableVG::kCategory),           // Equipping model
    __String::create("George"),                             // Name
    __String::create("The best muffin eater in the north"), // Description
    __String::create("george"),                             // Item ID
    CCPurchaseWithVirtualItem::create(                      // Purchase type
        __String::create("muffinCurrency_ID"),
        __Integer::create(350)));

// Character "Kramer" can be purchased for 500 Muffins.
CCVirtualGood *kramerGood = CCEquippableVG::create(
    __Integer::create(CCEquippableVG::kCategory),           // Equipping model
    __String::create("Kramer"),                             // Name
    __String::create("Knows how to get muffins"),           // Description
    __String::create("kramer"),                             // Item ID
    CCPurchaseWithVirtualItem::create(                      // Purchase type
        __String::create("muffinCurrency_ID"),
        __Integer::create(500)));
```
####**How to use**

**Buy:**

Buying an `EquippableVG` is exactly like buying a `LifetimeVG`. The balance of “kramer” will be checked and if it is 0, buying will be allowed.

``` cpp
CCError *soomlaError = NULL;

CCStoreInventory::sharedStoreInventory()->buyItem("kramer", &soomlaError);
```

**Give:**

Give an `EquippableVG` and get nothing in return.
This is useful if you’d like to give your users a free character to begin with when they first download your game.

``` cpp
CCError *soomlaError = NULL;

CCStoreInventory::sharedStoreInventory()->giveItem("george", 1, &soomlaError);
```

**Take:**

This function simply deducts the user’s balance. In case of a refund request, it is your responsibility to give the user back whatever he/she paid.

``` cpp
CCError *soomlaError = NULL;

CCStoreInventory::sharedStoreInventory()->takeItem("kramer", 1, &soomlaError);
```

**Equip & Unequip:**

``` cpp
CCError *soomlaError = NULL;

// The user equips an owned good, George:
CCStoreInventory::sharedStoreInventory()->equipVirtualGood("george", &soomlaError);

// The user tries to equip Kramer (while he has George equipped):
CCStoreInventory::sharedStoreInventory()->equipVirtualGood("kramer", &soomlaError);
// Internally, George will be unequipped and Kramer will be equipped instead.

// The user unequips the currently equipped character (Kramer).
CCStoreInventory::sharedStoreInventory()->unEquipVirtualGood("kramer", &soomlaError);
```

####**Check ownership**

``` cpp
//Check if user owns Kramer:
CCError *soomlaError = NULL;

//If the balance is greater than 0, the user owns Kramer.
CCStoreInventory::sharedStoreInventory()->getItemBalance("kramer", &soomlaError);
```

**Check equipping status:**

``` cpp
//Check if Kramer is currently equipped:
CCError *soomlaError = NULL;

CCStoreInventory::sharedStoreInventory()->isVirtualGoodEquipped("kramer", &soomlaError);
```

### UpgradeVG <a href="https://github.com/soomla/cocos2dx-store/blob/master/Soomla/domain/virtualGoods/CCUpgradeVG.h" target="_blank"><img class="link-icon" src="/img/tutorial_img/linkImg.png"></a>

An `UpgradeVG` is a `VirtualGood` in a series of `UpgradeVG`s that define an upgrade sequence for a given `VirtualGood`. The associated `VirtualGood` can be of any type (`SingleUseVG`, `EquippableVG`, etc..).

When the user buys an`UpgradeVG`, a check is performed to make sure the appropriate conditions are met.

**`UpgradeVG`'s characteristics:**

- Can be purchased whenever it's not the current upgrade.

- Doesn't have a balance in the database.

**Important `UpgradeVG` class members:**

- `goodItemId` - the itemId of the `VirtualGood` associated with this upgrade.

- `prevItemId` - the itemId of the `UpgradeVG` that comes before this one, or if this is the first `UpgradeVG` in the scale then this value is null.

- `nextItemId` - the itemId of the `UpgradeVG` that comes after this one, or if this is the last `UpgradeVG` in the scale then the value is null.

####**How to define**

Say you have a strength attribute in your game and that strength is upgradeable on a scale of 1-3.  This is what you'll need to create:

1. SingleUseVG for 'strength'

2. UpgradeVG for strength 'level 1'

3. UpgradeVG for strength 'level 2'

4. UpgradeVG for strength 'level 3'

In the following example there is a `SingleUseVG` for "Strength" and 3 upgrade levels for it.

``` cpp
// Create a SingleUseVG for "Strength"
CCVirtualGood *strength = CCSingleUseVG::create(
    __String::create("Strength Attribute"),                 // Name
    __String::create("Be strong!"),                         // Description
    __String::create("strength_ID"),                        // Item id
    CCPurchaseWithMarket::create(                           // Purchase type
        __String::create("strength_ProdID"),
        __Double::create(1.99)));
);

// Create 2 UpgradeVGs that represent 2 upgrade levels for strength.
CCVirtualGood *strength_upgrade1 = CCUpgradeVG::create(
    __String::create("strength_ID"),                        // Item ID of the associated good that is being upgraded
    NULL,                                                   // Item ID of the previous upgrade good
    __String::create("strength_upgrade2_ID"),               // Item ID of the next upgrade good
    __String::create("Strength Upgrade 1"),                 // Name
    __String::create("Kramer will be able to kick twice as many muffins"), // Description
    __String::create("strength_upgrade1_ID"),               // Item ID
    CCPurchaseWithVirtualItem::create(                      // Purchase type
        __String::create("muffinCurrency_ID"),
        __Integer::create(50)));

CCVirtualGood *strength_upgrade2 = CCUpgradeVG::create(
    __String::create("strength_ID"),                        // Item ID of the associated good that is being upgraded
    __String::create("muffincake_level_1"),                 // Item ID of the previous upgrade good
    __String::create("muffincake_level_3"),                 // Item ID of the next upgrade good
    __String::create("Strength Upgrade 2"),                 // Name
    __String::create("Kramer will be able to kick 4 times as many muffins"), // Description
    __String::create("strength_upgrade2_ID"),               // Item ID
    CCPurchaseWithVirtualItem::create(                      // Purchase type
        __String::create("muffinCurrency_ID"),
        __Integer::create(100)));

// UpgradeVG for strength level 3...
```

####**How to use**

**Buy:**

When a user buys an upgrade, the buy method checks that the upgrade that’s being purchased is valid.

``` cpp
CCError *soomlaError = NULL;

CCStoreInventory::sharedStoreInventory()->buyItem("strength_upgrade2_ID", &soomlaError);
```

**Upgrade:**

When you upgrade a virtual good, the method performs a check to see that this upgrade is valid.

``` cpp
CCError *soomlaError = NULL;

CCStoreInventory::sharedStoreInventory()->upgradeGood("strength_ID", &soomlaError);
```

**Remove upgrades:**

Remove all upgrades from the virtual good with the given ID (strength in our example).

``` cpp
CCError *soomlaError = NULL;

CCStoreInventory::sharedStoreInventory()->removeGoodUpgrades("strength_ID", &soomlaError);
```

**Give:**

Give a free upgrade.

``` cpp
CCError *soomlaError = NULL;

CCStoreInventory::sharedStoreInventory()->giveItem("strength_upgrade2_ID", 1, &soomlaError);
```

**Take:**

This function simply deducts the user’s balance. In case of a refund request, it is your responsibility to give the user back whatever he/she paid. Essentially, taking an upgrade is the same as a downgrade.

``` cpp
// The parameter amount is not used in this method.
CCError *soomlaError = NULL;

CCStoreInventory::sharedStoreInventory()->takeItem("strength_upgrade2_ID", 1, &soomlaError);
```

####**Get current upgrade**

To get the current upgrade of a virtual good use getGoodCurrentUpgrade. If the good has no upgrades, the method will return null.

``` cpp
CCError *soomlaError = NULL;

CCStoreInventory::sharedStoreInventory()->getGoodCurrentUpgrade("strength_ID", &soomlaError);
```

####**Get current upgrade level**

To find out the upgrade level of a virtual good use getGoodUpgradeLevel. If the good has no upgrades, the method returns 0.

``` cpp
CCError *soomlaError = NULL;

CCStoreInventory::sharedStoreInventory()->getGoodUpgradeLevel("strength_ID", &soomlaError);
```

## VirtualCategory <a href="https://github.com/soomla/cocos2dx-store/blob/master/Soomla/domain/CCVirtualCategory.h" target="_blank"><img class="link-icon" src="/img/tutorial_img/linkImg.png"></a>

A `VirtualCategory` is used to categorize `VirtualGood`s. Categories are helpful for organizational purposes, but especially come in handy when you have Equippable Virtual Goods.

<div class="info-box">If you don’t have any need for categories, you can just define all of your virtual goods in one category and call it something like “General”.</div>

####**Real Game Examples:**

Let’s suppose your game has the following categories of virtual goods: "Power Ups", "Weapons", and  "Hats". Say you decide to make “Weapons” and “Hats” `CATEGORY` `EquippableVG`s. You can easily implement this functionality once the goods are divided into virtual categories.

####How to define

``` cpp
CCVirtualCategory *cakes = CCVirtualCategory::create(
    __String::create("Cakes"),
    __Array::create(
        __String::create("MUFFINCAKE_ITEM_ID"),
        __String::create("PAVLOVA_ITEM_ID"),
        __String::create("PAVLOVA_10"),
        __String::create("CHOCLATECAKE_ITEM_ID"),
        __String::create("CREAMCUP_ITEM_ID"),
        __String::create("CREAMCUP_10"),
        NULL));
```

####Get category

**Check which category an item belongs to:**

``` cpp
CCError *soomlaError = NULL;

CCStoreInfo::sharedStoreInfo()->getCategoryForVirtualGood(MUFFIN_CAKE_GOOD_ITEM_ID, &soomlaError);
```
