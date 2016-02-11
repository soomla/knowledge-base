---
layout: "soomla-content"
image: "Modeling"
title: "Economy Model & API"
text: "Every game economy can be based on SOOMLA's economy model. Learn the building blocks of creating a virtual economy with currencies and virtual goods."
position: 3
theme: 'soomla-ios'
collection: 'soomla_ios_store'
module: 'store'
platform: 'ios'
---

# Economy Model & API

SOOMLA's ios-store provides a complete data model implementation for virtual economies. Every game economy has currencies, packs of currencies that can be sold, and items that can be sold either for money or in exchange for other items. And these are just the very basics, of course. This tutorial contains descriptions of each entity in the economy model, along with examples.

![alt text](/img/tutorial_img/soomla_diagrams/EconomyModel.png "Soomla Economy Model")

## Virtual Items

Almost every entity in your virtual economy will be a Virtual Item. There are many types of Virtual Items and you can select the ones that fit your needs. Each one of the various types extends the class `VirtualItem` and adds its own behavior.

Almost all `VirtualItems` are `PurchasableVirtualItems`. Among other features, all Virtual items have 2 functions to help you easily interact with them: `give` and `take`. Preferably, you should use the two methods provided in `StoreInventory` for these purposes, called `giveAmount:ofItem` and `takeAmount:ofItem`. Use these functions to give or take from your users a specific amount of a specific Virtual Item.

Use `giveAmount:ofItem` when you want to give your user something and get nothing in return. (If you want to give something and get something in return, you need to use `buy`). Use `takeAmount:ofItem` when you want to take something from your user, for example in the case of a refund.

Every virtual item has an ID, a unique string that we use to identify the different items.

## PurchaseTypes

As stated above, almost all Virtual Items are purchasable, or as we call them, `PurchasableVirtualItem`s. Purchase types are used to indicate whether an item will be purchased with money or with other virtual items: there are 2 different purchase types described below.

<div class="info-box">In the examples below the declarations of purchase types are shown as a part of `PurchasableVirtualItem` declarations, because this is the most common use of purchase types.</div>

### PurchaseWithMarket <a href="https://github.com/soomla/ios-store/blob/master/SoomlaiOSStore/PurchaseTypes/PurchaseWithMarket.h" target="_blank"><img class="link-icon-small" src="/img/tutorial_img/linkImg.png"></a>

This kind of `PurchaseType` should be attached to items that you want to make available for purchase in the Market (App Store, Google Play Store, etc..) for real money. When you create an instance of `PurchaseWithMarket`, you need to define the associated `VirtualItem` in the Market.

**NOTE:** The product ID you define in your implementation of `IStoreAssets` should be the same as you define in the Market.

#### **For Example**
Suppose that in your game, you offer a “No-Ads” feature for $1.99 in the Market. The code below shows how you need to declare the `PurchaseType` parameter of your “No-Ads” item.

``` objectivec
NO_ADS = [[LifetimeVG alloc]
    ...
    andPurchaseType:[[PurchaseWithMarket alloc]
        initWithMarketItem:[[MarketItem alloc]
            initWithProductId:NO_ADS_PRODUCT_ID
            andConsumable:kNonConsumable
            andPrice:1.99]]];
```

There is another way to define this purchase type:

``` objectivec
_1000_MUFFINS_PACK = [[VirtualCurrencyPack alloc]
    ...
    andPurchaseType:[[PurchaseWithMarket alloc]
        initWithProductId:_1000_MUFFINS_PRODUCT_ID
        andPrice:8.99]];
```

For more info on how to declare your items in the Market, see our tutorial on App Store [In-app billing](/soomla/ios/store/Store_AppStoreIAB).

### PurchaseWithVirtualItem <a href="https://github.com/soomla/ios-store/blob/master/SoomlaiOSStore/PurchaseTypes/PurchaseWithVirtualItem.h" target="_blank"><img class="link-icon-small" src="/img/tutorial_img/linkImg.png"></a>

