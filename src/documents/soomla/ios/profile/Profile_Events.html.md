---
layout: "soomla-content"
image: "Events"
title: "Events"
text: "Learn how to observe and handle social events triggered by ios-profile to customize your game-specific behavior."
position: 3
theme: 'soomla-ios'
collection: 'soomla_ios_profile'
module: 'profile'
platform: 'ios'
---

# Event Handling

## About

Profile allows you to subscribe to events, be notified when they occur, and implement your own application-specific behavior to handle them once they occur.

<div class="info-box">Your game-specific behavior is an addition to the default behavior implemented by SOOMLA. You don't replace SOOMLA's behavior.</div>

## How it Works

Events are triggered when SOOMLA wants to notify you about different things that happen involving Profile operations.

For example, when a user performs a social action such as uploading a status, an event is fired as a result.

## Observing & Handling Events

In order to observe store events you need to import `ProfileEventHandling.h` and then you can add a notification to `NSNotificationCenter`:

``` objectivec
[[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(yourCustomSelector:)
  name:EVENT_UP_LOGIN_STARTED object:nil];
```

OR, you can observe all events with the same selector by calling:

``` objectivec
[UserProfileEventHandling observeAllEventsWithObserver:self
  withSelector:@selector(yourCustomSelector:)];
```

## Profile Events

### PROFILE INITIALIZED  

The event `EVENT_UP_PROFILE_INITIALIZED` is triggered when Soomla Profile initialized and ready.

``` objectivec
// observe the event:
[[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(profileInitialized:)
  name:EVENT_UP_PROFILE_INITIALIZED object:nil];

// your handler:
- (void)profileInitialized:(NSNotification*)notification {
  // ... your game specific implementation here ...
}
```

### USER RATING

The event `EVENT_UP_USER_RATING` is triggered when the page for rating your app is opened.

``` objectivec
// observe the event:
[[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(userRating:)
  name:EVENT_UP_USER_RATING object:nil];

// your handler:
- (void)userRating:(NSNotification*)notification {
  // ... your game specific implementation here ...
}
```

### USER PROFILE UPDATED

The event `EVENT_UP_USER_PROFILE_UPDATED` is triggered when the user profile has been updated, after login.

``` objectivec
// observe the event:
[[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(userProfileUpdated:)
  name:EVENT_UP_USER_PROFILE_UPDATED object:nil];

// your handler:
- (void)userProfileUpdated:(NSNotification*)notification {
  // notification's userInfo contains the following keys:
  // DICT_ELEMENT_USER_PROFILE = The user's profile (UserProfile*) from the logged in provider

  // ... your game specific implementation here ...
}
```

### LOGIN STARTED

The event `EVENT_UP_LOGIN_STARTED` is triggered when logging into the social provider has started.

``` objectivec
// observe the event:
[[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(loginStarted:)
  name:EVENT_UP_LOGIN_STARTED object:nil];

// your handler:
- (void)loginStarted:(NSNotification*)notification {
  // notification's userInfo contains the following keys:
  // DICT_ELEMENT_PROVIDER = The provider (NSNumber*) where the login has started
  // DICT_ELEMENT_AUTO_LOGIN = will be "true" if the user was logged in using the AutoLogin functionality
  // DICT_ELEMENT_PAYLOAD  = An identification string (NSString*) that you can give when you
  //                     initiate the login operation and want to receive back upon starting

  // ... your game specific implementation here ...
}
```

### LOGIN FINISHED

The event `EVENT_UP_LOGIN_FINISHED` is triggered when logging into the social provider has finished successfully.

``` objectivec
// observe the event:
[[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(loginFinished:)
  name:EVENT_UP_LOGIN_FINISHED object:nil];

// your handler:
- (void)loginFinished:(NSNotification*)notification {
  // notification's userInfo contains the following keys:
  // DICT_ELEMENT_USER_PROFILE = The user's profile (UserProfile*) from the logged in provider
  // DICT_ELEMENT_AUTO_LOGIN = will be "true" if the user was logged in using the AutoLogin functionality
  // DICT_ELEMENT_PAYLOAD      = An identification string (NSString*) that you can give when
  //           you initiate the login operation and want to receive back upon its completion

  // ... your game specific implementation here ...
}
```

### LOGIN CANCELLED

