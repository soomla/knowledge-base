---
layout: "content"
image: "Events"
title: "Events"
text: "Learn how to observe and handle game progress events triggered by cocos2dx-levelup to customize your game-specific behavior."
position: 3
theme: 'platforms'
collection: 'cocos2dx_levelup'
module: 'levelup'
platform: 'cocos2dx'
---

# Event Handling

## About

LevelUp allows you to subscribe to events, be notified when they occur, and implement your own application-specific behavior to handle them once they occur.

<div class="info-box">Your game-specific behavior is an addition to the default behavior implemented by SOOMLA. You don't replace SOOMLA's behavior.</div>

## How it Works

Events are triggered when SOOMLA wants to notify you about different things that happen involving LevelUp operations.

For example, when a user completes a World, an `EVENT_WORLD_COMPLETED` event is fired as a result.


## Observing & Handling Events

SOOMLA uses the Cocos2d-x facilities to dispatch its own custom events.
The names of such events are defined in `CCLevelUpConsts`, the meta-data of the event is held in a `__Dictionary`. You can subscribe to any event from anywhere in your code.

When handling the event you can extract meta-data from the dictionary using pre-defined keys, which are also defined in `CCLevelUpConsts`.

### Cocos2d-x v3 Events

** Subscribing **

Subscribe to events through the Cocos2d-x  [`EventDispatcher`](http://www.cocos2d-x.org/wiki/EventDispatcher_Mechanism):

```cpp
cocos2d::Director::getInstance()->getEventDispatcher()->addCustomEventListener(soomla::CCLevelUpConsts::EVENT_WORLD_COMPLETED, CC_CALLBACK_1(ExampleScene::onWorldCompleted, this));
```

** Handling **

Handle the event through your own custom function:

```cpp
void ExampleScene::onWorldCompleted(cocos2d::EventCustom *event) {
  cocos2d::__Dictionary *eventData = (cocos2d::__Dictionary *)event->getUserData();
	// ... get meta-data information from eventData
}
```


### Cocos2d-x v2 Events

** Subscribing **

Subscribe to events through the Cocos2d-x `CCNotificationCenter`:

```cpp
cocos2d::CCNotificationCenter::sharedNotificationCenter()->addObserver(this, callfuncO_selector(ExampleScene::onWorldCompleted), soomla::CCLevelUpConsts::EVENT_WORLD_COMPLETED, NULL);
```

** Handling **

Handle the event through your own custom function:

```cpp
void ExampleScene::onWorldCompleted(cocos2d::CCDictionary *eventData) {
	// ... get meta-data information from eventData
}
```

## LevelUp Events

### EVENT_LEVEL_UP_INITIALIZED

This event is triggered when

```cpp
Director::getInstance()->getEventDispatcher()->addCustomEventListener(CCLevelUpConsts::EVENT_LEVEL_UP_INITIALIZED, CC_CALLBACK_1(Example::onLevelUpInitialized, this));

void Example::onLevelUpInitialized(EventCustom *event) {
  // ... your game specific implementation here ...
}
```

### EVENT_WORLD_COMPLETED

This event is triggered when a `CCWorld` has been completed.

```cpp
Director::getInstance()->getEventDispatcher()->addCustomEventListener(CCLevelUpConsts::EVENT_WORLD_COMPLETED, CC_CALLBACK_1(Example::onWorldCompleted, this));

void Example::onWorldCompleted(EventCustom *event) {
	// DICT_ELEMENT_WORLD - the world that was completed

	__Dictionary *eventData = (__Dictionary *)event->getUserData();
  CCWorld *world = dynamic_cast<CCWorld *>(eventData->objectForKey(CCLevelUpConsts::DICT_ELEMENT_WORLD));

  // ... your game specific implementation here ...
}
```

### EVENT_WORLD_REWARD_ASSIGNED

This event is triggered when a `CCReward` is assigned to a `CCWorld`.

```cpp
Director::getInstance()->getEventDispatcher()->addCustomEventListener(CCLevelUpConsts::EVENT_WORLD_REWARD_ASSIGNED, CC_CALLBACK_1(Example::onWorldRewardAssigned, this));

void Example::onWorldRewardAssigned(EventCustom *event) {
	// DICT_ELEMENT_WORLD - the world who had a reward assigned to it

	__Dictionary *eventData = (__Dictionary *)event->getUserData();
  CCWorld *world = dynamic_cast<CCWorld *>(eventData->objectForKey(CCLevelUpConsts::DICT_ELEMENT_WORLD));

  // ... your game specific implementation here ...
}
```

### EVENT_LEVEL_STARTED

This event is triggered when a `CCLevel` has started.

```cpp
Director::getInstance()->getEventDispatcher()->addCustomEventListener(CCLevelUpConsts::EVENT_LEVEL_STARTED, CC_CALLBACK_1(ExampleLevelUpEventHandler::onLevelStarted, this));

void Example::onGoodBalanceChanged(EventCustom *event) {
	// DICT_ELEMENT_LEVEL - the level that has started

	__Dictionary *eventData = (__Dictionary *)event->getUserData();
  CCLevel *level = dynamic_cast<CCLevel *>(eventData->objectForKey(CCLevelUpConsts::DICT_ELEMENT_LEVEL));

  // ... your game specific implementation here ...
}
```

### EVENT_LEVEL_ENDED

This event is triggered when a `CCLevel` has been completed.

```cpp
Director::getInstance()->getEventDispatcher()->addCustomEventListener(CCLevelUpConsts::EVENT_LEVEL_ENDED, CC_CALLBACK_1(Example::onLevelEnded, this));

void Example::onLevelEnded(EventCustom *event) {
	// DICT_ELEMENT_LEVEL - the level that has ended

	__Dictionary *eventData = (__Dictionary *)event->getUserData();
  CCLevel *level = dynamic_cast<CCLevel *>(eventData->objectForKey(CCLevelUpConsts::DICT_ELEMENT_LEVEL));

  // ... your game specific implementation here ...
}
```

### EVENT_SCORE_RECORD_REACHED

This event is triggered when a `CCScore`'s record has been reached.

```cpp
Director::getInstance()->getEventDispatcher()->addCustomEventListener(CCLevelUpConsts::EVENT_SCORE_RECORD_REACHED, CC_CALLBACK_1(Example::onScoreRecordReached, this));

void Example::onScoreRecordReached(EventCustom *event) {
	// DICT_ELEMENT_SCORE - the score whose record has been reached

	__Dictionary *eventData = (__Dictionary *)event->getUserData();
  CCScore *score = dynamic_cast<CCScore *>(eventData->objectForKey(CCLevelUpConsts::DICT_ELEMENT_SCORE));

  // ... your game specific implementation here ...
}
```

### EVENT_SCORE_RECORD_CHANGED

This event is triggered when a `CCScore`'s record has changed.

```cpp
Director::getInstance()->getEventDispatcher()->addCustomEventListener(CCLevelUpConsts::EVENT_SCORE_RECORD_CHANGED, CC_CALLBACK_1(Example::onScoreRecordChanged, this));

void Example::onScoreRecordChanged(EventCustom *event) {
	// DICT_ELEMENT_SCORE - the score whose record has changed

	__Dictionary *eventData = (__Dictionary *)event->getUserData();
  CCScore *score = dynamic_cast<CCScore *>(eventData->objectForKey(CCLevelUpConsts::DICT_ELEMENT_SCORE));

  // ... your game specific implementation here ...
}
```

### EVENT_LATEST_SCORE_CHANGED

This event is triggered when a latest score is changed.

```cpp
Director::getInstance()->getEventDispatcher()->addCustomEventListener(CCLevelUpConsts::EVENT_LATEST_SCORE_CHANGED, CC_CALLBACK_1(Example::onLatestScoreChanged, this));

void Example::onLatestScoreChanged(EventCustom *event) {
	// DICT_ELEMENT_SCORE - the score whose record has changed

	__Dictionary *eventData = (__Dictionary *)event->getUserData();
  CCScore *score = dynamic_cast<CCScore *>(eventData->objectForKey(CCLevelUpConsts::DICT_ELEMENT_SCORE));

  // ... your game specific implementation here ...
}
```

### EVENT_GATE_OPENED

This event is triggered when a `CCGate` has opened.

```cpp
Director::getInstance()->getEventDispatcher()->addCustomEventListener(CCLevelUpConsts::EVENT_GATE_OPENED, CC_CALLBACK_1(Example::onGateOpened, this));

void Example::onGateOpened(EventCustom *event) {
	// DICT_ELEMENT_GATE - the gate that was opened

	__Dictionary *eventData = (__Dictionary *)event->getUserData();
  CCGate *gate = dynamic_cast<CCGate *>(eventData->objectForKey(CCLevelUpConsts::DICT_ELEMENT_GATE));

  // ... your game specific implementation here ...
}
```

### EVENT_GATE_CLOSED

This event is triggered when a `CCGate` has closed.

```cpp
Director::getInstance()->getEventDispatcher()->addCustomEventListener(CCLevelUpConsts::EVENT_GATE_CLOSED, CC_CALLBACK_1(Example::onGateClosed, this));

void Example::onGateClosed(EventCustom *event) {
	// DICT_ELEMENT_GATE - the gate that was opened

	__Dictionary *eventData = (__Dictionary *)event->getUserData();
  CCGate *gate = dynamic_cast<CCGate *>(eventData->objectForKey(CCLevelUpConsts::DICT_ELEMENT_GATE));

  // ... your game specific implementation here ...
}
```

### EVENT_MISSION_COMPLETED

This event is triggered when a `CCMission` has been completed.

```cpp
Director::getInstance()->getEventDispatcher()->addCustomEventListener(CCLevelUpConsts::EVENT_MISSION_COMPLETED, CC_CALLBACK_1(Example::onMissionCompleted, this));

void Example::onMissionCompleted(EventCustom *event) {
	// DICT_ELEMENT_MISSION - the mission that was completed

	__Dictionary *eventData = (__Dictionary *)event->getUserData();
  CCMission *mission = dynamic_cast<CCMission *>(eventData->objectForKey(CCLevelUpConsts::DICT_ELEMENT_MISSION));

  // ... your game specific implementation here ...
}
```

### EVENT_MISSION_COMPLETION_REVOKED

This event is triggered when a `CCMission` has been revoked.

```cpp
Director::getInstance()->getEventDispatcher()->addCustomEventListener(CCLevelUpConsts::EVENT_MISSION_COMPLETION_REVOKED, CC_CALLBACK_1(Example::onMissionCompletionRevoked, this));

void Example::onMissionCompletionRevoked(EventCustom *event) {
	// DICT_ELEMENT_MISSION - the mission that was completed

	__Dictionary *eventData = (__Dictionary *)event->getUserData();
  CCMission *mission = dynamic_cast<CCMission *>(eventData->objectForKey(CCLevelUpConsts::DICT_ELEMENT_MISSION));

  // ... your game specific implementation here ...
}
```

### EVENT_WORLD_LAST_COMPLETED_INNER_WORLD_CHANGED

This event is triggered when the last completed world inside a world has changed.

```cpp
Director::getInstance()->getEventDispatcher()->addCustomEventListener(CCLevelUpConsts::EVENT_WORLD_LAST_COMPLETED_INNER_WORLD_CHANGED, CC_CALLBACK_1(Example::onLastCompletedInnerWorldChanged, this));

void Example::onLastCompletedInnerWorldChanged(EventCustom *event) {
	// DICT_ELEMENT_WORLD - the world which had last completed world changed
	// DICT_ELEMENT_INNER_WORLD - The inner world ID which was last completed.

	__Dictionary *eventData = (__Dictionary *)event->getUserData();
  CCWorld *world = dynamic_cast<CCWorld *>(eventData->objectForKey(CCLevelUpConsts::DICT_ELEMENT_WORLD));
  __String *innerWorldId = dynamic_cast<__String *>(eventData->objectForKey(CCLevelUpConsts::DICT_ELEMENT_INNER_WORLD));

  // ... your game specific implementation here ...
}
```