Any item with purchase type `PurchaseWithVirtualItem` can be purchased with any `VirtualItem`, like a sort of trade. When creating an instance of `PurchaseWithVirtualItem`, you need to provide the ID of the virtual item that you want to be paid with and the amount of that virtual item.

#### **For Example**
Suppose that in your game, you offer a chocolate cake that can be bought by paying 250 “Muffins”. The item being purchased is a `chocolateCakeGood`, the item (virtual currency) to pay with is “Muffin”, and the amount is 250.

``` objectivec
// A chocolate cake good can be purchased with 250 muffin currencies
CHOCOLATE_CAKE_GOOD = [[SingleUseVG alloc]
    ...
    andPurchaseType:[[PurchaseWithVirtualItem alloc]
        initWithVirtualItem:MUFFINS_CURRENCY_ITEM_ID
        andAmount:250]];
```

## Virtual Currencies

### VirtualCurrency <a href="https://github.com/soomla/ios-store/blob/master/SoomlaiOSStore/domain/virtualCurrencies/VirtualCurrency.h" target="_blank"><img class="link-icon-small" src="/img/tutorial_img/linkImg.png"></a>

Every game that has an economy has at least one `VirtualCurrency`. `VirtualCurrency` is NOT a `PurchasableVirtualItem`. This is because in game stores, you never buy just a single "Gold Coin" or a "Muffin", but rather you buy a pack of them. Your users will be able to buy packs of your game’s `VirtualCurrency` by using `VirtualCurrencyPack` (explained later in this document). If for some reason you *do* want to sell a single currency you can do so by providing a `VirtualCurrencyPack` with an amount of 1.

#### **How to define**

``` objectivec
NSString* const MUFFINS_CURRENCY_ITEM_ID = @"currency_muffin";

MUFFINS_CURRENCY = [[VirtualCurrency alloc]
    initWithName:@"Muffins"
    andDescription:@"Muffin currency"
    andItemId:MUFFINS_CURRENCY_ITEM_ID];
```

#### **How to use**
A `VirtualCurrency` by itself is not very useful, because it cannot be sold individually. To sell currency, you need to use a `VirtualCurrencyPack` (see section below).

Use `VirtualCurrency` when defining `VirtualCurrencyPack`s:

``` objectivec
NSString* const _10_MUFFINS_PACK_ITEM_ID = @"muffins_10";

_10_MUFFINS_PACK = [[VirtualCurrencyPack alloc]
    initWithName:@"10 Muffins"
    andDescription:@"Pack of ten muffins"
    andItemId:_10_MUFFINS_PACK_ITEM_ID
    andCurrencyAmount:10
    andCurrency:MUFFINS_CURRENCY_ITEM_ID
    andPurchaseType:[[PurchaseWithMarket alloc]
        initWithMarketItem:[[MarketItem alloc]
            initWithProductId:_10_MUFFINS_PRODUCT_ID
            andConsumable:kConsumable
            andPrice:0.99]]];
```

**Give:**

Give a `VirtualCurrency` and get nothing in return.
This is useful if you'd like to give your users some amount of currency to begin with when they first download your game.

``` objectivec
// Give the user an initial balance of 1000 muffins.
[StoreInventory giveAmount:1000 ofItem:@"currency_muffin"];
```

#### **Get the balance**
Get the balance of a specific `VirtualCurrency`.

``` objectivec
[StoreInventory getItemBalance:@"currency_muffin"];
```

### VirtualCurrencyPack <a href="https://github.com/soomla/ios-store/blob/master/SoomlaiOSStore/domain/virtualCurrencies/VirtualCurrencyPack.h" target="_blank"><img class="link-icon-small" src="/img/tutorial_img/linkImg.png"></a>

As mentioned above, in game stores you never buy just a "Gold Coin" or a "Muffin", you always buy a pack of the game's `VirtualCurrency`. This class represents exactly that: a pack of `VirtualCurrency`. Use this class to define various currency packs in your game.

#### **How to define**