The event `EVENT_UP_LOGIN_CANCELLED` is triggered when logging into the social provider has been cancelled.

``` objectivec
// observe the event:
[[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(loginCancelled:)
  name:EVENT_UP_LOGIN_CANCELLED object:nil];

// your handler:
- (void)loginCancelled:(NSNotification*)notification {
  // notification's userInfo contains the following keys:
  // DICT_ELEMENT_PROVIDER = The provider (NSNumber*) which the user has cancelled login to
  // DICT_ELEMENT_AUTO_LOGIN = will be "true" if the user was logged in using the AutoLogin functionality
  // DICT_ELEMENT_PAYLOAD  = An identification string (NSString*) that you can give when you
  //                 initiate the login operation and want to receive back upon cancellation

  // ... your game specific implementation here ...
}
```

### LOGIN FAILED

The event `EVENT_UP_LOGIN_FAILED` is triggered when logging into the social provider has failed.

``` objectivec
// observe the event:
[[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(loginFailed:)
  name:EVENT_UP_LOGIN_FAILED object:nil];

// your handler:
- (void)loginFailed:(NSNotification*)notification {
  // notification's userInfo contains the following keys:
  // DICT_ELEMENT_PROVIDER = The provider (NSNumber*) on which the login has failed
  // DICT_ELEMENT_MESSAGE  = Description (NSString*) of the reason for failure
  // DICT_ELEMENT_AUTO_LOGIN = will be "true" if the user was logged in using the AutoLogin functionality
  // DICT_ELEMENT_PAYLOAD  = An identification string (NSString*) that you can give when
  //              you initiate the login operation and want to receive back upon failure

  // ... your game specific implementation here ...
}
```

### LOGOUT STARTED

The event `EVENT_UP_LOGOUT_STARTED` is triggered when logging out of the social provider has started.

``` objectivec
// observe the event:
[[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(logoutStarted:)
  name:EVENT_UP_LOGOUT_STARTED object:nil];

// your handler:
- (void)logoutStarted:(NSNotification*)notification {
  // notification's userInfo contains the following keys:
  // DICT_ELEMENT_PROVIDER = The provider (NSNumber*) on which the login has started

  // ... your game specific implementation here ...
}
```

### LOGOUT FINISHED

The event `EVENT_UP_LOGOUT_FINISHED` is triggered when logging out of the social provider has finished successfully.

``` objectivec
// observe the event:
[[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(logoutFinished:)
  name:EVENT_UP_LOGOUT_FINISHED object:nil];

// your handler:
- (void)logoutFinished:(NSNotification*)notification {
  // notification's userInfo contains the following keys:
  // DICT_ELEMENT_PROVIDER = The provider (NSNumber*) on which the logout has finished

  // ... your game specific implementation here ...
}
```

### LOGOUT FAILED

The event `EVENT_UP_LOGOUT_FAILED` is triggered when logging out of the social provider has failed.

``` objectivec
// observe the event:
[[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(logoutFailed:)
  name:EVENT_UP_LOGOUT_FAILED object:nil];

// your handler:
- (void)logoutFailed:(NSNotification*)notification {
  // notification's userInfo contains the following keys:
  // DICT_ELEMENT_PROVIDER = The provider (NSNumber*) on which the logout has failed
  // DICT_ELEMENT_MESSAGE  = Description (NSString*) of the reason for failure

  // ... your game specific implementation here ...
}
```

### SOCIAL ACTION STARTED

The event `EVENT_UP_SOCIAL_ACTION_STARTED` is triggered when a social action (post status, etc..) has started.

``` objectivec
// observe the event:
[[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(socialActionStarted:)
  name:EVENT_UP_SOCIAL_ACTION_STARTED object:nil];

// your handler:
- (void)socialActionStarted:(NSNotification*)notification {
  // notification's userInfo contains the following keys:
  // DICT_ELEMENT_PROVIDER           = The provider (NSNumber*) on which the social action
  //                                   has started
  // DICT_ELEMENT_SOCIAL_ACTION_TYPE = The social action (NSNumber*) that started
  // DICT_ELEMENT_PAYLOAD            = An identification string (NSString*) that you can give
  //     when you initiate the social action operation and want to receive back upon starting

  // ... your game specific implementation here ...
}
```

