---
layout: "content"
image: "Sync"
title: "State & Economy Sync"
text: "Get started with GROW State & Economy Sync for Unity. Here you can find initialization instructions, event handling and usage examples."
position: 9
theme: 'platforms'
collection: 'unity_grow'
module: 'grow'
platform: 'unity'
---

# State & Economy Sync

## Overview

GROW's State & Economy Sync brings you a complete solution for saving your game state in the cloud, cross device synching and remote economy (model) management.
With State & Economy Sync you can:

- Save game progress & virtual items on the server.

- Restore the state upon uninstalling and reinstalling the game.

- Cross device synching - allow the player to have the same game state across all of his/her devices.

- Remote Economy (model) Management - manage your economy without changing any code in your game.

## Integration

1. Initialize `GrowSpend` according to the [instructions](/unity/GrowSpend_GettingStarted).

* Create event handler functions in order to be notified about (and handle) GROW Sync related events. See [Events](/unity/Grow_Sync/#Events) for more information.

* Once initialized, Sync will start syncing the player's state and game model, depending on the integrated modules. Synchronization is done with a unique identifier for every device.

* If [Profile](/soomla/unity/profile/Profile_GettingStarted) is integrated, once the player logs into a social network the game state will be synched across all of the player's devices, on which he/she is logged in with the same profile for that social provider.

<div class="warning-box">Make sure to start gameplay or change local state only **after** [`OnStateSyncFinished`](/unity/Grow_Sync#OnStateSyncFinished) event is triggered. </div>

## Events

Following is a list of all the events in GROW Sync and an example of how to observe & handle them.

### OnGrowSyncInitialized

This event is triggered when the GROW Sync feature is initialized and ready.

``` cs
ServicesEvents.OnGrowSyncInitialized += onGrowSyncInitialized;

public void onGrowSyncInitialized(GrowSyncInitializedEvent event) {
    // ... your game specific implementation here ...
}
```

### OnModelSyncStarted

This event is triggered when model sync has started.

``` cs
ServicesEvents.OnModelSyncStarted += onModelSyncStarted;

public void onModelSyncStarted(ModelSyncStartedEvent event) {
    // ... your game specific implementation here ...
}
```

### OnModelSyncFinished

This event is triggered when model sync has finished.
Provides a list of modules which were synced.

``` cs
ServicesEvents.OnModelSyncFinished += onModelSyncFinished;

public void onModelSyncFinished(ModelSyncFinishedEvent event) {
    IList<string> components = event.ChangedComponents;
    // ... your game specific implementation here ...
}
```

### OnModelSyncFailed

This event is triggered when model sync has failed.
Provides the error code and reason of the failure.

``` cs
ServicesEvents.OnModelSyncFailed += onModelSyncFailed;

public void onModelSyncFailed(ModelSyncFailedEvent event) {
    ModelSyncErrorCode errorCode = event.ErrorCode;
    string reason = event.ErrorMessage;
    // ... your game specific implementation here ...
}
```

### OnStateSyncStarted

This event is triggered when state sync has started.

``` cs
ServicesEvents.OnStateSyncStarted += onStateSyncStarted;

public void onStateSyncStarted(StateSyncStartedEvent event) {
// ... your game specific implementation here ...
}
```

### OnStateSyncFinished

This event is triggered when state sync has finished.
Provides a list of modules which had their state updated, and a list of modules which failed to update.

``` cs
ServicesEvents.OnStateSyncFinished += onStateSyncFinished;

public void onStateSyncFinished(StateSyncFinishedEvent event) {
    IList<string> changedComponents = event.ChangedComponents;
    IList<string> failedComponents = event.FailedComponents;
    // ... your game specific implementation here ...
}
```

<div class="info-box">`OnStateSyncFinished` event may be triggered not only when the game starts, but on other occasions as well. A sync process will start also after user login to a social network, or if the game goes back to foreground after being in the background for a while.</div>

### OnStateSyncFailed

This event is triggered when state sync has failed.
Provides the error code and reason of failure.

``` cs
ServicesEvents.OnStateSyncFailed += onStateSyncFailed;

public void onStateSyncFailed(StateSyncFailedEvent event) {
    StateSyncErrorCode errorCode = event.ErrorCode;
    string reason = event.ErrorMessage;
    // ... your game specific implementation here ...
}
```

### OnStateResetStarted

This event is triggered when state reset has started.

``` cs
ServicesEvents.OnStateResetStarted += onStateResetStarted;

public void onStateResetStarted(StateResetStartedEvent event) {
    // ... your game specific implementation here ...
}
```

### OnStateResetFinished

This event is triggered when state reset has finished.

``` cs
ServicesEvents.OnStateResetFinished += onStateResetFinished;

public void onStateResetFinished(StateResetFinishedEvent event) {
    // ... your game specific implementation here ...
}
```

### OnStateResetFailed

This event is triggered when state reset has failed.
Provides the error code and reason of failure.

``` cs
ServicesEvents.OnStateResetFailed += onStateResetFailed;

public void onStateResetFailed(StateResetFailedEvent event) {
    StateSyncErrorCode errorCode = event.ErrorCode;
    string reason = event.ErrorMessage;
    // ... your game specific implementation here ...
}
```

## Main Classes & Methods

Here you can find descriptions of the main classes of GROW Sync.

### GrowSync

`GrowSync` sepresents a class which is in charge of syncing model and state between the client and the server.

#### Functions

**`Initialize(modelSync, stateSync)`**

Initializes the GROW Sync feature. Once initialized, the `OnGrowSyncInitialized` event is triggered.
Params:

- modelSync - should GROW Sync synchronize the model for integrated modules.

- stateSync - should GROW Sync synchronize state for integrated modules.

**`ResetState()`**

Resets the user's state both on his device and in GROW.

## Example

``` cs
using Soomla;
using Soomla.Store;
using Grow.Highway;
using Grow.Sync;
...


public class ExampleWindow : MonoBehaviour {

	//
	// Various event handling methods
	//
	public void onGrowSyncInitialized(GrowSyncInitializedEvent event) {
	    Debug.Log("GROW Sync has been initialized.");
	}
	public void onModelSyncFinished(ModelSyncFinishedEvent event) {
	    Debug.Log("Model Sync has finished.");
	}
	public void onStateSyncFinished(StateSyncFinishedEvent event) {
	    Debug.Log("State Sync has finished.");
	}

	//
	// Initialize all of SOOMLA's modules
	//
	void Start () {
		...

		// Setup all event handlers - Make sure to set the event handlers before you initialize
		ServicesEvents.OnGrowSyncInitialized += onGrowSyncInitialized;
		ServicesEvents.OnModelSyncFinished += onModelSyncFinished;
		ServicesEvents.OnStateSyncFinished += onStateSyncFinished;

		// Make sure to make this call in your earliest loading scene,
		// and before initializing any other SOOMLA/GROW components
		// i.e. before SoomlaStore.Initialize(...)
		GrowHighway.Initialize();

		// Make sure to make this call AFTER initializing HIGHWAY,
		// and BEFORE initializing STORE/PROFILE
		bool modelSync = true; // Remote Economy Management - Synchronizes your game's
                               // economy model between the client and server - enables
                                // you to remotely manage your economy.

		bool stateSync = true; // Synchronizes the users' balances data with the server
                                // and across his other devices.

		// State sync and Model sync can be enabled/disabled separately.
		GrowSync.Initialize(modelSync, stateSync);

		SoomlaStore.Initialize(new ExampleAssets());
	}
}
```
