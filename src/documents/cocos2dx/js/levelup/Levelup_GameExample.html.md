---
layout: "content"
image: "InAppPurchase"
title: "Game Example"
text: "This guide demonstrates use of the different LevelUp entities through an example game with explanations and code snippets."
position: 4
theme: 'platforms'
collection: 'cocos2djs_levelup'
module: 'levelup'
lang: 'js' 
platform: 'cocos2dx'
---

#LevelUp Game Example

LevelUp models out worlds, levels, scores, missions, and more, all in one framework that allows game developers to build 
their game structure and progression behavior easily and effectively. The sense of progress that users feel in a game is 
what creates retention and long-term use, which usually lead to monetization.

In this document, you will find a game example that demonstrates the use of LevelUp's entities. There are detailed 
explanations following the example code, as well as gameplay examples. Use this document as a reference to create your 
game with LevelUp.

<div class="info-box">**NOTE:** If you haven't already, we suggest reading about the 
[LevelUp Model](/cocos2dx/js/levelup/Levelup_Model) and [LevelUp Event Handling](/cocos2dx/js/levelup/Levelup_Events).</div>

##The Game Example

**NOTE:** This document focuses on the game logic. Any wiring of the code examples to the user interface is only 
explained textually and is left for the developer to do.

###**Chimpo's Journey**

Chimpo is a smart, ambitious monkey, who embarks on a journey to reach the magical realms of Soomland. Players need to 
guide Chimpo through several lands (each land is a collection of levels), and need to avoid numerous predators and other 
obstacles on the dangerous way to Soomland.

###**Rules of the Game**

![alt text](/img/tutorial_img/levelup_game/levelStart.png "Start Level")

- To complete each level, Chimpo has to collect 2 bananas and reach the level's exit.

- To start each level, the previous level must be completed.

- Some levels have "Bonus Coconuts" that can be collected in order to receive rewards. These coconuts can be used as 
weapons to throw at enemies.

- Predators start to attack Chimpo after he has collected 2 bananas. Chimpo can escape the predators by either running 
to the exit before they catch him, or by knocking them out with coconuts.

