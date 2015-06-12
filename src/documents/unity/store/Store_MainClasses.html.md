---
layout: "content"
image: "Tutorial"
title: "Main Classes"
text: "The main classes of unity3d-store contain functionality for store-related operations such as purchasing, billing, inventory querying and storage."
position: 4
theme: 'platforms'
collection: 'unity_store'
module: 'store'
platform: 'unity'
---

#Main Classes

Here you can find descriptions of some of the main classes and interfaces of unity3d-store. These classes contain functionality for store-related operations such as purchasing, billing, inventory querying and storage.

To see more usage examples of the functions of these classes see [Economy Model](/unity/store/Store_Model).

##SoomlaStore <a href="https://github.com/soomla/unity3d-store/blob/master/Soomla/Assets/Plugins/Soomla/Store/SoomlaStore.cs" target="_blank"><img class="link-icon" src="/img/tutorial_img/linkImg.png"></a>

SoomlaStore holds the basic assets needed to operate the Store. You can use it to purchase products from the Market. It provides you with functionality such as querying the inventory for information, and starting a purchase process with the market (Google Play, Amazon Appstore, App Store).

<div class="info-box">This is the only class you need to initialize in order to use the SOOMLA SDK. More about this in [Getting Started](/unity/store/Store_GettingStarted).</div>

Taken from our Muffin Rush [Example](https://github.com/soomla/unity3d-store/tree/master/Soomla/Assets/Examples/MuffinRush):

``` cs
public class ExampleWindow : MonoBehaviour {
    ...
    void Start () {
		...
		SoomlaStore.Initialize(new MuffinRushAssets());
		...
	}
}
```

<br>

###Important Functions

**`RefreshInventory()`**

This function queries the Market’s inventory, and creates a list of all metadata stored in the Market (the items that have been purchased). The metadata includes the item's name, description, price, product ID, etc. Upon failure, an error message is printed.  

## StoreInfo <a href="https://github.com/soomla/unity3d-store/blob/master/Soomla/Assets/Plugins/Soomla/Store/data/StoreInfo.cs" target="_blank"><img class="link-icon" src="/img/tutorial_img/linkImg.png"></a>

`StoreInfo` is the mother of all metadata information about your specific game.

This class holds your store's

- Virtual currencies
- Virtual currency packs
- Virtual goods of all kinds
- Virtual categories
- Non-consumable items

`StoreInfo` can be questioned about the existence of `VirtualItem`s and the associations between them.

**Example:**

Get the first upgrade of the virtual good with item id "strength" (This is the "long" way, you should actually use `StoreInventory`'s function):

``` cs
UpgradeVG firstUpgrade = StoreInfo.GetFirstUpgradeForVirtualGood("strength");
```

`StoreInfo` is always initialized from the database, except for the first time the game is loaded - in that case it is initialized with your implementation of `IStoreAssets`, a class that represents your game's metadata. When your game loads for the first time, the virtual economy's metadata is saved, and from that moment on it'll be loaded from the database.


## StoreInventory <a href="https://github.com/soomla/unity3d-store/blob/master/Soomla/Assets/Plugins/Soomla/Store/StoreInventory.cs" target="_blank"><img class="link-icon" src="/img/tutorial_img/linkImg.png"></a>

`StoreInventory` is a utility class that provides you with functions that perform store-related operations. With `StoreInventory` you can give or take items from your users. You can buy items or upgrade them. You can also check their equipping status and change it.

###Important Functions

**`BuyItem(string itemId)`**

Buys the item that has the given itemId according to its purchase type - either with real money ($$$) or with other virtual items. Read more about PurchaseTypes in [Economy Model](/unity/store/Store_Model#purchase-types).

**Example:** Buy a virtual item with `itemId` "blue_hat":

``` cs
StoreInventory.BuyItem("blue_hat");
```

<br>

**`GiveItem(string itemId, int amount)`**

Gives your user the given amount of the virtual item with the given item ID, and gets nothing in return. For example, when your user plays your game for the first time you can GIVE him 1000 free gems to start out with.

**Example:** Give the user 10 pieces of a virtual currency with `itemId` "currency_coin":

``` cs
StoreInventory.GiveItem("currency_coin", 10);
```

<br>

**`TakeItem(string itemId, int amount)`**

Takes from your user the given amount of the virtual item with the given item ID. For example, when your user requests a refund you TAKE the item he/she is returning.

**Example:**  Take 1 virtual good with `itemId` "green_hat":

``` cs
StoreInventory.TakeItem("green_hat", 1);
```