``` objectivec
 _50_MUFFINS_PACK = [[VirtualCurrencyPack alloc]
    initWithName:@"50 Muffins"
    andDescription:@"A currency pack of 50 muffins"
    andItemId:_50_MUFFINS_PACK_ITEM_ID
    andCurrencyAmount:50
    andCurrency:MUFFINS_CURRENCY_ITEM_ID
    andPurchaseType:[[PurchaseWithMarket alloc]
        initWithMarketItem:[[MarketItem alloc]
            initWithProductId:_50_MUFFINS_PRODUCT_ID
            andConsumable:kConsumable
            andPrice:1.99]]];
```

#### **How to use**

**Buy:**

When your user buys a `VirtualCurrencyPack` of 50 muffins, his/her muffin currency balance will be increased by 50, and the payment will be deducted.

``` objectivec
[StoreInventory buyItemWithItemId:@"muffins_50"];
```

**Give:**

Give your users a 50-muffin pack for free. This is useful if you’d like to give your users a currency pack to begin with when they first download your game.
``` objectivec
[StoreInventory giveAmount:1 ofItem:@"muffins_50"];
```

**Take:**

This function simply deducts the user's balance. In case of a refund request, it is your responsibility to give the user back whatever he/she paid.

Take back the 50-muffin pack that the user owns:
``` objectivec
[StoreInventory takeAmount:1 ofItem:@"muffins_50"];
```

#### **Get the balance**

`VirtualCurrencyPack`s do not have a balance of their own in the database. When a user purchases a `VirtualCurrencyPack`, the balance of the associated `VirtualCurrency` is increased.

``` objectivec
[StoreInventory getItemBalance:@"currency_muffin"];
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

### SingleUseVG <a href="https://github.com/soomla/ios-store/blob/master/SoomlaiOSStore/domain/virtualGoods/SingleUseVG.h" target="_blank"><img class="link-icon-small" src="/img/tutorial_img/linkImg.png"></a>

The most basic and common kind of a `VirtualGood` is a `SingleUseVG`. `SingleUseVG`s can be purchase by your users multiple times. No limits!

**The SingleUseVG's characteristics are:**

- Can be purchased an unlimited number of times.

- Has a balance that is saved in the database. Its balance goes up when you "give" it or "buy" it. The balance goes down when you “take” it (for example in the case of a refund).

#### **How to define**

``` objectivec
FRUIT_CAKE_GOOD = [[SingleUseVG alloc]
    initWithName:@"Fruit Cake"
    andDescription:@"Customers buy a double portion on each purchase of this cake"
    andItemId:@"fruit_cake"
    andPurchaseType:[[PurchaseWithVirtualItem alloc]
        initWithVirtualItem:MUFFINS_CURRENCY_ITEM_ID
        andAmount:225]];
```

#### **How to use**

**Buy:**

When your user buys a `SingleUseVG` ("fruit_cake" in our example) his/her "fruit_cake" balance will be increased by 1, and the payment will be deducted.

``` objectivec
[StoreInventory buyItemWithItemId:@"fruit_cake"];
```

**Give:**

Gives your user the given amount of the `SingleUseVG` with the given `itemId` ("fruit_cake" in our example) for free. This is useful if you'd like to give your users a `SingleUseVG` to start with when they first download your game.

``` objectivec
[StoreInventory giveAmount:1 ofItem:@"fruit_cake"];
```

**Take:**

This function simply deducts the user's balance. In case of a refund request, it is your responsibility to give the user back whatever he/she paid.  

``` objectivec
[StoreInventory takeAmount:1 ofItem:@"fruit_cake"];
```


#### **Get the balance**
Get the balance of a specific `SingleUseVG`.

``` objectivec
[StoreInventory getItemBalance:@"fruit_cake"];
```

### SingleUsePackVG <a href="https://github.com/soomla/ios-store/blob/master/SoomlaiOSStore/domain/virtualGoods/SingleUsePackVG.h" target="_blank"><img class="link-icon-small" src="/img/tutorial_img/linkImg.png"></a>

Sometimes, you'll want to to sell packs of `SingleUseVG`s. To support these cases, we've created `SingleUsePackVG`.

**The SingleUsePackVG's characteristics are:**

- Can be purchased an unlimited number of times.

- Doesn't have a balance in the database. The `SingleUseVG` that's associated with this pack has its own balance. When your users buy a `SingleUsePackVG`, the balance of the associated `SingleUseVG` goes up in the amount you defined for the pack.

**For Example:**
Suppose you offer a `SingleUsePackVG` of “5 fruit cakes”. The `SingleUseVG` that’s associated with this Pack is "fruit_cake". When your user buys a “5 fruit cake" Pack, the balance of fruit_cake is increased by 5 in the storage.

#### **How to define**

``` objectivec
// Define a pack of 5 "Fruit cake" goods that costs $2.99.
FRUIT_CAKE_GOOD_PACK = [SingleUsePackVG alloc]
    initWithName:@"fruit_cake_5pack"
    andDescription:@"A pack of 5 Fruit Cakes"
    andItemId:@"fruit_cake_5pack"
    andPurchaseType:[[PurchaseWithMarket alloc]
        initWithMarketItem:[[MarketItem alloc]
            initWithProductId:FRUIT_CAKE_5PACK_PRODUCT_ID
            andConsumable:kConsumable
            andPrice:2.99]]];
