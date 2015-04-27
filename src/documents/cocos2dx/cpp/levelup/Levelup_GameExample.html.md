---
layout: "content"
image: "InAppPurchase"
title: "Game Example"
text: "This guide demonstrates use of the different LevelUp entities through an example game with explanations and code snippets."
position: 4
theme: 'platforms'
collection: 'cocos2dx_levelup'
module: 'levelup'
platform: 'cocos2dx'
---

#LevelUp Game Example

LevelUp models out worlds, levels, scores, missions, and more, all in one framework that allows game developers to build their game structure and progression behavior easily and effectively. The sense of progress that users feel in a game is what creates retention and long-term use, which usually lead to monetization.

In this document, you will find a game example that demonstrates the use of LevelUp's entities. There are detailed explanations following the example code, as well as gameplay examples. Use this document as a reference to create your game with LevelUp.

<div class="info-box">**NOTE:** If you haven't already, we suggest reading about the [LevelUp Model](/cocos2dx/cpp/levelup/Levelup_Model) and [LevelUp Event Handling](/cocos2dx/cpp/levelup/Levelup_Events).</div>

##The Game Example

**NOTE:** This document focuses on the game logic. Any wiring of the code examples to the user interface is only explained textually and is left for the developer to do.

###**Chimpo's Journey**

Chimpo is a smart, ambitious monkey, who embarks on a journey to reach the magical realms of Soomland. Players need to guide Chimpo through several lands (each land is a collection of levels), and need to avoid numerous predators and other obstacles on the dangerous way to Soomland.

###**Rules of the Game**

![alt text](/img/tutorial_img/levelup_game/levelStart.png "Start Level")

- To complete each level, Chimpo has to collect 2 bananas and reach the level's exit.

- To start each level, the previous level must be completed.

- Some levels have "Bonus Coconuts" that can be collected in order to receive rewards. These coconuts can be used as weapons to throw at enemies.

- Predators start to attack Chimpo after he has collected 2 bananas. Chimpo can escape the predators by either running to the exit before they catch him, or by knocking them out with coconuts.

