---
layout: "content"
image: "Events"
title: "Events"
text: "Learn how to observe and handle social events triggered by cocos2dx-profile to customize your game-specific behavior."
position: 3
theme: 'platforms'
collection: 'cocos2djs_profile'
module: 'profile'
lang: 'js' 
platform: 'cocos2dx'
---

# Event Handling

## About

Profile allows you to subscribe to events, be notified when they occur, and implement your own application-specific behavior to handle them once they occur.

<div class="info-box">Your game-specific behavior is an addition to the default behavior implemented by SOOMLA. You don't replace SOOMLA's behavior.</div>

## How it Works

Events are triggered when SOOMLA wants to notify you about different things that happen involving Profile operations.

For example, when a user performs a social action such as uploading a status, an `EVENT_SOCIAL_ACTION_STARTED` event is fired as a result.


## Observing & Handling Events

SOOMLA uses the common way of dispatching events used in Cocos2d-x.
The names of such events are defined in `Soomla.ProfileConsts`, the data of the event are passes as arguments to the handlers. You can subscribe to any event from anywhere in your code.

** Subscribing **

Subscribe to events calling `Soomla.addHandler(eventName, handler, target)`, 
where `handler` - is a function that will be called when event is fired, and `target` - is "thisArg" used in that call. 

```js
Soomla.addHandler(Soomla.ProfileConsts.EVENT_LOGIN_FINISHED, this.onLoginFinished, this);
```

** Handling **

Handle the event through your own custom function:

```js
onLoginFinished: function (userProfile, payload) {
  // your code is here
}
```
## Profile Events

Below we provide a list of all events in Store.

### EVENT_PROFILE_INITIALIZED

This event is triggered when Soomla Profile has been initialized.

```js
Soomla.addHandler(Soomla.ProfileConsts.EVENT_PROFILE_INITIALIZED, this.onProfileInitialized, this);

this.onProfileInitialized = function () {
  // ... your game specific implementation here ...
}
```

### EVENT_USER_RATING

This event is triggered when the page for rating your app is opened.

```js
Soomla.addHandler(Soomla.ProfileConsts.EVENT_USER_RATING, this.onUserRatingEvent, this);

this.onUserRatingEvent = function () {
  // ... your game specific implementation here ...
}
```

### EVENT_USER_PROFILE_UPDATED

This event is triggered when the user profile has been updated, after login.

```js
Soomla.addHandler(Soomla.ProfileConsts.EVENT_USER_PROFILE_UPDATED, this.onUserProfileUpdated, this);

this.onUserProfileUpdated = function (userProfile) {
  // ... your game specific implementation here ...
}
```

### EVENT_LOGIN_STARTED

This event is triggered when logging into the social provider has started.

```js
Soomla.addHandler(Soomla.ProfileConsts.EVENT_LOGIN_STARTED, this.onLoginStarted, this);

this.onLoginStarted = function (provider, payload) {
  // ... your game specific implementation here ...
}
```

### EVENT_LOGIN_FINISHED

This event is triggered when logging into the social provider has finished **successfully**.

```js
Soomla.addHandler(Soomla.ProfileConsts.EVENT_LOGIN_FINISHED, this.onLoginFinished, this);

this.onLoginFinished = function (userProfile, payload) {
  // ... your game specific implementation here ...
}
```

### EVENT_LOGIN_CANCELLED

This event is triggered when logging into the social provider has been cancelled.

```js
Soomla.addHandler(Soomla.ProfileConsts.EVENT_LOGIN_CANCELLED, this.onLoginCancelled, this);

this.onLoginCancelled = function (provider, payload) {
  // ... your game specific implementation here ...
}
```

### EVENT_LOGIN_FAILED

This event is triggered when logging into the social provider has failed.

```js
Soomla.addHandler(Soomla.ProfileConsts.EVENT_LOGIN_FAILED, this.onLoginFailed, this);

this.onLoginFailed = function (provider, errorDescription, payload) {
  // ... your game specific implementation here ...
}
```

### EVENT_LOGOUT_STARTED

This event is triggered when logging out of the social provider has started.

```js
Soomla.addHandler(Soomla.ProfileConsts.EVENT_LOGOUT_STARTED, this.onLogoutStarted, this);

this.onLogoutStarted = function (provider) {
  // ... your game specific implementation here ...
}
```