### SOCIAL ACTION FINISHED

The event `EVENT_UP_SOCIAL_ACTION_FINISHED` is triggered when a social action has finished successfully.

``` objectivec
// observe the event:
[[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(socialActionFinished:)
  name:EVENT_UP_SOCIAL_ACTION_FINISHED object:nil];

// your handler:
- (void)socialActionFinished:(NSNotification*)notification {
  // notification's userInfo contains the following keys:
  // DICT_ELEMENT_PROVIDER           = The provider (NSNumber*) on which the social action
  //                                   has finished
  // DICT_ELEMENT_SOCIAL_ACTION_TYPE = The social action (NSNumber*) that finished
  // DICT_ELEMENT_PAYLOAD            = An identification string (NSString*) that you can give
  //   when you initiate the social action operation and want to receive back upon completion

  // ... your game specific implementation here ...
}
```

### SOCIAL ACTION CANCELLED

The event `EVENT_UP_SOCIAL_ACTION_CANCELLED` is triggered when a social action has been cancelled.

``` objectivec
// observe the event:
[[NSNotificationCenter defaultCenter] addObserver:self
  selector:@selector(socialActionCancelled:) name:EVENT_UP_SOCIAL_ACTION_CANCELLED object:nil];

// your handler:
- (void)socialActionCancelled:(NSNotification*)notification {
  // notification's userInfo contains the following keys:
  // DICT_ELEMENT_PROVIDER           = The provider (NSNumber*) on which a social action was
  //                                   cancelled
  // DICT_ELEMENT_SOCIAL_ACTION_TYPE = The social action (NSNumber*) that was cancelled
  // DICT_ELEMENT_PAYLOAD            = An identification string (NSString*) that you can give
  // when you initiate the social action operation and want to receive back upon cancellation


  // ... your game specific implementation here ...
}
```

### SOCIAL ACTION FAILED

The event `EVENT_UP_SOCIAL_ACTION_FAILED` is triggered when a social action has failed.

``` objectivec
// observe the event:
[[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(socialActionFailed:)
  name:EVENT_UP_SOCIAL_ACTION_FAILED object:nil];

// your handler:
- (void)socialActionFailed:(NSNotification*)notification {
  // notification's userInfo contains the following keys:
  // DICT_ELEMENT_PROVIDER           = The provider (NSNumber*) on which the social action
  //                                   has failed
  // DICT_ELEMENT_SOCIAL_ACTION_TYPE = The social action (NSNumber*) that failed
  // DICT_ELEMENT_MESSAGE            = Description (NSString*) of the reason for failure
  // DICT_ELEMENT_PAYLOAD            = An identification string (NSString*) that you can give
  //      when you initiate the social action operation and want to receive back upon failure

  // ... your game specific implementation here ...
}
```

### GET CONTACTS STARTED

The event `EVENT_UP_GET_CONTACTS_STARTED` is triggered when fetching the contacts from the social provider has started.

``` objectivec
// observe the event:
[[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(getContactsStarted:)
  name:EVENT_UP_GET_CONTACTS_STARTED object:nil];

// your handler:
- (void)getContactsStarted:(NSNotification*)notification {
  // notification's userInfo contains the following keys:
  // DICT_ELEMENT_PROVIDER           = The provider (NSNumber*) on which the get contacts
  //                                   process started
  // DICT_ELEMENT_SOCIAL_ACTION_TYPE = The social action (NSNumber*) performed
  // DICT_ELEMENT_FROM_START         = Should we reset pagination or request the next page
  // DICT_ELEMENT_PAYLOAD            = An identification string (NSString*) that you can give
  //      when you initiate the get contacts operation and want to receive back upon starting

  // ... your game specific implementation here ...
}
```

### GET CONTACTS FINISHED

The event `EVENT_UP_GET_CONTACTS_FINISHED` is triggered when fetching the contacts from the social provider has finished successfully.

