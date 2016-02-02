---
layout: "content"
image: "Insights"
title: "Insights"
text: "Get started with GROW Insights for Unity. Here you can find initialization instructions, event handling and usage examples."
position: 8
theme: 'platforms'
collection: 'unity_grow'
module: 'grow'
platform: 'unity'
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

1. Initialize `GrowInsights` according to the instructions of your relevant integration.

* Create event handler functions in order to be notified about (and handle) GROW Insights related events. See [Events](/unity/grow/Grow_Insights/#Events) for more information.

* Once initialized, GROW Insights will automatically retrieve relevant insights from the server. Once the insights are ready (see [`OnGrowInsightsRefreshFinished`](/unity/grow/Grow_Insights/#OnInsightsRefreshFinished)) you can access them as explained below.

## Events

Following is a list of all the events in GROW Insights and an example of how to observe & handle them.

### OnGrowInsightsInitialized

This event is triggered when the GROW Insights feature is initialized and ready.

``` cs
HighwayEvents.OnGrowInsightsInitialized += onGrowInsightsInitialized;

public void onGrowInsightsInitialized() {
// ... your game specific implementation here ...
}
```

### OnInsightsRefreshStarted

This event is triggered when fetching insights from the server has started.

``` cs
HighwayEvents.OnInsightsRefreshStarted += onInsightsRefreshStarted;

public void onInsightsRefreshStarted() {
// ... your game specific implementation here ...
}
```

### OnInsightsRefreshFinished

This event is triggered when fetching insights from the server has finished.

``` cs
HighwayEvents.OnInsightsRefreshFinished += onInsightsRefreshFinished;

public void onInsightsRefreshFinished() {
// ... your game specific implementation here ...
}
```

### OnInsightsRefreshFailed

This event is triggered when fetching insights from the server has failed.

``` cs
HighwayEvents.OnInsightsRefreshFailed += onInsightsRefreshFailed;

public void onInsightsRefreshFailed() {
// ... your game specific implementation here ...
}
```

## Main Classes & Methods

Here you can find descriptions of the main classes of GROW Insights. These classes contain functionality for insights-related operations such as refreshing insights, retrieving and using them.

### GrowInsights

`GrowInsights` is the main class of GROW Insights which is in charge of fetching insights.

#### Functions

**`Initialize()`**

Initializes the GROW Insights feature. Once initialized, the `OnInsightsInitialized` event is triggered.

**`RefreshInsights()`**

Manually refresh the insights. The `OnInsightsRefreshStarted` event is triggered once the refresh process is started, and one of `OnInsightsRefreshFinished` or `OnInsightsRefreshFailed` is triggered depending on the refresh outcome.

#### Members

**`UserInsights`**

The [User-Insights](/unity/grow/Grow_Insights/#UserInsights) received from the server.

<div class="info-box">GROW Insights caches its data on the device so that it's accessible even when there is no internet connection.</div>

### UserInsights

`UserInsights` holds insights related to the user currently playing the game.
Located in `GrowInsights` and can be accessed using `GrowInsights.UserInsights`.

#### Members

**`PayInsights`**

The [Pay-Insights](/unity/grow/Grow_Insights/#PayInsights) received from the server.

### PayInsights

`PayInsights` holds insights related to the user's payments.
Located in `UserInsights` and can be accessed using `GrowInsights.UserInsights.PayInsights`.


**`PayRankByGenre`**

A `Dictionary` providing the user's pay-rank by [Genre](/unity/grow/Grow_Insights/#Genre)

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

**`TimeOfPurchase`**

A `Dictionary` providing the user's preferred time of purchase in the day by [DayQuarter](/unity/grow/Grow_Insights#DayQuarter), returns a number between 0 to 1 per quarter, which indicates the user's likelihood to pay in that specific quarter.

### Genre

`Genre` represents a game genre.

#### Usage

For example, in order to access a user's pay rank by the `Action` genre use `GrowInsights.UserInsights.PayInsights.PayRankByGenre[Genre.Action]`

### DayQuarter

`DayQuarter` represents a quarter of the day, we split the day as following:

- 12am to 6am

- 6am to 12pm

- 12pm to 6pm

- 6pm to 12am

<br/>

#### Usage

For example, in order to check the likelihood of the user to pay in the third quarter of the day `_12pm_6pm` use `GrowInsights.UserInsights.PayInsights.TimeOfPurchase[DayQuarter._12pm_6pm]`


## Example

``` cs

void Start () {

    // Add event listeners - Make sure to set the event handlers before you initialize
    HighwayEvents.OnGrowInsightsInitialized += OnGrowInsightsInitialized;
    HighwayEvents.OnInsightsRefreshFinished += OnInsightsRefreshFinished;

    // Initialize GrowHighway
    GrowHighway.Initialize();

    // Initialize GrowInsights
    GrowInsights.Initialize();

}

void OnGrowInsightsInitialized () {
    Debug.Log("GROW Insights has been initialized.");
}

void OnInsightsRefreshFinished (){
    if (GrowInsights.UserInsights.PayInsights.PayRankByGenre[Genre.Educational] > 3) {
        // ... Do stuff according to your business plan ...
    }
}


```
