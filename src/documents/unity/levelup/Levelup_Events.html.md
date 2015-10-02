---
layout: "content"
image: "Events"
title: "Events"
text: "Learn how to observe and handle game progress events triggered by unity3d-levelup to customize your game-specific behavior."
position: 3
theme: 'platforms'
collection: 'unity_levelup'
module: 'levelup'
platform: 'unity'
---

#Event Handling

##About

LevelUp allows you to subscribe to events, be notified when they occur, and implement your own application-specific behavior to handle them once they occur.

<div class="info-box">Your game-specific behavior is an addition to the default behavior implemented by SOOMLA. You don't replace SOOMLA's behavior.</div>

###Tips & Reminders

- As mentioned in [Getting Started](/unity/levelup/Levelup_GettingStarted), make sure you add the event prefabs (LevelUpEvents, ProfileEvents, StoreEvents, CoreEvents) to your earliest loading scene.


##How it works

Events are triggered when SOOMLA wants to notify you about different things that happen involving LevelUp operations.

For example, when a user completes a `World`, an `OnWorldCompleted` event is fired as a result.

##Observing & Handling Events

The `LevelUpEvents` class is where all events go through. To handle various events, just add your game-specific behavior to the delegates in the `LevelUpEvents` class.

**For example:** To "listen" for an `onWorldCompleted` event:

``` cs
// Sign up for the event
LevelUpEvents.OnWorldCompleted += onWorldCompleted;

// Handle the event with your game-specific behavior:
public void onWorldCompleted(World world) {
  // Do something...
}
```

##LevelUp Events

This is a list of all events of SOOMLA LevelUp and an example of how to listen to them:

###OnLevelUpInitialized

This event is triggered when the Soomla LevelUp module is initialized and ready.

``` cs
LevelUpEvents.OnLevelUpInitialized += onLevelUpInitialized;

public void onLevelUpInitialized() {
  // ... your game specific implementation here ...
}
```  

###OnWorldCompleted

This event is triggered when a `World` has been completed.

``` cs
LevelUpEvents.OnWorldCompleted += onWorldCompleted;

public void onWorldCompleted(World world) {
  // world is the world that was completed

  // ... your game specific implementation here ...
}
```

###OnWorldAssignedReward

This event is triggered when a `Reward` is assigned to a `World`.

``` cs
LevelUpEvents.OnWorldAssignedReward += onWorldAssignedReward;

public void onWorldAssignedReward(World world) {
  // world is the world who had a reward assigned to it

  // ... your game specific implementation here ...
}
```  

###OnLevelStarted

This event is triggered when a `Level` has started.

``` cs
LevelUpEvents.OnLevelStarted += onLevelStarted;

public void onLevelStarted(Level level) {
  // level is the level that has started

  // ... your game specific implementation here ...
}
```

###OnLevelEnded

This event is triggered when a `Level` has been completed.

``` cs
LevelUpEvents.OnLevelEnded += onLevelEnded;

public void onLevelEnded(Level level) {
  // level is the level that has ended

  // ... your game specific implementation here ...
}
```

###OnScoreRecordReached

This event is triggered when a `Score`'s record has been reached.

``` cs
LevelUpEvents.OnScoreRecordReached += onScoreRecordReached;

public void onScoreRecordReached(Score score) {
  // score is the score whose record has been reached

  // ... your game specific implementation here ...
}
```

###OnScoreRecordChanged

This event is triggered when a `Score`'s record has changed.

``` cs
LevelUpEvents.OnScoreRecordChanged += onScoreRecordChanged;

public void onScoreRecordChanged(Score score) {
  // score is the score whose record has changed

  // ... your game specific implementation here ...
}
```

###OnGateOpened

This event is triggered when a `Gate` has opened.

``` cs
LevelUpEvents.OnGateOpened += onGateOpened;

public void onGateOpened(Gate gate) {
  // gate is the gate that was opened

  // ... your game specific implementation here ...
}
```

###OnMissionCompleted

This event is triggered when a `Mission` has been completed.

``` cs
LevelUpEvents.OnMissionCompleted += onMissionCompleted;

public void onMissionCompleted(Mission mission) {
  // mission is the mission that was completed

  // ... your game specific implementation here ...
}
```

###OnMissionCompletionRevoked

This event is triggered when a `Mission` has been revoked.

``` cs
LevelUpEvents.OnMissionCompletionRevoked += onMissionCompletionRevoked;

public void onMissionCompletionRevoked((Mission mission) {
  // mission is the mission that was revoked

  // ... your game specific implementation here ...
}
```
