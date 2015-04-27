---
layout: "content"
image: "Tutorial"
title: "Getting Started"
text: "Get started with cocos2dx-levelup. Here you can find a detailed description of how to integrate LevelUp into your game, and see a basic example of initialization and functionality usage."
position: 1
theme: 'platforms'
collection: 'cocos2dx_levelup'
module: 'levelup'
platform: 'cocos2dx'
---

#Getting Started

##Getting Started

<div class="info-box">LevelUp depends on SOOMLA's other modules: [Core & Store](/cocos2dx/cpp/store), and [Profile](/cocos2dx/cpp/profile). This document assumes that you are new to SOOMLA and have not worked with any of the other SOOMLA modules. If this is not the case, and you already have some or all of the other modules, please follow these directions only for the modules you are missing and, of course, for the LevelUp module.</div>

<br>

1. If you didn't already, clone the Cocos2d-x framework from [here](https://github.com/cocos2d/cocos2d-x), or download it from the [Cocos2d-x website](http://www.cocos2d-x.org/download). Make sure the version you clone is supported by cocos2dx-levelup (the tag is the version).

  <div class="info-box">If you would like to develop with sources, refer to the [Working with sources](#working-with-sources) section below.</div>

2. Clone [soomla-cocos2dx-core](https://github.com/soomla/soomla-cocos2dx-core), [cocos2dx-store](https://github.com/soomla/cocos2dx-store), [cocos2dx-profile](https://github.com/soomla/cocos2dx-profile), and **cocos2dx-levelup** into the `extensions` directory located at the root of your Cocos2d-x framework:

    ```
    $ git clone git@github.com:soomla/soomla-cocos2dx-core.git extensions/soomla-cocos2dx-core

    $ git clone git@github.com:soomla/cocos2dx-store.git extensions/cocos2dx-store

    $ git clone git@github.com:soomla/cocos2dx-profile.git extensions/cocos2dx-profile

    $ git clone git@github.com:soomla/cocos2dx-levelup.git extensions/cocos2dx-levelup
    ```

3. We use a [fork](https://github.com/soomla/jansson) of the jansson library for JSON parsing. Clone our fork into the `external` directory at the root of your framework:

    ```
    $ git clone git@github.com:soomla/jansson.git external/jansson
    ```
4. Make sure to include the `Cocos2dxLevelUp.h` header whenever you use any of the **cocos2dx-levelup** functions:

    ```cpp
    #include "Cocos2dxLevelUp.h"
    ```

5. Initialize `CCSoomla`, `CCSoomlaStore`, `CCSoomlaProfile`, and `CCSoomlaLevelUp` with their appropriate params:

  ```cpp
  soomla::CCSoomla::initialize("customSecret");

  YourImplementationAssets *assets = YourImplementationAssets::create();

  __Dictionary *storeParams = __Dictionary::create();
  soomla::CCSoomlaStore::initialize(assets, storeParams);

  __Dictionary *profileParams = __Dictionary::create();
  soomla::CCSoomlaProfile::initialize(profileParams);

  // initialWorld - should be created here and contain all worlds and levels of the game
	// rewards - should contain a list of all rewards that are given through LevelUp
	soomla::CCSoomlaLevelUp::getInstance()->initialize(initialWorld, rewards);

  ```
  - *Custom Secret* is an encryption secret you provide that will be used to secure your data.

  - *Store Params* see the [Store Getting Started](/cocos2dx/cpp/store/Store_GettingStarted) for more information about initializing Store

  - *Profile Params* see the [Profile Getting Started](/cocos2dx/cpp/profile/Profile_GettingStarted) for more information about initializing Profile

  <div class="warning-box">Choose this secret wisely, you can't change it after you launch your game!
  <br>Initialize `CCSoomlaLevelUp` ONLY ONCE when your application loads.</div>

6. You'll need to subscribe to levelup events to get notified about Level-Up related events. refer to the [Event Handling](/cocos2dx/cpp/levelup/Levelup_Events) section for more information.

<div class="info-box">The next steps are different according to which platform you are using.</div>

###**Instructions for iOS**

In your XCode project, perform the following steps:

1. Add `jansson` (**external/jansson/**) to your project (just add it as a source folder, make sure to check "create group").

2. For each of the following XCode projects:

	- `Cocos2dXCore.xcodeproj` (**extensions/soomla-cocos2dx-core/**)

	- `Cocos2dXStore.xcodeproj` (**extensions/cocos2dx-store/**)

	- `Cocos2dXProfile.xcodeproj` (**extensions/soomla-cocos2dx-profile/**)

	- `Cocos2dXLevelUp.xcodeproj` (**extensions/cocos2dx-levelup/**)

  Perform the following:

  - Drag the project into your project

  - Add its targets to your **Build Phases->Target Dependencies**

  - Add the Products (\*.a) of the project to **Build Phases->Link Binary With Libraries**.

  ![alt text](/img/tutorial_img/cocos2dx-levelup/iosStep2.png "iOS Integration")

3. Add the following directories to **Build Settings->Header Search Paths** (with `recursive` option):

  NOTE: This article assumes you have a `cocos2d` folder under your project folder which either contains the Cocos2d-x framework, or links to to its root folder.

 - `$(SRCROOT)/../cocos2d/extensions/soomla-cocos2dx-core/Soomla`
 - `$(SRCROOT)/../cocos2d/extensions/soomla-cocos2dx-core/build/ios/headers`
 - `$(SRCROOT)/../cocos2d/extensions/cocos2dx-store/Soomla`
 - `$(SRCROOT)/../cocos2d/extensions/cocos2dx-profile/Soomla`
 - `$(SRCROOT)/../cocos2d/extensions/cocos2dx-profile/build/ios/headers`
 - `$(SRCROOT)/../cocos2d/extensions/cocos2dx-levelup/Soomla`

 ![alt text](/img/tutorial_img/cocos2dx-levelup/headerSP.png "Header search paths")

4. Make sure you have these 3 Frameworks linked to your XCode project: **Security**, **libsqlite3.0.dylib**, and **StoreKit**.

5. Follow our [tutorial](/cocos2dx/cpp/store/Store_GettingStarted#apple-app-store) on how to connect the Store module to the App Store billing service.

6. See the following links in order to connect the Profile module to a social network provider:

  - [Facebook for iOS](/cocos2dx/cpp/profile/Profile_GettingStarted#facebook-for-ios)

  - [Google+ for iOS](/cocos2dx/cpp/profile/Profile_GettingStarted#google-for-ios)

  - [Twitter for iOS](/cocos2dx/cpp/profile/Profile_GettingStarted#twitter-for-ios)

That's it! Now all you have to do is build your XCode project and run your game with cocos2dx-levelup.

###**Instructions for Android**

1. Import cocos2dx-store, cocos2dx-profile, and cocos2dx-levelup into your project's Android.mk by adding the following:

    ```
    LOCAL_WHOLE_STATIC_LIBRARIES += cocos2dx_store_static
    # add this line along with your other LOCAL_WHOLE_STATIC_LIBRARIES
    $(call import-module, extensions/cocos2dx-store)
    # add this line at the end of the file, along with the other import-module calls

    LOCAL_WHOLE_STATIC_LIBRARIES += cocos2dx_profile_static
    # add this line along with your other LOCAL_WHOLE_STATIC_LIBRARIES
    $(call import-module, extensions/cocos2dx-profile)
    # add this line at the end of the file, along with the other import-module calls

    LOCAL_WHOLE_STATIC_LIBRARIES += cocos2dx_levelup_static
    # add this line along with your other LOCAL_WHOLE_STATIC_LIBRARIES
    $(call import-module, extensions/cocos2dx-levelup)
    # add this line at the end of the file, along with the other import-module calls
    ```

2. Add the following jars to your android project's classpath:

  From `extensions/soomla-cocos2dx-core/build/android`

    - SoomlaAndroidCore.jar

    - Cocos2dxAndroidCore.jar

    - square-otto-1.3.2.jar

  From `extensions/cocos2dx-store/build/android`

    - AndroidStore.jar

    - Cocos2dxAndroidStore.jar

  From `extensions/cocos2dx-profile/build/android`

    - AndroidProfile.jar

    - Cocos2dxAndroidProfile.jar

  From `extensions/cocos2dx-levelup/build/android`

    - AndroidLevelUp.jar

    - Cocos2dxAndroidLevelUp.jar

3. Update your `AndroidManifest.xml` to include permissions and the `SoomlaApp`:

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

4. See the following links in order to connect the Store module to a billing service:

  - [Google Play](/cocos2dx/cpp/store/Store_GettingStarted#google-play)

  - [Amazon App Store](/cocos2dx/cpp/store/Store_GettingStarted#amazon)

5. See the following links in order to connect the Profile module to a social network provider:

  - [Facebook for Android](/cocos2dx/cpp/profile/Profile_GettingStarted#facebook-for-android)

  - [Google+ for Android](/cocos2dx/cpp/profile/Profile_GettingStarted#google-for-android)

  - [Twitter for Android](/cocos2dx/cpp/profile/Profile_GettingStarted#twitter-for-android)

That's it! Don't forget to run the **build_native.py** script so cocos2dx-levelup sources will be built with cocos2d-x.

##Working with sources

**To integrate cocos2dx-levelup into your game, follow these steps:**

1. **Recursively** clone soomla-cocos2dx-core, cocos2dx-store, cocos2dx-profile, and cocos2dx-levelup.

    ```
    $ git clone --recursive git@github.com:soomla/soomla-cocos2dx-core.git extensions/soomla-cocos2dx-core

    $ git clone --recursive git@github.com:soomla/cocos2dx-store.git extensions/cocos2dx-store

    $ git clone --recursive git@github.com:soomla/cocos2dx-profile.git extensions/cocos2dx-profile

    $ git clone --recursive git@github.com:soomla/cocos2dx-levelup.git extensions/cocos2dx-levelup
    ```

    **OR:** If you have already cloned the repositories, to obtain submodules, use command:

    ```
    $ git submodule update --init --recursive
    ```

    **NOTE:** You should run this command in every repository.

2. For iOS: Use sourced versions of Linked projects:
  - `extensions/soomla-cocos2dx-core/development/Cocos2dxCoreFromSources.xcodeproj`
  - `extensions/cocos2dx-store/development/Cocos2dxStoreFromSources.xcodeproj`
  - `extensions/cocos2dx-profile/development/Cocos2dxProfileFromSources.xcodeproj`
  - `extensions/cocos2dx-levelup/development/Cocos2dxLevelUpFromSources.xcodeproj`

3. For Android: You can use our "sourced" modules for Android Studio (or IntelliJ IDEA):
  - `extensions/soomla-cocos2dx-core/development/Cocos2dxCoreFromSources.iml`
  - `extensions/cocos2dx-store/development/Cocos2dxStoreFromSources.iml`
  - `extensions/cocos2dx-profile/development/Cocos2dxProfileFromSources.iml`
  - `extensions/cocos2dx-levelup/development/Cocos2dxLevelUpFromSources.iml`

##Example Usages

**NOTE:** Examples using virtual items are dependent on cocos2dx-store module, with proper `CCSoomlaStore` initialization and `CCStoreAssets` definitions. See the cocos2dx-store integration section for more details.

* `CCMission` with `CCReward` (collect 5 stars to get 1 mega star)

  ```cpp
  CCVirtualItemReward *virtualItemReward = CCVirtualItemReward::create(
    __String::create("mega_star_reward_id"),
    __String::create("MegaStarReward"),
    __Integer::create(1),
    megaStarItemId
  );

  __Array *rewards = __Array::create(virtualItemReward, NULL);

  CCBalanceMission *balanceMission = CCBalanceMission::create(
    __String::create("star_balance_mission_id"),
    __String::create("StarBalanceMission"),
    rewards,
    starItemId,
    __Integer::create(5)
  );

  // use the store to give the items out, usually this will be called from in-game events
  // such as player collecting the stars
  CCStoreInventory::sharedStoreInventory()->giveItem(starItemId->getCString(), 5, &error);

  // events posted:
  // 1. EVENT_GOOD_BALANCE_CHANGED (Store events)
  // 2. EVENT_MISSION_COMPLETED (LevelUp events)
  // 3. EVENT_REWARD_GIVEN (Core events)

  // now the mission is complete, and reward given
  balanceMission->isCompleted(); // true
  virtualItemReward->isOwned(); // true
  ```

* `CCRecordGate` with `CCRangeScore`

  ```cpp
  CCLevel *lvl1 = CCLevel::create(__String::create("lvl1_recordgate_rangescore"));
  CCLevel *lvl2 = CCLevel::create(__String::create("lvl2_recordgate_rangescore"));

  __String *scoreId = __String::create("range_score");
  CCRangeScore *rangeScore = CCRangeScore::create(
    scoreId,
    CCSRange::create(0.0, 100.0)
  );

  __String *recordGateId = __String::create("record_gate");

  CCRecordGate *recordGate = CCRecordGate::create(
    recordGateId,
    scoreId,
    __Double::create(100.0)
  );

  lvl1->addScore(rangeScore);

  // Lock level 2 with record gate
  lvl2->setGate(recordGate);

  // the initial world
  world->addInnerWorld(lvl1);
  world->addInnerWorld(lvl2);

  CCSoomlaLevelUp::getInstance()->initialize(world);

  lvl1->start();

  // events posted:
  // EVENT_LEVEL_UP_INITIALIZED (LevelUp events)

  rangeScore->inc(100);

  lvl1->end(true);

  // events posted:
  // EVENT_LEVEL_ENDED (LevelUp events)
  // EVENT_MISSION_COMPLETED (lvl1) (LevelUp events)
  // EVENT_GATE_OPENED (LevelUp events)
  // [EVENT_SCORE_RECORD_REACHED] - if record was broken (LevelUp events)

  recordGate->isOpen(); // true

  lvl2->canStart(); // true
  lvl2->start();
  lvl2->end(true);

  // events posted:
  // EVENT_WORLD_COMPLETED (lvl2) (LevelUp events)

  lvl2->isCompleted(); // true
  ```

* `CCVirtualItemScore`

  ```cpp
  CCLevel *lvl1 = CCLevel::create(__String::create("lvl1_viscore"));
  __String *itemId = __String::create(ITEM_ID_VI_SCORE);
  __String *scoreId = __String::create("vi_score");
  CCVirtualItemScore *virtualItemScore = CCVirtualItemScore::create(scoreId, itemId);
  lvl1->addScore(virtualItemScore);

  world->addInnerWorld(lvl1);

  CCSoomlaLevelUp::getInstance()->initialize(world);

  lvl1->start();
  // events posted:
  // EVENT_LEVEL_UP_INITIALIZED (LevelUp events)

  virtualItemScore->inc(2.0);
  // events posted:
  // EVENT_GOOD_BALANCE_CHANGED (Store events)

  lvl1->end(true);
  // events posted:
  // EVENT_LEVEL_ENDED (LevelUp events)
  // EVENT_WORLD_COMPLETED (lvl1) (LevelUp events)
  // [EVENT_SCORE_RECORD_REACHED] - if record was broken (LevelUp events)

  int currentBalance = CCStoreInventory::sharedStoreInventory()->getItemBalance(ITEM_ID_VI_SCORE, &error);
  // currentBalance == 2
  ```

* `CCChallenge` (Multi-Mission)

  ```cpp
  __String *scoreId = __String::create("main_score");
  CCScore *score = CCScore::create(scoreId);

  CCMission *mission1 = CCRecordMission::create(__String::create("record1_mission"),
                                                __String::create("Record 1 mission"),
                                                scoreId,
                                                __Double::create(10.0));

  CCMission *mission2 = CCRecordMission::create(__String::create("record2_mission"),
                                                __String::create("Record 2 mission"),
                                                scoreId,
                                                __Double::create(100.0));

  __Array *missions = __Array::create(mission1, mission2, NULL);

  CCBadgeReward *badgeReward = CCBadgeReward::create(__String::create("challenge_badge_reward_id"),
                                                     __String::create("ChallengeBadgeRewardId"));
  __Array *rewards = __Array::create(badgeReward, NULL);

  CCChallenge *challenge = CCChallenge::create(__String::create("challenge_id"),
                                               __String::create("Challenge"),
                                               missions,
                                               rewards);

  challenge->isCompleted(); //false

  CCWorld *world = CCWorld::create(__String::create("initial_world"));
  world->addMission(challenge);
  world->addScore(score);

  CCLevelUp::getInstance()->initialize(world);

  score->setTempScore(20.0);
  score->reset(true);

  // events:
  // EVENT_WORLD_COMPLETED (mission1) (LevelUp events)
  // [EVENT_SCORE_RECORD_REACHED] - if record is broken

  score->setTempScore(120.0);
  score->reset(true);

  // events:
  // EVENT_MISSION_COMPLETED (mission2) (LevelUp events)
  // EVENT_MISSION_COMPLETED (challenge) (LevelUp events)
  // EVENT_REWARD_GIVEN (badgeReward) (Core events)

  challenge->isCompleted(); // true
  badgeReward->isOwned(); // true
  ```

* `CCGatesList`
> Note that currently a `GatesList` gate is automatically opened when sub-gates fulfill the `GatesList` requirement.

  ```cpp
  __String *recordGateId1 = __String::create("gates_list_record_gate_id1");
  __String *scoreId1 = __String::create("gates_list_score_id1");
  __Double *desiredRecord1 = __Double::create(10.0);
  __String *recordGateId2 = __String::create("gates_list_record_gate_id2");
  __String *scoreId2 = __String::create("gates_list_score_id2");
  __Double *desiredRecord2 = __Double::create(20.0);

  CCScore *score1 = CCScore::create(scoreId1);
  CCScore *score2 = CCScore::create(scoreId2);

  CCWorld *world = CCWorld::create(__String::create("initial_world"));
  CCLevel *lvl1 = CCLevel::create(__String::create("level1_id"));
  lvl1->addScore(score1);
  lvl1->addScore(score2);
  world->addInnerWorld(lvl1);

  CCRecordGate *recordGate1 = CCRecordGate::create(
    recordGateId1,
    scoreId1,
    desiredRecord1
  );

  CCRecordGate *recordGate2 = CCRecordGate::create(
    recordGateId2,
    scoreId2,
    desiredRecord2
  );

  __Array *gates = __Array::create(recordGate1, recordGate2, NULL);

  CCGatesListOr *gatesListOR = CCGatesListOr::create(
    __String::create("gate_list_OR_id"),
    gates
  );

  CCGatesListAnd *gatesListAND = CCGatesListAnd::create(
    __String::create("gate_list_AND_id"),
    gates
  );

  CCSoomlaLevelUp::getInstance()->initialize(world);

  score1->setTempScore(desiredRecord1->getValue());
  score1->reset(true);

  recordGate1->isOpen(); // true
  gatesListOR->isOpen(); // true

  gatesListAND->canOpen(); // false (all sub-gates need to be open for AND)
  gatesListAND->isOpen(); // false

  score2->setTempScore(desiredRecord2->getValue());
  score2->reset(true);

  recordGate2->isOpen(); // true
  gatesListOR->isOpen(); // still true
  gatesListAND->isOpen(); // true
  ```
