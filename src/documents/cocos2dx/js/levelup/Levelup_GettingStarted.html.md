---
layout: "content"
image: "Tutorial"
title: "Getting Started"
text: "Get started with cocos2dx-levelup. Here you can find a detailed description of how to integrate LevelUp into your game, and see a basic example of initialization and functionality usage."
position: 1
theme: 'platforms'
collection: 'cocos2djs_levelup'
module: 'levelup'
lang: 'js' 
platform: 'cocos2dx'
---

#Getting Started

##Getting Started

<div class="info-box">LevelUp depends on SOOMLA's other modules: [Core & Store](/cocos2dx/js/store), and 
[Profile](/cocos2dx/js/profile). This document assumes that you are new to SOOMLA and have not worked with any of the 
other SOOMLA modules. If this is not the case, and you already have some or all of the other modules, please follow 
these directions only for the modules you are missing and, of course, for the LevelUp module.</div>

<br>

*If you want to develop with C++ sources, refer to the "Working with Sources" section below.*

<div class="info-box">If you didn't already, clone the Cocos2d-js framework from [here](https://github.com/cocos2d/cocos2d-js), 
or download it from the [Cocos2d-x website](http://www.cocos2d-x.org/download). Make sure the version you clone is 
supported by SOOMLA's cocos2dx-store (the tag is the version).</div>

1. Clone [soomla-cocos2dx-core](https://github.com/soomla/soomla-cocos2dx-core), [cocos2dx-store](https://github.com/soomla/cocos2dx-store), [cocos2dx-profile](https://github.com/soomla/cocos2dx-profile), and **cocos2dx-levelup** into the `Classes` folder of your project:

    ```
    $ git clone git@github.com:soomla/soomla-cocos2dx-core.git frameworks/runtime-src/Classes/soomla-cocos2dx-core

    $ git clone git@github.com:soomla/cocos2dx-store.git frameworks/runtime-src/Classes/cocos2dx-store

    $ git clone git@github.com:soomla/cocos2dx-profile.git frameworks/runtime-src/Classes/cocos2dx-profile

    $ git clone git@github.com:soomla/cocos2dx-levelup.git frameworks/runtime-src/Classes/cocos2dx-levelup
    ```

1. We use a [fork](https://github.com/soomla/jansson) of the jansson library for JSON parsing. Clone it there as well.

	```
	$ git clone git@github.com:soomla/jansson.git frameworks/runtime-src/Classes/jansson
	```

1. Register soomla js-bindings in your `js_module_register.h`:

    ```cpp
    #include "Cocos2dxStore.h"
    ...
    sc->addRegisterCallback(register_jsb_soomla);
    ```

1. Initialize Native Bridge in your `AppDelegate.cpp` in the method `applicationDidFinishLaunching`:

    ```cpp
        // Bind native bridges
        soomla::CCCoreBridge::getInstance();
        soomla::CCStoreBridge::getInstance();
        soomla::CCProfileBridge::getInstance();
        soomla::CCLevelUpBridge::getInstance();
    ```

1. Copy soomla js-files to your project:

    ```bash
    mkdir script/soomla
    cp frameworks/runtime-src/Classes/soomla-cocos2dx-core/js/* script/soomla/
    cp frameworks/runtime-src/Classes/cocos2dx-store/js/* script/soomla/
    cp frameworks/runtime-src/Classes/cocos2dx-profile/js/* script/soomla/
    cp frameworks/runtime-src/Classes/cocos2dx-levelup/js/* script/soomla/
    ```

1. Add them to your `project.json`:

    ```js
  "jsList": [
    "script/soomla/lodash.js",
    "script/soomla/soomla-core.js",
    "script/soomla/soomla-store.js",
    "script/soomla/soomla-profile.js",
    "script/soomla/soomla-levelup.js",
    // your other files
  ]
    ```

1. Initialize `Soomla`, `Soomla.soomlaStore`, `Soomla.soomlaProfile`, and `Soomla.soomlaLevelup` with their appropriate params:

  ```js
    Soomla.initialize("ExampleCustomSecret");
    
    var assets = new YourImplementationAssets();
    var storeParams = {
    androidPublicKey: "ExamplePublicKey",
    testPurchases: true
    };
    
    Soomla.soomlaStore.initialize(assets, storeParams);
    
    var profileParams = {};
    Soomla.soomlaProfile.initialize(profileParams);
    
    // initialWorld - should be created here and contain all worlds and levels of the game
    // rewards - should contain a list of all rewards that are given through LevelUp
	Soomla.soomlaLevelup.initialize(initialWorld, rewards);

  ```
  - *Custom Secret* is an encryption secret you provide that will be used to secure your data.

  - *Store Params* see the [Store Getting Started](/cocos2dx/js/store/Store_GettingStarted) for more information about initializing Store

  - *Profile Params* see the [Profile Getting Started](/cocos2dx/js/profile/Profile_GettingStarted) for more information about initializing Profile

  <div class="warning-box">Choose this secret wisely, you can't change it after you launch your game!
  <br>Initialize `Soomla.soomlaLevelup` ONLY ONCE when your application loads.</div>

1. You'll need to subscribe to levelup events to get notified about Level-Up related events. refer to the [Event Handling](/cocos2dx/js/levelup/Levelup_Events) section for more information.

<div class="info-box">The next steps are different according to which platform you are using.</div>

###**Instructions for iOS**

In your XCode project, perform the following steps:

1. In order to proceed Soomla needs to know, where your cocos2d-x is. Please, create a symlink with cocos2d-x at the path `frameworks/runtime-src` of the project, which looks at cocos2d-x. It can be something like that:

    ```bash
ln -s <your-cocos2d-js-path>/frameworks/js-bindings/cocos2d-x frameworks/runtime-src/cocos2d-x
    ````

1. Add `jansson` (**frameworks/runtime-src/Classes/jansson/**) to your project (just add it as a source folder, make sure to check "create group").

1. For each of the following XCode projects:

 * `Cocos2dXCore.xcodeproj` (**frameworks/runtime-src/Classes/soomla-cocos2dx-core/**).

 * `Cocos2dXStore.xcodeproj` (**frameworks/runtime-src/Classes/cocos2dx-store/**).

 * `Cocos2dXProfile.xcodeproj` (**frameworks/runtime-src/Classes/cocos2dx-profile/**)

 * `Cocos2dXLevelUp.xcodeproj` (**frameworks/runtime-src/Classes/cocos2dx-levelup/**)

    a. Drag the project into your project.

    b. Add its targets to your **Build Phases->Target Dependencies**.

    c. Add its `.a` files to **Build Phases->Link Binary With Libraries**.

  ![alt text](/img/tutorial_img/cocos2dx-levelup/iosStep2.png "iOS Integration")

1. Add the following directories to **Build Settings->Header Search Paths** (with `recursive` option):
  - `$(SRCROOT)/../Classes/soomla-cocos2dx-core/Soomla`
  - `$(SRCROOT)/../Classes/soomla-cocos2dx-core/build/ios/headers`
  - `$(SRCROOT)/../Classes/cocos2dx-store/Soomla`
  - `$(SRCROOT)/../Classes/cocos2dx-store/build/ios/headers`
  - `$(SRCROOT)/../Classes/cocos2dx-profile/Soomla`
  - `$(SRCROOT)/../Classes/cocos2dx-profile/build/ios/headers`
  - `$(SRCROOT)/../Classes/cocos2dx-levelup/Soomla`
  - `$(SRCROOT)/../Classes/cocos2dx-levelup/build/ios/headers`
 
  ![alt text](/img/tutorial_img/cocos2dx-levelup/headerSP.png "Header search paths")

1. Add `-ObjC` to your project **Build Settings->Other Linker Flags**.

	![alt text](/img/tutorial_img/cocos2dx_getting_started/objc.png "Other Linker Flags")

1. Make sure you have these 3 Frameworks linked to your XCode project: **Security, libsqlite3.0.dylib, StoreKit**.

**That's it! Now all you have to do is build your XCode project and run your game with cocos2dx-store.**

> If you use Cocos IDE you'll need to `Build Custom Simulator` for iOS there.

1. Follow our [tutorial](/cocos2dx/js/store/Store_GettingStarted#apple-app-store) on how to connect the Store module to the App Store billing service.

1. See the following links in order to connect the Profile module to a social network provider:

  - [Facebook for iOS](/cocos2dx/js/profile/Profile_GettingStarted#facebook-for-ios)

  - [Google+ for iOS](/cocos2dx/js/profile/Profile_GettingStarted#google-for-ios)

  - [Twitter for iOS](/cocos2dx/js/profile/Profile_GettingStarted#twitter-for-ios)

<br>

That's it! Now all you have to do is build your XCode project and run your game with cocos2dx-levelup.

###**Instructions for Android**

1. Set COCOS2D_JAVASCRIPT flag for your project changing your `frameworks/runtime-src/proj.android/jni/Application.mk`:
    ```
    APP_CPPFLAGS += -DCOCOS2D_JAVASCRIPT=1
    ```

1. Add "../Classes" to `ndk_module_path`, adding it to the file `frameworks/runtime-src/proj.android/build-cfg.json`

1. Import cocos2dx-store, cocos2dx-profile, and cocos2dx-levelup into your project's Android.mk by adding the following:

    ```
    # Add these lines along with your other LOCAL_WHOLE_STATIC_LIBRARIES
    LOCAL_WHOLE_STATIC_LIBRARIES += cocos2dx_store_static
    LOCAL_WHOLE_STATIC_LIBRARIES += cocos2dx_profile_static
    LOCAL_WHOLE_STATIC_LIBRARIES += cocos2dx_levelup_static

	# Add these lines at the end of the file, along with the other import-module calls
    $(call import-module, cocos2dx-store)  
    $(call import-module, cocos2dx-profile)  
    $(call import-module, cocos2dx-levelup)  
    ```

1. Add the following jars to your android project's classpath:

  From `frameworks/runtime-src/Classes/soomla-cocos2dx-core/build/android`

    - SoomlaAndroidCore.jar

    - Cocos2dxAndroidCore.jar

    - square-otto-1.3.2.jar

  From `frameworks/runtime-src/Classes/cocos2dx-store/build/android`

    - AndroidStore.jar

    - Cocos2dxAndroidStore.jar

  From `frameworks/runtime-src/Classes/cocos2dx-profile/build/android`

    - AndroidProfile.jar

    - Cocos2dxAndroidProfile.jar

  From `frameworks/runtime-src/Classes/cocos2dx-levelup/build/android`

    - AndroidLevelUp.jar

    - Cocos2dxAndroidLevelUp.jar

1. Update your `AndroidManifest.xml` to include permissions and the `SoomlaApp`:

    ``` xml
    <uses-permission android:name="android.permission.INTERNET"/>
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />

    <!-- optional: required for uploadImage from SD card -->
    <uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />

    <application ...
             android:name="com.soomla.SoomlaApp">
             ...
    </application>
    ```

1. See the following links in order to connect the Store module to a billing service:

  - [Google Play](/cocos2dx/js/store/Store_GettingStarted#google-play)

  - [Amazon App Store](/cocos2dx/js/store/Store_GettingStarted#amazon)

1. See the following links in order to connect the Profile module to a social network provider:

  - [Facebook for Android](/cocos2dx/js/profile/Profile_GettingStarted#facebook-for-android)

  - [Google+ for Android](/cocos2dx/js/profile/Profile_GettingStarted#google-for-android)

  - [Twitter for Android](/cocos2dx/js/profile/Profile_GettingStarted#twitter-for-android)

That's it!

> Don't forget to `Build Custom Simulator` for Android, if you use Cocos IDE.

##Working with sources

**To integrate cocos2dx-levelup into your game, follow these steps:**

1. **Recursively** clone soomla-cocos2dx-core, cocos2dx-store, cocos2dx-profile, and cocos2dx-levelup.

    ```
    $ git clone --recursive git@github.com:soomla/soomla-cocos2dx-core.git frameworks/runtime-src/Classes/soomla-cocos2dx-core

    $ git clone --recursive git@github.com:soomla/cocos2dx-store.git frameworks/runtime-src/Classes/cocos2dx-store

    $ git clone --recursive git@github.com:soomla/cocos2dx-profile.git frameworks/runtime-src/Classes/cocos2dx-profile

    $ git clone --recursive git@github.com:soomla/cocos2dx-levelup.git frameworks/runtime-src/Classes/cocos2dx-levelup
    ```

    **OR:** If you have already cloned the repositories, to obtain submodules, use command:

    ```
    $ git submodule update --init --recursive
    ```

    **NOTE:** You should run this command in every repository.

2. **For iOS:** Use sourced versions of Linked projects (
`frameworks/runtime-src/Classes/soomla-cocos2dx-core/development/Cocos2dxCoreFromSources.xcodeproj`, 
`frameworks/runtime-src/Classes/cocos2dx-store/development/Cocos2dxStoreFromSources.xcodeproj`,
`frameworks/runtime-src/Classes/cocos2dx-profile/development/Cocos2dxProfileFromSources.xcodeproj`,
`frameworks/runtime-src/Classes/cocos2dx-levelup/development/Cocos2dxLevelUpFromSources.xcodeproj`
)

3. **For Android:** You can use our "sourced" modules for Android Studio (or IntelliJ IDEA) (
`frameworks/runtime-src/Classes/soomla-cocos2dx-core/development/Cocos2dxCoreFromSources.iml`, 
`frameworks/runtime-src/Classes/cocos2dx-store/development/Cocos2dxStoreFromSources.iml`,
`frameworks/runtime-src/Classes/cocos2dx-profile/development/Cocos2dxProfileFromSources.iml`,
`frameworks/runtime-src/Classes/cocos2dx-levelup/development/Cocos2dxLevelUpFromSources.iml`
), just include them in your project.

##Example Usages

**NOTE:** Examples using virtual items are dependent on cocos2dx-store module, with proper `Soomla.soomlaStore` 
initialization and `Soomla.IStoreAssets` definitions. See the cocos2dx-store integration section for more details.

* `Mission` with `Reward` (collect 5 stars to get 1 mega star)

  ```js
    var virtualItemReward = Soomla.Models.VirtualItemReward.create({
        itemId: 'mega_star_reward_id',
        name: 'MegaStarReward',
        amount: 1,
        associatedItemId: megaStarItemId
    });

    var rewards = {virtualItemReward};

    var balanceMission = Soomla.Models.BalanceMission.create({
        itemId: 'star_balance_mission_id',
        name: 'StarBalanceMission',
        rewards: rewards,
        associatedItemId: starItemId,
        desiredBalance: 5});

    // use the store to give the items out, usually this will be called from in-game events
    // such as player collecting the stars
    Soomla.storeInventory.giveItem(starItemId, 5);
    
    // events posted:
    // 1. Soomla.Models.StoreConsts.EVENT_GOOD_BALANCE_CHANGED
    // 2. Soomla.Models.LevelUpConsts.EVENT_MISSION_COMPLETED (LevelUp events)
    // 3. Soomla.Models.CoreConsts.EVENT_REWARD_GIVEN (Core events)
    
    // now the mission is complete, and reward given
    balanceMission.isCompleted(); // true
    virtualItemReward.isOwned(); // true
  ```

* `RecordGate` with `RangeScore`

  ```js
    var lvl1 = Soomla.Models.Level.create({
        itemId: 'lvl1_recordgate_rangescore'
    }));
    var lvl2 = Soomla.Models.Level.create({
        itemId: 'lvl2_recordgate_rangescore'
    }));
    
    var scoreId = 'range_score';
    var rangeScore = Soomla.Models.RangeScore.create({
        itemId: scoreId,
        range: {
            high: 0 
            low: 100
        } 
    });
    
    var recordGateId = 'record_gate';
    
    var recordGate = RecordGate.create({
        itemId: recordGateId,
        associatedScoreId: scoreId,
        desiredRecord: 100
    });

    lvl1.addScore(rangeScore);
    
    // Lock level 2 with record gate
    lvl2.setGate(recordGate);
    
    // the initial world
    world.addInnerWorld(lvl1);
    world.addInnerWorld(lvl2);
    
	Soomla.soomlaLevelup.initialize(world);
    
    lvl1.start();
    
    // events posted:
    // Soomla.Models.LevelUpConsts.EVENT_LEVEL_UP_INITIALIZED (LevelUp events)
    
    rangeScore.inc(100);
    
    lvl1.end(true);
    
    // events posted:
    // Soomla.Models.LevelUpConsts.EVENT_LEVEL_ENDED (LevelUp events)
    // Soomla.Models.LevelUpConsts.EVENT_MISSION_COMPLETED (lvl1) (LevelUp events)
    // Soomla.Models.LevelUpConsts.EVENT_GATE_OPENED (LevelUp events)
    // [Soomla.Models.LevelUpConsts.EVENT_SCORE_RECORD_REACHED] - if record was broken (LevelUp events)
    
    recordGate.isOpen(); // true
    
    lvl2.canStart(); // true
    lvl2.start();
    lvl2.end(true);
    
    // events posted:
    // Soomla.Models.LevelUpConsts.EVENT_WORLD_COMPLETED (lvl2) (LevelUp events)
    
    lvl2.isCompleted(); // true
  ```

* `VirtualItemScore`

  ```js
    var lvl1 = Soomla.Models.Level.create({
        itemId: 'lvl1_viscore'
    });
    var scoreId = 'vi_score';
    var virtualItemScore = Soomla.Models.VirtualItemScore.create({
        itemId: scoreId, 
        associatedItemId: ITEM_ID_VI_SCORE
    });
    lvl1.addScore(virtualItemScore);
    
    world.addInnerWorld(lvl1);
    
	Soomla.soomlaLevelup.initialize(world);
    
    lvl1.start();
    // events posted:
    // Soomla.Models.LevelUpConsts.EVENT_LEVEL_UP_INITIALIZED (LevelUp events)
    
    virtualItemScore.inc(2.0);
    // events posted:
    // Soomla.Models.StoreConsts.EVENT_GOOD_BALANCE_CHANGED (Store events)
    
    lvl1.end(true);
    // events posted:
    // Soomla.Models.LevelUpConsts.EVENT_LEVEL_ENDED (LevelUp events)
    // Soomla.Models.LevelUpConsts.EVENT_WORLD_COMPLETED (lvl1) (LevelUp events)
    // [Soomla.Models.LevelUpConsts.EVENT_SCORE_RECORD_REACHED] - if record was broken (LevelUp events)
    
    var currentBalance = Soomla.storeInventory.getItemBalance(ITEM_ID_VI_SCORE);
    // currentBalance == 2
  ```

* `Challenge` (Multi-Mission)

  ```js
    var scoreId = 'main_score';
    var score = Soomla.Models.Score.create({
        itemId: scoreId
    });
    
    var mission1 = Soomla.Models.RecordMission.create({
        itemId: 'record1_mission',
        name: 'Record 1 mission',
        associatedScoreId: scoreId,
        desiredRecord: 10.0
    });
    
    var mission2 = Soomla.Models.RecordMission.create({
        itemId: 'record2_mission',
        name: 'Record 2 mission',
        associatedScoreId: scoreId,
        desiredRecord: 100.0
    });

    var badgeReward = Soomla.Models.BadgeReward.create({
        itemId: 'challenge_badge_reward_id',
        name: 'ChallengeBadgeRewardId'
    });
    
    var challenge = Soomla.Models.Challenge.create({
        itemId: 'challenge_id',
        name: 'Challenge',
        missions: [mission1, mission2],
        rewards: [badgeReward]
    });
    
    challenge.isCompleted(); //false
    
    var world = Soomla.Models.World.create({
        itemId: 'initial_world'
    });
    world.addMission(challenge);
    world.addScore(score);
    
	Soomla.soomlaLevelup.initialize(world);
    
    score.setTempScore(20.0);
    score.reset(true);
    
    // events:
    // Soomla.Models.LevelUpConsts.EVENT_WORLD_COMPLETED (mission1) (LevelUp events)
    // [Soomla.Models.LevelUpConsts.EVENT_SCORE_RECORD_REACHED] - if record is broken
    
    score.setTempScore(120.0);
    score.reset(true);
    
    // events:
    // Soomla.Models.LevelUpConsts.EVENT_MISSION_COMPLETED (mission2) (LevelUp events)
    // Soomla.Models.LevelUpConsts.EVENT_MISSION_COMPLETED (challenge) (LevelUp events)
    // Soomla.Models.CoreConsts.EVENT_REWARD_GIVEN (badgeReward) (Core events)
    
    challenge.isCompleted(); // true
    badgeReward.isOwned(); // true
  ```

* `GatesList`
> Note that currently a `GatesList` gate is automatically opened when sub-gates fulfill the `GatesList` requirement.

  ```js
    var recordGateId1 = 'gates_list_record_gate_id1';
    var scoreId1 = 'gates_list_score_id1';
    var desiredRecord1 = 10.0;
    var recordGateId2 = 'gates_list_record_gate_id2';
    var scoreId2 = 'gates_list_score_id2';
    var desiredRecord2 = 20.0;
    
    var score1 = Soomla.Models.Score.create(scoreId1);
    var score2 = Soomla.Models.Score.create(scoreId2);
    
    var world = Soomla.Models.World.create({
        itemId: 'initial_world'    
    });
    var lvl1 = Soomla.Models.Level.create({
        itemId: 'level1_id'
    });
    lvl1.addScore(score1);
    lvl1.addScore(score2);
    world.addInnerWorld(lvl1);
    
    var recordGate1 = RecordGate.create({
        itemId: recordGateId1,
        associatedScoreId: scoreId1,
        desiredRecord: desiredRecord1
    });
    
    var recordGate2 = RecordGate.create({
        itemId: recordGateId2,
        associatedScoreId: scoreId2,
        desiredRecord: desiredRecord2
    });
    
    var gates = [recordGate1, recordGate2];
    
    var gatesListOr = Soomla.Models.GatesListOr.create({
        itemId: 'gate_list_OR_id',
        gates: [recordGate1, recordGate2]
    );
    
    var gatesListAnd = Soomla.Models.GatesListAnd.create({
        itemId: 'gate_list_AND_id',
        gates: [recordGate1, recordGate2]
    );
    
	Soomla.soomlaLevelup.initialize(world);

    score1.setTempScore(desiredRecord1);
    score1.reset(true);
    
    recordGate1.isOpen(); // true
    gatesListOr.isOpen(); // true
    
    gatesListAnd.canOpen(); // false (all sub-gates need to be open for AND)
    gatesListAnd.isOpen(); // false
    
    score2.setTempScore(desiredRecord2);
    score2.reset(true);
    
    recordGate2.isOpen(); // true
    gatesListOr.isOpen(); // still true
    gatesListAnd.isOpen(); // true
  ```
