---
layout: "content"
image: "Tutorial"
title: "Main Classes"
text: "The main classes of cocos2dx-store contain functionality for store-related operations such as purchasing, billing, inventory querying and storage."
position: 4
theme: 'platforms'
collection: 'cocos2dx_store'
module: 'store'
platform: 'cocos2dx'
---

# Main Classes

Here you can find descriptions of some of the main classes and interfaces of cocos2dx-store. These classes contain functionality for store-related operations, provide you with various storages, and hold the basic assets needed to operate the store.

##CCSoomlaStore <a href="https://github.com/soomla/cocos2dx-store/blob/master/Soomla/CCSoomlaStore.h" target="_blank"><img class="link-icon" src="/img/tutorial_img/linkImg.png"></a>

`SoomlaStore` holds the basic assets needed to operate the store. You can use it to purchase products from the Market. It provides you with functionality such as querying the inventory for information, and starting a purchase process with the market (Google Play, App Store, etc…).

###Important Functions

`CCSoomlaStore::refreshInventory()`

This function queries the Market’s inventory, and creates a list of all metadata stored in the Market (the items that have been purchased). The metadata includes the item’s name, description, price, product ID, etc.

## CCStoreInfo <a href="https://github.com/soomla/cocos2dx-store/blob/master/Soomla/data/CCStoreInfo.h" target="_blank"><img class="link-icon" src="/img/tutorial_img/linkImg.png"></a>

StoreInfo is the mother of all metadata information about your specific game.

**This class holds your store’s**

- Virtual currencies
- Virtual currency packs
- Virtual goods of all kinds
- Virtual categories

StoreInfo can be questioned about the existence of `VirtualItem`s and the associations between them.

**Example:**

Get the first upgrade of the virtual good with item id “strength” (This is the “long” way, you should actually use `StoreInventory`’s function):

``` cpp
CCUpgradeVG *firstUpgrade = soomla::CCStoreInfo::sharedStoreInfo()->getFirstUpgradeForVirtualGood("strength");
```

`StoreInfo` is always initialized from the database, except for the first time the game is loaded - in that case it is initialized with your implementation of `IStoreAssets`, a class that represents your game’s metadata. When your game loads for the first time, the virtual economy’s metadata is saved, and from that moment on it’ll be loaded from the database.

## CCStoreInventory <a href="https://github.com/soomla/cocos2dx-store/blob/master/Soomla/CCStoreInventory.h" target="_blank"><img class="link-icon" src="/img/tutorial_img/linkImg.png"></a>

`StoreInventory` is a utility class that provides you with functions that perform store-related operations. With `StoreInventory` you can give or take items from your users. You can buy items or upgrade them. You can also check their equipping status and change it.

###Important Functions

`buyItem(const char *itemId, CCError **soomlaError)`

Buys the item with the given `itemId` according to its purchase type - either with real money ($$$) or with other virtual items. Read more about `PurchaseTypes` in Economy Model.

**Example:**

``` cpp
//Buy a virtual item with itemId “blue_hat”:
CCError *soomlaError = NULL;
CCStoreInventory::sharedStoreInventory()->buyItem("blue_hat", &soomlaError);
```
<br>

`giveItem(const char *itemId, int amount, CCError **soomlaError)`

Gives your user the given amount of the virtual item with the given item ID, and gets nothing in return. For example, when your user plays your game for the first time you can GIVE him 1000 free gems to start out with.

**Example:**

``` cpp
//Give the user 10 units of the virtual currency with itemId“currency_coin”:
CCError *soomlaError = NULL;
CCStoreInventory::sharedStoreInventory()->giveItem("currency_coin", 10, &soomlaError);
```
<br>

`takeItem(const char *itemId, int amount, CCError **soomlaError)`

Takes from your user the given amount of the virtual item with the given item ID. For example, when your user requests a refund you TAKE the item he/she is returning.

**Example:**

``` cpp
//Take 1 virtual good with itemId “green_hat”:
CCError *soomlaError = NULL;
CCStoreInventory::sharedStoreInventory()->takeItem("currency_coin", 1, &soomlaError);
```
