---
layout: "soomla-content"
image: "Wrench"
title: "Debugging"
text: "Debugging methods and tips for iOS-store."
position: 6
theme: 'soomla-ios'
collection: 'soomla_ios_store'
module: 'store'
platform: 'ios'
---

# Debugging

iOS-store provides debug messages that are printed to the log.
<br>In `StoreConfig.m` you will find:

``` objectivec
#ifdef DEBUG
BOOL STORE_DEBUG_LOG                = YES;
#else
BOOL STORE_DEBUG_LOG                = NO;
#endif
```

The debug messages are printed because the default value of `DEBUG` is 1 (can be found in your project's Build Settings).

![alt text](/img/tutorial_img/ios_debugging/debugging.png "Debugging")

If you don't want to see the log messages change `DEBUG` to 0.