```

#### **How to use**
The explanations for buying, giving, and taking are the same as those in [SingleUseVG](#singleusevg).

**Buy:**

``` objectivec
[StoreInventory buyItemWithItemId:@"fruit_cake_5pack"];
```

**Give:**

``` objectivec
[StoreInventory giveAmount:1 ofItem:@"fruit_cake_5pack"];
```

**Take:**

``` objectivec
[StoreInventory takeAmount:1 ofItem:@"fruit_cake_5pack"];
```

#### **Get the balance**
`SingleUsePackVG`s do not have a balance of their own in the database. When a user buys a `SingleUsePackVG`, the balance of the associated `SingleUseVG` is increased. After buying a pack of 5 fruit cakes, your user's fruit cake balance should be increased by 5.

Query the balance of the virtual good with item ID "fruit_cake":

``` objectivec
[StoreInventory getItemBalance:@"fruit_cake"];
```

### LifetimeVG <a href="https://github.com/soomla/ios-store/blob/master/SoomlaiOSStore/domain/virtualGoods/LifetimeVG.h" target="_blank"><img class="link-icon-small" src="/img/tutorial_img/linkImg.png"></a>

A `LifetimeVG` is a `VirtualGood` that can be bought once and is kept forever.

**The LifetimeVG's characteristics are:**

- Can only be purchased once.

- Your users can't have more than one of this item. In other words, they can have either 0 or 1 of this item at any given time.

If you declare a `LifetimeVG` with a purchase type of `PurchaseWithMarket`, it represents a non-consumable item in the market, i.e., it cannot be consumed and is owned by the user forever. (The Market saves this information forever)

However, notice that if you declare a `LifetimeVG` with a purchase type of `PurchaseWithVirtualItem`, the user will own the `LifetimeVG` **as long as the local storage of the game has NOT been deleted** (i.e. the version has been updated, or the game was deleted and re-downloaded, etc..).

#### **For Example**

``` cs
// A blue car that is purchased with virtual coins.
BLUE_CAR = [[LifetimeVG alloc]
    initWithName:@"Blue Car"
    andDescription:@"This car is yours as long as the game's storage doesn't change!"
    andItemId:@"blueCar_ID"
    andPurchaseType:[[PurchaseWithVirtualItem alloc]
        initWithVirtualItem:COIN_CURRENCY_ITEM_ID
        andAmount:3000]];

// A red car that is purchased with money in the market.
RED_CAR = [[LifetimeVG alloc]
    initWithName:@"Red Car"
    andDescription:@"This car is yours FOREVER!!!"
    andItemId:@"redCar_ID"
    andPurchaseType:[[PurchaseWithMarket alloc]
        initWithProductId:RED_CAR_PROD_ID
        andPrice:4.99]];
