---
layout: "content"
image: "Events"
title: "Events"
text: "Learn how to observe and handle social events triggered by unity3d-profile to customize your game-specific behavior."
position: 3
theme: 'platforms'
collection: 'unity_profile'
module: 'profile'
platform: 'unity'
---

#Event Handling

##About

Profile allows you to subscribe to events, be notified when they occur, and implement your own application-specific behavior to handle them once they occur.

<div class="info-box">Your game-specific behavior is an addition to the default behavior implemented by SOOMLA. You don't replace SOOMLA's behavior.</div>

###Tips & Reminders

- As mentioned in [Getting Started](/unity/profile/Profile_GettingStarted), make sure you add the event prefabs (ProfileEvents and CoreEvents) to your earliest loading scene.



##How it Works

Events are triggered when SOOMLA wants to notify you about different things that happen involving Profile operations.

For example, when a user posts a status, an `OnSocialActionStartedEvent` is fired as a result.


##Observing & Handling Events

The `ProfileEvents` class is where all events go through. To handle various events, just add your game-specific behavior to the delegates in the `ProfileEvents` class.

**For example:** To "listen" for an `onLoginFinished` event:

``` cs
// Sign up for the event
ProfileEvents.OnLoginFinished += onLoginFinished;

// Handle this event with your game-specific behavior:
public static void onLoginFinished(UserProfile userProfileJson, string payload){
	// ... your game specific implementation here ...
}
```

##Profile Events

###OnSoomlaProfileInitialized

This event is triggered when Soomla Profile has been initialized.

``` cs
ProfileEvents.OnSoomlaProfileInitialized += onSoomlaProfileInitialized;

public void onSoomlaProfileInitialized() {
	// ... your game specific implementation here ...
}
```

###OnUserRatingEvent

This event is triggered when the page for rating your app is opened.

``` cs
ProfileEvents.OnUserRatingEvent += onUserRatingEvent;

public void onUserRatingEvent() {
	// ... your game specific implementation here ...
}
```

###OnUserProfileUpdated

This event is triggered when the user profile has been updated, after login.

``` cs
ProfileEvents.OnUserProfileUpdated += onUserProfileUpdated;

public void onUserProfileUpdated(UserProfile userProfileJson) {
	// userProfileJson is the user's profile from the logged in provider

	// ... your game specific implementation here ...
}
```

###OnLoginStarted

This event is triggered when logging into the social provider has started.

``` cs
ProfileEvents.OnLoginStarted += onLoginStarted;

public void onLoginStarted(Provider provider, bool autoLogin, string payload) {
	// provider is the social provider
	// autoLogin will be "true" if the user was logged in using the AutoLogin functionality
	// payload is an identification string that you can give when you initiate the login operation and want to receive back upon starting

	// ... your game specific implementation here ...
}
```

###OnLoginFinished

This event is triggered when logging into the social provider has finished **successfully**.

``` cs
ProfileEvents.OnLoginFinished += onLoginFinished;

public void onLoginFinished(UserProfile userProfileJson, bool autoLogin, string payload) {
	// userProfileJson is the user's profile from the logged in provider
	// autoLogin will be "true" if the user was logged in using the AutoLogin functionality
	// payload is an identification string that you can give when you initiate the login operation and want to receive back upon its completion

	// ... your game specific implementation here ...
}
```

###OnLoginCancelled

This event is triggered when logging into the social provider has been cancelled.

``` cs
ProfileEvents.OnLoginCancelled += onLoginCancelled;

public void onLoginCancelled(Provider provider, bool autoLogin, string payload) {
	// provider is the social provider
	// autoLogin will be "true" if the user was logged in using the AutoLogin functionality
	// payload is an identification string that you can give when you initiate the login operation and want to receive back upon cancellation

	// ... your game specific implementation here ...
}
```

###OnLoginFailed

This event is triggered when logging into the social provider has failed.

``` cs
ProfileEvents.OnLoginFailed += onLoginFailed;

public void onLoginFailed(Provider provider, string message, bool autoLogin, string payload) {
	// provider is the social provider
	// message is the failure message
	// autoLogin will be "true" if the user was logged in using the AutoLogin functionality
	// payload is an identification string that you can give when you initiate the login operation and want to receive back upon failure

	// ... your game specific implementation here ...
}
```

###OnLogoutStarted

This event is triggered when logging out of the social provider has started.

``` cs
ProfileEvents.OnLogoutStarted += onLogoutStarted;

public void onLogoutStarted(Provider provider) {
	// provider is the social provider

	// ... your game specific implementation here ...
}
```

###OnLogoutFinished

This event is triggered when logging out of the social provider has finished **successfully**.

``` cs
ProfileEvents.OnLogoutFinished += onLogoutFinished;

public void onLogoutFinished(Provider provider) {
	// provider is the social provider

	// ... your game specific implementation here ...
}
```

###OnLogoutFailed

This event is triggered when logging out of the social provider has failed.

``` cs
ProfileEvents.OnLogoutFailed += onLogoutFailed;

public void onLogoutFailed(Provider provider, string message) {
	// provider is the social provider
	// message is the failure message

	// ... your game specific implementation here ...
}
```


###OnSocialActionStarted

This event is triggered when a social action has started.

``` cs
ProfileEvents.OnSocialActionStarted += onSocialActionStarted;

public void onSocialActionStarted(Provider provider, SocialActionType action, string payload) {
	// provider is the social provider
	// action is the social action (post status, etc..) that started
	// payload is an identification string that you can give when you initiate the social action operation and want to receive back upon starting

	// ... your game specific implementation here ...
}
```


###OnSocialActionFinished

This event is triggered when a social action has finished **successfully**.

