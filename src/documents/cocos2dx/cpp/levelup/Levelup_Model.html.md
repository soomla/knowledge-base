---
layout: "content"
image: "Modeling"
title: "Model & Operations"
text: "Learn about the different entities of LevelUp. See examples of how to initialize and use them."
position: 2
theme: 'platforms'
collection: 'cocos2dx_levelup'
module: 'levelup'
platform: 'cocos2dx'
---

# Model & Operations

## LevelUp

`LevelUp` models out worlds, levels, scores, missions, and more, all in one framework that allows game developers to build their game structure and progressions behavior easily and effectively. The sense of progress that users feel in a game is what creates retention and long-term use, which usually lead to monetization.

In this document, you will find definitions of each of the entities of `LevelUp`, the connections between them, and code examples that demonstrate how to use them.

### LevelUp Hierarchy

After observing dozens of games, the SOOMLA team realized that most game progress and accomplishment can be packed into **worlds**. Worlds can contain both levels and worlds, and may have missions that can be completed in order to receive rewards.

![alt text](/img/tutorial_img/soomla_diagrams/LevelUp.png "Soomla LevelUp Model")

### Prerequisites

This document assumes that you have a good understanding of SOOMLA's Store module. If not, please take time to read about the [Economy Model](/cocos2dx/cpp/store/Store_Model), and then come back to this document.

