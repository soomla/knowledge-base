---
layout: "content"
image: "Modeling"
title: "Main Classes & Operations"
text: "Read descriptions of the various social entities cocos2dx-profile provides, and see usage examples of operations that can be done to the different entities."
position: 2
theme: 'platforms'
collection: 'cocos2djs_profile'
module: 'profile'
lang: 'js'
platform: 'cocos2dx'
---

# Main Classes & Operations

In this document you'll find descriptions of most of the main classes and interfaces of cocos2dx-profile. Some of these classes represent the different social elements used in the Profile module, while others contain functionality to perform social-related operations.

![alt text](/img/tutorial_img/soomla_diagrams/Profile.png "Profile Diagram")

<br>
Social actions allow you to entice social engagement by offering your users rewards in exchange for social interactions. For example, you can ask your users to like your page or post a status about your game, and give them various rewards, such as a badge of recognition or free virtual items that you normally sell for money/virtual currency. In this win-win situation your users will be pleased, and the network effect will increase the popularity of your game.

<div class="info-box">`Reward`s are a part of SOOMLA's core module and are used in many methods of Profile. Read about the different types of `Reward`s below.</div>

## Soomla.Models.Provider

This class lists the different social networks that exist today. Currently, SOOMLA supports Facebook, Twitter, and Google+.

## Soomla.Models.SocialActionType

`SocialActionType` represents various social actions that can be performed in social networks, such as posting a status or story, or uploading an image.

This class simply holds a string enumeration of the different social actions.

## Soomla.Models.UserProfile

This class represents a profile of a user from a social network (provider).

<div class="info-box">Note that each social provider (FB, G+, Twitter, etc..) gives access to different information, so you won't necessarily receive all the fields mentioned below.</div>

**A `UserProfile` contains the following elements:**

- `Provider`
- `ProfileId`
- `Email`
- `Username`
- `FirstName`
- `LastName`
- `AvatarLink`
- `Location`
- `Gender`
- `Language`
- `Birthday`
- `Extra` - a cocos2dx::__Dictionary contains additional info provided by social provider:
  - `Facebook` - access_token(string), permissions(array of strings), expiration_date(UNIX timestamp as number) - not available for Android;
  - `Twitter` - access_token(string);
  - `Google+` - access_token(string), refresh_token(string) - not available for Android, expiration_date(UNIX timestamp as number) - not available for Android.

## Soomla.SoomlaProfile

This is the main class that controls the entire SOOMLA Profile module. Use this class to perform various social and
authentication operations for users. After Profile initialized the instance of this class is available through
`Soomla.soomlaProfile`. The Profile module will work with the social and authentication plugins of the integrated social
provider (FB, G+, Twitter, etc..).

<div class="info-box">Most of the functions in this class call relevant functions from the social provider's SDK, and do NOT return a value, but rather fire appropriate events that contain the return values. Read more about [Event Handling](/cocos2dx/cpp/profile/Profile_Events).</div>

<br>
The diagram below depicts the flow that takes place when a `SoomlaProfile` function is called. In the diagram, the example function shown is `login`, but this principle holds for all functions.

![alt text](/img/tutorial_img/profile/functionFlow.png "Method Flow")

<br>
### `login / logout`

The `login` function will log the user into the specified provider, and will give the user a reward if one was provided.

Most of the social actions provided in Profile depend on the user being logged in. Therefore, upon successful login, you'll want to enable the rest of your social action buttons. For example, after the user is successfully logged in, you can display "Like" and "Post Status" buttons.

`logout` simply logs the user out of the specified provider. Don't forget to disable the social action buttons in your UI once your user is logged out.

``` js
// If the user clicks on the login button you provide, call the login method:
Soomla.soomlaProfile.login(
	Soomla.Models.Provider.FACEBOOK       // Provider
);
```

<br>
### `isLoggedIn`

As its name implies, this method checks if the user is logged in and returns a boolean value.

``` js
var isLoggedIn = Soomla.soomlaProfile.isLoggedIn(
	Soomla.Models.Provider.FACEBOOK       // Provider
);
...
if (isLoggedIn) {
    // Here you can (and should) set the screen to match the logged-in state.
    // For example display the logout button, like button, share button, etc.
}
```

<div class="info-box">If the user is not logged in, please notice that `isLoggedIn` will not log the user in, you'll need to call the `login` method yourself. </div>