- The player can earn coins throughout the game and use them to buy cool stuff in the store (see [CCStoreAssets](#-ccstoreassets-code-) below). Coins can be accumulated by either buying them in the store for real money, or by earning them as rewards in the different missions of the game (see [Missions](#-missions-) below).

##Setup Code Example

We start by presenting the complete examples of the economy (`CCStoreAssets` implementation) and the LevelUp model.  In the following section we will breakdown the LevelUp code to explain it in more detail.

###**CCStoreAssets Code**

``` cpp
using namespace soomla;

ExampleAssets *ExampleAssets::create() {
    ExampleAssets *ret = new ExampleAssets();
    ret->autorelease();
    ret->init();

    return ret;
}

bool ExampleAssets::init() {

    /** Virtual Currencies **/

    CCVirtualCurrency *COIN_CURRENCY = CCVirtualCurrency::create(
      __String::create("Coin"),               // Name
      __String::create("Coin currency"),      // Description
      __String::create("coinCurrency_ID")     // Item ID
    );


    /** Virtual Currency Packs **/

    CCVirtualCurrencyPack *TWOHUND_COIN_PACK = CCVirtualCurrencyPack::create(
      __String::create("200 Coins"),          // Name
      __String::create("200 Coins"),          // Description
      __String::create("coins_pack_200_ID"),  // Item ID
      __Integer::create(200),                 // Number of currencies in the pack
      __String::create("coinCurrency_ID"),    // The currency associated with this pack
      CCPurchaseWithMarket::create(           // Purchase type
        __String::create("coins_200_ProdID"),
        __Double::create(0.99))
    );

    CCVirtualCurrencyPack *FIVEHUND_COIN_PACK = CCVirtualCurrencyPack::create(
      __String::create("500 Coins"),          // Name
      __String::create("500 Coins"),          // Description
      __String::create("coins_pack_500_ID"),  // Item ID
      __Integer::create(500),                 // Number of currencies in the pack
      __String::create("coinCurrency_ID"),    // The currency associated with this pack
      CCPurchaseWithMarket::create(           // Purchase type
        __String::create("coins_500_ProdID"),
        __Double::create(1.99))
    );


    /** Virtual Goods **/

    CCVirtualGood *COCONUT = CCSingleUseVG::create(
      __String::create("Coconut"),            // Name
      __String::create("Knock out enemies!"), // Description
      __String::create("coconut_ID"),         // Item ID
      CCPurchaseWithVirtualItem::create(      // Purchase type
        __String::create("coinCurrency_ID"),
        __Integer::create(300))
    );

    CCVirtualGood *IMMUNITY = CCSingleUseVG::create(
      __String::create("Immunity"),           // Name
      __String::create("Immunity-15 sec"),    // Description
      __String::create("immunity_ID"),        // Item ID
      CCPurchaseWithVirtualItem::create(      // Purchase type
        __String::create("coinCurrency_ID"),
        __Integer::create(250))
    );


    CCVirtualGood *SUPER_MONKEY = CCEquippableVG::create(
      __Integer::create(CCEquippableVG::kGlobal),
      __String::create("Super Monkey"),             // Name
      __String::create("Chimpo with super powers"), // Description
      __String::create("superMonkey_ID"),           // Item ID
      CCPurchaseWithVirtualItem::create(            // Purchase type
        __String::create("coinCurrency_ID"),
        __Integer::create(1000))
    );

    mCurrencies = __Array::create(COIN_CURRENCY, NULL);
    mCurrencyPacks = __Array::create(TWOHUND_COIN_PACK, FIVEHUND_COIN_PACK, NULL);
    mGoods = __Array::create(COCONUT, IMMUNITY, SUPER_MONKEY, NULL);

    mCurrencies->retain();
    mCurrencyPacks->retain();
    mGoods->retain();

    return true;
}

MuffinRushAssets::~MuffinRushAssets() {
    CC_SAFE_RELEASE(mGoods);
}

int MuffinRushAssets::getVersion() {
    return 0;
}

cocos2d::__Array *MuffinRushAssets::getCurrencies() {
    return mCurrencies;
}

cocos2d::__Array *MuffinRushAssets::getGoods() {
    return mGoods;
}

cocos2d::__Array *MuffinRushAssets::getCurrencyPacks() {
    return mCurrencyPacks;
}

cocos2d::__Array *MuffinRushAssets::getCategories() {
    return NULL;
}
```

<br>
###**LevelUp Code**

**`ChimposJourney.cpp`**

``` cpp

CCWorld *ChimposJourney::createInitialWorld() {

  /** Scores **/

  CCScore *pointScore = CCScore::create(
    __String::create("pointScore_ID"),            // ID  
    __String::create("Point Score"),              // Name
    __Bool::create(true)                          // Higher is better
  );

  CCScore *bananaScore = CCScore::create(
    __String::create("bananaScore_ID"),           // ID  
    __String::create("Banana Score"),             // Name
    __Bool::create(true)                          // Higher is better
  );

  /** Rewards **/

  CCReward *medalReward = CCBadgeReward::create(
    __String::create("medalReward_ID"),           // ID
    __String::create("Medal Reward")              // Name
  );

  CCReward *jungTwoHundCoinReward = CCVirtualItemReward::create(
    __String::create("jungTwoHundCoinReward_ID"), // ID
    __String::create("200 Coin Reward"),          // Name
    __String::create("coins_pack_200_ID")         // Associated item ID
    __Integer::create(1)                          // Amount
  );

  CCReward *desTwoHundCoinReward = CCVirtualItemReward::create(
    __String::create("desTwoHundCoinReward_ID"),  // ID
    __String::create("200 Coin Reward"),          // Name
    __String::create("coins_pack_200_ID")         // Associated item ID
    __Integer::create(1)                          // Amount
  );

  CCReward *fiveHundCoinReward = CCVirtualItemReward::create(
    __String::create("fiveHundCoinReward_ID"),    // ID
    __String::create("500 Coin Reward"),          // Name
    __String::create("coins_pack_500_ID"),        // Associated item ID
    __Integer::create(1)                          // Amount
  );

  /** Missions **/

  CCMission *pointMission = CCRecordMission::create(
    __String::create("pointMission_ID"),          // ID
    __String::create("Point Mission"),            // Name
    __Array::createWithObject(medalReward),       // Rewards
    __String::create("pointScore_ID"),            // Associated Score ID
    __Double::create(3)                           // Desired record
  );

  CCMission *coconutMission = CCBalanceMission::create(
    __String::create("coconutMission_ID"),        // ID
    __String::create("Coconut Mission"),          // Name
    __Array::createWithObject(fiveHundCoinReward), // Rewards
    __String::create("coconut_ID"),               // Associated virtual item
    __Integer::create(5)                          // Desired balance
  );
  coconutMission->setSchedule(soomla::CCSchedule::createAnyTimeOnce());

  CCMission *likeMission = CCSocialLikeMission::create(
    __String::create("likeMission_ID"),           // ID
    __String::create("Like Mission"),             // Name
    __Array::createWithObject(hundCoinReward),    // Rewards
    soomla::FACEBOOK,                             // Social provider
    __String::create("pageToLike")                // Page to like
  );
  likeMission->setSchedule(soomla::CCSchedule::createAnyTimeOnce());

  CCMission *statusMissionJungle = CCSocialStatusMission::create(
    __String::create("statusMissionJungle_ID"),   // ID
    __String::create("Status Mission Jungle"),    // Name
    __Array::createWithObject(jungTwoHundCoinReward), // Rewards
    soomla::FACEBOOK,                             // Social provider
    __String::create("World completed!")          // Status to post
  );

  CCMission *statusMissionDesert = CCSocialStatusMission::create(
    __String::create("statusMissionDesert_ID"),   // ID
    __String::create("Status Mission Desert"),    // Name
    __Array::createWithObject(desTwoHundCoinReward), // Rewards
    soomla::FACEBOOK,                             // Social provider
    __String::create("World completed!")          // Status to post
  );

  /** Worlds **/

  // Initial world
  CCWorld *mainWorld = CCWorld::create(
    __String::create("main_world")                 // ID
    NULL, NULL, NULL,                              // Gate, Inner worlds, Scores
    __Array::create(coconutMission, likeMission)   // Missions
  );

  CCWorld *jungleWorld = CCWorld::create(
    __String::create("jungleWorld_ID"),            // ID
    NULL, NULL, NULL,                              // Gate, Inner worlds, Scores
    __Array::createWithObject(statusMissionJungle) // Missions
  );

  CCWorld *desertWorld = CCWorld::create(
    __String::create("desertWorld_ID"),            // ID
    NULL, NULL, NULL,                              // Gate, Inner worlds, Scores
    __Array::createWithObject(statusMissionDesert) // Missions
  );

  /** Levels **/

  jungleWorld->batchAddLevelsWithTemplates(
    3,                                              // Number of levels in this world
    NULL,                                           // Gate for each of the levels
    __Array::create(bananaScore, NULL),             // Scores for each of the levels
    NULL                                            // Missions for each of the levels
  );

  desertWorld->batchAddLevelsWithTemplates(
    3,                                              // Number of levels in this world
    NULL,                                           // Gate for each of the levels
    __Array::create(bananaScore, NULL),             // Scores for each of the levels
    NULL                                            // Missions for each of the levels
  );

  // Bind pointMission to the first level of the first world (jungleWorld)
  soomla::CCLevel *firstLevel =
    dynamic_cast<soomla::CCLevel*>(jungleWorld->getInnerWorldAt(0));
  firstLevel->addMission(pointMission);

  /** Gates **/

  // Once users finish Jungle world, they can continue to Desert world.
  CCGate *desertGate = CCWorldCompletionGate::create(
    __String::create("desertGate_ID"),              // ID
    __String::create("jungleWorld_ID")              // Associated world ID
  );

  desertWorld->setGate(desertGate);

  // See function (addGatesToWorld) below
  addGatesToWorld(jungleWorld);
  addGatesToWorld(desertWorld);


  /** Add Worlds to Initial World **/

  mainWorld->addInnerWorld(jungleWorld);
  mainWorld->addInnerWorld(desertWorld);

  return mainWorld;
}
...  

void addGatesToWorld(soomla::CCWorld *world) {

  // Iterate over all levels of the given world
  for (int i=1; i < world->getInnerWorldsMap()->count(); i++) {

    soomla::CCLevel *previousLevel =
       dynamic_cast<soomla::CCLevel*>(world->getInnerWorldAt(i - 1));

    soomla::CCLevel *currentLevel =
       dynamic_cast<soomla::CCLevel*>(world->getInnerWorldAt(i));

    __String *bananaScoreOfPrevLevel_ID =
      dynamic_cast<__String*>(previousLevel->getScores()->allKeys()->getObjectAtIndex(1));

    // The associated score of this Level's RecordGate
    // is the bananaScore of the previous level.
    soomla::CCGate *bananaGate = soomla::CCRecordGate::create(
       __String::createWithFormat("bananaGate_%s_level_%d",
                                  world->getId()->getCString(), i)
       bananaScoreOfPrevLevel_ID, __Double::create(2)
    );

    // The associated world of this Level's WorldCompletionGate
    // is the previous level (level IS A world).
    soomla::CCGate *prevLevelCompletionGate = soomla::CCWorldCompletionGate::create(
       __String::createWithFormat("prevLevelCG_%s_level_%d",
                                  world->getId()->getCString(), i),
       previousLevel->getId()
    );

    // The gates in this Level's GatesListAnd are the 2 gates declared above.
    soomla::CCGate *currentLevelGate = soomla::CCGatesListAnd::create(
       __String::createWithFormat("gate_%s_level_%d", world->getId()->getCString(), i),
       __Array::create(prevLevelCompletionGate, bananaGate, NULL)
    );

    currentLevel->setGate(currentLevelGate);
  }
}
```

###**Event Handling**

Adding event handling to the main scene

``` cpp
void MainScene::addEvents() {
  Director::getInstance()->getEventDispatcher()->addCustomEventListener(CCLevelUpConsts::EVENT_WORLD_COMPLETED, CC_CALLBACK_1(MainScene::onWorldCompleted, this));
  Director::getInstance()->getEventDispatcher()->addCustomEventListener(CCLevelUpConsts::EVENT_MISSION_COMPLETED, CC_CALLBACK_1(MainScene::onMissionCompleted, this));
}

...

void MainScene::onWorldCompleted(EventCustom *event) {
  // Implemented in the relevant sections below.
}

void MainScene::onMissionCompleted(EventCustom *event) {
  // Implemented in the relevant sections below.
}
```

###**Initialization Code**

Read more in our [Getting Started](/cocos2dx/cpp/levelup/Levelup_GettingStarted) tutorial.

**`AppDelegate.cpp`**

``` cpp
...

bool AppDelegate::applicationDidFinishLaunching() {

  soomla::CCSoomla::initialize("customSecret");

  ExampleAssets *assets = ExampleAssets::create();

  __Dictionary *storeParams = __Dictionary::create();
  storeParams->setObject(__String::create("ExamplePublicKey"), "androidPublicKey");

  soomla::CCSoomlaStore::initialize(assets, storeParams);

  __Dictionary *profileParams = __Dictionary::create();
  soomla::CCSoomlaProfile::initialize(profileParams);

  soomla::CCSoomlaLevelUp::getInstance()->initialize(ChimposJourney::createInitialWorld(), NULL);
}
```

##Code Explained

Below are explanations of the different LevelUp entities used throughout Chimpo's Journey.

###**Worlds & Levels**

Our game has 2 worlds, with 3 levels each. We used the function `batchAddLevelsWithTemplates`  from the class `CCWorld`, in order to add 3 levels to each world in a convenient way.

We also defined `mainWorld` to contain the 2 worlds. Later we initialized `CCSoomlaLevelUp` with this initial world.

![alt text](/img/tutorial_img/levelup_game/worlds.png "Worlds & Levels")

<br>

**REMINDER:** Here is the code for the worlds as defined [above](#-levelup-code-).

``` cpp
/** Worlds **/

// Initial world
CCWorld *mainWorld = CCWorld::create(
__String::create("main_world")                 // ID
NULL, NULL, NULL,                              // Gate, Inner worlds, Scores
__Array::create(coconutMission, likeMission)   // Missions
);

CCWorld *jungleWorld = CCWorld::create(
__String::create("jungleWorld_ID"),            // ID
NULL, NULL, NULL,                              // Gate, Inner worlds, Scores
__Array::createWithObject(statusMissionJungle) // Missions
);

CCWorld *desertWorld = CCWorld::create(
__String::create("desertWorld_ID"),            // ID
NULL, NULL, NULL,                              // Gate, Inner worlds, Scores
__Array::createWithObject(statusMissionDesert) // Missions
);

...

/** Add Worlds to Initial World **/

mainWorld->addInnerWorld(jungleWorld);
mainWorld->addInnerWorld(desertWorld);
```

###**Scores**

Each level has 2 scores, a point score and a banana score:

<br>
####`pointScore`

This score can be based on any function you choose. In our example, `pointScore` (relevant only to the first level of the game) is calculated based on how long it took the player to complete the level. Our example score-calculation function is as follows:

``` cs
pointScore->setTempScore(3);

if the level hasn't been finished yet and 30 seconds have past {
  pointScore->setTempScore(2);
}

if the level hasn't been finished yet and 60 seconds have past {
  pointScore->setTempScore(1);
}
```

At the end of each level, we draw the `pointScore` as stars.

![alt text](/img/tutorial_img/levelup_game/pointScore.png "Time-based score")

One of the missions of the game is based on this point score, but more about that later in [Missions](#-missions-).

<br>
####`bananaScore`

This score is for collecting the 2 bananas in order to move on to the the next level. You'll read more about `bananaGate` in [Gates](#-gates-).

`bananaScore` is also individual to each level. Why? If we were to declare this score globally, then all the levels would share the same `bananaScore`. This means that after collecting the 2 bananas in the first level of the first world, that level's gate would open, as well as all the other banana gates. The banana gates of all the next levels would also open because 2 bananas (overall in the game) have been collected. To avoid this situation, we declare such a banana score for EACH level. Then each level's banana gate will open only after *its* 2 bananas have been collected.

###**Gates**

The first level in the first world, `jungleWorld` has no gate that needs to be opened in order to unlock the level, because it is the starting point of the game. The rest of the levels and worlds have gates, explained below.

<br>
####**Gates between Worlds**

In order to create an order of play among the worlds in our game, we need to add a `CCWorldCompletionGate` to each world, except for the first one. The first one is `jungleWorld` and it has no `Gate` because it's open for initial play. The second (and last) world is `desertWorld`. In order to be able to start `desertWorld`, `jungleWorld` must be completed.

<br>
####**Gates between Levels**

To finish a level, the player needs to have completed the previous level AND to collect 2 bananas in the current level.

- `bananaGate` - This gate is of type `CCRecordGate`, with the associated score, `bananaScore` and a desired record of 2.

- `previousLevelCompletionGate` - This gate is of type `CCWorldCompletionGate`, with the associated world as the previous level each time (remember that `CCLevel` inherits from `CCWorld`).

Gates are logical conditions.  To stipulate that a level's gate will be opened when both `bananaGate` AND `previousLevelCompletionGate` are opened, we create a gates list composed of both of these gates.  In the private function `addGatesToWorld`, a `CCGatesListAND` that includes a `bananaGate` and a `previousLevelCompletionGate` is added to all levels (except for the very first one because it needs to be open for initial play.)

###**Missions**

There are various missions throughout the game that can be completed for rewards. Some are individual to each level and others are per world or per the entire game.

<br>
####`pointMission`

`pointMission` is of type `CCRecordMission`, which is a mission that has an associated score and a desired record. In Chimpo's Journey, `pointMission` is available in the first level of the game. The mission's associated score is `pointScore`, and the desired record is 3 (see the `pointScore` calculation described in [Scores](#-scores-) above). If the user reaches the desired record, he/she will receive a medal badge as a reward.

<br>
####`coconutMission`

This mission is of type `CCBalanceMission`, which is a mission that has an associated virtual item and a desired balance. In `coconutMission`, the associated virtual item is a `CCSingleUseVG` named `COCONUT`, and the desired balance is 5.

There are several coconuts scattered throughout the levels of the game, and they can be used to throw at and kill enemies. Coconuts can be accumulated by finding and collecting them throughout the game, or by purchasing them in the store for an expensive price of 300 coins each. Once the player has accumulated 5 coconuts (without using them up of course), he/she will receive a reward.

![alt text](/img/tutorial_img/levelup_game/coconutMission.png "Coconut mission")

Since coconuts can be found in random levels, and are accumulated globally, `coconutMission` applies to the entire game, and is not level-specific. In other words, if we would have created such a mission for *each* level, then once a level ends, the player wouldn't be able to keep collecting coconuts.

In the [setup code example](#setup-code-example) above, notice that under the declaration of `coconutMission`, we set the mission's `CCSchedule` so that it can be completed once throughout the entire game, anytime the user likes.

``` cpp
coconutMission->setSchedule(soomla::CCSchedule::createAnyTimeOnce());
```

<br>
####`likeMission`

This is a `CCSocialLikeMission` that is offered in the main menu of the game and can be performed once anytime the user chooses. All the player has to do is like the given Facebook page, and in return he/she will receive free coins.

<br>
####`statusMission`s

`statusMissionJungle` and `statusMissionDesert` are of type `CCSocialStatusMission` and are offered each at the end of the world it is associated with. In these missions the user can post a specific status on Facebook and in return he/she will receive free coins. Upon world completion, an `onWorldCompleted` event is thrown - we created an event handler that displays a screen like in the image below, and allows the user to share the specific status.

``` cpp
void MainScene::onWorldCompleted(CCWorld *world) {
  // Draw the UI that allows the user to perform the mission (share the statusToPost).
}
```

![alt text](/img/tutorial_img/levelup_game/share.png "Share status mission")

##Gameplay Code

###**Check if level can start**

Chimpo's Journey has a menu that displays the worlds of the game and their levels. When the user clicks on a level the next screen is the game screen of the level. In order to render the relevant UI according to if a world/level is locked or not, we call `canStart()` of the `CCWorld` class.

``` cpp
// For example, iterate over the levels of jungleWorld...

int numLevels = soomla::CCSoomlaLevelUp::getInstance()->getLevelCountInWorld(jungleWorld);

for (int i=0; i < numLevels; i++) {
    soomla::CCLevel *level = dynamic_cast<soomla::CCLevel*>(jungleWorld->getInnerWorldAt(i));

    if(level->canStart()) {
        // If so, draw the menu without a lock icon on the level
    } else {
        // Draw the menu with a lock icon on the level
    }
}
```

###**Start level**

``` cpp
level->start();
```

During gameplay, the top left corner of the screen will display the number of coconuts that he/she has collected out of the 5 needed to complete the coconut mission. Once the mission has been completed, we won't want to display that information any longer since it is not relevant - remember, we defined the coconut mission as a global mission that can be played only once throughout the game; If the user has already completed it once, it won't be available anymore. To do this, before a level begins, we'll check if the coconut mission **is  available**, and if so we'll display the number of coconuts collected out of 5.  

``` cpp
if (mission->isAvailable()) {
  // Display UI that shows how many coconuts the user has collected out of 5.
}
```

###**Level progression**

####**Collecting Bananas**
During gameplay, the user will collect bananas in each level. Every time he/she collects a banana, we'll need to increase the `bananaScore`. We'll also need to check if the user's `bananaScore` has reached the desired balance of 2, and if so we'll open the exit so that Chimpo can finish the level. Once the level will end, the record score (2) will be saved and the next level's `CCRecordGate` (`bananaGate`) will open at this point (remember that the record for banana gate is 2).

``` cpp
soomla::CCScore *levelBananaScore =
  dynamic_cast<soomla::CCScore*>(currentLevel->getScores()->objectForKey(1));
double numOfBananas = 0;
bool exitIsOpen = false;

// Once the user collects a banana in the level:
levelBananaScore->inc(1);

// Check if this banana was the second collected.
numOfBananas = levelBananaScore->getTempScore();
if (numOfBananas == 2) {
    // If so, render the UI to show the exit as open.

    levelBananaScore->reset(true);
    exitIsOpen = true;
}
```

<br>
####**Collecting Coconuts**

As mentioned above, Chimpo can collect coconuts in various levels throughout the game. When he collects a coconut, we need to make sure to increase the coconut balance, by calling `CCStoreInventory`'s function `giveItem`.

``` cpp
// If the user collects a coconut, we increase the coconut balance.
soomla::CCStoreInventory::sharedStoreInventory()->giveItem("coconut_ID", 1, &levelupError);
```

Once the user completes the coconut mission at some point in the game, we'll want to change the UI at the top left corner of the screen from displaying the number of coconuts collected to the message "Mission Accomplished!". To do that we'll implement the event handler that is triggered when a mission is complete.

``` cpp
void MainScene::onMissionCompleted(CCMission *mission) {
  __Dictionary *eventData = (__Dictionary *)event->getUserData();
  CCMission *mission = dynamic_cast<CCMission *>(eventData->objectForKey(CCLevelUpConsts::DICT_ELEMENT_MISSION));

  if (mission->getName()->isEqual(__String::create("Coconut Mission"))) {
    // Remove from the UI the number of coconuts collected, and
    // instead insert the message "Mission Accomplished!".
  }
}
```

<br>
####**Throwing Coconuts**

If the user uses one of the coconuts he/she collected for knocking out enemies, we need to deduct his/her coconut balance.

``` cpp
// If the user uses a coconut, we decrease the coconut balance.
soomla::CCStoreInventory::sharedStoreInventory()->takeItem("coconut_ID", 1, &levelupError);
```

<br>
**For the curious:** What's going on behind the scenes is that every time we increase or decrease the user's coconut balance, an `onGoodBalanceChanged` event is thrown. Our `CCBalanceMission`, (`coconutMission`) is registered to such an event so every time this event is thrown, the coconut's balance is checked to determine if it reached the desired balance of 5. Once the user collects 5 coconuts (i.e. his/her coconut balance is 5), the `coconutMission` will be marked as complete and the user will receive his/her reward.

###**End level**

####**Case 1:**

If Chimpo is attacked by a predator, we'll need to end the level unsuccessfully:

``` cpp
level->end(false);
```

If we check the level's completion status, we'll get false. That means the `CCWorldCompletionGate` for the next level (whose associated world is this current level) will not be open, and therefore this will ensure that the next level won't be open for play. In this case, we'll want to draw the relevant UI, for example a screen that has a replay button that allows the player to try this level again.

<div class="info-box">TIP: Here, in your games, you can incorporate a concept of "lives" to limit the number of times the user can try again to succeed at a level. If he/she reaches the limit and still does not succeed, you can offer him/her either to wait some duration of time until they can try again, or offer a `CCPurchasingMission` where they can buy extra lives (to continue play immediately) AND they'll receive a reward.</div>

``` cpp
bool isLevelComplete = level1->isCompleted();

  if (!isLevelComplete) {
    // Draw the relevant UI
  }
}
```

<br>
####**Case 2:**

The user successfully escapes all predators and reaches the exit on the screen.

![alt text](/img/tutorial_img/levelup_game/levelEnd.png "End Level")

If the exit `isOpen` (meaning that Chimpo collected the 2 bananas), we'll end the level successfully! Here, we should also render the relevant UI, such as a screen that congratulates the user and offers him/her to continue to the next level or go back to the main menu.

Notice that once we call `End(true)`, the next level's `CCWorldCompletionGate` will open.

``` cpp
// If Chimpo tries to walk through the exit, we need to check that
// the exit is open.
if(exitIsOpen) {

  // End this level successfully
  // (Saves score internally)
  level1->end(true);

  // Draw the relevant UI screen
}
```
