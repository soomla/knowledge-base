---
layout: "soomla-content"
image: "Modeling"
title: "Economy Model & API"
text: "Every game economy can be based on SOOMLA's economy model. Learn the building blocks of creating a virtual economy with currencies and virtual goods."
position: 3
theme: 'soomla-unity'
collection: 'soomla_unity_store'
module: 'store'
platform: 'unity'
---

# Economy Model & API

SOOMLA's unity3d-store provides a complete data model implementation for virtual economies. Every game economy has currencies, packs of currencies that can be sold, and items that can be sold either for money or in exchange for other items. And these are just the very basics, of course. This tutorial contains descriptions of each entity in the economy model, along with examples.

![alt text](/img/tutorial_img/soomla_diagrams/EconomyModel.png "Soomla Economy Model")

## Virtual Items

Almost every entity in your virtual economy will be a Virtual Item. There are many types of Virtual Items and you can select the ones that fit your needs. Each one of the various types extends the class `VirtualItem` and adds its own behavior.

Almost all `VirtualItems` are `PurchasableVirtualItems`. Among other features, all Virtual items have 2 functions to help you easily interact with them: `give` and `take`. Preferably, you should use the two methods provided in `StoreInventory` for these purposes, called `giveVirtualItem` and `takeItem`. Use these functions to give or take from your users a specific amount of a specific Virtual Item.

Use `giveVirtualItem` when you want to give your user something and get nothing in return. (If you want to give something and get something in return, you need to use `buy`). Use `takeItem` when you want to take something from your user, for example in the case of a refund.

Every virtual item has an ID, a unique string that we use to identify the different items.

## PurchaseTypes

As stated above, almost all Virtual Items are purchasable, or as we call them, `PurchasableVirtualItem`s. Purchase types are used to indicate whether an item will be purchased with money or with other virtual items: there are 2 different purchase types described below.

<div class="info-box">In the examples below the declarations of purchase types are shown as a part of `PurchasableVirtualItem` declarations, because this is the most common use of purchase types.</div>

### PurchaseWithMarket <a href="https://github.com/soomla/unity3d-store/blob/master/Soomla/Assets/Plugins/Soomla/Store/purchaseTypes/PurchaseWithMarket.cs" target="_blank"><img class="link-icon-small" src="/img/tutorial_img/linkImg.png"></a>

This kind of `PurchaseType` should be attached to items that you want to make available for purchase in the Market (App Store, Google Play Store, etc..) for real money. When you create an instance of `PurchaseWithMarket`, you need to define the associated `VirtualItem` in the Market.

**NOTE:** The product ID you define in your implementation of `IStoreAssets` should be the same as you define in the Market.

#### **For Example**
Suppose that in your game, you offer a “No-Ads” feature for $1.99 in the Market. The code below shows how you need to declare the `PurchaseType` parameter of your “No-Ads” item.

``` cs
public const string NO_ADDS_PRODUCT_ID   = "no_ads";

public static LifetimeVG NO_AD = new LifetimeVG(
    ...
    new PurchaseWithMarket(new MarketItem(
        NO_ADDS_PRODUCT_ID,                     // Product ID
        MarketItem.Consumable.NONCONSUMABLE,    // Product type
        1.99))                                  // Initial price
);
```

There is another way to define this purchase type:

``` cs
public const string THOUSANDMUFF_PACK_PRODUCT_ID = "1000_pack";

public static VirtualCurrencyPack THOUSANDMUFF_PACK = new VirtualCurrencyPack(
    ...
    new PurchaseWithMarket(
        THOUSANDMUFF_PACK_PRODUCT_ID,           // Product ID
        8.99)                                   // Initial price
);
```

For more info on how to declare your items in the Market, see one of our tutorials on In-app Billing:

- For Android: [Google Play IAB](/soomla/android/store/Store_GooglePlayIAB) or [Amazon IAB](/soomla/android/store/Store_AmazonIAB)

- For iOS: [App Store IAB](/soomla/ios/store/Store_AppStoreIAB)

### PurchaseWithVirtualItem <a href="https://github.com/soomla/unity3d-store/blob/master/Soomla/Assets/Plugins/Soomla/Store/purchaseTypes/PurchaseWithVirtualItem.cs" target="_blank"><img class="link-icon-small" src="/img/tutorial_img/linkImg.png"></a>