<br>
### `like`

This function opens up the provider page to "like" (a web page in a browser), and grants the user the supplied reward. For example, give the user 100 coins for liking your page.

``` js
// A reward of 100 virtual coins
var likeReward = Soomla.Models.VirtualItemReward.create({
    itemId: 'like_reward',
    name: 'Like Reward',
    amount: 100,
    associatedItemId: COIN_CURRENCY_ITEM_ID
});

...

// If the user clicks the "Like" button provided in your game, call the
// like method and reward him/her with 100 coins.
Soomla.soomlaProfile.like(
	Soomla.Models.Provider.FACEBOOK,      // Provider
	'The.SOOMLA.Project',                 // Page to like
	likeReward                            // Reward for liking
);
```

<div class="info-box">Note that the user is given the reward just for clicking `Like` from the application. The `Like` function opens the page to like, but does not track if the user *actually* liked the page or not.</div>

<br>
### `updateStatus`

This function updates the user's status, which is simply a message, on the supplied social provider. Upon a successful update, the user will receive the supplied reward. For example, reward users that post a specific status with a `CCSingleUseVG`, such as a sword.

``` js
// A reward of a FREE sword
var statusReward = Soomla.Models.VirtualItemReward.create({
    itemId: 'status_reward',
    name: 'Status Reward',
    amount: 1,
    associatedItemId: SWORD_ITEM_ID
});

...

Soomla.soomlaProfile.updateStatus(
	Soomla.Models.Provider.FACEBOOK,      // Provider
	'I love SOOMLA! http://www.soom.la',  // Message to post as status
	statusReward                          // Reward for updating status
);
```

![alt text](/img/tutorial_img/profile/socialStatus.png "Update Status")

<br>
### `updateStatusWithConfirmation`

Works the same as `updateStatus` only here a confirmation dialog will be shown before the operation is performed.

``` js
Soomla.soomlaProfile.updateStatusWithConfirmation(
	Soomla.Models.Provider.FACEBOOK,      // Provider
	'I LOVE SOOMLA!  http://www.soom.la', // Message to post as status
	'',                                   // Payload
	null,                                 // Reward
	customMessage						 // Message to show in the confirmation dialog
);
```

<br>
### `updateStory`
This function posts a story (which is a detailed status) on the user's wall in the supplied social provider. Upon a successful update, the user will receive the supplied reward.

For example, once your user reaches a high score, you could display a popup that allows them to share their high score on Facebook with a click of a button. Once he/she shares the story, you can give them a reward such as a free character.

**NOTE:** This functionality is only **fully** supported in Facebook, since not all social network provide this type of customization in a post.

``` js
// A reward of a FREE Soombot character
var statusReward = Soomla.Models.VirtualItemReward.create({
    itemId: 'story_reward',
    name: 'Story Reward',
    amount: 1,
    associatedItemId: SOOMBOT_ITEM_ID
});

...

Soomla.soomlaProfile.updateStory(
	Soomla.Models.Provider.FACEBOOK,           // Provider
	'This is the story.',                      // Story message
	'The story of SOOMBOT (Profile Test App)', // Name (title of the story)
	'SOOMBOT Story',                           // Caption for the story
	'DESCRIPTION',                             // Description
	'http://about.soom.la/soombots',           // Link to post
	'http://about.soom.la/.../spockbot.png',   // Image
	storyReward                                // Reward for posting a story
);
```

![alt text](/img/tutorial_img/profile/socialStory.png "Post Story")

<br>
### `updateStoryWithConfirmation`

Works the same as `updateStory` only here a confirmation dialog will be shown before the operation is performed.

``` js
Soomla.soomlaProfile.updateStoryWithConfirmation(
	Soomla.Models.Provider.FACEBOOK,           // Provider
	'This is the story.',                       // Text of the story to post
	'The story of SOOMBOT (Profile Test App)',  // Name
	'SOOMBOT Story',                            // Caption
	'Hey! It's SOOMBOT Story',                 	// Description
	'http://about.soom.la/soombots',            // Link to post
	'http://about.soom.la/.../spockbot.png',    // Image URL
	'',                                         // Payload
	null,                                       // Reward
	customMessage 								// Message to show in the confirmation dialog
);
```


<br>
### `uploadImage`

This function uploads an image on the user's wall in the supplied social provider. Upon a successful upload, the user will receive the supplied reward.