``` cs
ProfileEvents.OnSocialActionFinished += onSocialActionFinished;

public void onSocialActionFinished(Provider provider, SocialActionType action, string payload) {
	// provider is the social provider
	// action is the social action (like, post status, etc..) that finished
	// payload is an identification string that you can give when you initiate the social action operation and want to receive back upon its completion

	// ... your game specific implementation here ...
}
```

###OnSocialActionCancelled

This event is triggered when a social action has been cancelled.

``` cs
ProfileEvents.OnSocialActionCancelled += onSocialActionCancelled;

public void onSocialActionCancelled(Provider provider, SocialActionType action, string payload) {
	// provider is the social provider
	// action is the social action (like, post status, etc..) that has been cancelled
	// payload is an identification string that you can give when you initiate the social action operation and want to receive back upon cancellation

	// ... your game specific implementation here ...
}
```

###OnSocialActionFailed

This event is triggered when a social action has failed.

``` cs
ProfileEvents.OnSocialActionFailed += onSocialActionFailed;

public void onSocialActionFailed(Provider provider, SocialActionType action, string message, string payload) {
	// provider is the social provider
	// action is the social action (like, post status, etc..) that failed
	// message is the failure message
	// payload is an identification string that you can give when you initiate the social action operation and want to receive back upon failure

	// ... your game specific implementation here ...
}
```

###OnGetContactsStarted

This event is triggered when fetching the contacts from the social provider has started.

``` cs
ProfileEvents.OnGetContactsStarted += onGetContactsStarted;

public void onGetContactsStarted(Provider provider, bool fromStart, string payload) {
	// provider is the social provider
	// fromStart Should we reset pagination or request the next page
	// payload is an identification string that you can give when you initiate the get contacts operation and want to receive back upon starting

	// ... your game specific implementation here ...
}
```

###OnGetContactsFinished

This event is triggered when fetching the contacts from the social provider has finished **successfully**.

``` cs
ProfileEvents.OnGetContactsFinished += onGetContactsFinished;

public void onGetContactsFinished(Provider provider, SocialPageData<UserProfile> userProfiles, string payload) {
	// provider is the social provider
	// userProfiles is a List of user profiles that have been fetched in the get contacts operation
	// payload is an identification string that you can give when you initiate the get contacts operation and want to receive back upon its completion

	// ... your game specific implementation here ...
}
```

###OnGetContactsFailed

This event is triggered when fetching the contacts from the social provider has failed.

``` cs
ProfileEvents.OnGetContactsFailed += onGetContactsFailed;

public void onGetContactsFailed(Provider provider, string message, bool fromStart, string payload) {
	// provider is the social provider
	// message Description of the reason for failure
	// fromStart Should we reset pagination or request the next page
	// payload is an identification string that you can give when you initiate the get contacts operation and want to receive back upon failure

	// ... your game specific implementation here ...
}
```

###OnGetFeedStarted

This event is triggered when fetching the feed from the social provider has started.

``` cs
ProfileEvents.OnGetFeedStarted += onGetFeedStarted;

public void onGetFeedStarted(Provider provider) {
	// provider is the social provider

	// ... your game specific implementation here ...
}
```

###OnGetFeedFinished

This event is triggered when fetching the feed from the social provider has finished **successfully**.

``` cs
ProfileEvents.OnGetFeedFinished += onGetFeedFinished;

public void onGetFeedFinished(Provider provider, SocialPageData<string> feed) {
	// provider is the social provider
	// feed is the user's feed that has been fetched in the get feed operation

	// ... your game specific implementation here ...
}
```

###OnGetFeedFailed

This event is triggered when fetching the feed from the social provider has failed.

``` cs
ProfileEvents.OnGetFeedFailed += onGetFeedFailed;

public void onGetFeedFailed(Provider provider, string payload) {
	// provider is the social provider
	// payload is an identification string that you can give when you initiate the get feed operation and want to receive back upon failure

	// ... your game specific implementation here ...
}
```

###OnInviteStarted

This event is triggered when an invitation has started.

``` cs
ProfileEvents.OnInviteStarted += onInviteStarted;

public void onInviteStarted(Provider provider, SocialActionType action, string payload) {
	// provider is the social provider
	// action is the social action (post status, etc..) that started
	// payload is an identification string that you can give when you initiate the social action operation and want to receive back upon starting

	// ... your game specific implementation here ...
}
```


###OnInviteFinished

This event is triggered when an invitation has finished **successfully**.

``` cs
ProfileEvents.OnInviteFinished += onInviteFinished;

public void onInviteFinished(Provider provider, SocialActionType action, string requestId, List<string> invitedIds, string payload) {
	// provider is the social provider
	// action is the social action (like, post status, etc..) that finished
	// requestId is an identifier of created invite request
  // invitedIds is a list of invited user's identifiers
	// payload is an identification string that you can give when you initiate the social action operation and want to receive back upon its completion

	// ... your game specific implementation here ...
}
```

###OnInviteCancelled

This event is triggered when an invitation has been cancelled.

``` cs
ProfileEvents.OnInviteCancelled += onInviteCancelled;

public void onInviteCancelled(Provider provider, SocialActionType action, string payload) {
	// provider is the social provider
	// action is the social action (like, post status, etc..) that has been cancelled
	// payload is an identification string that you can give when you initiate the social action operation and want to receive back upon cancellation

	// ... your game specific implementation here ...
}
```

###OnInviteFailed

This event is triggered when an invitation has failed.

``` cs
ProfileEvents.OnInviteFailed += onInviteFailed;

public void onInviteFailed(Provider provider, SocialActionType action, string message, string payload) {
	// provider is the social provider
	// action is the social action (like, post status, etc..) that failed
	// message is the failure message
	// payload is an identification string that you can give when you initiate the social action operation and want to receive back upon failure

	// ... your game specific implementation here ...
}
```
