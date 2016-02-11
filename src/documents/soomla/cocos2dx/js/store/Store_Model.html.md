---
layout: "soomla-content"
image: "Modeling"
title: "Economy Model & API"
text: "Every game economy can be based on SOOMLA's economy model. Learn the building blocks of creating a virtual economy with currencies and virtual goods."
position: 3
theme: 'soomla-cocos2dx'
collection: 'soomla_cocos2djs_store'
module: 'store'
lang: 'js'
platform: 'cocos2dx'
---

# Economy Model & API

SOOMLA's cocos2dx-store provides a complete data model implementation for virtual economies. Every game economy has
currencies, packs of currencies that can be sold, and items that can be sold either for money or in exchange for other
items. And these are just the very basics, of course. This tutorial contains descriptions of each entity in the economy
model, along with examples.

All the classes are implemented in [soomla-store.js](https://github.com/soomla/cocos2dx-store/blob/master/js/soomla-store.js).

![alt text](/img/tutorial_img/soomla_diagrams/EconomyModel.png "Soomla Economy Model")

## Virtual Items

Almost every entity in your virtual economy will be a Virtual Item.
There are many types of Virtual Items and you can select the ones that fit your needs. Each one of the various types
extends the class `VirtualItem` and adds its own behavior.

Almost all `VirtualItems` are `PurchasableVirtualItems`. Among other features, all Virtual items have 2 functions to
help you easily interact with them: `give` and `take`. Preferably, you should use the two methods provided in `StoreInventory`
for these purposes, called `giveItem` and `takeItem`. Use these functions to give or take from your users a specific
amount of a specific Virtual Item.

Use `giveItem` when you want to give your user something and get nothing in return. (If you want to give something and
get something in return, you need to use `buyItem`). Use `takeItem` when you want to take something from your user, for
example in the case of a refund.

Every virtual item has an ID, a unique string that we use to identify the different items.

## PurchaseTypes

As stated above, almost all Virtual Items are purchasable, or as we call them, `PurchasableVirtualItem`s. Purchase types
are used to indicate whether an item will be purchased with money or with other virtual items: there are 2 different
purchase types described below.

<div class="info-box">In the examples below the declarations of purchase types are shown as a part of
`PurchasableVirtualItem` declarations, because this is the most common use of purchase types.</div>

### PurchaseWithMarket

This kind of `PurchaseType` should be attached to items that you want to make available for purchase in the Market (App
Store, Google Play Store, etc..) for real money. When you create an instance of `PurchaseWithMarket`, you need to define
the associated `VirtualItem` in the Market.

**NOTE:** The product ID you define in your implementation of `IStoreAssets` should be the same as you define in the
Market.

#### **For Example**
Suppose that in your game, you offer a “No-Ads” feature for $1.99 in the Market. The code below shows how you need to
declare the `PurchaseType` parameter of your “No-Ads” item.

``` js
var noAds = Soomla.Models.LifetimeVG.create({
    ...
    purchasableItem: Soomla.Models.PurchaseWithMarket.create({
      marketItem: Soomla.Models.MarketItem.create({
        productId: NO_ADDS_PRODUCT_ID,
        consumable: Soomla.Models.MarketItem.Consumable.NONCONSUMABLE,
        price: 1.99
      })
    })
));
```

There is another way to define this purchase type:

``` js
var thousandmuffPack = Soomla.Models.VirtualCurrencyPack.create({
    ...
    purchasableItem: Soomla.Models.PurchaseWithMarket.createWithMarketItem(
        THOUSANDMUFF_PACK_PRODUCT_ID,                       // Product ID
        8.99)                                               // Initial price
});
```

For more info on how to declare your items in the Market, see one of our tutorials on In-app Billing:

- For Android: [Google Play IAB](/soomla/android/store/Store_GooglePlayIAB) or [Amazon IAB](/soomla/android/store/Store_AmazonIAB)

- For iOS: [App Store IAB](/soomla/ios/store/Store_AppStoreIAB)

### PurchaseWithVirtualItem

Any item with purchase type `PurchaseWithVirtualItem` can be purchased with any `VirtualItem`, like a sort of trade.
When creating an instance of `PurchaseWithVirtualItem`, you need to provide the ID of the virtual item that you want to
be paid with and the amount of that virtual item.

#### **For Example**
Suppose that in your game, you offer a chocolate cake that can be bought by paying 250 “Muffins”. The item being
purchased is a `chocolateCakeGood`, the item (virtual currency) to pay with is “Muffin”, and the amount is 250.

``` js
// A chocolate cake good can be purchased with 250 muffin currency units
var chocolateCakeGood = Soomla.Models.SingleUseVG.create({
    ...
    purchasableItem: Soomla.Models.PurchaseWithVirtualItem.create({
      pvi_itemId: muffinCurrency_ID,
      pvi_amount: 250
    })
});
```

## Virtual Currencies

### VirtualCurrency

Every game that has an economy has at least one `VirtualCurrency`. `VirtualCurrency` is NOT a `PurchasableVirtualItem`.
This is because in game stores, you never buy just a single "Gold Coin" or a "Muffin", but rather you buy a pack of them.
Your users will be able to buy packs of your game’s `VirtualCurrency` by using `VirtualCurrencyPack` (explained later in
this document). If for some reason you *do* want to sell a single currency you can do so by providing a
`VirtualCurrencyPack` with an amount of 1.

#### **How to define**

``` js
var muffinCurrency = Soomla.Models.VirtualCurrency.create({
  name: 'Muffins',              // Name
  description: '',              // Description
  itemId: 'muffinCurrency_ID'   // Item ID  
});
```

#### How to use

A `VirtualCurrency` by itself is not very useful, because it cannot be sold individually. To sell currency, you need to
use a VirtualCurrencyPack (see section below).

``` js
//Use VirtualCurrency when defining VirtualCurrencyPacks:

var tenmuffPack = Soomla.Models.VirtualCurrencyPack.create({
    name: "10 Muffins",                       // Name                    
    description: "Test refund of an item",    // Description             
    itemId: "muffins_10",                     // Item ID                 
    currency_amount: 10,                      // Number of currency units
    currency_itemId: MUFFIN_CURRENCY_ITEM_ID, // Associated currency     
    purchasableItem: Soomla.Models.PurchaseWithMarket.createWithMarketItem(           
        'tenMuff_prodID',                     // Product ID              
        0.99)                                 // Initial price           
  });
);
```

**Give:**

Give a VirtualCurrency and get nothing in return.
This is useful if you’d like to give your users some amount of currency to begin with when they first download your game.

``` js
// If this is the first time playing, give the user an initial balance of 1000 muffins.
Soomla.storeInventory.giveItem('currency_coin', 1000);
```

#### **Get the balance**

Get the balance of a specific VirtualCurrency.

``` js
Soomla.storeInventory.getItemBalance('currency_muffin');
```

### VirtualCurrencyPack

As mentioned above, in game stores you never buy just a "Gold Coin" or a "Muffin", you always buy a pack of the game's
`VirtualCurrency`. This class represents exactly that: a pack of `VirtualCurrency`. Use this class to define various
currency packs in your game.

####**How to define**

```js
var fiftymuffPack = Soomla.Models.VirtualCurrencyPack.create({
    name: '50 Muffins',                          // Name                    
    description: 'Test cancellation of an item', // Description             
    itemId: 'muffins_50',                        // Item id                 
    currency_amount: 50,                         // Number of currency units
    currency_itemId: 'muffinCurrency_ID,         // Associated currency     
    purchasableItem: Soomla.Models.PurchaseWithMarket.createWithMarketItem(          
        'fiftyMuff_prodID',                      // Target item ID          
        1.99)                                    // Initial price           
});
```

#### **How to use**

**Buy:**

When your user buys a VirtualCurrencyPack of 50 muffins, his/her muffin currency balance will be increased by 50, and
the payment will be deducted.

``` js
Soomla.storeInventory.buyItem('muffins_50');
```

**Give:**

Give your users a 50-muffin pack for free. This is useful if you’d like to give your users a currency_pack to begin with
when they first download your game.

``` js
Soomla.storeInventory.giveItem('muffins_50', 1);
```

**Take:**

This function simply deducts the user’s balance. In case of a refund request, it is your responsibility to give the user
back whatever he/she paid.

Take back the 50-muffin pack that the user owns:

``` js
Soomla.storeInventory.takeItem('muffins_50', 1);
```

#### **Get the balance**

VirtualCurrencyPacks do not have a balance of their own in the database. When a user purchases a VirtualCurrencyPack,
the balance of the associated VirtualCurrency is increased.

``` js
Soomla.storeInventory.getItemBalance('currency_muffin');
```

## Virtual Goods

Every virtual good is a `PurchasableVirtualItem`. You can buy it with other `VirtualItem`s, or in the market with money.
Virtual goods are the heart of every virtual economy. These are the game objects you’re going to want to sell in your
game's store.

Virtual goods need to be declared in your implementation of `IStoreAssets`.

Every virtual good belongs to one of the following groups:

1. Single Use

2. Single Use Pack

3. Lifetime

4. Equippables

5. Upgradables

Below are detailed descriptions of each category.

### SingleUseVG

The most basic and common kind of a `VirtualGood` is a `SingleUseVG`. `SingleUseVG`s can be purchase by your users
multiple times. No limits!

**The SingleUseVG's characteristics are:**

- Can be purchased an unlimited number of times.

- Has a balance that is saved in the database. Its balance goes up when you "give" it or "buy" it. The balance goes
down when you “take” it (for example in the case of a refund).

#### **How to define**

``` js
var fruitCakeGood = Soomla.Models.SingleUseVG.create({
    name: "Fruit Cake",                 // Name          
    description: "Tasty cake",          // Description   
    itemId: "fruit_cake",               // Item id       
    purchasableItem: Soomla.Models.PurchaseWithVirtualItem.create({
      pvi_itemId: 'muffinCurrency_ID',  // Target item ID
      pvi_amount: 225                   // Initial price
    })
  });
```

#### **How to use**

**Buy:**

When your user buys a `SingleUseVG`, for example “fruit_cake”, his/her “fruit_cake” balance will be increased by 1,
and the payment will be deducted.

``` js
Soomla.storeInventory.buyItem("fruit_cake");
```

**Give:**

Gives your user the given amount of the `SingleUseVG` with the given itemId(“fruit_cake” in our example) for free.
This is useful if you’d like to give your users a `SingleUseVG` to start with when they first download your game.

``` js
Soomla.storeInventory.giveItem("fruit_cake", 1);
```

**Take:**

This function simply deducts the user’s balance. In case of a refund request, it is your responsibility to give the
user back whatever he/she paid.

``` js
Soomla.storeInventory.takeItem("fruit_cake", 1);
```

#### **Get the balance**

Get the balance of a specific `SingleUseVG`.

``` js
Soomla.storeInventory.getItemBalance("fruit_cake");
```

### SingleUsePackVG

Sometimes, you'll want to to sell packs of `SingleUseVG`s. To support these cases, we've created `SingleUsePackVG`.

**The SingleUsePackVG's characteristics are:**

- Can be purchased an unlimited number of times.

- Doesn't have a balance in the database. The `SingleUseVG` that's associated with this pack has its own balance. When
your users buy a `SingleUsePackVG`, the balance of the associated `SingleUseVG` goes up in the amount you defined for
the pack.

**For Example:**
Suppose you offer a `SingleUsePackVG` of “5 fruit cakes”. The `SingleUseVG` that’s associated with this Pack is
"fruit_cake". When your user buys a “5 fruit cake" Pack, the balance of fruit_cake is increased by 5 in the storage.

#### **How to define**

``` js
  var fiveFruitcakeGoods = Soomla.Models.SingleUsePackVG.create({
     good_itemId: "fruit_cake",            // Item ID of associated good
     good_amount: 5,                       // Amount of goods in pack    
     name: "5 Fruitcakes",                 // Name                       
     description: "Pack of 5 fruitcakes",  // Description               
     itemId: "fruitcake_5",                // Item ID                    
     purchasableItem: Soomla.Models.PurchaseWithVirtualItem.create({             
       pvi_itemId: 'muffinCurrency_ID',    // Target item ID
       pvi_amount: 1750                    // Initial price
     })
   });
```

#### **How to use**

The explanations for buying, giving, and taking are the same as those in SingleUseVG.

**Buy:**

``` js
Soomla.storeInventory.buyItem("fruitcake_5");
```

**Give:**

``` js
Soomla.storeInventory.giveItem("fruitcake_5", 1);
```

**Take:**

```
Soomla.storeInventory.takeItem("fruitcake_5", 1);
```

#### **Get the balance**

SingleUsePackVGs do not have a balance of their own in the database. When a user buys a SingleUsePackVG, the balance of
the associated `SingleUseVG` is increased. After buying a pack of 5 fruit cakes, your user’s fruit cake balance should
be increased by 5.

Query the balance of the virtual good with item ID “fruit_cake”:

``` js
Soomla.storeInventory.getItemBalance("fruit_cake");
```

### LifetimeVG

A `LifetimeVG` is a `VirtualGood` that can be bought once and is kept forever.

**The LifetimeVG's characteristics are:**

- Can only be purchased once.

- Your users can't have more than one of this item. In other words, they can have either 0 or 1 of this item at any
given time.

If you declare a `LifetimeVG` with a purchase type of `PurchaseWithMarket`, it represents a non-consumable item in the
market, i.e., it cannot be consumed and is owned by the user forever. (The Market saves this information forever)

However, notice that if you declare a `LifetimeVG` with a purchase type of `PurchaseWithVirtualItem`, the user will own
the `LifetimeVG` **as long as the local storage of the game has NOT been deleted** (i.e. the version has been updated,
or the game was deleted and re-downloaded, etc..).

#### **For Example**

``` js
  // A blue car that is purchased with virtual coins.
  var BLUE_CAR = Soomla.Models.LifetimeVG.create({
    name: "Blue Car",
    description: "This car is yours as long as the game's storage doesn't change!",
    itemId: "blue_car",
    purchasableItem: Soomla.Models.PurchaseWithVirtualItem.create({
      pvi_itemId: 'coinCurrency_ID',
      pvi_amount: 3000
    })
  });

  // A red car that is purchased with money in the market.
  var RED_CAR = Soomla.Models.LifetimeVG.create({
    name: "Red Car",
    description: "his car is yours FOREVER!!!",
    itemId: "red_car",
    purchasableItem: Soomla.Models.PurchaseWithMarket.create({
      marketItem: Soomla.Models.MarketItem.create({
        productId: 'redCar_ProdID',
        price: 4.99
      })
    })
  });            
```

Let's say a user purchases both cars. Even if the game's local storage is deleted, the user will still own the red car
and will receive it upon `refreshInventory` process. However, the user will not own the blue car any longer.

<div class="info-box">IMPORTANT: When defining a `LifetimeVG` in the App Store (iOS), you MUST define its type as a
Non-Consumable! For more information see our guide for [defining IAP products in the App Store](/soomla/ios/store/Store_AppStoreIAB).
</div>

<br>

#### **How to define**

``` js
  var marriageGood = Soomla.Models.LifetimeVG.create({
    name: "Marriage",                          // Name
    description: "This is a LIFETIME thing.",  // Description
    itemId: "marriage",                        // Item ID
    purchasableItem: Soomla.Models.PurchaseWithVirtualItem.create({
      pvi_itemId: 'muffinCurrency_ID',
      pvi_amount: 100
    })
  });
```

#### **How to use**

**Buy:**

Buying a `LifetimeVG` means that the user will now own the item for the rest of time, unless the game developer decides
to explicitly take away the item from the user. Lifetime goods can be bought only once.

``` js
Soomla.storeInventory.buyItem("marriage");
```

**Give:**

Give a `LifetimeVG` and get nothing in return. This is useful if you’d like to give your users a `LifetimeVG` when they
first download your game.

```js
Soomla.storeInventory.giveItem("marriage", 1);
```

**Take:**

This function simply deducts the user’s balance. In case of a refund request, it is your responsibility to give the user
back whatever he/she paid.

```js
Soomla.storeInventory.takeItem("marriage", 1);
```

#### **Check ownership**

``` js
//If the balance is greater than 0, the user owns this LifetimeVG.
Soomla.storeInventory.getItemBalance("marriage");
```

### EquippableVG

An `EquippableVG` is a special type of `LifetimeVG`. In addition to the fact that an `EquippableVG` can be purchased
once, it can also be equipped by your users. Equipping means that the user decides to currently use a specific
`EquippableVG`.

**The EquippableVG's characteristics are:**

- Can be purchased only once.

- Can be equipped by the user.

- Inherits the definition of `LifetimeVG`.

**There are 3 equipping models:**

- `LOCAL` - The current `EquippableVG`'s equipping status doesn't affect any other `EquippableVG`.

- `CATEGORY` - In the containing category, if this `EquippableVG` is equipped, all other `EquippableVG`s are unequipped.

- `GLOBAL` - In the whole game, if this `EquippableVG` is equipped, all other `EquippableVG`s are unequipped.

#### **How to define**

In this example we're defining 2 characters, George and Kramer as `CATEGORY` equippable goods. This means the user can
own both characters but can play only as one at a time.

``` js
  // Character "George" can be purchased for 350 Muffins.
  var georgeGood = Soomla.Models.EquippableVG.create({
    equipping: Soomla.Models.EquippableVG.EquippingModel.CATEGORY,     
    name: "George",                                                    
    description: "The best muffin eater in the north",                 
    itemId: "george",                                                  
    purchasableItem: Soomla.Models.PurchaseWithVirtualItem.create({    
      pvi_itemId: 'muffinCurrency_ID',
      pvi_amount: 350
    })
  });

  // Character "Kramer" can be purchased for 500 Muffins.
  var kramerGood = Soomla.Models.EquippableVG.create({
    equipping: Soomla.Models.EquippableVG.EquippingModel.CATEGORY,     
    name: "Kramer",                                                               
    description: "Knows how to get muffins",                               
    itemId: "kramer",                                                          
    purchasableItem: Soomla.Models.PurchaseWithVirtualItem.create({      
      pvi_itemId: 'muffinCurrency_ID',
      pvi_amount: 500
    })
  });
```
#### **How to use**

**Buy:**

Buying an `EquippableVG` is exactly like buying a `LifetimeVG`. The balance of “kramer” will be checked and if it is 0,
buying will be allowed.

``` js
Soomla.storeInventory.buyItem("kramer");
```

**Give:**

Give an `EquippableVG` and get nothing in return.
This is useful if you’d like to give your users a free character to begin with when they first download your game.

``` js
Soomla.storeInventory.giveItem("george", 1);
```

**Take:**

This function simply deducts the user’s balance. In case of a refund request, it is your responsibility to give the user
back whatever he/she paid.

``` js
Soomla.storeInventory.takeItem("kramer", 1);
```

**Equip & Unequip:**

``` js
// The user equips an owned good, George:
Soomla.storeInventory.equipVirtualGood("george");

// The user tries to equip Kramer (while he has George equipped):
Soomla.storeInventory.equipVirtualGood("kramer");
// Internally, George will be unequipped and Kramer will be equipped instead.

// The user unequips the currently equipped character (Kramer).
Soomla.storeInventory.unEquipVirtualGood("kramer");
```

#### **Check ownership**

``` js
//Check if user owns Kramer:
//If the balance is greater than 0, the user owns Kramer.
Soomla.storeInventory.getItemBalance("kramer");
```

**Check equipping status:**

``` js
//Check if Kramer is currently equipped:
Soomla.storeInventory.isVirtualGoodEquipped("kramer");
```

### UpgradeVG

An `UpgradeVG` is a `VirtualGood` in a series of `UpgradeVG`s that define an upgrade sequence for a given `VirtualGood`.
The associated `VirtualGood` can be of any type (`SingleUseVG`, `EquippableVG`, etc..).

When the user buys an`UpgradeVG`, a check is performed to make sure the appropriate conditions are met.

**`UpgradeVG`'s characteristics:**

- Can be purchased whenever it's not the current upgrade.

- Doesn't have a balance in the database.

**Important `UpgradeVG` class members:**

- `goodItemId` - the itemId of the `VirtualGood` associated with this upgrade.

- `prevItemId` - the itemId of the `UpgradeVG` that comes before this one, or if this is the first `UpgradeVG` in the
scale then this value is null.

- `nextItemId` - the itemId of the `UpgradeVG` that comes after this one, or if this is the last `UpgradeVG` in the
scale then the value is null.

#### **How to define**

Say you have a strength attribute in your game and that strength is upgradeable on a scale of 1-3.  This is what you'll
need to create:

1. SingleUseVG for 'strength'

2. UpgradeVG for strength 'level 1'

3. UpgradeVG for strength 'level 2'

4. UpgradeVG for strength 'level 3'

In the following example there is a `SingleUseVG` for "Strength" and 3 upgrade levels for it.

``` js
  // Create a SingleUseVG for "Strength"
  var strength = Soomla.Models.SingleUseVG.create({
    name: "Strength Attribute",       // Name         
    description: "Be strong!",        // Description  
    itemId: "strength_ID",            // Item id      
    purchasableItem: Soomla.Models.PurchaseWithMarket.create({
      marketItem: Soomla.Models.MarketItem.create({
        productId: 'strength_ProdID',
        price: 1.99
      })
    })
  });

  // Create 2 UpgradeVGs that represent 2 upgrade levels for strength.
  var strength_upgrade1 = Soomla.Models.UpgradeVG.create({                  
      good_itemId: "strength_ID",   // Item ID of the associated good that is being upgraded
      prev_itemId: null,            // Item ID of the previous upgrade good
      next_itemId: "strength_upgrade2_ID", // Item ID of the next upgrade good
      name: "Strength Upgrade 1",   // Name
      description: "Kramer will be able to kick twice as many muffins",
      itemId: "strength_upgrade1_ID",   // Item ID
      purchasableItem: Soomla.Models.PurchaseWithVirtualItem.create({
        pvi_itemId: 'muffinCurrency_ID',
        pvi_amount: 50
      })
  });
  var strength_upgrade2 = Soomla.Models.UpgradeVG.create({                  
      good_itemId: "strength_ID",         
      prev_itemId: strength_upgrade1_ID,  
      next_itemId: "strength_upgrade3_ID",
      name: "Strength Upgrade 2",         
      description: "Kramer will be able to kick 4 times as many muffins",
      itemId: "strength_upgrade2_ID",
      purchasableItem: Soomla.Models.PurchaseWithVirtualItem.create({
        pvi_itemId: 'muffinCurrency_ID',
        pvi_amount: 100
      })
  });
// UpgradeVG for strength level 3...
```

#### **How to use**

**Buy:**

When a user buys an upgrade, the buy method checks that the upgrade that’s being purchased is valid.

``` js
Soomla.storeInventory.buyItem("strength_upgrade2_ID");
```

**Upgrade:**

When you upgrade a virtual good, the method performs a check to see that this upgrade is valid.

``` js
Soomla.storeInventory.upgradeGood("strength_ID");
```

**Remove upgrades:**

Remove all upgrades from the virtual good with the given ID (strength in our example).

``` js
Soomla.storeInventory.removeGoodUpgrades("strength_ID");
```

**Give:**

Give a free upgrade.

``` js
Soomla.storeInventory.giveItem("strength_upgrade2_ID", 1);
```

**Take:**

This function simply deducts the user’s balance. In case of a refund request, it is your responsibility to give the user back whatever he/she paid. Essentially, taking an upgrade is the same as a downgrade.

``` js
// The parameter amount is not used in this method.
Soomla.storeInventory.takeItem("strength_upgrade2_ID", 1);
```

#### **Get current upgrade**

To get the current upgrade of a virtual good use getGoodCurrentUpgrade. If the good has no upgrades, the method will return null.

``` js
Soomla.storeInventory.getGoodCurrentUpgrade("strength_ID");
```

#### **Get current upgrade level**

To find out the upgrade level of a virtual good use getGoodUpgradeLevel. If the good has no upgrades, the method returns 0.

``` js
Soomla.storeInventory.getGoodUpgradeLevel("strength_ID");
```

## VirtualCategory

A `VirtualCategory` is used to categorize `VirtualGood`s. Categories are helpful for organizational purposes, but
especially come in handy when you have Equippable Virtual Goods.

<div class="info-box">If you don’t have any need for categories, you can just define all of your virtual goods in one
category and call it something like “General”.</div>

#### **Real Game Examples:**

Let’s suppose your game has the following categories of virtual goods: "Power Ups", "Weapons", and  "Hats".
Say you decide to make “Weapons” and “Hats” `CATEGORY` `EquippableVG`s. You can easily implement this functionality once
the goods are divided into virtual categories.

#### How to define

``` js
  var cakes = Soomla.Models.VirtualCategory.create({
    name: "Cakes",
    goods_itemIds: [
      'MUFFINCAKE_ITEM_ID',
      'PAVLOVA_ITEM_ID',
      'PAVLOVA_10',
      'CHOCLATECAKE_ITEM_ID',
      'CREAMCUP_ITEM_ID',
      'CREAMCUP_10'
    ]
  });
```

#### Get category

**Check which category an item belongs to:**

``` js
Soomla.storeInfo.getCategoryForVirtualGood('MUFFINCAKE_ITEM_ID');
```
