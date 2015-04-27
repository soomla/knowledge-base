---
layout: "content"
image: "Events"
title: "Events"
text: "Learn how to observe and handle social events triggered by cocos2dx-profile to customize your game-specific behavior."
position: 3
theme: 'platforms'
collection: 'cocos2dx_profile'
module: 'profile'
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

SOOMLA uses the Cocos2d-x facilities to dispatch its own custom events.
The names of such events are defined in `CCProfileConsts`, the meta-data of the event is held in a `__Dictionary`. You can subscribe to any event from anywhere in your code.

When handling the event you can extract meta-data from the dictionary using pre-defined keys, which are also defined in `CCProfileConsts`.

### Cocos2d-x v3 Events

** Subscribing **

Subscribe to events through the Cocos2d-x  [`EventDispatcher`](http://www.cocos2d-x.org/wiki/EventDispatcher_Mechanism):

```cpp
cocos2d::Director::getInstance()->getEventDispatcher()->addCustomEventListener(soomla::CCProfileConsts::EVENT_LOGIN_FINISHED, CC_CALLBACK_1(ExampleScene::onLoginFinished, this));
```

** Handling **

Handle the event through your own custom function:

```cpp
void ExampleScene::onLoginFinished(cocos2d::EventCustom *event) {
  cocos2d::__Dictionary *eventData = (cocos2d::__Dictionary *)event->getUserData();
  // ... get meta-data information from eventData
}
```


### Cocos2d-x v2 Events

** Subscribing **

Subscribe to events through the Cocos2d-x `CCNotificationCenter`:

```cpp
cocos2d::CCNotificationCenter::sharedNotificationCenter()->addObserver(this, callfuncO_selector(ExampleScene::onLoginFinished), soomla::CCProfileConsts::EVENT_LOGIN_FINISHED, NULL);
```

** Handling **

Handle the event through your own custom function:

```cpp
void ExampleScene::onLoginFinished(cocos2d::CCDictionary *eventData) {
  // ... get meta-data information from eventData
}
```

## Profile Events

Below we provide a list of all events in Profile, their handling examples are written for v3, but it's easy to convert them to v2 dialect, see how above.

### EVENT_PROFILE_INITIALIZED

This event is triggered when Soomla Profile has been initialized.

```cpp
Director::getInstance()->getEventDispatcher()->addCustomEventListener(CCProfileConsts::EVENT_PROFILE_INITIALIZED, CC_CALLBACK_1(Example::onProfileInitialized, this));

void Example::onProfileInitialized(EventCustom *event) {
    // ... your game specific implementation here ...
}
```

### EVENT_USER_RATING

This event is triggered when the page for rating your app is opened.

```cpp
Director::getInstance()->getEventDispatcher()->addCustomEventListener(CCProfileConsts::EVENT_USER_RATING, CC_CALLBACK_1(Example::onUserRatingEvent, this));

void Example::onUserRatingEvent(EventCustom *event) {
    // ... your game specific implementation here ...
}
```

### EVENT_USER_PROFILE_UPDATED

This event is triggered when the user profile has been updated, after login.

```cpp
Director::getInstance()->getEventDispatcher()->addCustomEventListener(CCProfileConsts::EVENT_USER_PROFILE_UPDATED, CC_CALLBACK_1(Example::onUserProfileUpdatedEvent, this));

void Example::onUserProfileUpdatedEvent(EventCustom *event) {
  // DICT_ELEMENT_USER_PROFILE - the user's profile from the logged in provider

  __Dictionary *eventData = (__Dictionary *)event->getUserData();
  CCUserProfile *userProfile = dynamic_cast<CCUserProfile *>(eventData->objectForKey(CCProfileConsts::DICT_ELEMENT_USER_PROFILE));

  // ... your game specific implementation here ...
}
```

### EVENT_LOGIN_STARTED

This event is triggered when logging into the social provider has started.

```cpp
Director::getInstance()->getEventDispatcher()->addCustomEventListener(CCProfileConsts::EVENT_LOGIN_STARTED, CC_CALLBACK_1(Example::onLoginStarted, this));

void Example::onLoginStarted(EventCustom *event) {
  // DICT_ELEMENT_PROVIDER - the social provider
  // DICT_ELEMENT_PAYLOAD  - an identification string that you can give when you initiate
  //      the login operation and want to receive back upon starting

  __Dictionary *eventData = (__Dictionary *)event->getUserData();
  CCProvider provider = CCProvider((dynamic_cast<__Integer *>(eventData->objectForKey(CCProfileConsts::DICT_ELEMENT_PROVIDER)))->getValue());
  __String *payload = dynamic_cast<__String *>(eventData->objectForKey(CCProfileConsts::DICT_ELEMENT_PAYLOAD));

  // ... your game specific implementation here ...
}
```

### EVENT_LOGIN_FINISHED

This event is triggered when logging into the social provider has finished **successfully**.

```cpp
Director::getInstance()->getEventDispatcher()->addCustomEventListener(CCProfileConsts::EVENT_LOGIN_FINISHED, CC_CALLBACK_1(Example::onLoginFinished, this));

void Example::onLoginFinished(EventCustom *event) {
  // DICT_ELEMENT_USER_PROFILE - the user's profile from the logged in provider
  // DICT_ELEMENT_PAYLOAD      - an identification string that you can give when you initiate
  //      the login operation and want to receive back upon its completion

  __Dictionary *eventData = (__Dictionary *)event->getUserData();
  CCUserProfile *userProfile = dynamic_cast<CCUserProfile *>(eventData->objectForKey(CCProfileConsts::DICT_ELEMENT_USER_PROFILE));
  __String *payload = dynamic_cast<__String *>(eventData->objectForKey(CCProfileConsts::DICT_ELEMENT_PAYLOAD));

  // ... your game specific implementation here ...
}
```

### EVENT_LOGIN_CANCELLED

This event is triggered when logging into the social provider has been cancelled.

```cpp
Director::getInstance()->getEventDispatcher()->addCustomEventListener(CCProfileConsts::EVENT_LOGIN_CANCELLED, CC_CALLBACK_1(Example::onLoginCancelledEvent, this));

void Example::onLoginCancelledEvent(EventCustom *event) {
  // DICT_ELEMENT_PROVIDER - the social provider
  // DICT_ELEMENT_PAYLOAD  - an identification string that you can give when you initiate
  //      the login operation and want to receive back upon cancellation

  __Dictionary *eventData = (__Dictionary *)event->getUserData();
  CCProvider provider = CCProvider((dynamic_cast<__Integer *>(eventData->objectForKey(CCProfileConsts::DICT_ELEMENT_PROVIDER)))->getValue());
  __String *payload = dynamic_cast<__String *>(eventData->objectForKey(CCProfileConsts::DICT_ELEMENT_PAYLOAD));

  // ... your game specific implementation here ...
}
```

### EVENT_LOGIN_FAILED

This event is triggered when logging into the social provider has failed.

```cpp
Director::getInstance()->getEventDispatcher()->addCustomEventListener(CCProfileConsts::EVENT_LOGIN_FAILED, CC_CALLBACK_1(Example::onLoginFailed, this));

void Example::onLoginFailed(EventCustom *event) {
  // DICT_ELEMENT_PROVIDER - the social provider
  // DICT_ELEMENT_MESSAGE  - the failure message
  // DICT_ELEMENT_PAYLOAD  - an identification string that you can give when you initiate
  //      the login operation and want to receive back upon failure

  __Dictionary *eventData = (__Dictionary *)event->getUserData();
  CCProvider provider = CCProvider((dynamic_cast<__Integer *>(eventData->objectForKey(CCProfileConsts::DICT_ELEMENT_PROVIDER)))->getValue());
  __String *errorDescription = dynamic_cast<__String *>(eventData->objectForKey(CCProfileConsts::DICT_ELEMENT_MESSAGE));
  __String *payload = dynamic_cast<__String *>(eventData->objectForKey(CCProfileConsts::DICT_ELEMENT_PAYLOAD));

  // ... your game specific implementation here ...
}
```

### EVENT_LOGOUT_STARTED

This event is triggered when logging out of the social provider has started.

```cpp
Director::getInstance()->getEventDispatcher()->addCustomEventListener(CCProfileConsts::EVENT_LOGOUT_STARTED, CC_CALLBACK_1(Example::onLogoutStarted, this));

void Example::onLogoutStarted(EventCustom *event) {
  // DICT_ELEMENT_PROVIDER - the social provider

  __Dictionary *eventData = (__Dictionary *)event->getUserData();
  CCProvider provider = CCProvider((dynamic_cast<__Integer *>(eventData->objectForKey(CCProfileConsts::DICT_ELEMENT_PROVIDER)))->getValue());

  // ... your game specific implementation here ...
}
```

### EVENT_LOGOUT_FINISHED

This event is triggered when logging out of the social provider has finished **successfully**.

```cpp
Director::getInstance()->getEventDispatcher()->addCustomEventListener(CCProfileConsts::EVENT_LOGOUT_FINISHED, CC_CALLBACK_1(Example::onLogoutFinished, this));

void Example::onLogoutFinished(EventCustom *event) {
  // DICT_ELEMENT_PROVIDER - the social provider

  __Dictionary *eventData = (__Dictionary *)event->getUserData();
  CCProvider provider = CCProvider((dynamic_cast<__Integer *>(eventData->objectForKey(CCProfileConsts::DICT_ELEMENT_PROVIDER)))->getValue());

  // ... your game specific implementation here ...
}
```

### EVENT_LOGOUT_FAILED

This event is triggered when logging out of the social provider has failed.

```cpp
Director::getInstance()->getEventDispatcher()->addCustomEventListener(CCProfileConsts::EVENT_LOGOUT_FAILED, CC_CALLBACK_1(Example::onLogoutFailed, this));

void Example::onLogoutFailed(EventCustom *event) {
  // DICT_ELEMENT_PROVIDER - the social provider
  // DICT_ELEMENT_MESSAGE  - the failure message

  __Dictionary *eventData = (__Dictionary *)event->getUserData();
  CCProvider provider = CCProvider((dynamic_cast<__Integer *>(eventData->objectForKey(CCProfileConsts::DICT_ELEMENT_PROVIDER)))->getValue());
  __String *errorDescription = dynamic_cast<__String *>(eventData->objectForKey(CCProfileConsts::DICT_ELEMENT_MESSAGE));

  // ... your game specific implementation here ...
}
```

### EVENT_SOCIAL_ACTION_STARTED

This event is triggered when a social action has started.

```cpp
Director::getInstance()->getEventDispatcher()->addCustomEventListener(CCProfileConsts::EVENT_SOCIAL_ACTION_STARTED, CC_CALLBACK_1(Example::onSocialActionStarted, this));

void Example::onSocialActionStarted(EventCustom *event) {
  // DICT_ELEMENT_PROVIDER            - the social provider
  // DICT_ELEMENT_SOCIAL_ACTION_TYPE  - the social action (like, post status, etc..)
  //      that started
  // DICT_ELEMENT_PAYLOAD             - an identification string that you can give when you
  //      initiate the social action operation and want to receive back upon starting

  __Dictionary *eventData = (__Dictionary *)event->getUserData();
  CCProvider provider = CCProvider((dynamic_cast<__Integer *>(eventData->objectForKey(CCProfileConsts::DICT_ELEMENT_PROVIDER)))->getValue());
  CCSocialActionType socialActionType = CCSocialActionType((dynamic_cast<__Integer *>(eventData->objectForKey(CCProfileConsts::DICT_ELEMENT_SOCIAL_ACTION_TYPE)))->getValue());
  __String *payload = dynamic_cast<__String *>(eventData->objectForKey(CCProfileConsts::DICT_ELEMENT_PAYLOAD));

  // ... your game specific implementation here ...
}
```

### EVENT_SOCIAL_ACTION_FINISHED

This event is triggered when a social action has finished **successfully**.

```cpp
Director::getInstance()->getEventDispatcher()->addCustomEventListener(CCProfileConsts::EVENT_SOCIAL_ACTION_FINISHED, CC_CALLBACK_1(Example::onSocialActionFinished, this));

void Example::onSocialActionFinished(EventCustom *event) {
  // DICT_ELEMENT_PROVIDER            - the social provider
  // DICT_ELEMENT_SOCIAL_ACTION_TYPE  - the social action (like, post status, etc..)
  //      that finished
  // DICT_ELEMENT_PAYLOAD             - an identification string that you can give when you
  //      initiate the social action operation and want to receive back upon its completion

  __Dictionary *eventData = (__Dictionary *)event->getUserData();
  CCProvider provider = CCProvider((dynamic_cast<__Integer *>(eventData->objectForKey(CCProfileConsts::DICT_ELEMENT_PROVIDER)))->getValue());
  CCSocialActionType socialActionType = CCSocialActionType((dynamic_cast<__Integer *>(eventData->objectForKey(CCProfileConsts::DICT_ELEMENT_SOCIAL_ACTION_TYPE)))->getValue());
  __String *payload = dynamic_cast<__String *>(eventData->objectForKey(CCProfileConsts::DICT_ELEMENT_PAYLOAD));

  // ... your game specific implementation here ...
}
```

### EVENT_SOCIAL_ACTION_FAILED

This event is triggered when a social action has failed.

```cpp
Director::getInstance()->getEventDispatcher()->addCustomEventListener(CCProfileConsts::EVENT_SOCIAL_ACTION_FAILED, CC_CALLBACK_1(Example::onSocialActionFailed, this));

void Example::onSocialActionFailed(EventCustom *event) {
  // DICT_ELEMENT_PROVIDER            - the social provider
  // DICT_ELEMENT_SOCIAL_ACTION_TYPE  - the social action (like, post status, etc..) that failed
  // DICT_ELEMENT_MESSAGE             - the failure message
  // DICT_ELEMENT_PAYLOAD             - an identification string that you can give when you
  //      initiate the social action operation and want to receive back upon failure

  __Dictionary *eventData = (__Dictionary *)event->getUserData();
  CCProvider provider = CCProvider((dynamic_cast<__Integer *>(eventData->objectForKey(CCProfileConsts::DICT_ELEMENT_PROVIDER)))->getValue());
  CCSocialActionType socialActionType = CCSocialActionType((dynamic_cast<__Integer *>(eventData->objectForKey(CCProfileConsts::DICT_ELEMENT_SOCIAL_ACTION_TYPE)))->getValue());
  __String *errorDescription = dynamic_cast<__String *>(eventData->objectForKey(CCProfileConsts::DICT_ELEMENT_MESSAGE));
  __String *payload = dynamic_cast<__String *>(eventData->objectForKey(CCProfileConsts::DICT_ELEMENT_PAYLOAD));

  // ... your game specific implementation here ...
}
```

### EVENT_GET_CONTACTS_STARTED

This event is triggered when fetching the contacts from the social provider has started.

```cpp
Director::getInstance()->getEventDispatcher()->addCustomEventListener(CCProfileConsts::EVENT_GET_CONTACTS_STARTED, CC_CALLBACK_1(Example::onGetContactsStarted, this));

void Example::onGetContactsStarted(EventCustom *event) {
  // DICT_ELEMENT_PROVIDER - the social provider
  // DICT_ELEMENT_PAYLOAD - an identification string that you can give when you initiate
  //      the get contacts operation and want to receive back upon starting

  __Dictionary *eventData = (__Dictionary *)event->getUserData();
  CCProvider provider = CCProvider((dynamic_cast<__Integer *>(eventData->objectForKey(CCProfileConsts::DICT_ELEMENT_PROVIDER)))->getValue());
  __String *payload = dynamic_cast<__String *>(eventData->objectForKey(CCProfileConsts::DICT_ELEMENT_PAYLOAD));

  // ... your game specific implementation here ...
}
```

### EVENT_GET_CONTACTS_FINISHED

This event is triggered when fetching the contacts from the social provider has finished **successfully**.

```cpp
Director::getInstance()->getEventDispatcher()->addCustomEventListener(CCProfileConsts::EVENT_GET_CONTACTS_FINISHED, CC_CALLBACK_1(Example::onGetContactsFinished, this));

void Example::onGetContactsFinished(EventCustom *event) {
  // DICT_ELEMENT_PROVIDER - the social provider
  // DICT_ELEMENT_PAYLOAD  - an identification string that you can give when you initiate
  //      the get contacts operation and want to receive back upon its completion
  // DICT_ELEMENT_CONTACTS - a List of user profiles that have been fetched in the get
  //      contacts operation

  __Dictionary *eventData = (__Dictionary *)event->getUserData();
  CCProvider provider = CCProvider((dynamic_cast<__Integer *>(eventData->objectForKey(CCProfileConsts::DICT_ELEMENT_PROVIDER)))->getValue());
  __String *payload = dynamic_cast<__String *>(eventData->objectForKey(CCProfileConsts::DICT_ELEMENT_PAYLOAD));
  __Array *contactsArray = dynamic_cast<__Array *>(eventData->objectForKey(CCProfileConsts::DICT_ELEMENT_CONTACTS));

  // ... your game specific implementation here ...
}
```

### EVENT_GET_CONTACTS_FAILED

This event is triggered when fetching the contacts from the social provider has failed.

```cpp
Director::getInstance()->getEventDispatcher()->addCustomEventListener(CCProfileConsts::EVENT_GET_CONTACTS_FAILED, CC_CALLBACK_1(Example::onGetContactsFailed, this));

void Example::onGetContactsFailed(EventCustom *event) {
  // DICT_ELEMENT_PROVIDER - the social provider
  // DICT_ELEMENT_MESSAGE  - the failure message
  // DICT_ELEMENT_PAYLOAD  - an identification string that you can give when you initiate
  //      the get contacts operation and want to receive back upon failure

  __Dictionary *eventData = (__Dictionary *)event->getUserData();
  CCProvider provider = CCProvider((dynamic_cast<__Integer *>(eventData->objectForKey(CCProfileConsts::DICT_ELEMENT_PROVIDER)))->getValue());
  __String *errorDescription = dynamic_cast<__String *>(eventData->objectForKey(CCProfileConsts::DICT_ELEMENT_MESSAGE));
  __String *payload = dynamic_cast<__String *>(eventData->objectForKey(CCProfileConsts::DICT_ELEMENT_PAYLOAD));

  // ... your game specific implementation here ...
}
```

### EVENT_GET_FEED_STARTED

This event is triggered when fetching the feed from the social provider has started.

```cpp
Director::getInstance()->getEventDispatcher()->addCustomEventListener(CCProfileConsts::EVENT_GET_FEED_STARTED, CC_CALLBACK_1(Example::onGetFeedStarted, this));

void Example::onGetFeedStarted(EventCustom *event) {
  // DICT_ELEMENT_PROVIDER is the social provider
  // DICT_ELEMENT_PAYLOAD  - an identification string that you can give when you initiate
  //      the get feed operation and want to receive back upon failure

  __Dictionary *eventData = (__Dictionary *)event->getUserData();
  CCProvider provider = CCProvider((dynamic_cast<__Integer *>(eventData->objectForKey(CCProfileConsts::DICT_ELEMENT_PROVIDER)))->getValue());
  __String *payload = dynamic_cast<__String *>(eventData->objectForKey(CCProfileConsts::DICT_ELEMENT_PAYLOAD));

  // ... your game specific implementation here ...
}
```

### EVENT_GET_FEED_FINISHED

This event is triggered when fetching the feed from the social provider has finished **successfully**.

```cpp
Director::getInstance()->getEventDispatcher()->addCustomEventListener(CCProfileConsts::EVENT_GET_FEED_FINISHED, CC_CALLBACK_1(Example::onGetFeedFinished, this));

void Example::onGetFeedFinished(EventCustom *event) {
  // DICT_ELEMENT_PROVIDER - the social provider
  // DICT_ELEMENT_PAYLOAD  - an identification string that you can give when you initiate
  //      the get feed operation and want to receive back upon its completion
  // DICT_ELEMENT_FEEDS    - the user's feed that has been fetched in the get feed operation

  __Dictionary *eventData = (__Dictionary *)event->getUserData();
  CCProvider provider = CCProvider((dynamic_cast<__Integer *>(eventData->objectForKey(CCProfileConsts::DICT_ELEMENT_PROVIDER)))->getValue());
  __String *payload = dynamic_cast<__String *>(eventData->objectForKey(CCProfileConsts::DICT_ELEMENT_PAYLOAD));
  __Array *feedList = dynamic_cast<__Array *>(eventData->objectForKey(CCProfileConsts::DICT_ELEMENT_FEEDS));

  // ... your game specific implementation here ...
}
```

### EVENT_GET_FEED_FAILED

This event is triggered when fetching the feed from the social provider has failed.

```cpp
Director::getInstance()->getEventDispatcher()->addCustomEventListener(CCProfileConsts::EVENT_GET_FEED_FAILED, CC_CALLBACK_1(Example::onGetFeedFailed, this));

void Example::onGetFeedFailed(EventCustom *event) {
  // DICT_ELEMENT_PROVIDER - the social provider
  // DICT_ELEMENT_MESSAGE  - the failure message
  // DICT_ELEMENT_PAYLOAD  - an identification string that you can give when you initiate
  //      the get feed operation and want to receive back upon failure

  __Dictionary *eventData = (__Dictionary *)event->getUserData();
  CCProvider provider = CCProvider((dynamic_cast<__Integer *>(eventData->objectForKey(CCProfileConsts::DICT_ELEMENT_PROVIDER)))->getValue());
  __String *errorDescription = dynamic_cast<__String *>(eventData->objectForKey(CCProfileConsts::DICT_ELEMENT_MESSAGE));
  __String *payload = dynamic_cast<__String *>(eventData->objectForKey(CCProfileConsts::DICT_ELEMENT_PAYLOAD));

  // ... your game specific implementation here ...
}
```
