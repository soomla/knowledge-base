---
layout: "soomla-content"
image: "Wrench"
title: "Debugging"
text: "Debugging methods and tips for android-store."
position: 6
theme: 'soomla-android'
collection: 'soomla_android_store'
module: 'store'
platform: 'android'
---

# Debugging

## Debug Messages

Allow SOOMLA android-store's debugging messages to be printed to Logcat:

``` java
StoreConfig.logDebug = true;
```

## Newbie?

### Debug in Android Studio

- You can read [here](https://developer.android.com/sdk/installing/studio-debug.html) to learn how to debug in Android Studio.

- To filter the logcat messages, click the dropdown menu at the top right corner of the logcat window, and choose “Edit Filter Configurations”. Then, you can enter “SOOMLA” in the Log Tag field to display only messages that are SOOMLA-related.

    ![alt text](/img/tutorial_img/android_debugging/logcatFilter.png "Debugging")

- SOOMLA provides a Tag for almost every class in android-store. You can use these tags to filter the log messages according to your needs.

### Debug in the command line

Go to your AndroidSDK/platform-tools directory.

- To see that your device is connected, type `adb devices`. The serial number that corresponds to your phone should be displayed.

- Here are two options to filter the logcat messages and display only messages that have to do with SOOMLA:


   1. Type `adb logcat -v threadtime | grep SOOMLA`.

    OR

   2. Get [Pidcat](https://github.com/JakeWharton/pidcat)

       - `brew install pidcat`

       - For filtering: `pidcat com.soomla.example` (or other package name)