For example, when your user finishes a level in your game, you can offer him/her to upload an image (perhaps a screenshot of the finished level) and receive a reward.

``` js
var imageReward = Soomla.Models.VirtualItemReward.create({
    itemId: 'imageReward_ID',
    name: 'Upload Image Reward',
    rewards: [likeReward, statusReward, storyReward],
    schedule: Soomla.Models.Schedule.createAnyTimeUnLimited()
});

...

// Assume that saveScreenShot() is a private method that returns
// a string representation of the current screenshot.
var screenshotPath = saveScreenshot();

Soomla.soomlaProfile.uploadImage(
	Soomla.Models.Provider.FACEBOOK,      // Provider
	'I love SOOMLA! http://www.soom.la',  // Message
	screenshotPath,                       // Name of image file path
	imageReward                           // Reward for uploading an image
);
```

![alt text](/img/tutorial_img/profile/socialUpload.png "Upload Image")

<div class="info-box">The image to upload should be on the device already; the path supplied needs to be a full path to the image on the device.</div>

<br>
### `uploadImageWithConfirmation`

Works the same as `uploadImage` only here a confirmation dialog will be shown before the operation is performed.

``` js
Soomla.soomlaProfile.uploadImageWithConfirmation(
	Soomla.Models.Provider.FACEBOOK,        // Provider
	'I love SOOMLA! http://www.soom.la',  	// Message to post with imag
	'someFileName',                       	// File path
	'',                                   	// Payload
	null,                          		    // Reward
	customMessage 							// Message to show in the confirmation dialog
);
```

<br>
### `getStoredUserProfile`

This function retrieves the user's profile for the given social provider from the **local device storage** (`getStoredUserProfile` does not call any social provider function, it retrieves and returns its information from the storage, contrary to what is depicted in the diagram at the beginning of this section). This function allows you to get user information even if the user is offline.

For example, you could use `getStoredUserProfile` to get the user's `FirstName`, and welcome him to the game.

``` js
var userProf = Soomla.soomlaProfile.getStoredUserProfile(
	Soomla.Models.Provider.FACEBOOK         // Provider
);

// Get the user's first name
userProf.firstName;
```

<div class="info-box">This functionality is only available if the user has already logged into the provider.</div>

<br>
### `getContacts`

This function retrieves a list of the user's contacts from the supplied provider.

<div class="info-box">Notice that some social providers (G+, Twitter) supply all of the user's contacts and some (FB) supply only the contacts that use your app.</div>

You could use `getContacts` to show your users a personalized screen where they can see which of their friends are also playing your game, or you could offer the contacts that don't play your game to download your game and receive some free coins.

``` js
Soomla.soomlaProfile.getContacts(
	Soomla.Models.Provider.FACEBOOK,      // Provider
	contactsReward,                       // Reward upon success of getting contacts
	fromStart,                            // Should we reset pagination or request the next page
	payload,                              // a String to receive when the function returns.
	&profileError                         // Used for error handling
);
```

#### Pagination