The `CCSchedule` and `CCReward` entities are widely used in the examples of this document. You can read about them [here](#auxiliary-models).


##CCSoomlaLevelUp <a href="https://github.com/soomla/cocos2dx-levelup/blob/master/Soomla/CCSoomlaLevelUp.h" target="_blank"><img class="link-icon" src="/img/tutorial_img/linkImg.png"></a>

This class is the top level container for the cocos2dx-levelup model and definitions. It stores the configurations of the game's world-hierarchy and provides lookup methods for *LevelUp* model elements.

`CCSoomlaLevelUp` is the central point of initialization. To use *LevelUp* you'll need to compose `CCWorld`s, `CCLevel`s, `CCMission`s, `CCScore`s, `CCGate`s, `CCReward`s, and then initialize `CCSoomlaLevelUp` with your root `CCWorld`.

<br>
**Useful methods**

``` cpp
// Retrieve a `CCWorld` by its ID:
CCWorld *world = CCSoomlaLevelUp::getInstance()->getWorld(WORLD_ITEM_ID);

// Retrieve a `CCScore` by its ID:
CCScore *score = CCSoomlaLevelUp::getInstance()->getScore(SCORE_ITEM_ID);

// Retrieve a `CCGate` by its ID:
CCGate *gate = CCSoomlaLevelUp::getInstance()->getGate(GATE_ITEM_ID);

// Retrieve a `CCMission` by its ID:
CCMission *mission = CCSoomlaLevelUp::getInstance()->getMission(MISSION_ITEM_ID);

// Retrieve a `CCReward` by its ID:
CCReward *oldReward = CCSoomlaLevelUp::getInstance()->getReward(REWARD_ITEM_ID);

// Retrieve the number of Worlds in your game:
int numOfWorlds = CCSoomlaLevelUp::getInstance()->getWorldCount(true);

// Retrieve the number of Levels in a given World:
int levels = CCSoomlaLevelUp::getInstance()->getLevelCountInWorld(someWorld);

// Retrieve the number of `CCLevel`s in the game:
int totalLevels = CCSoomlaLevelUp::getInstance()->getLevelCount();

// Retrieve the number of completed `CCLevel`s:
int completedLevels = CCSoomlaLevelUp::getInstance()->getCompletedWorldCount();
```

<br>
## CCWorld <a href="https://github.com/soomla/cocos2dx-levelup/blob/master/Soomla/CCWorld.h" target="_blank"><img class="link-icon" src="/img/tutorial_img/linkImg.png"></a>

A game can have multiple `CCWorld`s or a single one, and `CCWorld`s can also contain other `CCWorld`s in them. In some games, these are referred to as level packs. Often, a `CCWorld` contains a set of `CCLevel`s, or multiple sets. A `CCWorld` can also have a potential `CCGate` that defines the criteria to enter it. Games that don’t have the concept of `CCWorld`s can be modeled as single `CCWorld` games.

**A `CCWorld` contains the following elements:**

- `mGate` - The `CCGate` that defines the criteria to enter this `CCWorld`.

- `mInnerWorldsMap` - The `CCWorld`s contained in this `CCWorld`.

- `mScores` - A list of `CCScore`s that this `CCWorld` contains . In the class `CCWorld`, you can add, retrieve, set, increase and decrease `CCScore`s, whether there are multiple `CCScore`s, or a single one.

- `mMissions` - A list of `CCMission`s that this `CCWorld` contains. In the class `CCWorld` you can add more `CCMission`s.

<br>
**HOW TO DEFINE**

For your convenience, there are multiple ways to define a `CCWorld`.

``` cpp
// Simple constructor that receives only an ID.
CCWorld *worldA = CCWorld::create(CCString::create(WORLD_A_ID));

// Instantiation of gates, scores, and missions will
// be demonstrated in the relevant sections below.
CCWorld *worldB = CCWorld::create(
	CCString::create("world_B_ID"),
	gate,
	innerWorlds,  
	scores,  
	missions
);
```

<br>
**COMMON USE**

**Add `CCWorld`s or `CCLevels` to a `CCWorld`**:

``` cpp
// Create a batch of `CCLevels`s and add them to the `CCWorld`. Instead
// of creating many levels one by one, you can create them all at
// once, and save time.
worldA->batchAddLevelsWithTemplates(
	10,               // Number of levels in this world
	gate,             // Gate for each of the levels
	scores,           // Scores for each of the levels
	missions          // Missions for each of the levels
);

// Add an inner world
CCWorld *innerWorld = CCWorld::create(CCString::create("inner_world"));
worldA->addInnerWorld(innerWorld);

// Fetch an inner world
CCWorld *world = worldA->getInnerWorldAt(0);
```

<br>
**Get/Set completion status:**

``` cpp
bool canStart = world->canStart(); // check world's status

worldA->setCompleted(true); //set as completed

bool isCompleted = worldA->isCompleted(); //check if complete
```

<br>
**Add elements to a `CCWorld`:**
You can add `CCMission`s and `CCScore`s to an existing `CCWorld`.

``` cpp
worldA->addMission(someMission);

worldA->addScore(someScore);
```

<br>
**Manipulate/Query `CCScore`(s) of a `CCWorld`:**

Use the different methods provided in `CCWorld` to get, set, increase, and decrease its `CCScore`s.  You can use the `sumInnerWorldsRecords` method to sum up the `CCScore`s of the inner `CCWorld`s or `CCLevel`s. Some methods are intended for a single `CCScore`, while others are for multiple `CCScore`s - you can differentiate between them according to their names and signatures.

``` cpp
/** For single scores: **/

worldA->setSingleScoreValue(300); // set score to 300

worldA->incSingleScore(100); // increase score by 100

worldA->decSingleScore(50); // decrease score by 50

CCScore *score =  worldA->getSingleScore(); // get score: value is 350


/** For multiple scores: **/

worldA->setScoreValue("some_score_ID", 500); // set SomeScore to 200

worldA->incScore("someScore", 200); // increase score by 200

worldA->decScore("someScore", 100); // decrease score by 50
```

<br>
**Manipulate/Query `Reward(s)` of a `CCWorld`:**

``` cpp
worldA->assignReward(coinReward); // assign a reward to this world

cocos2d::__String rewardID = worldA->getAssignedRewardID(); // get reward ID
```

<br>
**Retrieve World Scores**

``` cpp
// Retrieve the record scores of worldA
__Dictionary *recordScores = worldA->getRecordScores();
DictElement *el = NULL;
CCDICT_FOREACH(recordScores, el) {
    // Do something...
}

// Retrieve the latest scores of worldA
__Dictionary *latestScores = worldA->getLatestScores();
DictElement *el = NULL;
CCDICT_FOREACH(latestScores, el) {
    // Do something...
}
```

<br>
## CCLevel <a href="https://github.com/soomla/cocos2dx-levelup/blob/master/Soomla/CCLevel.h" target="_blank"><img class="link-icon" src="/img/tutorial_img/linkImg.png"></a>

One of the most common ways to create a sense of progress and accomplishment in games is to have levels. Every `CCLevel` has a state, that is always one of: `Idle`, `Running`, `Paused`, `Ended`, or `Completed`. To use levels correctly, you need to use the provided `start`, `pause`, and `end` functions, in order for the level to keep an updated record of what its state is.

**A `CCLevel` contains the following elements:**

- `mStartTime` - The start time of this level.

- `mElapsed` - The duration of the play time for this level. This can be used for games that need to keep track of how long the user has been playing the level in order to calculate his / her `CCScore` at the end.

- `mState` - The state of the level is initially `Idle`. While the user is playing the level is in `Running` mode, and can later be one of `Paused`, `Ended`, or `Completed`.

<br>
**HOW TO DEFINE**

`CCLevel` has a few different constructors, 2 of which are shown here. The section about `CCWorld`s, above, demonstrates the use of a function called `batchAddLevelsWithTemplates` which is much more convenient to use than the `CCLevel` constructors.

``` cpp
CCLevel *level1 = CCLevel::create(CCString::create("level1ID"));

CCLevel *level2 = CCLevel::create(
	CCString::create("level2ID"),
	gate,
	scores,
	missions
);
```

<br>
**COMMON USE**

**Start, pause, restart, or end this `CCLevel`:**

``` cpp
level1->start();

level1->pause();

level1->start();

level1->end(true);

level1->restart(false);
```

<br>
**Manipulate a specific `CCLevel`:**

``` cpp
level1->setCompleted(true);
```

<br>
**Query a specific level**

``` cpp
int timesPlayed = level1->getTimesPlayed();

int timesStarted = level1->getTimesStarted();

long slowDuration = level1->getSlowestDurationMillis();

long fastDuration = level1->getFastestDurationMillis();

long playDuration = level1->getPlayDurationMillis();
```

<br>
## CCScore <a href="https://github.com/soomla/cocos2dx-levelup/blob/master/Soomla/scoring/CCScore.h" target="_blank"><img class="link-icon" src="/img/tutorial_img/linkImg.png"></a>

Represents a score in the game. A simple game usually has one generic numeric score which grows as the user progresses in the game. A game can also have multiple scores for different aspects such as time, speed, points, etc.

**A `CCScore` contains the following elements:**

- `mStartValue` - The initial `CCScore` value in the game.

- `mHigherBetter`- This field defines whether it's better that the score is higher or lower. A `CCScore` can be ascending in nature such as regular points (higher is better) or can be descending such as time-to-complete level (lower is better).

- `mTempScore` - The *current* value of this score.

<br>
**HOW TO DEFINE**

There are multiple ways to define a `CCScore`.

``` cpp
CCScore *score1 = CCScore::create(CCString::create("score1ID"));

CCScore *score2 = CCScore::create(
	CCString::create("score2ID"),            // ID  
	CCString::create("Number Score"),        // Name
	CCBool::create(true)                     // Higher is better
);
```

<br>
**COMMON USE**

**Check if the `CCScore` in the current game session has reached a certain value:**

``` cpp
// If the score has reached 5000 give the user a badge.
if (numScore->hasTempReached(5000)) {
	// Give a reward
}
```

<br>
**Check if the `CCScore` has reached a record:**

``` cpp
// Check if a value of 300 breaks numScore's record.
if (numScore->hasRecordReached(300)) {
	// Do something...
}
```

<br>
**Manipulate a `CCScore` by using its methods to get, set, increase, and decrease it.**

``` cpp
numScore->setTempScore(50); // numScore = 50

numScore->inc(200); // numScore = 250

numScore->dec(100); // numScore = 150

// When the level ends, the record score will be saved and
// numScore will be reset to its initial value.
```

<br>
### CCRangeScore <a href="https://github.com/soomla/cocos2dx-levelup/blob/master/Soomla/scoring/CCRangeScore.h" target="_blank"><img class="link-icon-small" src="/img/tutorial_img/linkImg.png"></a>

A specific type of `CCScore` that has an associated range. The `CCScore`'s  value can be only inside that range of values. **For example:** A puzzle or quiz can measure the user's success on a scale of 1 - 10, or a shooting `CCScore` can be on a scale of 10 to 100 according to the user's performance in the game.

<br>
**HOW TO DEFINE**

``` cpp
CCScore *quizScore = CCRangeScore::create(
	CCString::create("quizScore"),           // ID
	CCSRange::create(0, 10)                  // Range
);

CCScore *shootingScore = CCRangeScore::create(
	CCString::create("shootingScore"),       // ID
	CCString::create("Shooting Score"),      // Name
	CCSBool::create(true),                   // Higher is better
	CCSRange::create(10, 100)                // Range
);
```

<br>
### CCVirtualItemScore <a href="https://github.com/soomla/cocos2dx-levelup/blob/master/Soomla/scoring/CCVirtualItemScore.h" target="_blank"><img class="link-icon-small" src="/img/tutorial_img/linkImg.png"></a>

A specific type of `CCScore` that has an associated virtual item, whose balance is affected by the score.

**For example:** In a game that has diamonds that the user needs to collect, you would define a `VirtualCurrency` "diamond" and a `CCVirtualItemScore` "diamondScore". The balance of "diamond" changes according to "diamondScore".  

<br>
**HOW TO DEFINE**

For your convenience, there are multiple ways to define a `CCVirtualItemScore`.

``` cpp
CCVirtualCurrency *diamond = CCVirtualCurrency::create(...);
CCScore *diamondScore = CCVirtualItemScore::create(
	CCString::create("diamondScore"),       // ID
	CCString::create("diamond_ID")          // Associated item ID
);

CCVirtualCurrency *coin = CCVirtualCurrency::create(...);
CCScore *coinScore = CCVirtualItemScore::create(
	CCString::create("coinScore"),          // ID
	CCString::create("Coin Score"),         // Name
	CCBool::create(true),                   // Higher is better
	CCString::create("coin_ID")             // Associated item ID
);
```

<br>
## CCGate <a href="https://github.com/soomla/cocos2dx-levelup/blob/master/Soomla/gates/CCGate.h" target="_blank"><img class="link-icon" src="/img/tutorial_img/linkImg.png"></a>

A `CCGate` is an object that defines certain criteria for progressing between the game's `CCWorld`s or `CCLevel`s. The `CCGate` is a criteria or list of rules which which must be met in order to enter the `CCWorld` or `CCLevel`. The rules are based on components of the previous `CCWorld` or `CCLevel`: scores achieved, missions completed, etc. The `CCGate` is opened once the logical conditions are met. In some games, `CCGate`s can be opened with a payment or social task, such as sharing the game on Facebook.

<div class="info-box">`CCGate` is an abstract class. Below are explanations of several types of `CCGate`s that implement `CCGate` and how to instantiate them.</div>

<br>
####**HOW TO OPEN**

All `CCGate`s share the same definition, as explained above, but each `CCGate` opens in a different way. Some `CCGate`s need to be opened manually by the developer, and others will open automatically when a specific event is thrown. You'll find below an explanation of each type of `CCGate` including how it opens.

<br>
### CCBalanceGate <a href="https://github.com/soomla/cocos2dx-levelup/blob/master/Soomla/gates/store/CCBalanceGate.h" target="_blank"><img class="link-icon-small" src="/img/tutorial_img/linkImg.png"></a>

A specific type of `CCGate` that has an associated virtual item and a desired balance. The`CCGate` opens **automatically** once the item's balance has reached the desired balance.

This type of gate encourages the user to collect more of some virtual item, such as coins or diamonds, and therefore creates motivation for the user to keep playing.  

<br>
**HOW TO DEFINE**

``` cpp
CCVirtualCurrency *coin = CCVirtualCurrency::create(...);

// Collect 50 coins to open the gate
CCGate *bGate = CCBalanceGate::create(
	CCString::create("bGate"),           // ID
	CCString::create("coin_ID"),         // Associated item ID
	CCInteger::create(50)                // Desired balance
);
```

<br>
####**USE CASE**

``` cpp
int balance;
bool isOpen;

balance = CCStoreInventory::sharedStoreInventory()->getItemBalance(
	"coin_ID",
	&error
);
// balance = 0

isOpen = bGate->isOpen();
//False because the coin balance hasn't reached the desired balance (50).

CCStoreInventory::sharedStoreInventory()->giveItem(
	"coin",
	50,
	&error
);

balance = CCStoreInventory::sharedStoreInventory()->getItemBalance(
	"coin_ID",
	&error
);
// balance = 50

isOpen = bGate->isOpen();
// True because the balance has reached the desired balance (50).
```

<br>
### CCPurchasableGate <a href="https://github.com/soomla/cocos2dx-levelup/blob/master/Soomla/gates/store/CCPurchasableGate.h" target="_blank"><img class="link-icon-small" src="/img/tutorial_img/linkImg.png"></a>

A specific type of `CCGate` that has an associated Virtual item. This `CCGate` is useful when you want to allow unlocking of certain `CCLevel`s or `CCWorld`s only if they are purchased.

`CCPurchasableGate` can be used either to monetize your game, by making the user pay real money, or to create retention by giving the user motivation to collect enough currencies to make the needed purchase.

<div class="info-box">**IMPORTANT:** This `CCGate` does not open automatically when the relevant item has been purchased, but rather the developer has to *manually* open it. Once the `CCGate` is opened (by calling `open()`), the purchase process of the associated virtual item begins. See "Use Case" below.</div>

<br>
**HOW TO DEFINE**

``` cpp
CCVirtualGood *itemToBuy = CCSingleUseVG::create(
  CCString::create("Item To Buy"),
  CCString::create(""),
  CCString::create("itemToBuy_ID"),
  CCPurchaseWithMarket::create(
    CCString::create("itemToBuy_ProductID"),
    CCDouble::create(8.99))
);

// The user must buy the 'itemToBuy' in order to open this Gate.
CCGate *pGate = CCPurchasableGate::create(
	CCString::create("pGate"),             // ID
	CCString::create("itemToBuy_ID")       // Associated item ID
);
```

<br>
**USE CASE**

``` cpp
bool isOpen;

isOpen = gate->isOpen();  // False

pGate->open(); // Eventually, open() calls CCStoreInventory::sharedStoreInventory()->buyItem("itemToBuy_ID", &error);

isOpen = gate->isOpen(); // True because the shield has been purchased.
```

<br>
### CCRecordGate <a href="https://github.com/soomla/cocos2dx-levelup/blob/master/Soomla/gates/CCRecordGate.h" target="_blank"><img class="link-icon-small" src="/img/tutorial_img/linkImg.png"></a>

A `CCRecordGate` has an associated score and a desired record. The `CCGate` opens **automatically** once the player achieves the desired record.

`CCRecordGate` can be used to highly promote game progression. For example, if there is `CCRecordGate` for each level in the game, the user will be encouraged to continuously beat his/her own score.

<br>
**HOW TO DEFINE**

``` cpp
//The user needs to reach a record of 5000 for numberScore in order to open this Gate.
CCGate *rGate = CCRecordGate::create(
	CCString::create("rGate"),            // ID
	CCString::create("scoreID"),          // Associated score ID
	CCDouble::create(5000)                // Desired record
);
```

<br>
**USE CASE**

``` cpp
bool isOpen;
bool reachedRecord;

isOpen = rGate->isOpen(); // False, because numberScore hasn't reached a record of 5000

numberScore->inc(5000.0); // Now, the value of numberScore is 5000

// Once the level ends, the score's record is saved

reachedRecord = numberScore->hasRecordReached(5000); // True!

isOpen = rGate->isOpen(); // True because numberScore has reached the record of 5000
```

<br>
### CCScheduleGate <a href="https://github.com/soomla/cocos2dx-levelup/blob/master/Soomla/gates/CCScheduleGate.h" target="_blank"><img class="link-icon-small" src="/img/tutorial_img/linkImg.png"></a>

A specific type of `CCGate` that has a schedule that defines when the `CCGate` can be opened. The `CCGate` opens **automatically** according to the defined schedule.

`CCScheduleGate` can be used to create suspense and reel in the user to play at specific times that you define. For example, you can define a `CCScheduleGate` that unlocks a bonus level on Friday at 5pm. Chances are that the exclusivity of the bonus level (that can only be unlocked once a week), will cause the user to make himself/herself available for play on the time you specified.

<br>
**HOW TO DEFINE**

``` cpp
// This Gate is open if we are within the time frame defined
// in the schedule. In this case the gate is always open.
CCGate *sGate = CCScheduleGate::create(
	CCString::create("scheduleGate"),           // ID
	CCSchedule::createAnyTimeUnLimited()        // Schedule
);
```

<br>
####**USE CASE**

``` cpp
// The schedule defines this gate to be open all the time.
bool isOpen = sGate->isOpen();  // TRUE!
```

<br>
### CCWorldCompletionGate <a href="https://github.com/soomla/cocos2dx-levelup/blob/master/Soomla/gates/CCWorldCompletionGate.h" target="_blank"><img class="link-icon-small" src="/img/tutorial_img/linkImg.png"></a>

A `CCWorldCompletionGate` has an associated `CCWorld` that. Once the `CCWorld` is completed, the `CCGate` **automatically** opens.

This gate is perhaps, the simplest of the gates, in that its only requirement is that the user finish the previous World in order to move on to the next.

<br>
####**HOW TO DEFINE**

``` cpp
CCWorld *worldA = CCWorld::create(__String::create("worldA"));

CCGate *wGate = CCWorldCompletionGate::create(
	CCString::create("wGate"),          // ID
	CCString::create("worldA")          // Associated world ID
);
```

<br>
####**USE CASE**

``` cpp
bool isOpen;
bool isCompleted;

isCompleted = worldA->isCompleted(); // False because worldA has not been completed.

isOpen = wGate->isOpen(); // False because worldA has not been completed.

worldA->setCompleted(true); // Set worldA as completed.

isOpen = wGate->isOpen(); // True because worldA has been completed.
```

<br>
### CCGatesList <a href="https://github.com/soomla/cocos2dx-levelup/blob/master/Soomla/gates/CCGatesList.h" target="_blank"><img class="link-icon-small" src="/img/tutorial_img/linkImg.png"></a>

A list of one or more `CCGate`s that together define a composite criteria for progressing between the game's `CCWorld`s or `CCLevel`s.

There are two kinds of `CCGatesList`s:

<br>
#### **CCGatesListAND** <a href="https://github.com/soomla/cocos2dx-levelup/blob/master/Soomla/gates/CCGatesListAND." target="_blank"><img class="link-icon-small" src="/img/tutorial_img/linkImg.png"></a>

A specific type of `CCGatesList` that can be opened only if ALL `CCGate`s in its list are open.

This gate is very demanding since it requires multiple criteria in order to be opened. Once the gate is opened, you can allow the user to skip one `CCWorld` and continue on to the next.

<br>
**HOW TO DEFINE**

``` cpp
// NOTE: wGate and rGate are defined in the sections above.

CCArray *gates = CCArray::create(wGate, rGate, NULL);

CCGatesList *wGateANDrGate = CCGatesListAnd::create(
	CCString::create("wGateANDrGate"),
	gates
);
```

<br>
####**USE CASE**

``` cpp
// The user needs to meet the criteria of wGate AND of rGate
// in order to open this Gate. For the definitions of wGate and
// rGate, see the topics WorldCompletionGate and RecordGate above.

int isOpen;
bool isCompleted;
bool wGateIsOpen;
bool rGateIsOpen;

isCompleted = worldA->isCompleted(); // False because worldA has not been completed

wGateIsOpen = wGate->isOpen(); // False because worldA has not been completed

rGateIsOpen = rGate->isOpen(); // False because numberScore hasn't reached a record of 5000

worldA->setCompleted(true); // Set worldA as completed

wGateIsOpen = wGate->isOpen(); // True because worldA has been completed

isOpen = wGateANDrGate->isOpen(); // False because we need both of the gates to be open

numberScore->inc(5000.0); // Now numberScore = 5000

// Once the level ends, the score's record is saved

isOpen = wGateANDrGate->isOpen(); // TRUE! because both gates have been opened
```

<br>
#### **CCGatesListOR** <a href="https://github.com/soomla/cocos2dx-levelup/blob/master/Soomla/gates/CCGatesListOr.h" target="_blank"><img class="link-icon-small" src="/img/tutorial_img/linkImg.png"></a>

A specific type of `CCGatesList` that can be opened if AT LEAST ONE `CCGate`s in its list is open.

<br>
**HOW TO DEFINE**

``` cpp
// NOTE: wGate and rGate are defined in the sections above.

CCArray *gates = CCArray::create(wGate, rGate, NULL);

CCGatesList *wGateORrGate = CCGatesListOr::create(
	CCString::create("wGateORrGate"),
	gates
);
```

<br>
####**USE CASE**

``` cpp
// The user needs to meet the criteria of wGate OR of rGate
// in order to open this Gate. For the definitions of wGate and
// rGate, see the topics WorldCompletionGate and RecordGate above.

bool isOpen;
bool wGateIsOpen;
bool rGateIsOpen;
bool isCompleted;

isCompleted = worldA->isCompleted(); // False because worldA has not been completed.

wGateIsOpen = wGate->isOpen(); // False because worldA has not been completed.

rGateIsOpen = rGate->isOpen();  // False because numberScore hasn't reached a record of 5000.0

worldA->setCompleted(true); // Set worldA as completed.

wGateIsOpen = wGate->isOpen();  // True because worldA has been completed.

isOpen = wGateORrGate->isOpen(); // TRUE! We only need one of the gates to be open,
// in this case it's wGate that's open.
```

<br>
### CCSocialActionGate <a href="https://github.com/soomla/cocos2dx-levelup/blob/master/Soomla/gates/profile/CCSocialActionGate.h" target="_blank"><img class="link-icon-small" src="/img/tutorial_img/linkImg.png"></a>

`CCSocialActionGate`s require the user to perform a specific social action in order to open the `CCGate`s. Currently, the social provider that's available is Facebook, so the `CCGate`s are FB-oriented. In the future, more social providers will be added.

`CCSocialActionGate`s allow you to enforce social engagement by locking certain levels or worlds behind social interactions. For example, you can ask your users to like your page or share a specific status about your game, and in return unlock a bonus level or world for them. In this win-win situation your users will be pleased, and the network effect will increase the popularity of your game.

<div class="info-box">`CCSocialActionGate` is an abstract class. Below are explanations of the four types of social `CCGate`s that implement `CCSocialActionGate`. All types of `CCGate`s implement the superclass `CCGate` and therefore, implement its functionality and behavior.

Also, please note that `CCSocialActionGate`s are dependent on SOOMLA's [cocos2dx-profile](https://github.com/soomla/cocos2dx-profile).</div>

<br>
### CCSocialLikeGate <a href="https://github.com/soomla/cocos2dx-levelup/blob/master/Soomla/gates/profile/CCSocialLikeGate.h" target="_blank"><img class="link-icon-small" src="/img/tutorial_img/linkImg.png"></a>

A specific type of `CCGate` that has an associated page name.

<div class="info-box">**IMPORTANT:** This `CCGate` does not open automatically when the relevant page has been liked, but rather the developer has to *manually* open it. Once the `CCGate` is opened (by calling `open()`), the `like()` function is called.</div>

<br>
**HOW TO DEFINE**

``` cpp
CCGate *likeGate = CCSocialLikeGate::create(
	CCString::create("likeGate"),          // ID
	soomla::FACEBOOK,                      // Social Provider
	CCString::create("[page name]")        // Page to "Like"
);
```

<br>
### CCSocialStatusGate <a href="https://github.com/soomla/cocos2dx-levelup/blob/master/Soomla/gates/profile/CCSocialStatusGate.h" target="_blank"><img class="link-icon-small" src="/img/tutorial_img/linkImg.png"></a>

A specific type of `CCGate` that has an associated status.

<div class="info-box">**IMPORTANT:** This `CCGate` does not open automatically when the relevant status has been posted, but rather the developer has to *manually* open it. Once the `CCGate` is opened (by calling `open()`), the `updateStatus()` function is called.</div>

<br>
**HOW TO DEFINE**

``` cpp
CCGate *statusGate = CCSocialStatusGate::create(
	CCString::create("statusGate"),        // ID
	soomla::FACEBOOK,                      // Social Provider
	CCString::create("[status]")           // Status to post
);
```

<br>
### CCSocialStoryGate <a href="https://github.com/soomla/cocos2dx-levelup/blob/master/Soomla/gates/profile/CCSocialStoryGate.h" target="_blank"><img class="link-icon-small" src="/img/tutorial_img/linkImg.png"></a>

A specific type of `CCGate` that has an associated story.

<div class="info-box">**IMPORTANT:** This `CCGate` does not open automatically when the relevant story has been posted, but rather the developer has to *manually* open it. Once the `CCGate` is opened (by calling `open()`), the `updateStory()` function is called.</div>

<br>
**HOW TO DEFINE**

``` cpp
CCGate *storyGate = CCSocialStoryGate::create(
	CCString::create("storyGate"),         // ID
	soomla::FACEBOOK,                      // Social Provider
	CCString::create("[message]"),         // Message to post
	CCString::create("[Story Name]"),      // Story name
	CCString::create("[Caption]"),         // Caption for image
	CCString::create("[Link]"),            // Link to post
	CCString::create("[Image Url]")        // Image URL
);
```

<br>
### CCSocialUploadGate <a href="https://github.com/soomla/cocos2dx-levelup/blob/master/Soomla/gates/profile/CCSocialUploadGate.h" target="_blank"><img class="link-icon-small" src="/img/tutorial_img/linkImg.png"></a>

A specific type of `CCGate` that has an associated image.

<div class="info-box">**IMPORTANT:** This `CCGate` does not open automatically when the relevant image has been uploaded, but rather the developer has to *manually* open it. Once the `CCGate` is opened (by calling `open()`), the `uploadImage()` function is called.</div>

<br>
**HOW TO DEFINE**

``` cpp
CCGate *uploadGate = CCSocialUploadGate::create(
	CCString::create("uploadGate"),         // ID
	soomla::FACEBOOK,                       // Social Provider
	CCString::create("[Filename]")          // Image URL
	CCString::create("[message]"),          // Message to post
);
```

<br>
## CCMission <a href="https://github.com/soomla/cocos2dx-levelup/blob/master/Soomla/challenges/CCMission.h" target="_blank"><img class="link-icon" src="/img/tutorial_img/linkImg.png"></a>

A `CCMission` is a task your users need to complete in your game. `CCMission`s are the glue between `CCGate`s and `CCReward`s; Specific `CCReward`s can be given to users that complete `CCMission`s, and every `CCMission` has a `CCGate` that must be opened in order to complete it. You can create `CCMission`s and use them as single, independent entities OR create a `CCChallenge` to handle several `CCMission`s and monitor their completion. Some `CCMission`s may be completed multiple times.

**A `CCMission` contains the following elements:**

- `mRewards` - Rewards that can be earned when completing this `CCMission`.

- `mSchedule` - A schedule that defines the number of times this `CCMission` can be played, how often, etc.

- `mGate` - A `CCGate` that needs to be opened in order to complete this `CCMission`.

<br>
####**HOW TO COMPLETE**

All `CCMission`s share the same definition, as explained above, but each `CCMission` is completed in a different way. Some `CCMission`s need to be completed manually by the developer (by calling `Complete()`), and others will automatically be completed when a specific event is thrown. You'll find below an explanation of each type of `CCMission` including how it's completed.

<br>
####**COMMON USE**

All missions have the same functionality.

**Check if a `CCMission` is available:**

This function determines whether a `CCMission` is available to be set as completed, by checking the criteria that makes the `CCMission` available, as well as the number of times that this `CCMission` can be completed.

For example, let's say that you allow your user to complete "mission A" once, and "Mission B" an unlimited number of times. In this case, when you call `isAvailable()` on "mission A", it'll return true before the user has completed the mission, and false afterwards. However, every time you call `isAvailable()` on "mission B", it'll return true, no matter how many times the mission has been completed.

``` cpp
if (someMission->isAvailable()) {
	//do something...
}
```

**Check if the `CCMission` is completed:**

``` cpp
if (someMission->isCompleted()) {
	//do something...
}
```

<br>
### CCBalanceMission <a href="https://github.com/soomla/cocos2dx-levelup/blob/master/Soomla/challenges/store/CCBalanceMission.h" target="_blank"><img class="link-icon-small" src="/img/tutorial_img/linkImg.png"></a>

A specific type of `CCMission` that has an associated virtual item and a desired balance. The `CCMission` is **automatically** complete once the item's balance reaches the desired balance.

<br>
**HOW TO DEFINE**

``` cpp
// To complete this mission the user needs to collect 250 coins.
// Once the mission is complete he/she will receive the reward.
CCMission *bMission = CCBalanceMission::create(
	CCString::create("coinMission"),       // ID
	CCString::create("Coin Mission"),      // Name
	balanceReward,                         // Optional reward(s)
	CCString::create("coinScore_ID"),      // Associated Score ID
	CCInteger::create(250)                 // Desired balance
);
```

<br>
####**USE CASE**

``` cpp
int balance;
bool isCompleted;
soomla::CCError *error = NULL;

balance = CCStoreInventory::sharedStoreInventory()->getItemBalance("coinID", &error);
// Balance = 0

isCompleted = bMission->isCompleted(); // False because the coin balance hasn't
																			 // reached the desired balance (250).

CCStoreInventory::sharedStoreInventory()->giveItem("coinID", 250, &error);

balance = CCStoreInventory::sharedStoreInventory()->getItemBalance("coinID", &error);
// Now balance = 250

isCompleted = bMission->isCompleted();
// True because the balance has reached the desired balance (250).
```

<br>
### CCRecordMission <a href="https://github.com/soomla/cocos2dx-levelup/blob/master/Soomla/challenges/CCRecordMission.h" target="_blank"><img class="link-icon-small" src="/img/tutorial_img/linkImg.png"></a>

A specific type of `CCMission` that has an associated score and a desired record. The `CCMission` is **automatically** complete once the player achieves the desired record for the given score.

<br>
**HOW TO DEFINE**

``` cpp
// To complete this mission, the user needs his "coinScore" to reach a record of 5000.
CCMission *rMission = CCRecordMission::create(
	CCString::create("rMission"),          // ID
	CCString::create("Coin Record Score"), // Name
	coinReward,                            // Optional reward(s)
	CCString::create("coinScore_ID"),      // Associated Score ID
	CCDouble::create(2000.0)               // Desired record
);
```

<br>
####**USE CASE**

``` cpp
bool isCompleted;
bool reachedRecord;

reachedRecord = numberScore->hasRecordReached(2000.0);
// False because numberScore has a record of 0

isCompleted = rMission->isCompleted();  
// False, because numberScore hasn't reached a record of 2000

numberScore->inc(2000.0); // Now, the value of numberScore is 2000

// Once the level ends, the score's record is saved

reachedRecord = numberScore->hasRecordReached(2000.0);
// True because numberScore has reached the record of 2000

isCompleted = rMission->isCompleted();  // TRUE!
```

<br>
### CCPurchasingMission <a href="https://github.com/soomla/cocos2dx-levelup/blob/master/Soomla/challenges/store/CCPurchasingMission.h" target="_blank"><img class="link-icon-small" src="/img/tutorial_img/linkImg.png"></a>

A specific type of `CCMission` that has an associated market item.

<div class="info-box">**IMPORTANT:** This `CCMission` is not completed automatically when the relevant item has been purchased, but rather the developer has to *manually* complete it. Once the `CCMission` is complete (by calling `complete()`), the purchase process of the associated virtual item begins. See "Use Case" below.</div>


<br>
**HOW TO DEFINE**

``` cpp
CCVirtualGood *itemToBuy = CCSingleUseVG::create(
	CCString::create("name"),
	CCString::create("description"),
	CCString::create("itemToBuy_ID"),  
	CCPurchaseWithMarket::create(CCString::create("itemToBuy_ProdID",
		CCDouble::create(1.99))
);

// To complete this mission, the user needs to buy the item.
CCMission *pMission = CCPurchasingMission::create(
	CCString::create("pMission"),          // ID
	CCString::create("Purchase Mission"),  // Name
	purchaseReward,                        // Optional reward(s)
	CCString::create("itemToBuy_ID"),      // Associated item ID
);
```

<br>
####**USE CASE**

``` cpp
bool isCompleted;

isCompleted = pMission->isCompleted();  // False

pMission->complete(); // Eventually, complete() calls CCStoreInventory::sharedStoreInventory()->buyItem("itemToBuy_ID", &error)

isCompleted = pMission->isCompleted();  // True because the item has been purchased.
```

<br>
### WorldCompletionMission <a href="https://github.com/soomla/cocos2dx-levelup/blob/master/Soomla/challenges/CCWorldCompletionMission.h" target="_blank"><img class="link-icon-small" src="/img/tutorial_img/linkImg.png"></a>

A specific type of `CCMission` that has an associated `CCWorld`. The `CCMission` is **automatically** complete once the `CCWorld` has been completed.

<br>
**HOW TO DEFINE**

``` cpp
CCWorld *worldA = CCWorld::create(__String::create("worldA_ID"));

CCMission *wMission = CCWorldCompletionMission::create(
	CCString::create("wMission"),            // ID
	CCString::create("Complete World A"),    // Name
	coinReward,                              // Optional reward(s)
	CCString::create("worldA_ID")            // Associated World ID
);
```

####**USE CASE**

``` cpp
bool isMissionComplete;
bool isWorldComplete;

isWorldComplete = worldA->isCompleted(); // False because worldA has not been completed yet.

isMissionComplete = wMission->isCompleted(); // False because worldA has not been completed.

worldA->setCompleted(true); // Set worldA as completed.

isMissionComplete = wMission->isCompleted(); // True because worldA has been completed.
```

<div class="info-box">The following `CCMission`s require the user to perform a specific social action in order to receive a `CCReward`. Currently, the social provider that's available is Facebook, so the `CCMission`s are FB-oriented. In the future, more social providers will be added. Please note that `CCSocialActionGate`s are dependent on SOOMLA's [cocos2dx-profile](https://github.com/soomla/cocos2dx-profile).</div>

<br>
### SocialLikeMission <a href="https://github.com/soomla/cocos2dx-levelup/blob/master/Soomla/challenges/profile/CCSocialLikeMission.h" target="_blank"><img class="link-icon-small" src="/img/tutorial_img/linkImg.png"></a>

A specific type of `CCMission` that has an associated page name.

<div class="info-box">**IMPORTANT:** This `CCMission` is not completed automatically when the relevant page has been liked, but rather the developer has to *manually* complete it. Once the `CCMission` is complete (by calling `complete()`), the `like()` function is called.</div>

<br>
**HOW TO DEFINE**

``` cpp
CCMission *likeMission = CCSocialLikeMission::create(
	CCString::create("likeMission"),         // ID
	CCString::create("Like Mission"),        // Name
	likeReward,                              // Optional reward(s)
	soomla::FACEBOOK,                        // Social Provider
	CCString::create("[page name]")          // Page to "Like"
);
```

<br>
###**CCSocialStatusMission**

A specific type of `CCMission` that has an associated status.

<div class="info-box">**IMPORTANT:** This `CCMission` is not completed automatically when the relevant status has been posted, but rather the developer has to *manually* complete it. Once the `CCMission` is complete (by calling `complete()`), the `updateStatus()` function is called.</div>

<br>
**HOW TO DEFINE**

``` cpp
CCMission *statusMission = CCSocialStatusMission::create(
	CCString::create("statusMission"),       // ID
	CCString::create("Status Mission"),      // Name
	statusReward,                            // Optional reward(s)
	soomla::FACEBOOK,                        // Social Provider
	CCString::create("status")               // Status to post
);
```

<br>

### CCSocialStoryMission <a href="https://github.com/soomla/cocos2dx-levelup/blob/master/Soomla/challenges/profile/CCSocialStoryMission.h" target="_blank"><img class="link-icon-small" src="/img/tutorial_img/linkImg.png"></a>

A specific type of `CCMission` that has an associated story that includes a message, story name, caption, link, and image.

<div class="info-box">**IMPORTANT:** This `CCMission` is not completed automatically when the relevant story has been posted, but rather the developer has to *manually* complete it. Once the `CCMission` is complete (by calling `complete()`), the `updateStory()` function is called.</div>

<br>
**HOW TO DEFINE**

``` cpp
CCMission *storyMission = CCSocialStoryMission::create(
	CCString::create("storyMission"),      // ID
	CCString::create("Story Mission"),     // Name
	storyReward,                           // Optional reward(s)
	soomla::FACEBOOK,                      // Social Provider
	CCString::create("message"),           // Message to post
	CCString::create("Story Name"),        // Story name
	CCString::create("Caption"),           // Caption for image
	CCString::create("https://..."),       // Link to post
	CCString::create("Image Url")          // Image URL
);
```

<br>

### CCSocialUploadMission <a href="https://github.com/soomla/cocos2dx-levelup/blob/master/Soomla/challenges/profile/CCSocialUploadMission.h" target="_blank"><img class="link-icon-small" src="/img/tutorial_img/linkImg.png"></a>

A specific type of `CCMission` that has an associated filename and message.

<div class="info-box">**IMPORTANT:** This `CCMission` is not completed automatically when the relevant image has been uploaded, but rather the developer has to *manually* complete it. Once the `CCMission` is complete (by calling `complete()`), the `uploadImage()` function is called.</div>

<br>
**HOW TO DEFINE**

``` cpp
CCMission *uploadMission = CCSocialUploadMission::create(
	CCString::create("uploadMission"),       // ID
	CCString::create("Upload Mission"),      // Name
	uploadReward,                            // Optional reward(s)
	soomla::FACEBOOK,                        // Social Provider
	CCString::create("filename")             // Image URL
	CCString::create("message"),             // Message to post
);
```

<br>
## CCChallenge <a href="https://github.com/soomla/cocos2dx-levelup/blob/master/Soomla/challenges/CCChallenge.h" target="_blank"><img class="link-icon" src="/img/tutorial_img/linkImg.png"></a>

Missions can be aggregated into challenges which can contain a single mission or multiple ones. The user is required to complete all these missions in order to earn the reward associated with the challenge.

<br>
**HOW TO DEFINE**

``` cpp
// To complete this challenge, the user must complete both missions.
// Once the challenge is complete, he/she will receive the reward(s).
// Note that wMission and rMission are defined in the examples above.
cocos2d::__Array *missions = cocos2d::__Array::create();
missions->addObject(wMission);
missions->addObject(rMission);

// To complete this challenge, the user must complete both missions.
CCChallenge *challenge = CCChallenge::create(
	__String::create("challenge"),             // ID
	__String::create("2 Mission Challenge"),   // Name
	missions,                                  // Mission(s) to complete
	challengeReward                            // Optional reward(s)
);
```

<br>
####**USE CASE**

``` cpp
bool isCompleted;

isCompleted = challenge->isCompleted(); // False because the missions haven't been completed

worldA->setCompleted(true); // Complete wMission

isCompleted = challenge->isCompleted(); // False because only 1 of 2 missions is complete

numberScore->inc(2000.0); // Now numberScore = 2000

// Once the level ends, the score's record is saved

isCompleted = challenge->isCompleted(); // TRUE! Both missions have been completed
```

##Auxiliary Models

### CCSchedule <a href="https://github.com/soomla/soomla-cocos2dx-core/blob/master/Soomla/CCSchedule.h" target="_blank"><img class="link-icon-small" src="/img/tutorial_img/linkImg.png"></a>

A `CCSchedule` defines any time restrictions that an entity may have.

**A `CCSchedule` contains the following restrictions:**

- **Recurrence** - How often is this entity available? Every month, week, day, hour?

	**For example:** A `CCMission` that is available to be completed every Monday.

- **Time Ranges** - A time range that defines when this entity is available, with a start time and an end time.

	**For example:** A `CCReward` that can be given starting when the user finishes a certain `CCLevel` and ending 8 seconds later.

- **Activation Limit** - The number of times that this entity is available for use.

	**For example:** A `CCMission` that can be attempted 10 times throughout gameplay.

### Reward <a href="https://github.com/soomla/soomla-cocos2dx-core/blob/master/Soomla/rewards/CCReward.h" target="_blank"><img class="link-icon-small" src="/img/tutorial_img/linkImg.png"></a>

<div class="info-box">Note that `CCReward` is a part of soomla-cocos2dx-core, and not part of the LevelUp module. However, because `CCReward`s are used very often throughout Levelup, it's important that you are familiar with the different `CCReward` types.</div>

A `CCReward` is an entity which can be earned by the user for meeting certain criteria in game progress. For example - a user can earn a badge for completing a `CCMission`. Dealing with `CCReward`s is very similar to dealing with `CCVirtualItem`s: grant a `CCReward` by giving it, and recall a `CCReward` by taking it.

`CCReward` itself cannot be instantiated, but there are many types of rewards, all explained below, and those will have the functionality that `CCReward` provides.

<br>

**COMMON USE**

All rewards have the same functionality.

**Give a `CCReward`:**

Use this to give your user a `CCReward`, for example you can give your users a `CCReward` of 100 coins just for downloading your game.

``` cpp
reward->give();
```

**Take a `CCReward`:**

Use this to redeem a `CCReward` from your user.

``` cpp
reward->take();
```

<br>
#### **CCVirtualItemReward** <a href="https://github.com/soomla/cocos2dx-store/blob/master/Soomla/rewards/CCVirtualItemReward.h" target="_blank"><img class="link-icon-small" src="/img/tutorial_img/linkImg.png"></a>

A specific type of `CCReward` that you can use to give your users some amount of a virtual item. **For example:** Give users a reward of 100 coins (virtual currency).

**HOW TO DEFINE**

``` cpp
CCReward *coinReward = CCVirtualItemReward::create(
	CCString::create("coinReward"),         // ID
	CCString::create("Coin Reward"),        // Name
	CCString::create("coinCurrency_ID")     // Associated item ID
	CCInteger::create(100)                  // Amount
);
```

<br>
#### **CCBadgeReward** <a href="https://github.com/soomla/soomla-cocos2dx-core/blob/master/Soomla/rewards/CCBadgeReward.h" target="_blank"><img class="link-icon-small" src="/img/tutorial_img/linkImg.png"></a>

A specific type of `CCReward` that represents a badge with an icon. **For example:** when the user achieves a top score, the user can earn a "Highest Score" badge reward.

**HOW TO DEFINE**

``` cpp
CCReward *goldMedal = CCBadgeReward::create(
	CCString::create("badge_goldMedal"),    // ID
	CCString::create("Gold Medal"),         // Name
);
```

<br>
#### **CCSequenceReward** <a href="https://github.com/soomla/soomla-cocos2dx-core/blob/master/Soomla/rewards/CCSequenceReward.h" target="_blank"><img class="link-icon-small" src="/img/tutorial_img/linkImg.png"></a>

A specific type of `CCReward` that holds a list of other `CCReward`s in a certain sequence. The rewards are given in ascending order. **For example:** In a Karate game the user can progress between belts and can be rewarded a sequence of: blue belt, yellow belt, green belt, brown belt, and lastly, black belt.

**HOW TO DEFINE**

``` cpp
cocos2d::__Array *belts = cocos2d::__Array::create();
// Assume that the below belts are BadgeRewards that have been defined.
belts->addObject(blueBelt);
belts->addObject(yellowBelt);
belts->addObject(greenBelt);
belts->addObject(brownBelt);
belts->addObject(blackBelt);

CCReward *beltReward = CCSequenceReward::create(
	CCString::create("beltReward"),         // ID
	CCString::create("Belt Reward"),        // Name
	belts                                   // Sequence of rewards
);
```

<br>
#### **CCRandomReward** <a href="https://github.com/soomla/soomla-cocos2dx-core/blob/master/Soomla/rewards/CCRandomReward.h" target="_blank"><img class="link-icon-small" src="/img/tutorial_img/linkImg.png"></a>

A specific type of `CCReward` that holds a list of other `CCReward`s. When this `CCReward` is given, it randomly chooses a `CCReward` from the list of `CCReward`s it internally holds. **For example:** A user can earn a mystery box `CCReward` that grants him/her a random `CCReward`.

**HOW TO DEFINE**

``` cpp
cocos2d::__Array *rewards = cocos2d::__Array::create();
rewards->addObject(rewardA);
rewards->addObject(rewardB);

CCReward *mysteryReward = CCRandomReward::create(
	CCString::create("mysteryReward"),        // ID
	CCString::create("Mystery Box Reward"),   // Name
	rewards                                   // Rewards to choose from
);
```
