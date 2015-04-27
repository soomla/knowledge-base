---
layout: "content"
image: "Wrench"
title: "Debugging"
text: "Debugging methods and tips for cocos2dx-store."
position: 6
theme: 'platforms'
collection: 'cocos2dx_store'
module: 'store'
platform: 'cocos2dx'
---

# Error Handling & Debugging

This document addresses both cocos2dx-store and cocos2dx-profile.

## Debugging

You can enable debug logging in cocos2dx-store and cocos2dx-profile by setting `SOOMLA_DEBUG` in `CCSoomlaUtils.h` (located in soomla-cocos2dx-core submodule) to **true**.  

**Debug logging can also be enabled at build time:**

- **Android**:  Add `-DSOOMLA_DEBUG=1` to `APP_CPPFLAGS` in your Application.mk.

- **iOS**:  Set `SOOMLA_DEBUG=1` in your Build Settings' Preprocessor Macros.

**See debug messages from native platforms:**

- **android-profile:** Set the `logDebug` variable in com.soomla.store.StoreConfig to **true**.

- **ios-profile:** Make sure you have also `DEBUG=1` in your Build Settings' Preprocessor Macros (for Debug only).


## Error Handling

Since Cocos2d-x doesn't support exceptions, we use a different tactic to catch and work with exceptions on the native side. All functions that raise an exception on the native side have an additional `CCError` parameter to them on the cocos2dx-profile side. In order to know if an exception was raised, send a reference of `CCError` to the function, and inspect it after running.

### Example

If you'd like to purchase an item with the item ID `huge_sword`, and check if all went well after the purchase, call `CCSoomlaStore::buyItem()` like this:

``` cpp
soomla::CCError *err;
soomla::CCStoreInventory::sharedStoreInventory()->buyItem("huge_sword", &err);
if (err != NULL) {
    // ... handle error
}
```

<div class="warning-box">The CCError parameter is entirely optional, you can pass NULL instead if you do not wish to handle errors. But remember(!), error handling is your responsibility - cocos2dx-store won't do any external error handling (i.e. error handling that uses `CCError`) for you.</div>
