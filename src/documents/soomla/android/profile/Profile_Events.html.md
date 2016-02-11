---
layout: "soomla-content"
image: "Events"
title: "Events"
text: "Learn how to observe and handle social events triggered by android-profile to customize your game-specific behavior."
position: 3
theme: 'soomla'
collection: 'soomla_android_profile'
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
public void onUserRatingEvent(UserRatingEvent userRatingEvent) {
	// ... your game specific implementation here ...
}
```

### UserProfileUpdatedEvent

This event is triggered when the user profile has been updated, after login.

``` java
@Subscribe
public void onUserProfileUpdated(UserProfileUpdatedEvent userProfileUpdatedEvent) {
	// A UserProfileUpdatedEvent contains the following:
	// UserProfile = the user's profile from the logged in provider

	// ... your game specific implementation here ...
}
```

### LoginStartedEvent

This event is triggered when logging into the social provider has started.

``` java
@Subscribe
public void onLoginStarted(LoginStartedEvent loginStartedEvent) {
	// A LoginStartedEvent contains the following:
	// Provider = the provider where the login has started
	// AutoLogin = will be "true" if the user was logged in using the AutoLogin functionality
	// Payload  = an identification String that you can give when you initiate the login
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
	// UserProfile = the user's profile from the logged in provider
	// AutoLogin = will be "true" if the user was logged in using the AutoLogin functionality
	// Payload     = an identification string that you can give when you initiate the
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
	// Provider = the provider which the user has cancelled login to
	// AutoLogin = will be "true" if the user was logged in using the AutoLogin functionality
	// Payload  = an identification string that you can give when you initiate the
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
	// Provider         = the provider on which the login has failed
	// ErrorDescription = description of the reason for failure
	// AutoLogin 		    = will be "true" if the user was logged in using the AutoLogin functionality
	// Payload          = an identification string that you can give when you initiate
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
	// Provider = the provider on which the login has started

	// ... your game specific implementation here ...
}
```

### LogoutFinishedEvent

This event is triggered when logging out of the social provider has finished **successfully**.

``` java
@Subscribe
public void onLogoutFinished(LogoutFinishedEvent logoutFinishedEvent) {
	// A LogoutFinishedEvent contains the following:
	// Provider = the provider on which the logout has finished

	// ... your game specific implementation here ...
}
```

### LogoutFailedEvent

This event is triggered when logging out of the social provider has failed.

``` java
@Subscribe
public void onLogoutFailed(LogoutFailedEvent logoutFailedEvent) {
	// A LogoutFailedEvent contains the following:
	// Provider         = the provider on which the logout has failed
	// ErrorDescription = description of the reason for failure

	// ... your game specific implementation here ...
}
```


### SocialActionStartedEvent

This event is triggered when a social action has started.

``` java
@Subscribe
public void onSocialActionStarted(SocialActionStartedEvent socialActionStartedEvent) {
	// A SocialActionStartedEvent contains the following:
	// Provider         = the provider on which the social action has started
	// SocialActionType = the social action (post status, etc..) that started
	// Payload          = an identification string that you can give when you initiate
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
	// Provider         = the provider on which the social action has finished
	// SocialActionType = the social action (post status, etc..) that finished
	// Payload          = an identification string that you can give when you initiate
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
	// Provider         = the provider on which a social action was cancelled
	// SocialActionType = the social action (post status, etc..) that has been cancelled
	// Payload          = an identification string that you can give when you initiate the
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
	// Provider         = the provider on which the social action has failed
	// SocialActionType = the social action (post status, etc..) that failed
	// ErrorDescription = description of the reason for failure
	// Payload          = an identification string that you can give when you initiate
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
	// Provider         = the provider on which the get contacts process started
	// SocialActionType = the social action performed
	// FromStart        = should we reset pagination or request the next page
	// Payload          = an identification string that you can give when you initiate
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
	// Provider         = the provider on which the get contacts process finished
	// SocialActionType = the social action performed
	// Contacts         = an Array of contacts represented by UserProfile
	// Payload          = an identification string that you can give when you initiate the
	//                    get contacts operation and want to receive back upon its completion
	// HasMore          = if there are more items in pagination

	// ... your game specific implementation here ...
}
```

### GetContactsFailedEvent

This event is triggered when fetching the contacts from the social provider has failed.

``` java
@Subscribe
public void onGetContactsFailed(GetContactsFailedEvent getContactsFailedEvent) {
	// A GetContactsFailedEvent contains the following:
	// Provider         = the provider on which the get contacts process has failed
	// SocialActionType = the social action performed
	// ErrorDescription = description of the reason for failure
	// FromStart        = should we reset pagination or request the next page
	// Payload          = an identification string that you can give when you initiate
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
	// Provider    = the provider on which the get feed process started
	// GetFeedType = the social action performed
	// FromStart   = should we reset pagination or request the next page
	// Payload     = an identification string that you can give when you initiate
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
	// Provider    = the provider on which the get feed process finished
	// GetFeedType = the social action performed
	// FeedPosts   = an Array of feed entries represented by strings
	// Payload     = an identification string that you can give when you initiate
	//               the get feed operation and want to receive back upon completion
	// HasMore     = if there are more items in pagination

	// ... your game specific implementation here ...
}
```

### GetFeedFailedEvent

This event is triggered when fetching the feed from the social provider has failed.

``` java
@Subscribe
public void onGetFeedFailed(GetFeedFailedEvent getFeedFailedEvent) {
	// A GetFeedFailedEvent contains the following:
	// Provider         = the provider on which the get feed process has
	// GetFeedType      = the social action performed
	// ErrorDescription = description of the reason for failure
	// FromStart        = should we reset pagination or request the next page
	// Payload          = an identification string that you can give when you initiate
	//                    the get feed operation and want to receive back upon failure

	// ... your game specific implementation here ...
}
```

### InviteStartedEvent

This event is triggered when an invitation has started.

``` java
@Subscribe
public void onInviteStarted(InviteStartedEvent inviteStartedEvent) {
	// A SocialActionStartedEvent contains the following:
	// Provider         = the provider on which the invite has started
	// SocialActionType = the social action type (INVITE) that started
	// Payload          = an identification string that you can give when you initiate
	//                    the social action operation and want to receive back upon starting

	// ... your game specific implementation here ...
}
```


### InviteFinishedEvent

This event is triggered when an invitation has finished **successfully**.

``` java
@Subscribe
public void onInviteFinished(InviteFinishedEvent inviteFinishedEvent) {
	// A SocialActionFinishedEvent contains the following:
	// Provider         = the provider on which the social action has finished
	// SocialActionType = the social action (post status, etc..) that finished
	// RequestId			  = An identifier of created invite request
	// InvitedIds				= A list of invited user's identifiers
	// Payload          = an identification string that you can give when you initiate
	//                    the social action operation and want to receive back upon completion

	// ... your game specific implementation here ...
}
```

### InviteCancelledEvent

This event is triggered when an invitation has been cancelled.

``` java
@Subscribe
public void onInviteCancelled(InviteCancelledEvent inviteCancelledEvent) {
	// A SocialActionCancelledEvent contains the following:
	// Provider         = the provider on which a social action was cancelled
	// SocialActionType = the social action (post status, etc..) that has been cancelled
	// Payload          = an identification string that you can give when you initiate the
	//                    social action operation and want to receive back upon cancellation

	// ... your game specific implementation here ...
}
```

### InviteFailedEvent

This event is triggered when an invitation has failed.

``` java
@Subscribe
public void onInviteFailed(InviteFailedEvent inviteFailedEvent) {
	// A SocialActionFailedEvent contains the following:
	// Provider         = the provider on which the social action has failed
	// SocialActionType = the social action (post status, etc..) that failed
	// ErrorDescription = description of the reason for failure
	// Payload          = an identification string that you can give when you initiate
	//                    the social action operation and want to receive back upon failure

	// ... your game specific implementation here ...
}
```

### GetLeaderboardsStartedEvent

The event is triggered when fetching leaderboards has started.

``` java
@Subscribe
public void onGetLeaderboardsStarted(GetLeaderboardsStartedEvent getLeaderboardsStartedEvent) {
	// Provider is the social provider
  // Payload is an identification string that you can give when you initiate the social action operation and want to receive back upon failure

  // ... your game specific implementation here ...
}
```

### GetLeaderboardsFinishedEvent

The event is triggered when fetching leaderboards has finished successfully.

``` java
@Subscribe
public void onGetLeaderboardsFinished(GetLeaderboardsFinishedEvent getLeaderboardsFinishedEvent) {
	// Provider is the social provider
  // Leaderboards is a list of leaderboards
  // Payload is an identification string that you can give when you initiate the social action operation and want to receive back upon failure

 	// ... your game specific implementation here ...
}
```

### GetLeaderboardsFailedEvent

The event is triggered when fetching leaderboards was failed.

``` java
@Subscribe
public void onGetLeaderboardsFailed(GetLeaderboardsFailedEvent getLeaderboardsFailedEvent) {
	// Provider is the social provider
  // ErrorDescription is Description of the reason for failure
  // Payload is an identification string that you can give when you initiate the social action operation and want to receive back upon failure

  // ... your game specific implementation here ...
}
```

### GetScoresStartedEvent

The event is triggered when fetching scores from the current leaderboard has started.

``` java
@Subscribe
public void onGetScoresStarted(GetScoresStartedEvent getScoresStartedEvent) {
	// Provider is the social provider
  // Leaderboard is a source leaderboard
  // FromStart should we reset pagination or request the next page
  // Payload is an identification string that you can give when you initiate the social action operation and want to receive back upon failure

  // ... your game specific implementation here ...
}
```

### GetScoresFinishedEvent

The event is triggered when fetching scores from the current leaderboard has finished successfully.

``` java
@Subscribe
public void onGetScoresFinished(GetScoresFinishedEvent getScoresFinishedEvent) {
	// Provider is the social provider
  // Leaderboard is a source leaderboard
  // Scores an array of scores
  // Payload is an identification string that you can give when you initiate the social action operation and want to receive back upon failure

  // ... your game specific implementation here ...
}
```

### GetScoresFailedEvent

The event is triggered when fetching scores from the current leaderboard has failed.

``` java
@Subscribe
public void onGetScoresFailed(GetScoresFailedEvent getScoresFailedEvent) {
	// Provider is the social provider
  // Leaderboard is a source leaderboard
  // ErrorDescription description of the reason for failure
  // FromStart should we reset pagination or request the next page
  // Payload is an identification string that you can give when you initiate the social action operation and want to receive back upon failure

  // ... your game specific implementation here ...
}
```

### SubmitScoreStartedEvent

The event is triggered when score submission for the current leaderboard has started.

``` java
@Subscribe
public void onSubmitScoreStarted(SubmitScoreStartedEvent submitScoreStartedEvent) {
	// Provider is the social provider
  // Leaderboard is a source leaderboard    
  // Payload is an identification string that you can give when you initiate the social action operation and want to receive back upon failure

  // ... your game specific implementation here ...
}
```

### SubmitScoreFinishedEvent

The event is triggered when score submission for the current leaderboard has finished successfully.

``` java
@Subscribe
public void onSubmitScoreFinished(SubmitScoreFinishedEvent submitScoreFinishedEvent) {
	// Provider is the social provider
  // Leaderboard is a source leaderboard
  // Score is a new score instance was created
  // Payload is an identification string that you can give when you initiate the social action operation and want to receive back upon failure

  // ... your game specific implementation here ...
}
```

### SubmitScoreFailedEvent

The event is triggered when score submission for the current leaderboard has failed.

``` java
@Subscribe
public void onSubmitScoreFailed(SubmitScoreFailedEvent submitScoreFailedEvent) {
	// Provider is the social provider
  // Leaderboard is a source leaderboard
  // ErrorDescription description of the reason for failure
  // Payload is an identification string that you can give when you initiate the social action operation and want to receive back upon failure

  // ... your game specific implementation here ...
}
```

### ShowLeaderboardsEvent

The event is triggered when leaderboards native dialog was shown.

``` java
@Subscribe
public void onShowLeaderboards(ShowLeaderboardsEvent showLeaderboardsEvent) {
	// Provider is the social provider  
  // Payload is an identification string that you can give when you initiate the social action operation and want to receive back upon failure

  // ... your game specific implementation here ...
}
```
