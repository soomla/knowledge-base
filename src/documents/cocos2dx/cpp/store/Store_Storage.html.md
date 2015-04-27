---
layout: "content"
image: "Storage"
title: "Storage"
text: "Your users get access to their data even when there’s no internet around. Our encrypted local storage is designed just for that."
position: 7
theme: 'platforms'
collection: 'cocos2dx_store'
module: 'store'
platform: 'cocos2dx'
---

# Storage & Metadata

## How SOOMLA storage works

Every user that downloads your game will have local, on-device storage that is encrypted and kept in an SQLite database. `CCSoomlaStore` is the only class you need to initialize in order to use the SOOMLA SDK.

Upon initialization, `CCSoomlaStore` initializes `CCStoreInfo`. The first time your game is loaded, `CCStoreInfo` is initialized with your implementation of `CCStoreAssets`. On that first time, your virtual economy’s metadata is saved on the local database on your users’ devices. After that it is always initialized from the database.

When you want to change the metadata (for example change the name of an item), in order for your users to see your changes, you'll have to bump the version, or in other words, increase the version number that the `getVersion` function (in your implementation of `CCStoreAssets`) returns. The value of `getVersion` will determine if the saved data in the local (on-device) database will be deleted or not. You'll need to bump the version after ANY change in order to see the changes, otherwise your store’s metadata will always be loaded from the local database, hence your changes will not be shown. **Keep this in mind when you release updates to your users.**

## Security

SOOMLA keeps the game's data in an encrypted database, and uses the AES (Advanced Encryption Standard) algorithm for encryption. This algorithm is a standard symmetric encryption algorithm used to secure the user's data on the device. AES is a common approach to solve this kind of problem. To learn more about AES read [here](http://en.wikipedia.org/wiki/Advanced_Encryption_Standard).

In order to protect your game from 'bad people', follow these guidelines:

- SOOMLA keeps your game's data in an encrypted database. In order to encrypt your data, SOOMLA generates a private key out of several parts of information. The **Soomla Secret** (in older versions was called customSec) is one of them. SOOMLA requires that you provide this value when initializing `CCSoomlaStore` and before releasing your game.

  <div class="warning-box">You can change this value once! If you try to change it again, old data from the database will become unavailable.</div>

- Following Google's recommendation, SOOMLA also recommends that you split your public key and construct it on runtime or even use bit manipulation on it in order to hide it. The key itself is not secret information but if someone replaces it, your application might receive fake messages that might harm it.

## Useful Classes
To further understand how SOOMLA handles storage, below are descriptions about each of the relevant classes.

### CCStoreInventory

`CCStoreInventory` is a utility class that provides you with functions that perform store-related operations. With `CCStoreInventory` you can give or take items from your users, buy items or upgrade them, as well as get the balance of items.

<br>

#### **Example Usages**

Give the user 10 pieces of a virtual currency with itemId "currency_coin":

``` cpp
soomla::CCStoreInventory::sharedStoreInventory()->giveItem("currency_coin", 10);
```

Take 10 virtual goods with itemId "green_hat":

``` cpp
soomla::CCStoreInventory::sharedStoreInventory()->takeItem("green_hat", 10);
```

Get the current balance of green hats (virtual goods with itemId "green_hat"):

``` cpp
int greenHatsBalance = soomla::CCStoreInventory::sharedStoreInventory()->getItemBalance("green_hat");
```

### CCStoreInfo

This class holds all of the metadata information about your specific game. It is initialized with your implementation of `CCStoreAssets` and you can use it to retrieve information about your specific game. `CCStoreInfo` holds your store's:

- Virtual currencies

- Virtual currency packs

- Virtual goods of all kinds

- Virtual categories

`CCStoreInfo` can be questioned about the existence of `VirtualItem`s and the associations between them.
`CCStoreInfo` is always initialized from the database, except for the first time the game is loaded - in that case it is initialized with your implementation of `CCStoreAssets`. When your game loads for the first time, the virtual economy's metadata is saved, and from that moment on it'll be loaded from the database.

<br>

#### **Example Usage**

Get all the `CCVirtualCurrencies`:

``` cpp
CCArray *vcArray = soomla::CCStoreInfo::sharedStoreInfo()->getVirtualCurrencies();
```

## Storage Internals

Following are the internal classes that make up the storage. However, we highly recommend that you avoid accessing these storage mechanisms directly, and instead use `CCStoreInventory`’s convenient functions.

- `CCVirtualGoodsStorage`

- `CCVirtualItemStorage`

- `CCVirtualCurrencyStorage`