Any item with purchase type `PurchaseWithVirtualItem` can be purchased with any `VirtualItem`, like a sort of trade. When creating an instance of `PurchaseWithVirtualItem`, you need to provide the ID of the virtual item that you want to be paid with and the amount of that virtual item.

#### **For Example**
Suppose that in your game, you offer a PAVLOVA_GOOD that can be bought by paying 175 “Muffins”. The item being purchased is a PAVLOVA_GOOD, the item (virtual currency) to pay with is “Muffin”, and the amount is 175.

``` cs
public const string MUFFIN_CURRENCY_ITEM_ID = "currency_muffin";

public static VirtualGood PAVLOVA_GOOD = new SingleUseVG(
    ...
    new PurchaseWithVirtualItem(
        MUFFIN_CURRENCY_ITEM_ID,                // Item ID
        175));                                  // Initial price
```

## Virtual Currencies

### VirtualCurrency <a href="https://github.com/soomla/unity3d-store/blob/master/Soomla/Assets/Plugins/Soomla/Store/domain/virtualCurrencies/VirtualCurrency.cs" target="_blank"><img class="link-icon-small" src="/img/tutorial_img/linkImg.png"></a>

Every game that has an economy has at least one `VirtualCurrency`. `VirtualCurrency` is NOT a `PurchasableVirtualItem`. This is because in game stores, you never buy just a single "Gold Coin" or a "Muffin", but rather you buy a pack of them. Your users will be able to buy packs of your game’s `VirtualCurrency` by using `VirtualCurrencyPack` (explained later in this document). If for some reason you *do* want to sell a single currency you can do so by providing a `VirtualCurrencyPack` with an amount of 1.

#### **How to define**

``` cs
public static final String MUFFIN_CURRENCY_ITEM_ID = "currency_muffin";

public static final VirtualCurrency MUFFIN_CURRENCY = new VirtualCurrency(
    "Muffins",                                  // Name
    "Muffin currency",                          // Description
    MUFFIN_CURRENCY_ITEM_ID                     // Item id
);
```

#### **How to use**

As explained above, `VirtualCurrency` by itself is not very useful - in this example it is used when declaring a `VirtualCurrencyPack`.

Use `VirtualCurrency` when defining `VirtualCurrencyPack`s:

``` cs
public static VirtualCurrencyPack TENMUFF_PACK = new VirtualCurrencyPack(
    "10 Muffins",                               // Name
    "Pack of 10 muffin currency units",         // Description
    "muffins_10",                               // Item id
    10,                                         // Number of currency units in this pack
    MUFFIN_CURRENCY_ITEM_ID,                    // Associated currency with this pack
    new PurchaseWithMarket(                     // Purchase type
      TENMUFF_PACK_PRODUCT_ID,
      0.99)
);
```

**Give:**

Give a `VirtualCurrency` and get nothing in return.
This is useful if you'd like to give your users some amount of currency to begin with when they first download your game.

``` cs
// Give the user an initial balance of 1000 muffins.
StoreInventory.GiveItem("currency_muffin", 1000);
```

#### **Get the balance**
Get the balance of a specific `VirtualCurrency`.

``` cs
StoreInventory.GetItemBalance("currency_muffin");
```

### VirtualCurrencyPack <a href="https://github.com/soomla/unity3d-store/blob/master/Soomla/Assets/Plugins/Soomla/Store/domain/virtualCurrencies/VirtualCurrencyPack.cs" target="_blank"><img class="link-icon-small" src="/img/tutorial_img/linkImg.png"></a>

As mentioned above, in game stores you never buy just a "Gold Coin" or a "Muffin", you always buy a pack of the game's `VirtualCurrency`. This class represents exactly that: a pack of `VirtualCurrency`. Use this class to define various currency packs in your game.

#### **How to define**

``` cs
public static VirtualCurrencyPack FIFTYMUFF_PACK = new VirtualCurrencyPack(
    "50 Muffins",                              // Name
    "Pack of 50 muffin currency units",        // Description
    "muffins_50",                              // Item id
    50,                                        // Number of currency units in this pack
    MUFFIN_CURRENCY_ITEM_ID,                   // Associated currency with this pack
    new PurchaseWithMarket(                    // Purchase type
      FIFTYMUFF_PACK_PRODUCT_ID,
      1.99)
);
```

#### **How to use**

**Buy:**

When your user buys a `VirtualCurrencyPack` of 50 muffins, his/her muffin currency balance will be increased by 50, and the payment will be deducted.

