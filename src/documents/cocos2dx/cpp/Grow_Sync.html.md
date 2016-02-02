---
layout: "content"
image: "Sync"
title: "State & Economy Sync"
text: "Get started with GROW State & Economy Sync for Unity. Here you can find initialization instructions, event handling and usage examples."
position: 3
theme: 'platforms'
collection: 'cocos2dx_grow'
module: 'grow'
lang: 'cpp'
platform: 'cocos2dx'
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

<div class="info-box">GROW's State & Economy Sync is included in [GrowSpend](/cocos2dx/cpp/grow/GrowSpend_GettingStarted#SetupGrowSpend).</div>


1. Initialize `CCGrowSync` according to the instructions of your relevant bundle.

* Create event handler functions in order to be notified about (and handle) GROW Sync related events. See [Events](/cocos2dx/cpp/grow/Grow_Sync/#Events) for more information.

* Once initialized, Sync will start syncing the player's state and game model, depending on the integrated modules. Synchronization is done with a unique identifier for every device.

* If [Profile](/cocos2dx/cpp/profile/Profile_GettingStarted) is integrated, once the player logs into a social network the game state will be synched across all of the player's devices, on which he/she is logged in with the same profile for that social provider.

<div class="warning-box">Make sure to start gameplay or change local state only **after** [`EVENT_STATE_SYNC_FINISHED`](/cocos2dx/cpp/grow/Grow_Sync#OnStateSyncFinished) event is triggered. </div>

## Observing & Handling Events

GROW Insights uses the Cocos2d-x facilities to dispatch its own custom events.
The names of such events are defined in `CCHighwayConsts`.

### Cocos2d-x v3 Events

** Subscribing **

Subscribe to events through the Cocos2d-x  [`EventDispatcher`](http://www.cocos2d-x.org/wiki/EventDispatcher_Mechanism):

```cpp
cocos2d::Director::getInstance()->getEventDispatcher()->addCustomEventListener(CCHighwayConsts::EVENT_STATE_SYNC_FINISHED, CC_CALLBACK_1(ExampleScene::onStateSyncFinished, this));
```

** Handling **

Handle the event through your own custom function:

```cpp
void ExampleScene::onStateSyncFinished(cocos2d::EventCustom *event) {
    __Dictionary *eventData = (__Dictionary *)event->getUserData();
    __Array *changedComponents = dynamic_cast<__Array *>(eventData->objectForKey(CCHighwayConsts::DICT_ELEMENT_STATE_CHANGED_COMPONENTS));
    __Array *failedComponents = dynamic_cast<__Array *>(eventData->objectForKey(CCHighwayConsts::DICT_ELEMENT_STATE_FAILED_COMPONENTS));
	// ... your game specific implementation here ...
}
```


### Cocos2d-x v2 Events

** Subscribing **

Subscribe to events through the Cocos2d-x `CCNotificationCenter`:

```cpp
cocos2d::CCNotificationCenter::sharedNotificationCenter()->addObserver(this, callfuncO_selector(ExampleScene::onStateSyncFinished), CCHighwayConsts::EVENT_STATE_SYNC_FINISHED, NULL);
```

** Handling **

Handle the event through your own custom function:

```cpp
void ExampleScene::onStateSyncFinished(cocos2d::CCDictionary *eventData) {
	__Array *changedComponents = dynamic_cast<__Array *>(eventData->objectForKey(CCHighwayConsts::DICT_ELEMENT_STATE_CHANGED_COMPONENTS));
	__Array *failedComponents = dynamic_cast<__Array *>(eventData->objectForKey(CCHighwayConsts::DICT_ELEMENT_STATE_FAILED_COMPONENTS));
	// ... your game specific implementation here ...
}
```

## Events

Following is a list of all the events in GROW Sync and an example of how to observe & handle them.

### EVENT_GROW_SYNC_INITIALIZED

This event is triggered when the GROW Sync feature is initialized and ready.

``` cpp
Director::getInstance()->getEventDispatcher()->addCustomEventListener(CCHighwayConsts::EVENT_GROW_SYNC_INITIALIZED, CC_CALLBACK_1(Example::onGrowSyncInitialized, this));

void Example::onGrowSyncInitialized(EventCustom *event) {
// ... your game specific implementation here ...
}
```

### EVENT_MODEL_SYNC_STARTED

This event is triggered when model sync has started.

``` cpp
Director::getInstance()->getEventDispatcher()->addCustomEventListener(CCHighwayConsts::EVENT_MODEL_SYNC_STARTED, CC_CALLBACK_1(Example::onModelSyncStarted, this));

void Example::onModelSyncStarted(EventCustom *event) {
// ... your game specific implementation here ...
}
```

### EVENT_MODEL_SYNC_FINISHED

This event is triggered when model sync has finished.
Provides a list of modules which were synced.

``` cpp
Director::getInstance()->getEventDispatcher()->addCustomEventListener(CCHighwayConsts::EVENT_MODEL_SYNC_FINISHED, CC_CALLBACK_1(Example::onModelSyncFinished, this));

void Example::onModelSyncFinished(EventCustom *event) {
    __Dictionary *eventData = (__Dictionary *)event->getUserData();
    __Array *changedComponents = dynamic_cast<__Array *>(eventData->objectForKey(CCHighwayConsts::DICT_ELEMENT_MODEL_CHANGED_COMPONENTS));
	// ... your game specific implementation here ...
}
```

### EVENT_MODEL_SYNC_FAILED

This event is triggered when model sync has failed.
Provides the error code and reason of the failure.

``` cpp
Director::getInstance()->getEventDispatcher()->addCustomEventListener(CCHighwayConsts::EVENT_MODEL_SYNC_FAILED, CC_CALLBACK_1(Example::onModelSyncFailed, this));

void Example::onModelSyncFailed(EventCustom *event) {
    __Dictionary *eventData = (__Dictionary *)event->getUserData();
    CCModelSyncError errorCode = CCModelSyncError(dynamic_cast<__Integer *>(eventData->objectForKey(CCHighwayConsts::DICT_ELEMENT_ERROR_CODE))->getValue());
    __String *errorMessage = dynamic_cast<__String *>(eventData->objectForKey(CCHighwayConsts::DICT_ELEMENT_ERROR_MESSAGE));
	// ... your game specific implementation here ...
}
```

### EVENT_STATE_SYNC_STARTED

This event is triggered when state sync has started.

``` cpp
Director::getInstance()->getEventDispatcher()->addCustomEventListener(CCHighwayConsts::EVENT_STATE_SYNC_STARTED, CC_CALLBACK_1(Example::onStateSyncStarted, this));

void Example::onStateSyncStarted(EventCustom *event) {
// ... your game specific implementation here ...
}
```

### EVENT_STATE_SYNC_FINISHED

This event is triggered when state sync has finished.
Provides a list of modules which had their state updated, and a list of modules which failed to update.

``` cpp
Director::getInstance()->getEventDispatcher()->addCustomEventListener(CCHighwayConsts::EVENT_STATE_SYNC_FINISHED, CC_CALLBACK_1(Example::onStateSyncFinished, this));

void Example::onStateSyncFinished(EventCustom *event) {
    __Dictionary *eventData = (__Dictionary *)event->getUserData();
    __Array *changedComponents = dynamic_cast<__Array *>(eventData->objectForKey(CCHighwayConsts::DICT_ELEMENT_STATE_CHANGED_COMPONENTS));
    __Array *failedComponents = dynamic_cast<__Array *>(eventData->objectForKey(CCHighwayConsts::DICT_ELEMENT_STATE_FAILED_COMPONENTS));
	// ... your game specific implementation here ...
}
```

<div class="info-box">`EVENT_STATE_SYNC_FINISHED` event may be triggered not only when the game starts, but on other occasions as well. A sync process will start also after user login to a social network, or if the game goes back to foreground after being in the background for a while.</div>

### EVENT_STATE_SYNC_FAILED

This event is triggered when state sync has failed.
Provides the error code and reason of failure.

``` cpp
Director::getInstance()->getEventDispatcher()->addCustomEventListener(CCHighwayConsts::EVENT_STATE_SYNC_FAILED, CC_CALLBACK_1(Example::onStateSyncFailed, this));

void Example::onStateSyncFailed(EventCustom *event) {
    __Dictionary *eventData = (__Dictionary *)event->getUserData();
    CCStateSyncError errorCode = CCStateSyncError(dynamic_cast<__Integer *>(eventData->objectForKey(CCHighwayConsts::DICT_ELEMENT_ERROR_CODE))->getValue());
    __String *errorMessage = dynamic_cast<__String *>(eventData->objectForKey(CCHighwayConsts::DICT_ELEMENT_ERROR_MESSAGE));
	// ... your game specific implementation here ...
}
```

### EVENT_STATE_RESET_STARTED

This event is triggered when state reset has started.

``` cpp
Director::getInstance()->getEventDispatcher()->addCustomEventListener(CCHighwayConsts::EVENT_STATE_RESET_STARTED, CC_CALLBACK_1(Example::onStateResetStarted, this));

void Example::onStateResetStarted(EventCustom *event) {
// ... your game specific implementation here ...
}
```

### EVENT_STATE_RESET_FINISHED

This event is triggered when state reset has finished.

``` cpp
Director::getInstance()->getEventDispatcher()->addCustomEventListener(CCHighwayConsts::EVENT_STATE_RESET_FINISHED, CC_CALLBACK_1(Example::onStateResetFinished, this));

void Example::onStateResetFinished(EventCustom *event) {
// ... your game specific implementation here ...
}
```

### EVENT_STATE_RESET_FAILED

This event is triggered when state reset has failed.
Provides the error code and reason of failure.

``` cpp
Director::getInstance()->getEventDispatcher()->addCustomEventListener(CCHighwayConsts::EVENT_STATE_RESET_FAILED, CC_CALLBACK_1(Example::onStateResetFailed, this));

void Example::onStateResetFailed(EventCustom *event) {
    __Dictionary *eventData = (__Dictionary *)event->getUserData();
    CCStateSyncError errorCode = CCStateSyncError(dynamic_cast<__Integer *>(eventData->objectForKey(CCHighwayConsts::DICT_ELEMENT_ERROR_CODE))->getValue());
    __String *errorMessage = dynamic_cast<__String *>(eventData->objectForKey(CCHighwayConsts::DICT_ELEMENT_ERROR_MESSAGE));
	// ... your game specific implementation here ...
}
```

## Main Classes & Methods

Here you can find descriptions of the main classes of GROW Sync.

### CCGrowSync

`CCGrowSync` represents a class which is in charge of syncing model and state between the client and the server.

#### Functions

**`CCGrowSync::initShared(modelSync, stateSync)`**

Initializes the GROW Sync feature. Once initialized, the `EVENT_GROW_SYNC_INITIALIZED` event is triggered.
Params:

- modelSync - should GROW Sync synchronize the model for integrated modules.

- stateSync - should GROW Sync synchronize state for integrated modules.

**`CCGrowSync::resetState()`**

Resets the user's state both on his device and in GROW.

## Example

```cpp
using namespace grow;

bool AppDelegate::applicationDidFinishLaunching() {

	Director::getInstance()->getEventDispatcher()->addCustomEventListener(CCHighwayConsts::EVENT_GROW_SYNC_INITIALIZED, CC_CALLBACK_1(AppDelegate::onGrowSyncInitialized, this));
	Director::getInstance()->getEventDispatcher()->addCustomEventListener(CCHighwayConsts::EVENT_MODEL_SYNC_FINISHED, CC_CALLBACK_1(AppDelegate::onModelSyncFinished, this));
	Director::getInstance()->getEventDispatcher()->addCustomEventListener(CCHighwayConsts::EVENT_STATE_SYNC_FINISHED, CC_CALLBACK_1(AppDelegate::onStateSyncFinished, this));

	soomla::CCSoomla::initialize("ExampleCustomSecret");

	// Make sure to make this call in your AppDelegate's
	// applicationDidFinishLaunching method, and before
	// initializing any other SOOMLA/GROW components
	// i.e. before CCSoomlaStore::initialize(...)
	grow::CCGrowHighway::initShared(__String::create("yourGameKey"),
									__String::create("yourEnvKey"));

	// Make sure to make this call AFTER initializing HIGHWAY,
	// and BEFORE initializing STORE/PROFILE
	bool modelSync = true; 	// Remote Economy Management - Synchronizes your game's
							 // economy model between the client and server - enables
							 // you to remotely manage your economy.

	bool stateSync = true; // Synchronizes the users' balances data with the server
						   // and across his other devices.
						   // Must be TRUE in order to use LEADERBOARDS.

	// State sync and Model sync can be enabled/disabled separately.
	grow::CCGrowSync::initShared(modelSync, stateSync);

	/** Set up and initialize Store and Profile**/
	ExampleAssets *assets = ExampleAssets::create();

	__Dictionary *storeParams = __Dictionary::create();
	storeParams->setObject(__String::create("ExamplePublicKey"), "androidPublicKey");

	soomla::CCSoomlaStore::initialize(assets, storeParams);
}

void AppDelegate::onGrowSyncInitialized(EventCustom *event) {
	cocos2d::log("GROW Sync has been initialized.");
}

void AppDelegate::onModelSyncFinished(EventCustom *event) {
    __Dictionary *eventData = (__Dictionary *)event->getUserData();
    __Array *changedComponents = dynamic_cast<__Array *>(eventData->objectForKey(CCHighwayConsts::DICT_ELEMENT_MODEL_CHANGED_COMPONENTS));
	cocos2d::log("Model Sync has finished.");
}

void AppDelegate::onStateSyncFinished(EventCustom *event) {
    __Dictionary *eventData = (__Dictionary *)event->getUserData();
    __Array *changedComponents = dynamic_cast<__Array *>(eventData->objectForKey(CCHighwayConsts::DICT_ELEMENT_STATE_CHANGED_COMPONENTS));
    __Array *failedComponents = dynamic_cast<__Array *>(eventData->objectForKey(CCHighwayConsts::DICT_ELEMENT_STATE_FAILED_COMPONENTS));
	cocos2d::log("State Sync has finished.");
}
```