``` objectivec
// observe the event:
[[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(getContactsFinished:)
  name:EVENT_UP_GET_CONTACTS_FINISHED object:nil];

// your handler:
- (void)getContactsFinished:(NSNotification*)notification {
  // notification's userInfo contains the following keys:
  // DICT_ELEMENT_PROVIDER           = The provider (NSNumber*) on which the get contacts process
  //                                   finished
  // DICT_ELEMENT_SOCIAL_ACTION_TYPE = The social action (NSNumber*) performed
  // DICT_ELEMENT_CONTACTS           = An Array (NSArray*) of contacts (UserProfile*)
  // DICT_ELEMENT_PAYLOAD            = An identification string (NSString*) that you can give
  // DICT_ELEMENT_HAS_MORE           = if there are more items in pagination
  // when you initiate the get contacts operation and want to receive back upon its completion


  // ... your game specific implementation here ...
}
```

### GET CONTACTS FAILED

The event `EVENT_UP_GET_CONTACTS_FAILED` is triggered when fetching the contacts from the social provider has failed.

``` objectivec
// observe the event:
[[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(getContactsFailed:)
  name:EVENT_UP_GET_CONTACTS_FAILED object:nil];

// your handler:
- (void)getContactsFailed:(NSNotification*)notification {
  // notification's userInfo contains the following keys:
  // DICT_ELEMENT_PROVIDER           = The provider (NSNumber*) on which the get contacts
  //                                   process has failed
  // DICT_ELEMENT_SOCIAL_ACTION_TYPE = The social action (NSNumber*) performed
  // DICT_ELEMENT_MESSAGE            = Description (NSString*) of the reason for failure
  // DICT_ELEMENT_FROM_START         = Should we reset pagination or request the next page
  // DICT_ELEMENT_PAYLOAD            = An identification string (NSString*) that you can give
  //       when you initiate the get contacts operation and want to receive back upon failure

  // ... your game specific implementation here ...
}
```

### GET FEED STARTED

The event `EVENT_UP_GET_FEED_STARTED` is triggered when fetching the feed from the social provider has started.

``` objectivec
// observe the event:
[[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(getFeedStarted:)
  name:EVENT_UP_GET_FEED_STARTED object:nil];

// your handler:
- (void)getFeedStarted:(NSNotification*)notification {
  // notification's userInfo contains the following keys:
  // DICT_ELEMENT_PROVIDER           = The provider (NSNumber*) on which the get feed process
  //                                   started
  // DICT_ELEMENT_SOCIAL_ACTION_TYPE = The social action (NSNumber*) performed
  // DICT_ELEMENT_FROM_START         = Should we reset pagination or request the next page
  // DICT_ELEMENT_PAYLOAD            = An identification string (NSString*) that you can give
  //          when you initiate the get feed operation and want to receive back upon starting

  // ... your game specific implementation here ...
}
```

### GET FEED FINISHED

The event `EVENT_UP_GET_FEED_FINISHED` is triggered when fetching the feed from the social provider has finished successfully.

``` objectivec
// observe the event:
[[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(getFeedFinished:)
  name:EVENT_UP_GET_FEED_FINISHED object:nil];

// your handler:
- (void)getFeedFinished:(NSNotification*)notification {
  // notification's userInfo contains the following keys:
  // DICT_ELEMENT_PROVIDER           = The provider (NSNumber*) on which the get feed process
  //                                   finished
  // DICT_ELEMENT_SOCIAL_ACTION_TYPE = The social action (NSNumber*) performed
  // DICT_ELEMENT_FEEDS              = An Array (NSArray*) of feed entries (NSString*)
  // DICT_ELEMENT_PAYLOAD            = An identification string (NSString*) that you can give
  //        when you initiate the get feed operation and want to receive back upon completion
  // DICT_ELEMENT_HAS_MORE           = if there are more items in pagination

  // ... your game specific implementation here ...
}
```

### GET FEED FAILED

The event `EVENT_UP_GET_FEED_FAILED` is triggered when fetching the feed from the social provider has failed.

``` objectivec
// observe the event:
[[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(getFeedFailed:)
  name:EVENT_UP_GET_FEED_FAILED object:nil];

// your handler:
- (void)getFeedFailed:(NSNotification*)notification {
  // notification's userInfo contains the following keys:
  // DICT_ELEMENT_PROVIDER           = The provider (NSNumber*) on which the get feed process
  //                                   has failed
  // DICT_ELEMENT_SOCIAL_ACTION_TYPE = The social action (NSNumber*) performed
  // DICT_ELEMENT_MESSAGE            = Description (NSString*) of the reason for failure
  // DICT_ELEMENT_FROM_START         = Should we reset pagination or request the next page
  // DICT_ELEMENT_PAYLOAD            = An identification string (NSString*) that you can give  
  //           when you initiate the get feed operation and want to receive back upon failure

  // ... your game specific implementation here ...
}
```

