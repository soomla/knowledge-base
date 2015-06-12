---
layout: "content"
image: "Events"
title: "Events"
text: "Learn how to observe and handle game progress events triggered by cocos2dx-levelup to customize your game-specific behavior."
position: 3
theme: 'platforms'
collection: 'cocos2djs_levelup'
module: 'levelup'
lang: 'js' 
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

SOOMLA uses the common way of dispatching events used in Cocos2d-x.
The names of such events are defined in `Soomla.LevelUpConsts`, the data of the event are passes as arguments to the 
handlers. You can subscribe to any event from anywhere in your code.

** Subscribing **

Subscribe to events calling `Soomla.addHandler(eventName, handler, target)`, 
where `handler` - is a function that will be called when event is fired, and `target` - is "thisArg" used in that call. 

```js
Soomla.addHandler(Soomla.LevelUpConsts.EVENT_WORLD_COMPLETED, this.onWorldCompleted, this);
```

** Handling **

Handle the event through your own custom function:

```js
onWorldCompleted: function (world) {
  // your code is here
}
```

## LevelUp Events

### EVENT_LEVEL_UP_INITIALIZED

This event is triggered when

```js
Soomla.addHandler(Soomla.LevelUpConsts.EVENT_LEVEL_UP_INITIALIZED, 
        this.onLevelUpInitialized, this);

this.onLevelUpInitialized = function () {
  // ... your game specific implementation here ...
}
```

### EVENT_WORLD_COMPLETED

This event is triggered when a `World` has been completed.

```js
Soomla.addHandler(Soomla.LevelUpConsts.EVENT_WORLD_COMPLETED, this.onWorldCompleted, this);

this.onWorldCompleted = function (world) {
  // ... your game specific implementation here ...
}
```

### EVENT_WORLD_REWARD_ASSIGNED

This event is triggered when a `Reward` is assigned to a `World`.

```js
Soomla.addHandler(Soomla.LevelUpConsts.EVENT_WORLD_REWARD_ASSIGNED, 
        this.onWorldRewardAssigned, this);

this.onWorldRewardAssigned = function (world) {
  // ... your game specific implementation here ...
}
```

### EVENT_LEVEL_STARTED

This event is triggered when a `Level` has started.

```js
Soomla.addHandler(Soomla.LevelUpConsts.EVENT_LEVEL_STARTED, this.onLevelStarted, this);

this.onLevelStarted = function (level) {
  // ... your game specific implementation here ...
}
```

### EVENT_LEVEL_ENDED

This event is triggered when a `Level` has been completed.

```js
Soomla.addHandler(Soomla.LevelUpConsts.EVENT_LEVEL_ENDED, this.onLevelEnded, this);

this.onLevelEnded = function (level) {
  // ... your game specific implementation here ...
}
```

### EVENT_SCORE_RECORD_REACHED

This event is triggered when a `Score`'s record has been reached.

```js
Soomla.addHandler(Soomla.LevelUpConsts.EVENT_SCORE_RECORD_REACHED, 
        this.onScoreRecordReached, this);

this.onScoreRecordReached = function (score) {
  // ... your game specific implementation here ...
}
```

### EVENT_SCORE_RECORD_CHANGED

This event is triggered when a `Score`'s record has changed.

```js
Soomla.addHandler(Soomla.LevelUpConsts.EVENT_SCORE_RECORD_CHANGED, 
        this.onScoreRecordChanged, this);

this.onScoreRecordChanged = function (score) {
  // ... your game specific implementation here ...
}
```

### EVENT_LATEST_SCORE_CHANGED

This event is triggered when a latest score is changed.

```js
Soomla.addHandler(Soomla.LevelUpConsts.EVENT_LATEST_SCORE_CHANGED, 
        this.onLatestScoreChanged, this);

this.onLatestScoreChanged = function (score) {
  // ... your game specific implementation here ...
}
```

### EVENT_GATE_OPENED

This event is triggered when a `Gate` has opened.

```js
Soomla.addHandler(Soomla.LevelUpConsts.EVENT_GATE_OPENED, this.onGateOpened, this);

this.onGateOpened = function (gate) {
  // ... your game specific implementation here ...
}
```

### EVENT_GATE_CLOSED

This event is triggered when a `Gate` has closed.

```js
Soomla.addHandler(Soomla.LevelUpConsts.EVENT_GATE_CLOSED, this.onGateClosed, this);

this.onGateClosed = function (gate) {
  // ... your game specific implementation here ...
}
```

### EVENT_MISSION_COMPLETED

This event is triggered when a `Mission` has been completed.

```js
Soomla.addHandler(Soomla.LevelUpConsts.EVENT_MISSION_COMPLETED, this.onMissionCompleted, this);

this.onMissionCompleted = function (mission) {
  // ... your game specific implementation here ...
}
```

### EVENT_MISSION_COMPLETION_REVOKED

This event is triggered when a `Mission` has been revoked.

```js
Soomla.addHandler(Soomla.LevelUpConsts.EVENT_MISSION_COMPLETION_REVOKED, 
        this.onMissionCompletionRevoked, this);

this.onMissionCompletionRevoked = function (mission) {
  // ... your game specific implementation here ...
}
```

### EVENT_WORLD_LAST_COMPLETED_INNER_WORLD_CHANGED

This event is triggered when the last completed world inside a world has changed.

```js
Soomla.addHandler(Soomla.LevelUpConsts.EVENT_WORLD_LAST_COMPLETED_INNER_WORLD_CHANGED, 
        this.onLastCompletedInnerWorldChanged, this);

this.onLastCompletedInnerWorldChanged = function (world, innerWorldId) {
  // ... your game specific implementation here ...
}
```