``` cs
StoreInventory.BuyItem("muffins_50");
```

**Give:**
Give your users a 50-muffin pack for free. This is useful if you’d like to give your users a currency_pack to begin with when they first download your game.

``` cs
StoreInventory.GiveItem("muffins_50", 1);
```

**Take:**

This function simply deducts the user's balance. In case of a refund request, it is your responsibility to give the user back whatever he/she paid.

``` cs
StoreInventory.TakeItem("muffins_50", 1);
```

#### **Get the balance**

`VirtualCurrencyPack`s do not have a balance of their own in the database. When a user purchases a `VirtualCurrencyPack`, the balance of the associated `VirtualCurrency` is increased.

``` cs
StoreInventory.GetItemBalance("currency_muffin");
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

### SingleUseVG <a href="https://github.com/soomla/unity3d-store/blob/master/Soomla/Assets/Plugins/Soomla/Store/domain/virtualGoods/SingleUseVG.cs" target="_blank"><img class="link-icon-small" src="/img/tutorial_img/linkImg.png"></a>

#### **How to define**

The most basic and common kind of a `VirtualGood` is a `SingleUseVG`. `SingleUseVG`s can be purchase by your users multiple times. No limits!

**The SingleUseVG's characteristics are:**

- Can be purchased an unlimited number of times.

- Has a balance that is saved in the database. Its balance goes up when you "give" it or "buy" it. The balance goes down when you “take” it (for example in the case of a refund).

``` cs
public static VirtualGood MUFFINCAKE_GOOD = new SingleUseVG(
    "Fruit Cake",                              // Name
    "Customers buy a double portion!",         // Description
    "fruit_cake",                              // Item ID
    new PurchaseWithVirtualItem(               // Purchase type
      MUFFIN_CURRENCY_ITEM_ID,
      225));
```

#### **How to use**

**Buy:**

When your user buys a `SingleUseVG` ("fruit_cake" in our example) his/her "fruit_cake" balance will be increased by 1, and the payment will be deducted.

``` cs
StoreInventory.BuyItem("fruit_cake");
```

**Give:**

Gives your user the given amount of the `SingleUseVG` with the given `itemId` ("fruit_cake" in our example) for free. This is useful if you'd like to give your users a `SingleUseVG` to start with when they first download your game.

``` cs
StoreInventory.GiveItem("fruit_cake", 10);
```

**Take:**

This function simply deducts the user's balance. In case of a refund request, it is your responsibility to give the user back whatever he/she paid.  

``` cs
StoreInventory.TakeItem("fruit_cake", 1);
```

#### **Get the balance**
Get the balance of a specific `SingleUseVG`.

``` cs
StoreInventory.GetItemBalance("fruit_cake");
```

### SingleUsePackVG <a href="https://github.com/soomla/unity3d-store/blob/master/Soomla/Assets/Plugins/Soomla/Store/domain/virtualGoods/SingleUsePackVG.cs" target="_blank"><img class="link-icon-small" src="/img/tutorial_img/linkImg.png"></a>

Sometimes, you'll want to to sell packs of `SingleUseVG`s. To support these cases, we've created `SingleUsePackVG`.

**The SingleUsePackVG's characteristics are:**

- Can be purchased an unlimited number of times.

- Doesn't have a balance in the database. The `SingleUseVG` that's associated with this pack has its own balance. When your users buy a `SingleUsePackVG`, the balance of the associated `SingleUseVG` goes up in the amount you defined for the pack.

**For Example:**
Suppose you offer a `SingleUsePackVG` of “5 fruit cakes”. The `SingleUseVG` that’s associated with this Pack is "fruit_cake". When your user buys a “5 fruit cake" Pack, the balance of fruit_cake is increased by 5 in the storage.

#### **How to define**

``` cs
public const string FRUITCAKE_PACK_PRODUCT_ID = "fruitcake_5pack";