### INVITE STARTED

The event `EVENT_UP_INVITE_STARTED` is triggered when an invitation process has started.

``` objectivec
// observe the event:
[[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(inviteStarted:)
  name:EVENT_UP_INVITE_STARTED object:nil];

// your handler:
- (void)inviteStarted:(NSNotification*)notification {
  // notification's userInfo contains the following keys:
  // DICT_ELEMENT_PROVIDER           = The provider (NSNumber*) on which the social action
  //                                   has started
  // DICT_ELEMENT_SOCIAL_ACTION_TYPE = The social action (NSNumber*) that started
  // DICT_ELEMENT_PAYLOAD            = An identification string (NSString*) that you can give
  //     when you initiate the social action operation and want to receive back upon starting

  // ... your game specific implementation here ...
}
```

### INVITE FINISHED

The event `EVENT_UP_INVITE_FINISHED` is triggered when an invitation has finished successfully.

``` objectivec
// observe the event:
[[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(inviteFinished:)
  name:EVENT_UP_INVITE_FINISHED object:nil];

// your handler:
- (void)inviteFinished:(NSNotification*)notification {
  // notification's userInfo contains the following keys:
  // DICT_ELEMENT_PROVIDER           = The provider (NSNumber*) on which the social action
  //                                   has finished
  // DICT_ELEMENT_SOCIAL_ACTION_TYPE = The social action (NSNumber*) that finished
  // DICT_ELEMENT_REQUEST_ID         = An identifier of created invite request
  // DICT_ELEMENT_INVITED_LIST       = A list of invited user's identifiers
  // DICT_ELEMENT_PAYLOAD            = An identification string (NSString*) that you can give
  //   when you initiate the social action operation and want to receive back upon completion

  // ... your game specific implementation here ...
}
```

### INVITE CANCELLED

The event `EVENT_UP_INVITE_CANCELLED` is triggered when an invitation has been cancelled.

``` objectivec
// observe the event:
[[NSNotificationCenter defaultCenter] addObserver:self
  selector:@selector(inviteCancelled:) name:EVENT_UP_INVITE_CANCELLED object:nil];

// your handler:
- (void)inviteCancelled:(NSNotification*)notification {
  // notification's userInfo contains the following keys:
  // DICT_ELEMENT_PROVIDER           = The provider (NSNumber*) on which a social action was
  //                                   cancelled
  // DICT_ELEMENT_SOCIAL_ACTION_TYPE = The social action (NSNumber*) that was cancelled
  // DICT_ELEMENT_PAYLOAD            = An identification string (NSString*) that you can give
  // when you initiate the social action operation and want to receive back upon cancellation


  // ... your game specific implementation here ...
}
```

### INVITE FAILED

The event `EVENT_UP_INVITE_FAILED` is triggered when a social action has failed.

``` objectivec
// observe the event:
[[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(inviteFailed:)
  name:EVENT_UP_INVITE_FAILED object:nil];

// your handler:
- (void)inviteFailed:(NSNotification*)notification {
  // notification's userInfo contains the following keys:
  // DICT_ELEMENT_PROVIDER           = The provider (NSNumber*) on which the social action
  //                                   has failed
  // DICT_ELEMENT_SOCIAL_ACTION_TYPE = The social action (NSNumber*) that failed
  // DICT_ELEMENT_MESSAGE            = Description (NSString*) of the reason for failure
  // DICT_ELEMENT_PAYLOAD            = An identification string (NSString*) that you can give
  //      when you initiate the social action operation and want to receive back upon failure

  // ... your game specific implementation here ...
}
```

### GET LEADERBOARDS STARTED

The event `EVENT_UP_GET_LEADERBOARDS_STARTED` is triggered when fetching leaderboards has started.

