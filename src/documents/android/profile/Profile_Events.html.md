---
layout: "content"
image: "Events"
title: "Events"
text: "Learn how to observe and handle social events triggered by android-profile to customize your game-specific behavior."
position: 3
theme: 'platforms'
collection: 'android_profile'
module: 'profile'
platform: 'android'
---

# Event Handling

## About

Profile allows you to subscribe to events, be notified when they occur, and implement your own application-specific behavior to handle them once they occur.

<div class="info-box">Your game-specific behavior is an addition to the default behavior implemented by SOOMLA. You don't replace SOOMLA's behavior.</div>

## How it Works

Events are triggered when SOOMLA wants to notify you about different things that happen involving Profile operations.

For example, when a user posts a status, a `SocialActionStartedEvent` is fired as a result.

## Observing & Handling Events

For event handling, SOOMLA uses Square's great open-source project [Otto](http://square.github.io/otto/). In ordere to be notified of profile related events, you can register for specific events and create your game-specific behavior to handle them.

**In order to register for & handle events:**

1. In the class that should receive the event, create your event handler function with the annotation '@Subscribe'. Example:

    ``` java
    @Subscribe
    public void onLoginFinishedEvent(LoginFinishedEvent loginFinishedEvent) {
        // ... your game specific implementation here ...
    }
    ```

2. You'll also have to register your event handler class in the event bus (and unregister when needed):

   ``` java
   BusProvider.getInstance().register(this);
   ```

   ``` java
   BusProvider.getInstance().unregister(this);
   ```

<div class="info-box">If your class is an Activity, register in 'onResume' and unregister in 'onPause'.</div>