// Define a pack of 5 "Fruit cake" goods that costs $2.99.
public static VirtualGood FRUITCAKE_GOOD_PACK = new SingleUsePackVG(
    "fruit_cake",                                // Item ID of associated good
    5,                                           // Amount of goods in pack
    "Fruit Cake Pack",                           // Name
    "A pack of 5 Fruit Cakes",                   // Description
    "fruit_cake_5pack",                          // Item ID
    new PurchaseWithMarket(                      // Purchase type
      FRUITCAKE_PACK_PRODUCT_ID,
      2.99)
);
```

#### **How to use**
The explanations for buying, giving, and taking are the same as those in [SingleUseVG](#singleusevg).

**Buy:**

``` cs
StoreInventory.BuyItem("fruit_cake_5pack");
```

**Give:**

``` cs
StoreInventory.GiveItem("fruit_cake_5pack", 1);
```

**Take:**

``` cs
StoreInventory.TakeItem("fruit_cake_5pack", 1);
```

#### **Get the balance**
`SingleUsePackVG`s do not have a balance of their own in the database. When a user buys a `SingleUsePackVG`, the balance of the associated `SingleUseVG` is increased. After buying a pack of 5 cream cup goods, your user's cream cup balance should be increased by 5.

Query the balance of the virtual good with item ID "cream_cup":

``` cs
StoreInventory.getVirtualItemBalance("fruit_cake");
```

### LifetimeVG <a href="https://github.com/soomla/unity3d-store/blob/master/Soomla/Assets/Plugins/Soomla/Store/domain/virtualGoods/LifetimeVG.cs" target="_blank"><img class="link-icon-small" src="/img/tutorial_img/linkImg.png"></a>

A `LifetimeVG` is a `VirtualGood` that can be bought once and is kept forever.

**The LifetimeVG's characteristics are:**

- Can only be purchased once.

- Your users can't have more than one of this item. In other words, they can have either 0 or 1 of this item at any given time.

If you declare a `LifetimeVG` with a purchase type of `PurchaseWithMarket`, it represents a non-consumable item in the market, i.e., it cannot be consumed and is owned by the user forever. (The Market saves this information forever)

However, notice that if you declare a `LifetimeVG` with a purchase type of `PurchaseWithVirtualItem`, the user will own the `LifetimeVG` **as long as the local storage of the game has NOT been deleted** (i.e. the version has been updated, or the game was deleted and re-downloaded, etc..).

#### **For Example**

``` cs
// A blue car that is purchased with virtual coins.
public static VirtualGood BLUE_CAR = new LifetimeVG(
    "Blue Car",                                                            // Name
    "This car is yours for as long as the game's storage doesn't change!", // Description
    "blue_car",                                                            // Item ID
    new PurchaseWithVirtualItem(COIN_CURRENCY_ITEM_ID, 3000)               // Purchase type
);

// A red car that is purchased with money in the market.
public static VirtualGood RED_CAR = new LifetimeVG(
    "Red Car",                                                             // Name
    "This car is yours FOREVER!!!",                                        // Description
    "red_car",                                                             // Item ID
    new PurchaseWithMarket(RED_CAR_PRODUCT_ID, 4.99)                       // Purchase type
);
```

Let's say a user purchases both cars. Even if the game's local storage is deleted, the user will still own the red car and will receive it upon `refreshInventory` process. However, the user will not own the blue car any longer.

<div class="info-box">IMPORTANT: When defining a `LifetimeVG` in the App Store (iOS), you MUST define its type as a Non-Consumable! For more information see our guide for [defining IAP products in the App Store](/soomla/ios/store/Store_AppStoreIAB).</div>

<br>

#### **How to define**

``` cs
public static VirtualGood MARRIAGE_GOOD = new LifetimeVG(
    "Marriage",                                  // Name
    "This is a lifetime thing",                  // Description
    "marriage",                                  // Item ID
    new PurchaseWithMarket(                      // Purchase type
      MARRIAGE_PRODUCT_ID,
      7.99)
);
```

#### **How to use**

**Buy:**

Buying a `LifetimeVG` means that the user will now own the item for the rest of time. Lifetime goods can be bought only once.

``` cs
StoreInventory.BuyItem("marriage");
```

**Give:**

Give a `LifetimeVG` and get nothing in return.
This is useful if you’d like to give your users a `LifetimeVG` when they first download your game.

``` cs
StoreInventory.GiveItem("marriage", 1);
```

**Take:**

This function simply deducts the user's balance. In case of a refund request, it is your responsibility to give the user back whatever he/she paid.

``` cs
StoreInventory.TakeItem("marriage", 1);
```

#### **Check ownership**
Check the ownership of a lifetime good:

``` cs
int balance = StoreInventory.GetItemBalance("marriage");