``` objectivec
// observe the event:
[[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(getLeaderboardsStarted:)
  name:EVENT_UP_GET_LEADERBOARDS_STARTED object:nil];

// your handler:
- (void)getLeaderboardsStarted:(NSNotification*)notification {
  // notification's userInfo contains the following keys:
  // DICT_ELEMENT_PROVIDER           = The provider (NSNumber*) on which fetching will be performed  
  // DICT_ELEMENT_PAYLOAD            = An identification string (NSString*) that you can give
  //     when you initiate the social action operation and want to receive back upon starting

  // ... your game specific implementation here ...
}
```

### GET LEADERBOARDS FINISHED

The event `EVENT_UP_GET_LEADERBOARDS_FINISHED` is triggered when fetching leaderboards has finished successfully.

``` objectivec
// observe the event:
[[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(getLeaderboardsFinished:)
  name:EVENT_UP_GET_LEADERBOARDS_FINISHED object:nil];

// your handler:
- (void)getLeaderboardsFinished:(NSNotification*)notification {
  // notification's userInfo contains the following keys:
  // DICT_ELEMENT_PROVIDER           = The provider (NSNumber*) on which fetching was performed  
  // DICT_ELEMENT_LEADERBOARDS       = A list of leaderboards
  // DICT_ELEMENT_PAYLOAD            = An identification string (NSString*) that you can give
  //   when you initiate the social action operation and want to receive back upon completion

  // ... your game specific implementation here ...
}
```

### GET LEADERBOARDS FAILED

The event `EVENT_UP_GET_LEADERBOARDS_FAILED` is triggered when fetching leaderboards  was failed.

``` objectivec
// observe the event:
[[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(getLeaderboardsFailed:)
  name:EVENT_UP_GET_LEADERBOARDS_FAILED object:nil];

// your handler:
- (void)getLeaderboardsFailed:(NSNotification*)notification {
  // notification's userInfo contains the following keys:
  // DICT_ELEMENT_PROVIDER           = The provider (NSNumber*) on which fetching was performed  
  // DICT_ELEMENT_MESSAGE            = Description (NSString*) of the reason for failure
  // DICT_ELEMENT_PAYLOAD            = An identification string (NSString*) that you can give
  //      when you initiate the social action operation and want to receive back upon failure

  // ... your game specific implementation here ...
}
```

### GET SCORES STARTED

The event `EVENT_UP_GET_SCORES_STARTED` is triggered when fetching scores from the current leaderboard has started.

``` objectivec
// observe the event:
[[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(getScoresStarted:)
  name:EVENT_UP_GET_SCORES_STARTED object:nil];

// your handler:
- (void)getScoresStarted:(NSNotification*)notification {
  // notification's userInfo contains the following keys:
  // DICT_ELEMENT_PROVIDER           = The provider (NSNumber*) on which the get scores
  //                                   process started
  // DICT_ELEMENT_LEADERBOARD        = Source leaderboard(Leaderboard*)
  // DICT_ELEMENT_FROM_START         = Should we reset pagination or request the next page
  // DICT_ELEMENT_PAYLOAD            = An identification string (NSString*) that you can give
  //      when you initiate the get scores operation and want to receive back upon starting

  // ... your game specific implementation here ...
}
```

### GET SCORES FINISHED

The event `EVENT_UP_GET_SCORES_FINISHED` is triggered when fetching scores from the current leaderboard has finished successfully.

``` objectivec
// observe the event:
[[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(getScoresFinished:)
  name:EVENT_UP_GET_SCORES_FINISHED object:nil];

// your handler:
- (void)getScoresFinished:(NSNotification*)notification {
  // notification's userInfo contains the following keys:
  // DICT_ELEMENT_PROVIDER           = The provider (NSNumber*) on which the get scores process
  //                                   finished
  // DICT_ELEMENT_LEADERBOARD        = Source leaderboard(Leaderboard*)
  // DICT_ELEMENT_SCORES             = An Array (NSArray*) of scores (Score*)
  // DICT_ELEMENT_PAYLOAD            = An identification string (NSString*) that you can give
  // DICT_ELEMENT_HAS_MORE           = if there are more items in pagination
  // when you initiate the get scores operation and want to receive back upon its completion


  // ... your game specific implementation here ...
}
```

### GET SCORES FAILED

The event `EVENT_UP_GET_SCORES_FAILED` is triggered when fetching scores from the current leaderboard has failed.