- The player can earn coins throughout the game and use them to buy cool stuff in the store (see 
[IStoreAssets](#-istoreassets-code-) below). Coins can be accumulated by either buying them in the store for real 
money, or by earning them as rewards in the different missions of the game (see [Missions](#-missions-) below).

##Setup Code Example

We start by presenting the complete examples of the economy (`Soomla.IStoreAssets` implementation) and the LevelUp model.  
In the following section we will breakdown the LevelUp code to explain it in more detail.

###**IStoreAssets Code**

``` js


function ExampleAssets() {
  
  /** Virtual Currencies **/
  var COIN_CURRENCY = Soomla.Models.VirtualCurrency.create({
    itemId: 'coinCurrency_ID',                // Item ID
    name: 'Coin',                             // Name
    description: 'Coin currency'              // Description
  });


  /** Virtual Currency Packs **/

  var TWOHUND_COIN_PACK = Soomla.Models.VirtualCurrencyPack.create({
    itemId: 'coins_pack_200_ID',              // Item ID
    name: '200 Coins',                        // Name
    description: '200 Coins',                 // Description
    currency_itemId: 'coinCurrency_ID',       // The currency associated with this pack 
    currency_amount: 200,                     // Number of currencies in the pack
    purchasableItem: Soomla.Models.PurchaseWithMarket.createWithMarketItem(
      'coins_200_ProdID',                     // Product ID
      0.99)                                   // Initial price
  });

  var FIVEHUND_COIN_PACK = Soomla.Models.VirtualCurrencyPack.create({
    itemId: 'coins_pack_500_ID',              // Item ID
    name: '500 Coins',                        // Name
    description: '500 Coins',                 // Description
    currency_itemId: 'coinCurrency_ID',       // The currency associated with this pack 
    currency_amount: 500,                     // Number of currencies in the pack
    purchasableItem: Soomla.Models.PurchaseWithMarket.createWithMarketItem(
      'coins_500_ProdID',                     // Product ID
      1.99)                                   // Initial price
  });


  /** Virtual Goods **/

  var COCONUT = Soomla.Models.SingleUseVG.create({
    itemId: 'coconut_ID',                     // Item ID
    name: 'Coconut',                          // Name
    description: 'Knock out enemies!',        // Description
    purchasableItem: Soomla.Models.PurchaseWithVirtualItem.create({
      pvi_itemId: 'coinCurrency_ID',
      pvi_amount: 300
    })
  });

  var IMMUNITY = Soomla.Models.SingleUseVG.create({
    itemId: 'immunity_ID',                    // Item ID
    name: 'Immunity',                         // Name
    description: 'Immunity-15 sec',           // Description
    purchasableItem: Soomla.Models.PurchaseWithVirtualItem.create({
      pvi_itemId: 'coinCurrency_ID',
      pvi_amount: 250
    })
  });


  var SUPER_MONKEY = Soomla.Models.EquippableVG.create({
    itemId: 'superMonkey_ID',                 // Item ID
    name: 'Super Monkey',                     // Name
    description: 'Chimpo with super powers',  // Description
    equipping: EquippableVG.EquippingModel.GLOBAL,
    purchasableItem: Soomla.Models.PurchaseWithVirtualItem.create({
      pvi_itemId: 'coinCurrency_ID',
      pvi_amount: 1000
    })
  });

  return Soomla.IStoreAssets.create({
    categories: [],
    currencies: [COIN_CURRENCY],
    currencyPacks: [TWOHUND_COIN_PACK, FIVEHUND_COIN_PACK],
    goods: {
      singleUse: [COCONUT, IMMUNITY],
      lifetime: [],
      equippable: [SUPER_MONKEY],
      goodUpgrades: [],
      goodPacks: []
    },
    version: 0
  });
}
```

<br>
###**LevelUp Code**

**`ChimposJourney.js`**

``` js
ChimposJourney = {};
ChimposJourney.createInitialWorld = function () {

  /** Scores **/

  var pointScore = Soomla.Models.Score.create({
    itemId: 'pointScore_ID',                    // ID
    name: 'Point Score'                         // Name
  });

  var bananaScore = Soomla.Models.Score.create({
    itemId: 'bananaScore_ID',                   // ID
    name: 'Banana Score'                        // Name
  });

  /** Rewards **/

  var medalReward = Soomla.Models.BadgeReward.create({
    itemId: 'medalReward_ID',                   // ID
    name: 'Medal Reward'                        // Name
  });

  var jungTwoHundCoinReward = Soomla.Models.VirtualItemReward.create({
    itemId: 'jungTwoHundCoinReward_ID',         // ID
    name: '200 Coin Reward',                    // Name
    associatedItemId: 'coins_pack_200_ID',      // Associated item ID
    amount: 1                                   // Amount
  });

  var desTwoHundCoinReward = Soomla.Models.VirtualItemReward.create({
    itemId: 'desTwoHundCoinReward_ID',         // ID
    name: '200 Coin Reward',                    // Name
    associatedItemId: 'coins_pack_200_ID',      // Associated item ID
    amount: 1                                   // Amount
  });

  var fiveHundCoinReward = Soomla.Models.VirtualItemReward.create({
    itemId: 'fiveHundCoinReward_ID',            // ID
    name: '500 Coin Reward',                    // Name
    associatedItemId: 'coins_pack_500_ID',      // Associated item ID
    amount: 1                                   // Amount
  });

  /** Missions **/

  var pointMission = Soomla.Models.RecordMission.create({
    itemId: 'pointMission_ID',                  // ID
    name: 'Point Mission',                      // Name
    rewards: [medalReward],                     // Rewards
    associatedScoreId: 'pointScore_ID',         // Associated Score ID
    desiredRecord: 3                            // Desired record
  });

  var pointMission = Soomla.Models.BalanceMission.create({
    itemId: 'coconutMission_ID',                // ID
    name: 'Coconut Mission',                    // Name
    rewards: [fiveHundCoinReward],              // Rewards
    associatedItemId: 'coconut_ID',             // Associated virtual item
    desiredBalance: 5,                          // Desired balance
    schedule: Soomla.Models.Schedule.createAnyTimeOnce()
  });
    
  var likeMission = Soomla.Models.SocialLikeMission.create({
    itemId: 'likeMission_ID',                   // ID
    name: 'Like Mission',                       // Name
    rewards: [hundCoinReward],                  // Rewards
    provider: Soomla.Models.Provider.FACEBOOK,  // Social Provider
    pageName: 'pageToLike',                     // Page to "Like"
    schedule: Soomla.Models.Schedule.createAnyTimeOnce()
  });

  var statusMissionJungle = Soomla.Models.SocialStatusMission.create({
    itemId: 'statusMissionJungle_ID',           // ID
    name: 'Status Mission Jungle',              // Name
    rewards: [jungTwoHundCoinReward],           // Rewards
    provider: Soomla.Models.Provider.FACEBOOK,  // Social Provider
    status: 'World completed!'                  // Status to post
  });

  var statusMissionDesert = Soomla.Models.SocialStatusMission.create({
    itemId: 'statusMissionDesert_ID',           // ID
    name: 'Status Mission Desert',              // Name
    rewards: [desTwoHundCoinReward],            // Rewards
    provider: Soomla.Models.Provider.FACEBOOK,  // Social Provider
    status: 'World completed!'                  // Status to post
  });

  /** Worlds **/

  // Initial world
  var mainWorld = Soomla.Models.World.create({
    itemId: 'main_world',                       // ID
    missions: [coconutMission, likeMission]     // Missions
  });

  var jungleWorld = Soomla.Models.World.create({
    itemId: 'jungleWorld_ID',                   // ID
    missions: [statusMissionJungle]             // Missions
  });

  var desertWorld = Soomla.Models.World.create({
    itemId: 'desertWorld_ID',                   // ID
    missions: [statusMissionDesert]             // Missions
  });

  /** Levels **/

  jungleWorld.batchAddLevelsWithTemplates(
    3,                                              // Number of levels in this world
    null,                                           // Gate for each of the levels
    [bananaScore],                                  // Scores for each of the levels
    null                                            // Missions for each of the levels
  );

  desertWorld.batchAddLevelsWithTemplates(
    3,                                              // Number of levels in this world
    null,                                           // Gate for each of the levels
    [bananaScore],                                  // Scores for each of the levels
    null                                            // Missions for each of the levels
  );

  // Bind pointMission to the first level of the first world (jungleWorld)
  var firstLevel = jungleWorld.getInnerWorldAt(0);
  firstLevel.addMission(pointMission);

  /** Gates **/

  // Once users finish Jungle world, they can continue to Desert world.
  var desertGate = Soomla.Models.WorldCompletionGate.create({
    itemId: 'desertGate_ID',                        // ID
    associatedWorldId: 'jungleWorld_ID'             // Associated world ID
  });

  desertWorld.setGate(desertGate);

  // See function (addGatesToWorld) below
  ChimposJourney.addGatesToWorld(jungleWorld);
  ChimposJourney.addGatesToWorld(desertWorld);


  /** Add Worlds to Initial World **/

  mainWorld.addInnerWorld(jungleWorld);
  mainWorld.addInnerWorld(desertWorld);

  return mainWorld;
};
...  

ChimposJourney.addGatesToWorld = function (world) {

  // Iterate over all levels of the given world
  var i = 1;
  _.forEach(innerWorldsMap, function (world) {
    var previousLevel = world.getInnerWorldAt(i - 1));
    var currentLevel = world.getInnerWorldAt(i);

    var bananaScoreOfPrevLevel_ID = previousLevel.scores[_.keys(previousLevel.scores)[1]];

    // The associated score of this Level's RecordGate
    // is the bananaScore of the previous level.
    var bananaGate = Soomla.Models.RecordGate.create({
        itemId: 'bananaGate_' + world.itemId +'_level_' + i,    // ID
        associatedScoreId: bananaScoreOfPrevLevel_ID,           // Associated score ID
        desiredRecord: 2                                        // Desired record
    });

    // The associated world of this Level's WorldCompletionGate
    // is the previous level (level IS A world).
    var prevLevelCompletionGate = Soomla.Models.WorldCompletionGate.create({
      itemId: 'prevLevelCG_' + world.itemId + '_level_' + i,    // ID
      associatedWorldId: previousLevel.itemId                   // Associated world ID
    });

    // The gates in this Level's GatesListAnd are the 2 gates declared above.
    var currentLevelGate = Soomla.Models.GatesListAnd.create({
       itemId: 'gate_' + world.itemId + '_level_' + i,
       gates: [prevLevelCompletionGate, bananaGate]
    });

    currentLevel.setGate(currentLevelGate);
    i++;
  });
};
```

###**Event Handling**

Adding event handling to the main scene

``` js
MainScene.addEvents = function () {
  Soomla.addHandler(Soomla.LevelUpConsts.EVENT_WORLD_COMPLETED, 
        MainScene.onWorldCompleted, MainScene);
  Soomla.addHandler(Soomla.LevelUpConsts.EVENT_MISSION_COMPLETED, 
        MainScene.onMissionCompleted, MainScene);
};

...

MainScene.onWorldCompleted = function (world) {
  // Implemented in the relevant sections below.
}

MainScene.onMissionCompleted = function (mission) {
  // Implemented in the relevant sections below.
}
```

###**Initialization Code**

Read more in our [Getting Started](/cocos2dx/js/levelup/Levelup_GettingStarted) tutorial.

**`main.js`**

``` js
...

cc.game.onStart = function () {
  Soomla.initialize("customSecret");

  // We initialize SoomlaStore before we open the store.
  var assets = new ExampleAssets();
  var storeParams = {
    androidPublicKey: "ExamplePublicKey",
    testPurchases: true
  };

  // This is the call to initialize SoomlaStore
  Soomla.soomlaStore.initialize(assets, storeParams);

  var profileParams = {
  };
  Soomla.soomlaProfile.initialize(profileParams);

  Soomla.soomlaLevelUp.initialize(ChimposJourney.createInitialWorld());

  ...
}
```

##Code Explained

Below are explanations of the different LevelUp entities used throughout Chimpo's Journey.

###**Worlds & Levels**

Our game has 2 worlds, with 3 levels each. We used the function `batchAddLevelsWithTemplates`  from the class `World`, 
in order to add 3 levels to each world in a convenient way.

We also defined `mainWorld` to contain the 2 worlds. Later we initialized `SoomlaLevelUp` with this initial world.

![alt text](/img/tutorial_img/levelup_game/worlds.png "Worlds & Levels")

<br>

**REMINDER:** Here is the code for the worlds as defined [above](#-levelup-code-).

``` js
/** Worlds **/

// Initial world
  var mainWorld = Soomla.Models.World.create({
    itemId: 'main_world',                       // ID
    missions: [coconutMission, likeMission]     // Missions
  });

  var jungleWorld = Soomla.Models.World.create({
    itemId: 'jungleWorld_ID',                   // ID
    missions: [statusMissionJungle]             // Missions
  });

  var desertWorld = Soomla.Models.World.create({
    itemId: 'desertWorld_ID',                   // ID
    missions: [statusMissionDesert]             // Missions
  });

...

/** Add Worlds to Initial World **/

mainWorld.addInnerWorld(jungleWorld);
mainWorld.addInnerWorld(desertWorld);
```

###**Scores**

Each level has 2 scores, a point score and a banana score:

<br>
####`pointScore`

This score can be based on any function you choose. In our example, `pointScore` (relevant only to the first level of 
the game) is calculated based on how long it took the player to complete the level. Our example score-calculation 
function is as follows:

``` js
pointScore.setTempScore(3);

if the level hasn't been finished yet and 30 seconds have past {
  pointScore.setTempScore(2);
}

if the level hasn't been finished yet and 60 seconds have past {
  pointScore.setTempScore(1);
}
```

At the end of each level, we draw the `pointScore` as stars.

![alt text](/img/tutorial_img/levelup_game/pointScore.png "Time-based score")

One of the missions of the game is based on this point score, but more about that later in [Missions](#-missions-).

<br>
####`bananaScore`

This score is for collecting the 2 bananas in order to move on to the the next level. You'll read more about 
`bananaGate` in [Gates](#-gates-).

`bananaScore` is also individual to each level. Why? If we were to declare this score globally, then all the levels 
would share the same `bananaScore`. This means that after collecting the 2 bananas in the first level of the first 
world, that level's gate would open, as well as all the other banana gates. The banana gates of all the next levels 
would also open because 2 bananas (overall in the game) have been collected. To avoid this situation, we declare such 
a banana score for EACH level. Then each level's banana gate will open only after *its* 2 bananas have been collected.

###**Gates**

The first level in the first world, `jungleWorld` has no gate that needs to be opened in order to unlock the level, 
because it is the starting point of the game. The rest of the levels and worlds have gates, explained below.

<br>
####**Gates between Worlds**

In order to create an order of play among the worlds in our game, we need to add a `WorldCompletionGate` to each world, 
except for the first one. The first one is `jungleWorld` and it has no `Gate` because it's open for initial play. 
The second (and last) world is `desertWorld`. In order to be able to start `desertWorld`, `jungleWorld` must be completed.

<br>
####**Gates between Levels**

To finish a level, the player needs to have completed the previous level AND to collect 2 bananas in the current level.

- `bananaGate` - This gate is of type `RecordGate`, with the associated score, `bananaScore` and a desired record of 2.

- `previousLevelCompletionGate` - This gate is of type `WorldCompletionGate`, with the associated world as the previous 
level each time (remember that `Level` inherits from `World`).

Gates are logical conditions.  To stipulate that a level's gate will be opened when both `bananaGate` AND 
`previousLevelCompletionGate` are opened, we create a gates list composed of both of these gates.  In the private 
function `addGatesToWorld`, a `GatesListAnd` that includes a `bananaGate` and a `previousLevelCompletionGate` is added 
to all levels (except for the very first one because it needs to be open for initial play.)

###**Missions**

There are various missions throughout the game that can be completed for rewards. Some are individual to each level and 
others are per world or per the entire game.

<br>
####`pointMission`

`pointMission` is of type `RecordMission`, which is a mission that has an associated score and a desired record. In 
Chimpo's Journey, `pointMission` is available in the first level of the game. The mission's associated score is 
`pointScore`, and the desired record is 3 (see the `pointScore` calculation described in [Scores](#-scores-) above). 
If the user reaches the desired record, he/she will receive a medal badge as a reward.

<br>
####`coconutMission`

This mission is of type `BalanceMission`, which is a mission that has an associated virtual item and a desired balance. 
In `coconutMission`, the associated virtual item is a `SingleUseVG` named `COCONUT`, and the desired balance is 5.

There are several coconuts scattered throughout the levels of the game, and they can be used to throw at and kill 
enemies. Coconuts can be accumulated by finding and collecting them throughout the game, or by purchasing them in the 
store for an expensive price of 300 coins each. Once the player has accumulated 5 coconuts (without using them up of 
course), he/she will receive a reward.

![alt text](/img/tutorial_img/levelup_game/coconutMission.png "Coconut mission")

Since coconuts can be found in random levels, and are accumulated globally, `coconutMission` applies to the entire game, 
and is not level-specific. In other words, if we would have created such a mission for *each* level, then once a level 
ends, the player wouldn't be able to keep collecting coconuts.

In the [setup code example](#setup-code-example) above, notice that under the declaration of `coconutMission`, we set 
the mission's `Schedule` so that it can be completed once throughout the entire game, anytime the user likes.

<br>
####`likeMission`

This is a `SocialLikeMission` that is offered in the main menu of the game and can be performed once anytime the user 
chooses. All the player has to do is like the given Facebook page, and in return he/she will receive free coins.

<br>
####`statusMission`s

`statusMissionJungle` and `statusMissionDesert` are of type `SocialStatusMission` and are offered each at the end of 
the world it is associated with. In these missions the user can post a specific status on Facebook and in return he/she 
will receive free coins. Upon world completion, an `onWorldCompleted` event is thrown - we created an event handler that 
displays a screen like in the image below, and allows the user to share the specific status.

``` js
MainScene.onWorldCompleted = function (world) {
  // Draw the UI that allows the user to perform the mission (share the statusToPost).
}
```

![alt text](/img/tutorial_img/levelup_game/share.png "Share status mission")

##Gameplay Code

###**Check if level can start**

Chimpo's Journey has a menu that displays the worlds of the game and their levels. When the user clicks on a level the 
next screen is the game screen of the level. In order to render the relevant UI according to if a world/level is locked 
or not, we call `canStart()` of the `World` class.

``` js
// For example, iterate over the levels of jungleWorld...

var numLevels = Soomla.soomlaLevelUp.getLevelCountInWorld(jungleWorld);

for (var i=0; i < numLevels; i++) {
    var level = jungleWorld.getInnerWorldAt(i);

    if (level.canStart()) {
        // If so, draw the menu without a lock icon on the level
    } else {
        // Draw the menu with a lock icon on the level
    }
}
```

###**Start level**

``` js
level.start();
```

During gameplay, the top left corner of the screen will display the number of coconuts that he/she has collected out of 
the 5 needed to complete the coconut mission. Once the mission has been completed, we won't want to display that 
information any longer since it is not relevant - remember, we defined the coconut mission as a global mission that can 
be played only once throughout the game; If the user has already completed it once, it won't be available anymore. To do 
this, before a level begins, we'll check if the coconut mission **is  available**, and if so we'll display the number of 
coconuts collected out of 5.  

``` js
if (mission.isAvailable()) {
  // Display UI that shows how many coconuts the user has collected out of 5.
}
```

###**Level progression**

####**Collecting Bananas**
During gameplay, the user will collect bananas in each level. Every time he/she collects a banana, we'll need to 
increase the `bananaScore`. We'll also need to check if the user's `bananaScore` has reached the desired balance of 2, 
and if so we'll open the exit so that Chimpo can finish the level. Once the level will end, the record score (2) will 
be saved and the next level's `RecordGate` (`bananaGate`) will open at this point (remember that the record for banana 
gate is 2).

``` js
var levelBananaScore = currentLevel.getScores().objectForKey(1);
var numOfBananas = 0;
var exitIsOpen = false;

// Once the user collects a banana in the level:
levelBananaScore.inc(1);

// Check if this banana was the second collected.
numOfBananas = levelBananaScore.getTempScore();
if (numOfBananas == 2) {
    // If so, render the UI to show the exit as open.

    levelBananaScore.reset(true);
    exitIsOpen = true;
}
```

<br>
####**Collecting Coconuts**

As mentioned above, Chimpo can collect coconuts in various levels throughout the game. When he collects a coconut, we 
need to make sure to increase the coconut balance, by calling `StoreInventory`'s function `giveItem`.

``` js
// If the user collects a coconut, we increase the coconut balance.
Soomla.storeInventory.giveItem('coconut_ID', 1);
```

Once the user completes the coconut mission at some point in the game, we'll want to change the UI at the top left 
corner of the screen from displaying the number of coconuts collected to the message "Mission Accomplished!". To do that 
we'll implement the event handler that is triggered when a mission is complete.

``` js
MainScene.onMissionCompleted = function (mission) {
  if (mission.name === 'Coconut Mission') {
    // Remove from the UI the number of coconuts collected, and
    // instead insert the message "Mission Accomplished!".
  }
}
```

<br>
####**Throwing Coconuts**

If the user uses one of the coconuts he/she collected for knocking out enemies, we need to deduct his/her coconut balance.

``` js
// If the user uses a coconut, we decrease the coconut balance.
Soomla.storeInventory.takeItem('coconut_ID', 1);
```

<br>
**For the curious:** What's going on behind the scenes is that every time we increase or decrease the user's coconut 
balance, an `onGoodBalanceChanged` event is thrown. Our `BalanceMission`, (`coconutMission`) is registered to such an 
event so every time this event is thrown, the coconut's balance is checked to determine if it reached the desired 
balance of 5. Once the user collects 5 coconuts (i.e. his/her coconut balance is 5), the `coconutMission` will be marked 
as complete and the user will receive his/her reward.

###**End level**

####**Case 1:**

If Chimpo is attacked by a predator, we'll need to end the level unsuccessfully:

``` js
level.end(false);
```

If we check the level's completion status, we'll get false. That means the `WorldCompletionGate` for the next level 
(whose associated world is this current level) will not be open, and therefore this will ensure that the next level 
won't be open for play. In this case, we'll want to draw the relevant UI, for example a screen that has a replay button 
that allows the player to try this level again.

<div class="info-box">TIP: Here, in your games, you can incorporate a concept of "lives" to limit the number of times 
the user can try again to succeed at a level. If he/she reaches the limit and still does not succeed, you can offer 
him/her either to wait some duration of time until they can try again, or offer a `PurchasingMission` where they can 
buy extra lives (to continue play immediately) AND they'll receive a reward.</div>

``` js
bool isLevelComplete = level1.isCompleted();

  if (!isLevelComplete) {
    // Draw the relevant UI
  }
}
```

<br>
####**Case 2:**

The user successfully escapes all predators and reaches the exit on the screen.

![alt text](/img/tutorial_img/levelup_game/levelEnd.png "End Level")

If the exit `isOpen` (meaning that Chimpo collected the 2 bananas), we'll end the level successfully! Here, we should 
also render the relevant UI, such as a screen that congratulates the user and offers him/her to continue to the next 
level or go back to the main menu.

Notice that once we call `end(true)`, the next level's `WorldCompletionGate` will open.

``` js
// If Chimpo tries to walk through the exit, we need to check that
// the exit is open.
if (exitIsOpen) {

  // End this level successfully
  // (Saves score internally)
  level1.end(true);

  // Draw the relevant UI screen
}
```
