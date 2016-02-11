---
layout: "soomla-content"
image: "Wrench"
title: "Debugging"
text: "Debugging methods and tips for cocos2dx-store."
position: 6
theme: 'soomla-cocos2dx'
collection: 'soomla_cocos2djs_store'
module: 'store'
lang: 'js'
platform: 'cocos2dx'
---

# Debugging

This document addresses all Soomla's libs.

## Debugging

You can enable debug logging by setting `Soomla.DEBUG` to **true**.  

**Debug logging can also be enabled at build time:**

- **Android**:  Add `-DSOOMLA_DEBUG=1` to `APP_CPPFLAGS` in your Application.mk.

- **iOS**:  Set `SOOMLA_DEBUG=1` in your Build Settings' Preprocessor Macros.

**See debug messages from native platforms:**

- **android-profile:** Set the `logDebug` variable in com.soomla.store.StoreConfig to **true**.

- **ios-profile:** Make sure you have also `DEBUG=1` in your Build Settings' Preprocessor Macros (for Debug only).