if (balance > 0) {
    ...
    // User owns a marriage lifetime good!
}
```

### EquippableVG <a href="https://github.com/soomla/unity3d-store/blob/master/Soomla/Assets/Plugins/Soomla/Store/domain/virtualGoods/EquippableVG.cs" target="_blank"><img class="link-icon-small" src="/img/tutorial_img/linkImg.png"></a>

An `EquippableVG` is a special type of `LifetimeVG`. In addition to the fact that an `EquippableVG` can be purchased once, it can also be equipped by your users. Equipping means that the user decides to currently use a specific `EquippableVG`.

**The EquippableVG's characteristics are:**

- Can be purchased only once.

- Can be equipped by the user.

- Inherits the definition of `LifetimeVG`.

**There are 3 equipping models:**

- `LOCAL` - The current `EquippableVG`'s equipping status doesn't affect any other `EquippableVG`.

- `CATEGORY` - In the containing category, if this `EquippableVG` is equipped, all other `EquippableVG`s are unequipped.

- `GLOBAL` - In the whole game, if this `EquippableVG` is equipped, all other `EquippableVG`s are unequipped.

#### **How to define**

In this example we're defining 2 characters, George and Kramer as `CATEGORY` equippable goods. This means the user can own both characters but can play only as one at a time.

``` cs
// Character "George" can be purchased for 350 Muffins.
public static VirtualGood GEORGE_GOOD = new EquippableVG(
    EquippableVG.EquippingModel.CATEGORY,                       // Equipping model
    "George",                                                   // Name
    "George is the best muffin eater in the north",             // Description
    "george_good",                                              // Item ID
    new PurchaseWithVirtualItem(MUFFIN_CURRENCY_ITEM_ID, 350)   // Purchase type
);

// Character "Kramer" can be purchased for 500 Muffins.
public static final VirtualGood KRAMER_GOOD = new EquippableVG(
    EquippableVG.EquippingModel.CATEGORY,                       // Equipping model
    "Kramer",                                                   // Name
    "Kramer kicks muffins like a super hero",                   // Description
    "kramer_good",                                              // Item ID
    new PurchaseWithVirtualItem(MUFFIN_CURRENCY_ITEM_ID, 500)   // Purchase type
);
```

#### **How to use**

**Buy:**

Buying an `EquippableVG` is exactly like buying a [`LifetimeVG`](#lifetimevg). The balance of "kramer" will be checked and if it is 0 buying will be allowed.

``` cs
StoreInventory.BuyItem("kramer");
```

**Give:**

Give an `EquippableVG` and get nothing in return.
This is useful if you’d like to give your users a free character to begin with when they first download your game.

``` cs
StoreInventory.GiveItem("george", 1);
```

**Take:**

This function simply deducts the user's balance. In case of a refund request, it is your responsibility to give the user back whatever he/she paid.

``` cs
StoreInventory.TakeItem("kramer", 1);
```

**Equip & Unequip:**

``` cs
// The user equips an owned good, George:
StoreInventory.EquipVirtualGood("george");

// The user tries to equip Kramer (while he has George equipped):
StoreInventory.EquipVirtualGood("kramer");
// Internally, George will be unequipped and Kramer will be equipped instead.

// The user unequips the currently equipped character (Kramer).
StoreInventory.UnEquipVirtualGood("kramer");
```

#### **Check ownership**

Check if user owns Kramer:

``` cs
int balance = StoreInventory.GetItemBalance("kramer");

if (balance > 0) {
    ...
    // User owns Kramer!
}
```

#### **Check equipping status**

Check if Kramer is currently equipped:

``` cs
StoreInventory.IsVirtualGoodEquipped("kramer");
```

### UpgradeVG <a href="https://github.com/soomla/unity3d-store/blob/master/Soomla/Assets/Plugins/Soomla/Store/domain/virtualGoods/UpgradeVG.cs" target="_blank"><img class="link-icon-small" src="/img/tutorial_img/linkImg.png"></a>

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

``` cs
// Create a SingleUseVG for "Strength"
public static VirtualGood STRENGTH_ATTR = new SingleUseVG(
    "Strength",                                                 // Name
    "Makes Kramer stronger so he can kick more muffins",        // Description
    "strength",                                                 // Item ID
    new PurchaseWithMarket(STRENGTH_PRODUCT_ID, 1.99)           // Purchase type
);