Note that the results will contain only part of the list. In order to get more items you should call the method again with `fromStart` param set to `false` (it's a default value for overloaded methods). You can use the following workflow:

```js
this.getContacts = function() {
    Soomla.addHandler(Soomla.ProfileConsts.EVENT_GET_CONTACTS_FINISHED, this.onGetContactsFinished, this);

    // request for the 1st page
    Soomla.soomlaProfile.getContacts(
	    Soomla.Models.Provider.FACEBOOK,    // Provider
        null,                               // no reward
        true                                // you definitely need the 1st page
    );
}

this.onGetContactsFinished = function (provider, contacts, payload, hasMore) {

    if (hasMore) {
        Soomla.soomlaProfile.getContacts(
            Soomla.Models.Provider.FACEBOOK,    // Provider
            null,                               // no reward
            false                               // going on with the pagination
        );
    } else {
        // no pages anymore
    }
}
```

<br>
### `getFeed`

This function Retrieves a list of the user's feed entries from the supplied provider. Upon a successful retrieval of
feed entries the user will be granted the supplied reward.

<div class="info-box">G+ does not support this.</div>

```js
Soomla.soomlaProfile.getFeed(
    Soomla.Models.Provider.FACEBOOK,      // Provider
	reward,                               // Reward upon success of getting of feed
	fromStart,                            // Should we reset pagination or request the next page
	payload                               // a String to receive when the function returns.
);
```

#### Pagination

Note that the results will contain only part of the list. In order to get more items you should call the method again with `fromStart` param set to `false` (it's a default value for overloaded methods). You can use the following workflow:

```js
this.getFeed = function() {
    Soomla.addHandler(Soomla.ProfileConsts.EVENT_GET_FEED_FINISHED, this.onGetFeedFinished, this);

    // request for the 1st page
    Soomla.soomlaProfile.getFeed(
	    Soomla.Models.Provider.FACEBOOK,    // Provider
        null,                               // no reward
        true                                // you definitely need the 1st page
    );
}

this.onGetFeedFinished = function (provider, contacts, payload, hasMore) {

    if (hasMore) {
        Soomla.soomlaProfile.getFeed(
            Soomla.Models.Provider.FACEBOOK,    // Provider
            null,                               // no reward
            false                               // going on with the pagination
        );
    } else {
        // no pages anymore
    }
}
```

<br>
### `openAppRatingPage`

`openAppRatingPage` conveniently opens your application's page on the platform store (for example on an iOS device it'll open your app's page in the App Store) so that it's simple to rate the app. You can offer your users to rate your app after they've completed a level successfully or have progressed significantly in your game.

``` js
Soomla.soomlaProfile.openAppRatingPage();
```

<br>
### `multiShare`

`multiShare` Shares text and/or image using native sharing functionality of your target platform.
The user will be shown a screen where he selects where he wants to share.

``` js
Soomla.soomlaProfile.multiShare(
    'I\'m happy. I can be shared everywhere.',
    'path/to/file/you/want/to/share'
);
```

## Auxiliary Model: Soomla.Models.Reward

A `Reward` is an entity which can be earned by the user for meeting certain criteria in game progress.

<div class="info-box">Note that `Reward` is a part of soomla-cocos2dx-core, and not part of the Profile module. However,
because `Reward`s are used very often throughout Profile, it's important that you are familiar with the different
`Reward` types.</div>

`Reward` itself cannot be instantiated, but there are many types of rewards, all explained below.

<br>
### Soomla.Models.VirtualItemReward

A specific type of `Reward` that you can use to give your users some amount of a virtual item. **For example:** Give users 100 coins (virtual currency) for liking your page.

<div class="info-box">`VirtualItemReward` is a part of `cocos2dx-store`. In case you want to use it, you'll need to import cocos2dx-store as well.</div>

``` js
var coinReward = Soomla.Models.VirtualItemReward.create({
    itemId: 'coinReward',                       // ID
    name: 'Coin Reward',                        // Name
    amount: 100,                                // Amount
    associatedItemId: 'coinCurrency_ID'         // Associated item ID
});
```

<br>
### Soomla.Models.BadgeReward

A specific type of `Reward` that represents a badge with an icon. **For example:** Give the user a badge reward for posting a status on his/her wall.

``` js
var coinReward = Soomla.Models.BadgeReward.create({
	itemId: 'badge_goldMedal',             // ID
	name: 'Gold Medal'                     // Name
);
```

<br>
### Soomla.Models.SequenceReward

A specific type of `Reward` that holds a list of other `Reward`s in a certain sequence. The rewards are given in ascending order. **For example:** In a Karate game the user can progress between belts and can be rewarded a sequence of: blue belt, purple belt, brown belt, and lastly, black belt.

``` js
// Assume that the below belts are BadgeRewards that have been defined.
var belts = [blueBelt, purpleBelt, brownBelt, blackBelt];

var beltReward = Soomla.Models.SequenceReward.create({
	itemId: 'beltReward',             // ID
	name: 'Gold Medal',               // Name
	rewards: belts                    // Sequence of rewards
});
```

<br>
### Soomla.Models.RandomReward

A specific type of `Reward` that holds a list of other `Reward`s. When this `Reward` is given, it randomly chooses a `Reward` from the list of `Reward`s it internally holds. **For example:** Give users a mystery box `Reward` for uploading an image, that grants him/her a random `Reward`.

``` js
var rewards = [rewardA, rewardB];

var mysteryReward = Soomla.Models.RandomReward.create({
	itemId: 'mysteryReward',             // ID
	name: 'Mystery Box Reward',          // Name
	rewards: rewards                     // Sequence of rewards
});
```