See a full [event handler example](https://github.com/soomla/android-profile/blob/master/SoomlaAndroidExample/src/com/soomla/example/ExampleEventHandler.java).

## Profile Events

### ProfileInitializedEvent

This event is triggered when Soomla Profile has been initialized.

``` java
@Subscribe
public void onProfileInitialized(ProfileInitializedEvent profileInitializedEvent) {
	// ... your game specific implementation here ...
}
```

### UserRatingEvent

This event is triggered when the page for rating your app is opened.

``` java
@Subscribe
public void onUserRatingEvent(UserRatingEvent UserRating) {
	// ... your game specific implementation here ...
}
```

### UserProfileUpdatedEvent

This event is triggered when the user profile has been updated, after login.

``` java
@Subscribe
public void onUserProfileUpdated(UserProfileUpdatedEvent userProfileUpdatedEvent) {
	// A UserProfileUpdatedEvent contains the following:
	// userProfile = the user's profile from the logged in provider

	// ... your game specific implementation here ...
}
```

### LoginStartedEvent

This event is triggered when logging into the social provider has started.

``` java
@Subscribe
public void onLoginStarted(LoginStartedEvent loginStartedEvent) {
	// A LoginStartedEvent contains the following:
	// provider = the provider where the login has started
	// autoLogin = comes "true" if user login automatically
	// payload  = an identification String that you can give when you initiate the login
	//            operation and want to receive back upon starting

	// ... your game specific implementation here ...
}
```

### LoginFinishedEvent

This event is triggered when logging into the social provider has finished **successfully**.

``` java
@Subscribe
public void onLoginFinished(LoginFinishedEvent loginFinishedEvent) {
	// A LoginFinishedEvent contains the following:
	// userProfile = the user's profile from the logged in provider
	// autoLogin = comes "true" if user login automatically
	// payload     = an identification string that you can give when you initiate the
	//               login operation and want to receive back upon its completion

	// ... your game specific implementation here ...
}
```

### LoginCancelledEvent

This event is triggered when logging into the social provider has been cancelled.

``` java
@Subscribe
public void onLoginCancelled(LoginCancelledEvent loginCancelledEvent) {
	// A LoginCancelledEvent contains the following:
	// provider = the provider which the user has cancelled login to
	// autoLogin = comes "true" if user login automatically
	// payload  = an identification string that you can give when you initiate the
	//            login operation and want to receive back upon cancellation

	// ... your game specific implementation here ...
}
```

### LoginFailedEvent

This event is triggered when logging into the social provider has failed.

``` java
@Subscribe
public void onLoginFailed(LoginFailedEvent loginFailedEvent) {
	// A LoginFailedEvent contains the following:
	// provider         = the provider on which the login has failed
	// errorDescription = description of the reason for failure
	// autoLogin 		= comes "true" if user login automatically
	// payload          = an identification string that you can give when you initiate
	//                    the login operation and want to receive back upon failure

	// ... your game specific implementation here ...
}
```

### LogoutStartedEvent

This event is triggered when logging out of the social provider has started.

``` java
@Subscribe
public void onLogoutStarted(LogoutStartedEvent logoutStartedEvent) {
	// A LogoutStartedEvent contains the following:
	// provider = the provider on which the login has started

	// ... your game specific implementation here ...
}
```

### LogoutFinishedEvent

This event is triggered when logging out of the social provider has finished **successfully**.

``` java
@Subscribe
public void onLogoutFinished(LogoutFinishedEvent logoutFinishedEvent) {
	// A LogoutFinishedEvent contains the following:
	// provider = the provider on which the logout has finished

	// ... your game specific implementation here ...
}
```

### LogoutFailedEvent

This event is triggered when logging out of the social provider has failed.

``` java
@Subscribe
public void onLogoutFailed(LogoutFailedEvent logoutFailedEvent) {
	// A LogoutFailedEvent contains the following:
	// provider         = the provider on which the logout has failed
	// errorDescription = description of the reason for failure

	// ... your game specific implementation here ...
}
```


### SocialActionStartedEvent

This event is triggered when a social action has started.

``` java
@Subscribe
public void onSocialActionStarted(SocialActionStartedEvent socialActionStartedEvent) {
	// A SocialActionStartedEvent contains the following:
	// provider         = the provider on which the social action has started
	// socialActionType = the social action (like, post status, etc..) that started
	// payload          = an identification string that you can give when you initiate
	//                    the social action operation and want to receive back upon starting

	// ... your game specific implementation here ...
}
```


### SocialActionFinishedEvent

This event is triggered when a social action has finished **successfully**.

``` java
@Subscribe
public void onSocialActionFinished(SocialActionFinishedEvent socialActionFinishedEvent) {
	// A SocialActionFinishedEvent contains the following:
	// provider         = the provider on which the social action has finished
	// socialActionType = the social action (like, post status, etc..) that finished
	// payload          = an identification string that you can give when you initiate
	//                    the social action operation and want to receive back upon completion

	// ... your game specific implementation here ...
}
```

### SocialActionCancelledEvent

This event is triggered when a social action has been cancelled.

``` java
@Subscribe
public void onSocialActionCancelled(SocialActionCancelledEvent socialActionCancelledEvent) {
	// A SocialActionCancelledEvent contains the following:
	// provider         = the provider on which a social action was cancelled
	// socialActionType = the social action (like, post status, etc..) that has been cancelled
	// payload          = an identification string that you can give when you initiate the
	//                    social action operation and want to receive back upon cancellation

	// ... your game specific implementation here ...
}
```

### SocialActionFailedEvent

This event is triggered when a social action has failed.

``` java
@Subscribe
public void onSocialActionFailed(SocialActionFailedEvent socialActionFailedEvent) {
	// A SocialActionFailedEvent contains the following:
	// provider         = the provider on which the social action has failed
	// socialActionType = the social action (like, post status, etc..) that failed
	// errorDescription = description of the reason for failure
	// payload          = an identification string that you can give when you initiate
	//                    the social action operation and want to receive back upon failure

	// ... your game specific implementation here ...
}
```


### GetContactsStartedEvent

This event is triggered when fetching the contacts from the social provider has started.

``` java
@Subscribe
public void onGetContactsStarted(GetContactsStartedEvent getContactsStartedEvent) {
	// A GetContactsStartedEvent contains the following:
	// provider         = the provider on which the get contacts process started
	// socialActionType = the social action performed
	// fromStart        = should we reset pagination or request the next page
	// payload          = an identification string that you can give when you initiate
	//                    the get contacts operation and want to receive back upon starting

	// ... your game specific implementation here ...
}
```

### GetContactsFinishedEvent

This event is triggered when fetching the contacts from the social provider has finished **successfully**.

``` java
@Subscribe
public void onGetContactsFinished(GetContactsFinishedEvent getContactsFinishedEvent) {
	// A GetContactsFinishedEvent contains the following:
	// provider         = the provider on which the get contacts process finished
	// socialActionType = the social action performed
	// contacts         = an Array of contacts represented by UserProfile
	// payload          = an identification string that you can give when you initiate the
	//                    get contacts operation and want to receive back upon its completion
	// hasMore          = if there are more items in pagination

	// ... your game specific implementation here ...
}
```

### GetContactsFailedEvent

This event is triggered when fetching the contacts from the social provider has failed.

``` java
@Subscribe
public void onGetContactsFailed(GetContactsFailedEvent getContactsFailedEvent) {
	// A GetContactsFailedEvent contains the following:
	// provider         = the provider on which the get contacts process has failed
	// socialActionType = the social action performed
	// errorDescription = description of the reason for failure
	// fromStart        = should we reset pagination or request the next page
	// payload          = an identification string that you can give when you initiate
	//                    the get contacts operation and want to receive back upon failure

	// ... your game specific implementation here ...
}
```

### GetFeedStartedEvent

This event is triggered when fetching the feed from the social provider has started.

``` java
@Subscribe
public void onGetFeedStarted(GetFeedStartedEvent getFeedStartedEvent) {
	// A GetFeedStartedEvent contains the following:
	// provider    = the provider on which the get feed process started
	// getFeedType = the social action performed
	// fromStart   = should we reset pagination or request the next page
	// payload     = an identification string that you can give when you initiate
	//               the get feed operation and want to receive back upon starting

	// ... your game specific implementation here ...
}
```

### GetFeedFinishedEvent

This event is triggered when fetching the feed from the social provider has finished **successfully**.

``` java
@Subscribe
public void onGetFeedFinished(GetFeedFinishedEvent getFeedFinishedEvent) {
	// A GetFeedFinishedEvent contains the following:
	// provider    = the provider on which the get feed process finished
	// getFeedType = the social action performed
	// feedPosts   = an Array of feed entries represented by strings
	// payload     = an identification string that you can give when you initiate
	//               the get feed operation and want to receive back upon completion
	// hasMore     = if there are more items in pagination

	// ... your game specific implementation here ...
}
```

### GetFeedFailedEvent

This event is triggered when fetching the feed from the social provider has failed.

``` java
@Subscribe
public void onGetFeedFailed(GetFeedFailedEvent getFeedFailedEvent) {
	// A GetFeedFailedEvent contains the following:
	// provider         = the provider on which the get feed process has
	// getFeedType      = the social action performed
	// errorDescription = description of the reason for failure
	// fromStart        = should we reset pagination or request the next page
	// payload          = an identification string that you can give when you initiate
	//                    the get feed operation and want to receive back upon failure

	// ... your game specific implementation here ...
}
```