### EVENT_LOGOUT_FINISHED

This event is triggered when logging out of the social provider has finished **successfully**.

```js
Soomla.addHandler(Soomla.ProfileConsts.EVENT_LOGOUT_FINISHED, this.onLogoutFinished, this);

this.onLogoutFinished = function (provider) {
  // ... your game specific implementation here ...
}
```

### EVENT_LOGOUT_FAILED

This event is triggered when logging out of the social provider has failed.

```js
Soomla.addHandler(Soomla.ProfileConsts.EVENT_LOGOUT_FAILED, this.onLogoutFailed, this);

this.onLogoutFailed = function (provider, errorDescription) {
  // ... your game specific implementation here ...
}
```

### EVENT_SOCIAL_ACTION_STARTED

This event is triggered when a social action has started.

```js
Soomla.addHandler(Soomla.ProfileConsts.EVENT_SOCIAL_ACTION_STARTED, this.onSocialActionStarted, this);

this.onSocialActionStarted = function (provider, socialActionType, payload) {
  // ... your game specific implementation here ...
}
```

### EVENT_SOCIAL_ACTION_FINISHED

This event is triggered when a social action has finished **successfully**.

```js
Soomla.addHandler(Soomla.ProfileConsts.EVENT_SOCIAL_ACTION_FINISHED, this.onSocialActionFinished, this);

this.onSocialActionFinished = function (provider, socialActionType, payload) {
  // ... your game specific implementation here ...
}
```

### EVENT_SOCIAL_ACTION_FAILED

This event is triggered when a social action has failed.

```js
Soomla.addHandler(Soomla.ProfileConsts.EVENT_SOCIAL_ACTION_FAILED, this.onSocialActionFailed, this);

this.onSocialActionFailed = function (provider, socialActionType, errorDescription, payload) {
  // ... your game specific implementation here ...
}
```

### EVENT_GET_CONTACTS_STARTED

This event is triggered when fetching the contacts from the social provider has started.

```js
Soomla.addHandler(Soomla.ProfileConsts.EVENT_GET_CONTACTS_STARTED, this.onGetContactsStarted, this);

this.onGetContactsStarted = function (provider, fromStart, payload) {
  // ... your game specific implementation here ...
}
```

### EVENT_GET_CONTACTS_FINISHED

This event is triggered when fetching the contacts from the social provider has finished **successfully**.

```js
Soomla.addHandler(Soomla.ProfileConsts.EVENT_GET_CONTACTS_FINISHED, this.onGetContactsFinished, this);

this.onGetContactsFinished = function (provider, contacts, payload, hasMore) {
  // ... your game specific implementation here ...
}
```

### EVENT_GET_CONTACTS_FAILED

This event is triggered when fetching the contacts from the social provider has failed.

```js
Soomla.addHandler(Soomla.ProfileConsts.EVENT_GET_CONTACTS_FAILED, this.onGetContactsFailed, this);

this.onGetContactsFailed = function (provider, errorDescription, fromStart, payload) {
  // ... your game specific implementation here ...
}
```

### EVENT_GET_FEED_STARTED

This event is triggered when fetching the feed from the social provider has started.

```js
Soomla.addHandler(Soomla.ProfileConsts.EVENT_GET_FEED_STARTED, this.onGetFeedStarted, this);

this.onGetFeedStarted = function (provider, fromStart, payload) {
  // ... your game specific implementation here ...
}
```

### EVENT_GET_FEED_FINISHED

This event is triggered when fetching the feed from the social provider has finished **successfully**.

```js
Soomla.addHandler(Soomla.ProfileConsts.EVENT_GET_FEED_FINISHED, this.onGetFeedFinished, this);

this.onGetFeedFinished = function (provider, feedList, payload, hasMore) {
  // ... your game specific implementation here ...
}
```

### EVENT_GET_FEED_FAILED

This event is triggered when fetching the feed from the social provider has failed.

```js
Soomla.addHandler(Soomla.ProfileConsts.EVENT_GET_FEED_FAILED, this.onGetFeedFailed, this);

this.onGetFeedFailed = function (provider, errorDescription, fromStart, payload) {
  // ... your game specific implementation here ...
}
```