```

Let's say a user purchases both cars. Even if the game's local storage is deleted, the user will still own the red car and will receive it upon `refreshInventory` process. However, the user will not own the blue car any longer.

<div class="info-box">IMPORTANT: When defining a `LifetimeVG` in the App Store, you MUST define its type as a Non-Consumable! For more information see our guide for [defining IAP products in the App Store](/soomla/ios/store/Store_AppStoreIAB).</div>

<br>

#### **How to define**

``` objectivec
MARRIAGE_GOOD = [[LifetimeVG alloc]
    initWithName:@"Marriage"
    andDescription:@"This is a LIFETIME thing."
    andItemId:MARRIAGE_GOOD_ITEM_ID
    andPurchaseType:[[PurchaseWithMarket alloc]
        initWithMarketItem:[[MarketItem alloc]
            initWithProductId:MARRIAGE_PRODUCT_ID
            andConsumable:kNonConsumable andPrice:9.99]]];
```

#### **How to use**

**Buy:**

Buying a `LifetimeVG` means that the user will now own the item for the rest of time. Lifetime goods can be bought only once.

``` objectivec
[StoreInventory buyItemWithItemId:@"marriage"];
```

**Give:**

Give a `LifetimeVG` and get nothing in return.
This is useful if you’d like to give your users a `LifetimeVG` when they first download your game.

``` objectivec
[StoreInventory giveAmount:1 ofItem:@"marriage"];
```

**Take:**

This function simply deducts the user's balance. In case of a refund request, it is your responsibility to give the user back whatever he/she paid.

``` objectivec
[StoreInventory takeAmount:1 ofItem:@"marriage"];
```

#### **Check ownership**
Check the ownership of a lifetime good:

``` objectivec
//If the balance is greater than 0, the user owns this LifetimeVG.
[StoreInventory getItemBalance:@"marriage"];
```

### EquippableVG <a href="https://github.com/soomla/ios-store/blob/master/SoomlaiOSStore/domain/virtualGoods/EquippableVG.h" target="_blank"><img class="link-icon-small" src="/img/tutorial_img/linkImg.png"></a>

An `EquippableVG` is a special type of `LifetimeVG`. In addition to the fact that an `EquippableVG` can be purchased once, it can also be equipped by your users. Equipping means that the user decides to currently use a specific `EquippableVG`.

**The EquippableVG's characteristics are:**

- Can be purchased only once.

- Can be equipped by the user.

- Inherits the definition of `LifetimeVG`.

**There are 3 equipping models:**

- `kLocal` - The current `EquippableVG`'s equipping status doesn't affect any other `EquippableVG`.

- `kCategory` - In the containing category, if this `EquippableVG` is equipped, all other `EquippableVG`s are unequipped.

- `kGlobal` - In the whole game, if this `EquippableVG` is equipped, all other `EquippableVG`s are unequipped.

#### **How to define**

In this example we're defining 2 characters, George and Kramer. These are `kCategory` equippable goods because the user can own both characters but can play only as one at a time.

``` objectivec
// Character "George" can be purchased for 350 Muffins.
GEORGE_GOOD = [[EquippableVG alloc]
    initWithName:@"George"
    andDescription:@"The best muffin eater in the north"
    andItemId:GEORGE_GOOD_ITEM_ID
    andPurchaseType:[[PurchaseWithVirtualItem alloc]
        initWithVirtualItem:MUFFINS_CURRENCY_ITEM_ID
        andAmount:350]
    andEquippingModel:kCategory];

// Character "Kramer" can be purchased for 500 Muffins.
KRAMER_GOOD = [[EquippableVG alloc]
    initWithName:@"Kramer"
    andDescription:@"Knows how to get muffins"
    andItemId:KRAMER_GOOD_ITEM_ID
    andPurchaseType:[[PurchaseWithVirtualItem alloc]
        initWithVirtualItem:MUFFINS_CURRENCY_ITEM_ID
        andAmount:500]
    andEquippingModel:kCategory];