// Create 2 UpgradeVGs that represent 2 levels for the Strength attribute.
public static VirtualGood STRENGTH_UPGRADE_1 = new UpgradeVG(
    "strength",                   // Item ID of the associated good that is being upgraded
    "strength_upgrade_2",         // Item ID of the next upgrade good
    null,                         // Item ID of the previous upgrade good
    "Strength Upgrade Level 1",                                 // Name
    "Kramer will be able to kick twice as many muffins!",       // Description
    "strength_upgrade_1",                                       // Item ID
    new PurchaseWithVirtualItem(MUFFIN_CURRENCY_ITEM_ID, 50)    // Purchase type
);

public static VirtualGood STRENGTH_UPGRADE_2 = new UpgradeVG(
    "strength",                   // Item ID of the associated good that is being upgraded
    "strength_upgrade_3",         // Item ID of the next upgrade good
    "strength_upgrade_1",         // Item ID of the previous upgrade good
    "Strength Upgrade Level 2",                                 // Name
    "Kramer will be able to kick 4 times as many muffins!",     // Description
    "strength_upgrade_2",                                       // Item ID
    new PurchaseWithVirtualItem(MUFFIN_CURRENCY_ITEM_ID, 250)   // Purchase type
);

// UpgradeVG for strength level 3...
```

#### **How to use**

**Buy:**

When a user buys an upgrade, the `buy` method checks that the upgrade that's being purchased is valid.

``` cs
StoreInventory.BuyItem("strength_upgrade_2");
```

**Upgrade:**

When you upgrade a virtual good, the method performs a check to see that this upgrade is valid.

``` cs
StoreInventory.UpgradeGood("strength");
```

**Remove upgrades:**

Remove all upgrades from the virtual good with the given id (Strength in our example).

``` cs
StoreInventory.RemoveGoodUpgrades("strength");
```

**Give:**

Give a free upgrade:

``` cs
StoreInventory.GiveItem("strength_upgrade_2");
```

**Take:**

This function simply deducts the user's balance. In case of a refund request, it is your responsibility to give the user back whatever he/she paid. Essentially, taking an upgrade is the same as a downgrade.

``` cs
// The parameter amount is not used in this method.
StoreInventory.TakeItem("strength_upgrade_2", 1);
```

#### **Get current upgrade**

To get the current upgrade of a virtual good use `GetGoodCurrentUpgrade`. If our Strength attribute is currently upgraded to level 2, this method will return "strength_upgrade_2". (If the good has no upgrades, the method returns null).

``` cs
StoreInventory.GetGoodCurrentUpgrade("strength");
```

#### **Get current upgrade level**

To find out the upgrade level of a virtual good use `GetGoodUpgradeLevel`. If our Strength attribute is currently upgraded to level 2, this method will return 2. (If the good has no upgrades, the method returns 0).

``` cs
StoreInventory.GetGoodUpgradeLevel("strength");
```

## VirtualCategory <a href="https://github.com/soomla/unity3d-store/blob/master/Soomla/Assets/Plugins/Soomla/Store/domain/VirtualCategory.cs" target="_blank"><img class="link-icon" src="/img/tutorial_img/linkImg.png"></a>

A `VirtualCategory` is used to categorize `VirtualGood`s. Categories are helpful for organizational purposes, but especially come in handy when you have Equippable Virtual Goods.

<div class="info-box">If you don’t have any need for categories, you can just define all of your virtual goods in one category and call it something like “General”.</div>

#### **Real Game Examples:**
Let’s suppose your game has the following categories of virtual goods: "Power Ups", "Weapons", and  "Hats". Say you decide to make “Weapons” and “Hats” `CATEGORY` `EquippableVG`s. You can easily implement this functionality once the goods are divided into virtual categories.

#### **How to define**

``` cs
// Assume that MUFFINCAKE_ITEM_ID, PAVLOVA_ITEM_ID, etc.. are item ids of virtual goods that have been declared.
public static VirtualCategory SWEETS_CATEGORY = new VirtualCategory(
    "Cakes and Sweets",                         // Name
    new List<string>(new string[]               // List of item IDs
        { MUFFINCAKE_ITEM_ID,
          PAVLOVA_ITEM_ID,
          CHOCLATECAKE_ITEM_ID,
          CREAMCUP_ITEM_ID })
);
```

#### **Get category**

Check which category an item belongs to:

``` cs
StoreInfo.GetCategoryForVirtualGood(MUFFINCAKE_ITEM_ID);
```
