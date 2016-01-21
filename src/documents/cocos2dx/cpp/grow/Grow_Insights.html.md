---
layout: "content"
image: "Insights"
title: "Insights"
text: "Get started with GROW Insights for Unity. Here you can find initialization instructions, event handling and usage examples."
position: 5
theme: 'platforms'
collection: 'cocos2dx_grow'
module: 'grow'
lang: 'cpp'
platform: 'cocos2dx'
---

# Insights

## Overview

GROW Insights brings you priceless insights about your users at real-time and inside your code. You can use the provided insights to take actions on your users at real time during gameplay when your users actually arrive in your game. With Insights you can:  


- Create special prices for paying users in your genre.

- Adapt the game difficulty for the specific user.

- Create push campaigns.

- Adapt Ads to your specific users.

- A lot more ... you can create your own personalization features and even share them with others on the SOOMLA [Forums](http://answers.soom.la).

Currently, Insights supports PayInsights which categorizes users according to their pay-rank. More about this below.

## Integration

<div class="info-box">GROW Insights is included in the GROW bundles: [GrowUltimate](/cocos2dx/cpp/grow/GrowUltimate_GettingStarted), [GrowSpend](/cocos2dx/cpp/grow/GrowSpend_GettingStarted#SetupGrowSpend) and [GrowSoomla](/cocos2dx/cpp/grow/GrowSoomla_GettingStarted#SetupGrowSoomla). Please refer to the relevant bundle for initialization instructions.</div>


1. Initialize `CCGrowInsights` according to the instructions of your relevant bundle.

* Create event handler functions in order to be notified about (and handle) GROW Insights related events. See [Events](/cocos2dx/cpp/grow/Grow_Insights/#Events) for more information.

3. Once initialized, GROW Insights will automatically retrieve relevant insights from the server. Once the insights are ready (see [`EVENT_INSIGHTS_REFRESH_FINISHED`](/cocos2dx/cpp/grow/Grow_Insights/#EVENT_INSIGHTS_REFRESH_FINISHED)) you can access them as explained below.

## Observing & Handling Events

GROW Insights uses the Cocos2d-x facilities to dispatch its own custom events.
The names of such events are defined in `CCHighwayConsts`.

### Cocos2d-x v3 Events

** Subscribing **

Subscribe to events through the Cocos2d-x  [`EventDispatcher`](http://www.cocos2d-x.org/wiki/EventDispatcher_Mechanism):

```cpp
cocos2d::Director::getInstance()->getEventDispatcher()->addCustomEventListener(CCHighwayConsts::EVENT_INSIGHTS_REFRESH_FINISHED, CC_CALLBACK_1(ExampleScene::onInsightsRefreshFinished, this));
```

** Handling **

Handle the event through your own custom function:

```cpp
void ExampleScene::onInsightsRefreshFinished(cocos2d::EventCustom *event) {
    // No meta-data information to get from eventData for GROW Insights events
}
```


### Cocos2d-x v2 Events

** Subscribing **

Subscribe to events through the Cocos2d-x `CCNotificationCenter`:

```cpp
cocos2d::CCNotificationCenter::sharedNotificationCenter()->addObserver(this, callfuncO_selector(ExampleScene::onInsightsRefreshFinished), CCHighwayConsts::EVENT_INSIGHTS_REFRESH_FINISHED, NULL);
```

** Handling **

Handle the event through your own custom function:

```cpp
void ExampleScene::onInsightsRefreshFinished(cocos2d::CCDictionary *eventData) {
    // No meta-data information to get from eventData for GROW Insights events
}
```

## Events

Following is a list of all the events in GROW Insights and an example of how to observe & handle them. The events' handling examples are written for v3, but it's easy to convert them to v2 dialect, see how above.

### EVENT_GROW_INSIGHTS_INITIALIZED

This event is triggered when the GROW Insights feature is initialized and ready.

``` cpp
Director::getInstance()->getEventDispatcher()->addCustomEventListener(CCHighwayConsts::EVENT_GROW_INSIGHTS_INITIALIZED, CC_CALLBACK_1(Example::onGrowInsightsInitialized, this));

void Example::onGrowInsightsInitialized(EventCustom *event) {
// ... your game specific implementation here ...
}
```

### EVENT_INSIGHTS_REFRESH_STARTED

This event is triggered when fetching insights from the server has started.

``` cpp
Director::getInstance()->getEventDispatcher()->addCustomEventListener(CCHighwayConsts::EVENT_INSIGHTS_REFRESH_STARTED, CC_CALLBACK_1(Example::onInsightsRefreshStarted, this));

void Example::onInsightsRefreshStarted(EventCustom *event) {
// ... your game specific implementation here ...
}
```

### EVENT_INSIGHTS_REFRESH_FINISHED

This event is triggered when fetching insights from the server has finished.

``` cpp
Director::getInstance()->getEventDispatcher()->addCustomEventListener(CCHighwayConsts::EVENT_INSIGHTS_REFRESH_FINISHED, CC_CALLBACK_1(Example::onInsightsRefreshFinished, this));

void Example::onInsightsRefreshFinished(EventCustom *event) {
// ... your game specific implementation here ...
}
```

### EVENT_INSIGHTS_REFRESH_FAILED

This event is triggered when fetching insights from the server has failed.

``` cpp
Director::getInstance()->getEventDispatcher()->addCustomEventListener(CCHighwayConsts::EVENT_INSIGHTS_REFRESH_FAILED, CC_CALLBACK_1(Example::onInsightsRefreshFailed, this));

void Example::onInsightsRefreshFailed(EventCustom *event) {
// ... your game specific implementation here ...
}
```

## Main Classes & Methods

Here you can find descriptions of the main classes of GROW Insights. These classes contain functionality for insights-related operations such as refreshing insights, retrieving and using them.

### CCGrowInsights

`CCGrowInsights` is the main class of GROW Insights which is in charge of fetching insights.

#### Functions

**`CCGrowInsights::initShared()`**

Initializes the GROW Insights feature. Once initialized, the `EVENT_GROW_INSIGHTS_INITIALIZED` event is triggered.

**`CCGrowInsights::refreshInsights()`**

Manually refresh the insights. The `EVENT_INSIGHTS_REFRESH_STARTED` event is triggered once the refresh process is started, and one of `EVENT_INSIGHTS_REFRESH_FINISHED` or `EVENT_INSIGHTS_REFRESH_FAILED` is triggered depending on the refresh outcome.

**`CCGrowInsights::getUserInsights()`**

Returns the [User-Insights](/cocos2dx/cpp/grow/Grow_Insights/#CCUserInsights) received from the server.

<div class="info-box">GROW Insights caches its data on the device so that it's accessible even when there is no internet connection.</div>

### CCUserInsights

`CCUserInsights` holds insights related to the user currently playing the game.
Located in `CCGrowInsights` and can be accessed using `CCGrowInsights::getUserInsights()`.

#### Functions

**`CCUserInsights::getPayInsights()`**

Returns the [Pay-Insights](/cocos2dx/cpp/grow/Grow_Insights/#CCPayInsights) received from the server.

### CCPayInsights

`CCPayInsights` holds insights related to the user's payments.
Located in `CCUserInsights` and can be accessed using `CCUserInsights::getPayInsights()`.

#### Functions

**`CCPayInsights::getPayRankForGenre(grow::Genre genre)`**

Returns the user's pay-rank for the given [genre](/cocos2dx/cpp/grow/Grow_Insights/#Genre)

##### Possible return values

- -1: No insights for selected genre

- 0: The user has paid $0 in total

- 1: The user has paid up to $1

- 2: The user has paid up to $5

- 3: The user has paid up to $10

- 4: The user has paid up to $50

- 5: The user has paid up to $100

- 6: The user has paid more than $100

<div class="info-box">NOTE: Pay rank is calculated according to the user's total revenue from ALL games using GROW.</div>
<br/>

**`CCPayInsights::getTimeOfPurchase(grow::DayQuarter quarter)`**

Returns a number between 0 to 1 per [DayQuarter](/unity/grow/Grow_Insights#DayQuarter) which indicates the user's likelihood to pay in that specific quarter.

### Genre

`Genre` represents a game genre.

#### Usage

For example, in order to access a user's pay rank by the `Action` genre use `CCGrowInsights::getInstance()->getUserInsights()->getPayInsights()->getPayRankForGenre(Genre::Action)`

### DayQuarter

`DayQuarter` represents a quarter of the day, we split the day as following:

- 12am to 6am

- 6am to 12pm

- 12pm to 6pm

- 6pm to 12am

<br/>

#### Usage

For example, in order to check the likelihood of the user to pay in the third quarter of the day `_12pm_6pm` use `CCGrowInsights::getInstance()->getUserInsights()->getPayInsights()->getTimeOfPurchase(DayQuarter::_12pm_6pm)`

## Example

``` cpp
using namespace grow;

    // Add event listeners - Make sure to set the event handlers before you initialize
Director::getInstance()->getEventDispatcher()->addCustomEventListener(
            CCHighwayConsts::EVENT_GROW_INSIGHTS_INITIALIZED, CC_CALLBACK_1(Example::onGrowInsightsInitialized, this));
Director::getInstance()->getEventDispatcher()->addCustomEventListener(
            CCHighwayConsts::EVENT_INSIGHTS_REFRESH_FINISHED, CC_CALLBACK_1(Example::onInsightsRefreshFinished, this));

soomla::CCSoomla::initialize("ExampleCustomSecret");

// Initialize GrowHighway
CCGrowHighway::initShared(
     __String::create("yourGameKey"),
     __String::create("yourEnvKey"));

// Initialize GrowInsights
CCGrowInsights::initShared();


void ExampleScene::onGrowInsightsInitialized(cocos2d::CCDictionary *eventData) {
    cocos2d::log("GROW insights has been initialized.");
}

void ExampleScene::onInsightsRefreshFinished(cocos2d::CCDictionary *eventData) {
    if (CCGrowInsights::getInstance()->getUserInsights()->
                getPayInsights()->getPayRankForGenre(Genre::Educational) > 3) {
        // ... Do stuff according to your business plan ...
    }
}


```
