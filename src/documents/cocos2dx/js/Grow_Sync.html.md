---
layout: "content"
image: "Sync"
title: "State & Economy Sync"
text: "Get started with GROW State & Economy Sync for Unity. Here you can find initialization instructions, event handling and usage examples."
position: 3
theme: 'platforms'
collection: 'cocos2djs_grow'
module: 'grow'
lang: 'js'
platform: 'cocos2dx'
---

# State & Economy Sync

## Overview

GROW's State & Economy Sync brings you a complete solution for saving your game state in the cloud, cross device synching
and remote economy (model) management.
With State & Economy Sync you can:

- Save game progress & virtual items on the server.

- Restore the state upon uninstalling and reinstalling the game.

- Cross device synching - allow the player to have the same game state across all of his/her devices.

- Remote Economy (model) Management - manage your economy without changing any code in your game.

## Integration

<div class="info-box">GROW's State & Economy Sync is included in [GrowSpend](/cocos2dx/js/grow/GrowSpend_GettingStarted#SetupGrowSpend).</div>


1. Initialize `GrowSync` according to the instructions of your relevant bundle.

* Create event handler functions in order to be notified about (and handle) GROW Sync related events. See
[Events](/cocos2dx/js/grow/Grow_Sync/#Events) for more information.

* Once initialized, Sync will start syncing the player's state and game model, depending on the integrated modules.
Synchronization is done with a unique identifier for every device.

* If [Profile](/cocos2dx/js/profile/Profile_GettingStarted) is integrated, once the player logs into a social network
the game state will be synched across all of the player's devices, on which he/she is logged in with the same profile
for that social provider.

<div class="warning-box">Make sure to start gameplay or change local state only **after**
[`EVENT_STATE_SYNC_FINISHED`](/cocos2dx/js/grow/Grow_Sync#OnStateSyncFinished) event is triggered. </div>

## Observing & Handling Events

### Subscribing

The names of events are defined in `Soomla.Models.HighwayConsts`.
Subscribe to events in the following way:

```js
Soomla.addHandler(Soomla.Models.HighwayConsts.EVENT_STATE_SYNC_FINISHED, this.onInsightsRefreshFinished, this);
```

### Handling

Handle the event through your own custom function:

```js
this.onStateSyncFinished = function (changedComponents, failedComponents) {
  // ... your game specific implementation here ...
}
```

## Events

Following is a list of all the events in GROW Sync and an example of how to observe & handle them.

### EVENT_GROW_SYNC_INITIALIZED

This event is triggered when the GROW Sync feature is initialized and ready.

``` js
Soomla.addHandler(Soomla.Models.HighwayConsts.EVENT_GROW_SYNC_INITIALIZED, this.onGrowSyncInitialized, this);

this.onGrowSyncInitialized = function () {
  // ... your game specific implementation here ...
}
```

### EVENT_MODEL_SYNC_STARTED

This event is triggered when model sync has started.

``` js
Soomla.addHandler(Soomla.Models.HighwayConsts.EVENT_MODEL_SYNC_STARTED, this.onModelSyncStarted, this);

this.onModelSyncStarted = function () {
  // ... your game specific implementation here ...
}
```

### EVENT_MODEL_SYNC_FINISHED

This event is triggered when model sync has finished.
Provides a list of modules which were synced.

``` js
Soomla.addHandler(Soomla.Models.HighwayConsts.EVENT_MODEL_SYNC_FINISHED, this.onModelSyncFinished, this);

this.onModelSyncFinished = function (changedComponents) {
  // ... your game specific implementation here ...
}
```

### EVENT_MODEL_SYNC_FAILED

This event is triggered when model sync has failed.
Provides the error code and reason of the failure.

``` js
Soomla.addHandler(Soomla.Models.HighwayConsts.EVENT_MODEL_SYNC_FAILED, this.onModelSyncFailed, this);

this.onModelSyncFailed = function (errorCode, errorMessage) {
  // ... your game specific implementation here ...
}
```

### EVENT_STATE_SYNC_STARTED

This event is triggered when state sync has started.

``` js
Soomla.addHandler(Soomla.Models.HighwayConsts.EVENT_STATE_SYNC_STARTED, this.onStateSyncStarted, this);

this.onStateSyncStarted = function () {
  // ... your game specific implementation here ...
}
```

### EVENT_STATE_SYNC_FINISHED

This event is triggered when state sync has finished.
Provides a list of modules which had their state updated, and a list of modules which failed to update.

``` js
Soomla.addHandler(Soomla.Models.HighwayConsts.EVENT_STATE_SYNC_FINISHED, this.onStateSyncFinished, this);

this.onStateSyncFinished = function (changedComponents, failedComponents) {
  // ... your game specific implementation here ...
}
```

<div class="info-box">`EVENT_STATE_SYNC_FINISHED` event may be triggered not only when the game starts, but on other occasions as well. A sync process will start also after user login to a social network, or if the game goes back to foreground after being in the background for a while.</div>

### EVENT_STATE_SYNC_FAILED

This event is triggered when state sync has failed.
Provides the error code and reason of failure.

``` js
Soomla.addHandler(Soomla.Models.HighwayConsts.EVENT_STATE_SYNC_FAILED, this.onStateSyncFailed, this);

this.onStateSyncFailed = function (errorCode, errorMessage) {
  // ... your game specific implementation here ...
}
```

### EVENT_STATE_RESET_STARTED

This event is triggered when state reset has started.

``` js
Soomla.addHandler(Soomla.Models.HighwayConsts.EVENT_STATE_RESET_STARTED, this.onStateResetStarted, this);

this.onStateResetStarted = function () {
  // ... your game specific implementation here ...
}
```

### EVENT_STATE_RESET_FINISHED

This event is triggered when state reset has finished.

``` js
Soomla.addHandler(Soomla.Models.HighwayConsts.EVENT_STATE_RESET_FINISHED, this.onStateResetFinished, this);

this.onStateResetFinished = function () {
  // ... your game specific implementation here ...
}
```

### EVENT_STATE_RESET_FAILED

This event is triggered when state reset has failed.
Provides the error code and reason of failure.

``` js
Soomla.addHandler(Soomla.Models.HighwayConsts.EVENT_STATE_RESET_FAILED, this.onStateResetFailed, this);

this.onStateResetFailed = function (errorCode, errorMessage) {
  // ... your game specific implementation here ...
}
```

## Main Classes & Methods

Here you can find descriptions of the main classes of GROW Sync.

### GrowSync

`Soomla.GrowSync` represents a class which is in charge of syncing model and state between the client and the server.

#### Functions

**`GrowSync.createShared(modelSync, stateSync)`**

Initializes the GROW Sync feature. Once initialized, the `EVENT_GROW_SYNC_INITIALIZED` event is triggered.
Params:

- modelSync - should GROW Sync synchronize the model for integrated modules.

- stateSync - should GROW Sync synchronize state for integrated modules.

**`GrowSync.resetState()`**

Resets the user's state both on his device and in GROW.

## Example

```js
Soomla.addHandler(Soomla.Models.HighwayConsts.EVENT_GROW_SYNC_INITIALIZED, this.onGrowSyncInitialized, this);
Soomla.addHandler(Soomla.Models.HighwayConsts.EVENT_MODEL_SYNC_FINISHED, this.onModelSyncFinished, this);
Soomla.addHandler(Soomla.Models.HighwayConsts.EVENT_STATE_SYNC_FINISHED, this.onStateSyncFinished, this);

Soomla.initialize('ExampleCustomSecret');

// Make sure to make this call before
// initializing any other SOOMLA/GROW components
// i.e. before SoomlaStore.initialize(...)
Soomla.GrowHighway.createShared('yourGameKey', 'yourEnvKey');

// Make sure to make this call AFTER initializing HIGHWAY,
// and BEFORE initializing STORE/PROFILE
var modelSync = true; 	 // Remote Economy Management - Synchronizes your game's
						 // economy model between the client and server - enables
						 // you to remotely manage your economy.

var stateSync = true;  // Synchronizes the users' balances data with the server
					   // and across his other devices.
					   // Must be TRUE in order to use LEADERBOARDS.

// State sync and Model sync can be enabled/disabled separately.
Soomla.GrowSync.createShared(modelSync, stateSync);

/** Set up and initialize Store and Profile **/
var assets = new ExampleAssets();
var storeParams = {
  androidPublicKey: "ExamplePublicKey",
  testPurchases: true
};

Soomla.soomlaStore.initialize(assets, storeParams);



this.onGrowSyncInitialized = function () {
    // GROW Sync has been initialized.
}

this.onModelSyncFinished = function (changedComponents) {
  // ... your game specific implementation here ...
}

this.onStateSyncFinished = function (changedComponents, failedComponents) {
  // State Sync has finished.
  // ... your game specific implementation here ...
}
```