``` objectivec
// observe the event:
[[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(getScoresFailed:)
  name:EVENT_UP_GET_SCORES_FAILED object:nil];

// your handler:
- (void)getScoresFailed:(NSNotification*)notification {
  // notification's userInfo contains the following keys:
  // DICT_ELEMENT_PROVIDER           = The provider (NSNumber*) on which the get scores
  //                                   process has failed
  // DICT_ELEMENT_LEADERBOARD        = Source leaderboard(Leaderboard*)
  // DICT_ELEMENT_MESSAGE            = Description (NSString*) of the reason for failure
  // DICT_ELEMENT_FROM_START         = Should we reset pagination or request the next page
  // DICT_ELEMENT_PAYLOAD            = An identification string (NSString*) that you can give
  //       when you initiate the get scores operation and want to receive back upon failure

  // ... your game specific implementation here ...
}
```

### SUBMIT SCORE STARTED

The event `EVENT_UP_SUBMIT_SCORE_STARTED` is triggered when score submission for the current leaderboard has started.

``` objectivec
// observe the event:
[[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(submitScoreStarted:)
  name:EVENT_UP_SUBMIT_SCORE_STARTED object:nil];

// your handler:
- (void)submitScoreStarted:(NSNotification*)notification {
  // notification's userInfo contains the following keys:
  // DICT_ELEMENT_PROVIDER           = The provider (NSNumber*) on which the score submission
  //                                   process started
  // DICT_ELEMENT_LEADERBOARD        = Source leaderboard(Leaderboard*)  
  // DICT_ELEMENT_PAYLOAD            = An identification string (NSString*) that you can give
  //      when you initiate the submit score operation and want to receive back upon starting

  // ... your game specific implementation here ...
}
```

### SUBMIT SCORE FINISHED

The event `EVENT_UP_SUBMIT_SCORE_FINISHED` is triggered when score submission for the current leaderboard has finished successfully.

``` objectivec
// observe the event:
[[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(submitScoreFinished:)
  name:EVENT_UP_SUBMIT_SCORE_FINISHED object:nil];

// your handler:
- (void)submitScoreFinished:(NSNotification*)notification {
  // notification's userInfo contains the following keys:
  // DICT_ELEMENT_PROVIDER           = The provider (NSNumber*) on which the score submission process
  //                                   finished
  // DICT_ELEMENT_LEADERBOARD        = Source leaderboard(Leaderboard*)
  // DICT_ELEMENT_SCORE              = New score (Score*) instance was created
  // DICT_ELEMENT_PAYLOAD            = An identification string (NSString*) that you can give  
  // when you initiate the submit score operation and want to receive back upon its completion


  // ... your game specific implementation here ...
}
```

### SUBMIT SCORE FAILED

The event `EVENT_UP_SUBMIT_SCORE_FAILED` is triggered when score submission for the current leaderboard has failed.

``` objectivec
// observe the event:
[[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(submitScoreFailed:)
  name:EVENT_UP_SUBMIT_SCORE_FAILED object:nil];

// your handler:
- (void)submitScoreFailed:(NSNotification*)notification {
  // notification's userInfo contains the following keys:
  // DICT_ELEMENT_PROVIDER           = The provider (NSNumber*) on which the score submission
  //                                   process has failed
  // DICT_ELEMENT_LEADERBOARD        = Source leaderboard(Leaderboard*)
  // DICT_ELEMENT_MESSAGE            = Description (NSString*) of the reason for failure  
  // DICT_ELEMENT_PAYLOAD            = An identification string (NSString*) that you can give
  //       when you initiate the submit score operation and want to receive back upon failure

  // ... your game specific implementation here ...
}
```

### SHOW LEADERBOARDS

The event `EVENT_UP_SHOW_LEADERBOARDS` is triggered when leaderboards native dialog was shown.

``` objectivec
// observe the event:
[[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(leaderboardsShown:)
  name:EVENT_UP_SHOW_LEADERBOARDS object:nil];

// your handler:
- (void)leaderboardsShown:(NSNotification*)notification {
  // notification's userInfo contains the following keys:
  // DICT_ELEMENT_PROVIDER           = The provider (NSNumber*) that displays native leaderboards dialog      
  // DICT_ELEMENT_PAYLOAD            = An identification string (NSString*) that you can give
  //       when you initiate the operation and want to receive back upon failure

  // ... your game specific implementation here ...
}
```