```

#### **How to use**
**Buy:**

Buying an `EquippableVG` is exactly like buying a [`LifetimeVG`](#lifetimevg). The balance of "kramer" will be checked and if it is 0, buying will be allowed.

``` objectivec
[StoreInventory buyItemWithItemId:@"kramer"];
```

**Give:**

Give an `EquippableVG` and get nothing in return.
This is useful if you’d like to give your users a free character to begin with when they first download your game.

``` objectivec
[StoreInventory giveAmount:1 ofItem:@"george"];
```

**Take:**

This function simply deducts the user's balance. In case of a refund request, it is your responsibility to give the user back whatever he/she paid.

``` objectivec
[StoreInventory takeAmount:1 ofItem:@"kramer"];
```

**Equip & Unequip:**

``` objectivec
// The user equips an owned good, George:
[StoreInventory equipVirtualGoodWithItemId:@"george"];

// The user tries to equip Kramer (while he has George equipped):
[StoreInventory equipVirtualGoodWithItemId:@"kramer"];
// Internally, George will be unequipped and Kramer will be equipped instead.

// The user unequips the currently equipped character (Kramer).
[StoreInventory unEquipVirtualGoodWithItemId:@"kramer"];
```

#### **Check ownership**
Check if user owns Kramer:

``` objectivec
//If the balance is greater than 0, the user owns Kramer.
[StoreInventory getItemBalance:@"kramer"];
```

#### **Check equipping status**
Check if Kramer is currently equipped:

``` objectivec
[StoreInventory isVirtualGoodWithItemIdEquipped:@"kramer"];
```

### UpgradeVG <a href="https://github.com/soomla/ios-store/blob/master/SoomlaiOSStore/domain/virtualGoods/UpgradeVG.h" target="_blank"><img class="link-icon-small" src="/img/tutorial_img/linkImg.png"></a>

An `UpgradeVG` is a `VirtualGood` in a series of `UpgradeVG`s that define an upgrade sequence for a given `VirtualGood`. The associated `VirtualGood` can be of any type (`SingleUseVG`, `EquippableVG`, etc..).

When the user buys an`UpgradeVG`, a check is performed to make sure the appropriate conditions are met.

**`UpgradeVG`'s characteristics:**

- Can be purchased whenever it's not the current upgrade.

- Doesn't have a balance in the database.

**Important `UpgradeVG` class members:**

- `goodItemId` - the itemId of the `VirtualGood` associated with this upgrade.

- `prevItemId` - the itemId of the `UpgradeVG` that comes before this one, or if this is the first `UpgradeVG` in the scale then this value is null.

- `nextItemId` - the itemId of the `UpgradeVG` that comes after this one, or if this is the last `UpgradeVG` in the scale then the value is null.

#### **How to define**

Say you have a strength attribute in your game and that strength is upgradeable on a scale of 1-3.  This is what you'll need to create:

1. SingleUseVG for 'strength'

2. UpgradeVG for strength 'level 1'

3. UpgradeVG for strength 'level 2'

4. UpgradeVG for strength 'level 3'

In the following example there is a `SingleUseVG` for "Strength" and 3 upgrade levels for it.

``` objectivec
// Create a SingleUseVG for "Strength"
STRENGTH_GOOD = [[SingleUseVG alloc]
    initWithName:@"Strength Attribute"
    andDescription:@"Be strong!"
    andItemId:@"strength_ID"
    andPurchaseType:[[PurchaseWithMarket alloc]
        initWithProductId:STRENGTH_PROD_ID
        andPrice:1.99]];

// Create 2 UpgradeVGs that represent 2 upgrade levels for strength.
STRENGTH_UPGRADE_1 = [[UpgradeVG alloc]
    initWithName:@"Strength Upgrade 1"
    andDescription:@"Upgrade Level 1"
    andItemId:@"strength_upgrade_1_ID"
    andPurchaseType:[[PurchaseWithVirtualItem alloc]
        initWithVirtualItem:MUFFINS_CURRENCY_ITEM_ID
        andAmount:50]
    andLinkedGood:@"strength_ID"
    andPreviousUpgrade:@""
    andNextUpgrade:@"strength_upgrade_2_ID"];

STRENGTH_UPGRADE_2 = [[UpgradeVG alloc]
    initWithName:@"Strength Upgrade 2"
    andDescription:@"Upgrade Level 2"
    andItemId:@"strength_upgrade_2_ID"
    andPurchaseType:[[PurchaseWithVirtualItem alloc]
        initWithVirtualItem:MUFFINS_CURRENCY_ITEM_ID
        andAmount:250]
    andLinkedGood:@"strength_ID"
    andPreviousUpgrade:@"strength_upgrade_1_ID"
    andNextUpgrade:@"strength_upgrade_3_ID"];

// UpgradeVG for strength level 3...
```

#### **How to use**
**Buy:**

When a user buys an upgrade, the `buy` method checks that the upgrade that's being purchased is valid.

``` objectivec
[StoreInventory buyItemWithItemId:LEVEL_1_GOOD_ITEM_ID];
```

**Upgrade:**
When you upgrade a virtual good, the method performs a check to see that this upgrade is valid.

``` objectivec
[StoreInventory upgradeVirtualGood:MUFFIN_CAKE_GOOD_ITEM_ID];
```

**Remove upgrades:**
Remove all upgrades from the virtual good with the given ID (Muffin cake in our example).

``` objectivec
[StoreInventory removeUpgrades:MUFFIN_CAKE_GOOD_ITEM_ID];
```

**Give:**

To give a free upgrade use `forceUpgrade`.

``` objectivec
[StoreInventory forceUpgrade:LEVEL_1_GOOD_ITEM_ID];
```

**Take:**

This function simply deducts the user's balance. In case of a refund request, it is your responsibility to give the user back whatever he/she paid. Essentially, taking an upgrade is the same as a downgrade.

``` objectivec
// The parameter amount is not used in this method.
[StoreInventory takeAmount:1 ofItem:LEVEL_1_GOOD_ITEM_ID];
```

#### **Get current upgrade**

To get the current upgrade of a virtual good use `goodCurrentUpgrade`. If the good has no upgrades, the method will return null.

``` objectivec
[StoreInventory goodCurrentUpgrade:MUFFIN_CAKE_GOOD_ITEM_ID];
```
#### **Get Current Upgrade Level**
To find out the upgrade level of a virtual good use `goodUpgradeLevel`. If the good has no upgrades, the method returns 0.

``` objectivec
[StoreInventory goodUpgradeLevel:MUFFIN_CAKE_GOOD_ITEM_ID];
```

## VirtualCategory <a href="https://github.com/soomla/ios-store/blob/master/SoomlaiOSStore/domain/VirtualCategory.h" target="_blank"><img class="link-icon" src="/img/tutorial_img/linkImg.png"></a>

A `VirtualCategory` is used to categorize `VirtualGood`s. Categories are helpful for organizational purposes, but especially come in handy when you have Equippable Virtual Goods.

<div class="info-box">If you don’t have any need for categories, you can just define all of your virtual goods in one category and call it something like “General”.</div>

#### **Real Game Examples:**

Let’s suppose your game has the following categories of virtual goods: "Power Ups", "Weapons", and  "Hats". Say you decide to make “Weapons” and “Hats” `CATEGORY` `EquippableVG`s. You can easily implement this functionality once the goods are divided into virtual categories.

#### **How to define**

``` objectivec
// Assume that MUFFIN_CAKE_GOOD_ITEM_ID, PAVLOVA_GOOD_ITEM_ID, etc. are item IDs
// of virtual goods that have been declared.
_MUFFINS_CATEGORY  = [[VirtualCategory alloc]
    initWithName:@"Muffins"
    andGoodsItemIds:@[MUFFIN_CAKE_GOOD_ITEM_ID, PAVLOVA_GOOD_ITEM_ID, MUFFIN_CAKE_GOOD_ITEM_ID]];
```

#### **Get ccategory**
Check which category an item belongs to:

``` objectivec
[StoreInfo categoryForGoodWithItemId:MUFFIN_CAKE_GOOD_ITEM_ID];
```
