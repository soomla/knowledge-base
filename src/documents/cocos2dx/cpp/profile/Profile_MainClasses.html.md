---
layout: "content"
image: "Modeling"
title: "Main Classes & Operations"
text: "Read descriptions of the various social entities cocos2dx-profile provides, and see usage examples of operations that can be done to the different entities."
position: 2
theme: 'platforms'
collection: 'cocos2dx_profile'
module: 'profile'
platform: 'cocos2dx'
---

# Main Classes & Operations

In this document you'll find descriptions of most of the main classes and interfaces of cocos2dx-profile. Some of these classes represent the different social elements used in the Profile module, while others contain functionality to perform social-related operations.

![alt text](/img/tutorial_img/soomla_diagrams/Profile.png "Profile Diagram")

<br>
Social actions allow you to entice social engagement by offering your users rewards in exchange for social interactions. For example, you can ask your users to like your page or post a status about your game, and give them various rewards, such as a badge of recognition or free virtual items that you normally sell for money/virtual currency. In this win-win situation your users will be pleased, and the network effect will increase the popularity of your game.

<div class="info-box">`CCReward`s are a part of SOOMLA's core module and are used in many methods of Profile. Read about the different types of `CCReward`s below.</div>

##CCUserProfileUtils <a href="https://github.com/soomla/cocos2dx-profile/blob/master/Soomla/CCUserProfileUtils.h" target="_blank"><img class="link-icon" src="/img/tutorial_img/linkImg.png"></a>

This class lists the different social networks that exist today. Currently, SOOMLA supports Facebook, Twitter, and Google+.

## CCSocialActionUtils <a href="https://github.com/soomla/cocos2dx-profile/blob/master/Soomla/CCSocialActionUtils.h" target="_blank"><img class="link-icon" src="/img/tutorial_img/linkImg.png"></a>

`CCSocialActionUtils` represents various social actions that can be performed in social networks, such as posting a status or story, or uploading an image.

This class simply holds a string enumeration of the different social actions.

## CCUserProfile <a href="https://github.com/soomla/cocos2dx-profile/blob/master/Soomla/domain/CCUserProfile.h" target="_blank"><img class="link-icon" src="/img/tutorial_img/linkImg.png"></a>

This class represents a profile of a user from a social network (provider).

<div class="info-box">Note that each social provider (FB, G+, Twitter, etc..) gives access to different information, so you won't necessarily receive all the fields mentioned below.</div>

**A `CCUserProfile` contains the following elements:**

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

## CCSoomlaProfile <a href="https://github.com/soomla/cocos2dx-profile/blob/master/Soomla/CCSoomlaProfile.h" target="_blank"><img class="link-icon" src="/img/tutorial_img/linkImg.png"></a>

This is the main class that controls the entire SOOMLA Profile module. Use this class to perform various social and authentication operations for users. The Profile module will work with the social and authentication plugins of the integrated social provider (FB, G+, Twitter, etc..).

<div class="info-box">Most of the functions in this class call relevant functions from the social provider's SDK, and do NOT return a value, but rather fire appropriate events that contain the return values. Read more about [Event Handling](/cocos2dx/cpp/profile/Profile_Events).</div>

<br>
The diagram below depicts the flow that takes place when a `CCSoomlaProfile` function is called. In the diagram, the example function shown is `login`, but this principle holds for all functions.

![alt text](/img/tutorial_img/profile/functionFlow.png "Method Flow")

<br>
### `login / logout`

The `login` function will log the user into the specified provider, and will give the user a reward if one was provided.

Most of the social actions provided in Profile depend on the user being logged in. Therefore, upon successful login, you'll want to enable the rest of your social action buttons. For example, after the user is successfully logged in, you can display "Like" and "Post Status" buttons.

`logout` simply logs the user out of the specified provider. Don't forget to disable the social action buttons in your UI once your user is logged out.

``` cpp
// If the user clicks on the login button you provide, call the login method:
soomla::CCSoomlaProfile::getInstance()->login(
	soomla::FACEBOOK,                     // Provider
	&profileError                         // Used for error handling
);

// If the user would like to logout:
soomla::CCSoomlaProfile::getInstance()->logout(
	soomla::FACEBOOK,                     // Provider
	&profileError                         // Used for error handling
);
```

<br>
### `isLoggedIn`

As its name implies, this method checks if the user is logged in and returns a boolean value.

``` cpp
bool isLoggedIn = soomla::CCSoomlaProfile::getInstance()->isLoggedIn(
	 soomla::FACEBOOK,                    // Provider
	 &profileError                        // Used for error handling
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

``` cpp
// A reward of 100 virtual coins
likeReward = soomla::CCVirtualItemReward::create(
	__String::create("like_reward"),
  __String::create("Like Reward"),
  __Integer::create(100),
	__String::create(COIN_CURRENCY_ITEM_ID)
);

...

// If the user clicks the "Like" button provided in your game, call the
// like method and reward him/her with 100 coins.
soomla::CCSoomlaProfile::getInstance()->like(
	soomla::FACEBOOK,                     // Provider
	"The.SOOMLA.Project",                 // Page to like
	likeReward,                           // Reward for liking
	&profileError                         // Used for error handling
);
```

<div class="info-box">Note that the user is given the reward just for clicking `Like` from the application. The `Like` function opens the page to like, but does not track if the user *actually* liked the page or not.</div>

<br>
### `updateStatus`

This function updates the user's status, which is simply a message, on the supplied social provider. Upon a successful update, the user will receive the supplied reward. For example, reward users that post a specific status with a `CCSingleUseVG`, such as a sword.

``` cpp
// A reward of a FREE sword
statusReward = soomla::CCVirtualItemReward::create(
	__String::create("status_reward"),
	__String::create("Status Reward"),
	__Integer::create(1),
	__String::create(SWORD_ITEM_ID)
);

...

soomla::CCSoomlaProfile::getInstance()->updateStatus(
	soomla::FACEBOOK,                     // Provider
	"I love SOOMLA! http://www.soom.la",  // Message to post as status
	statusReward,                         // Reward for updating status
	&profileError                         // Used for error handling
);
```

![alt text](/img/tutorial_img/profile/socialStatus.png "Update Status")

<br>
### `updateStory`
This function posts a story (which is a detailed status) on the user's wall in the supplied social provider. Upon a successful update, the user will receive the supplied reward.

For example, once your user reaches a high score, you could display a popup that allows them to share their high score on Facebook with a click of a button. Once he/she shares the story, you can give them a reward such as a free character.

**NOTE:** This functionality is only **fully** supported in Facebook, since not all social network provide this type of customization in a post.

``` cpp
// A reward of a FREE Soombot character
storyReward = soomla::CCVirtualItemReward::create(
	__String::create("story_reward"),
	__String::create("Story Reward"),
	__Integer::create(1),
	__String::create(SOOMBOT_ITEM_ID)
);

...

soomla::CCSoomlaProfile::getInstance()->updateStory(
	soomla::FACEBOOK,                          // Provider
	"This is the story.",                      // Story message
	"The story of SOOMBOT (Profile Test App)", // Name (title of the story)
	"SOOMBOT Story",                           // Caption for the story
	"DESCRIPTION",                             // Description
	"http://about.soom.la/soombots",           // Link to post
	"http://about.soom.la/.../spockbot.png",   // Image
	storyReward,                               // Reward for posting a story
	&profileError                              // Used for error handling
);
```

![alt text](/img/tutorial_img/profile/socialStory.png "Post Story")

<br>
### `uploadImage`

This function uploads an image on the user's wall in the supplied social provider. Upon a successful upload, the user will receive the supplied reward.

For example, when your user finishes a level in your game, you can offer him/her to upload an image (perhaps a screenshot of the finished level) and receive a reward.

``` cpp
__Array *rewards = __Array::create();
rewards->addObject(likeReward);
rewards->addObject(statusReward);
rewards->addObject(storyReward);

soomla::CCSchedule *schedule = soomla::CCSchedule::createAnyTimeUnlimited();

imageReward = soomla::CCRandomReward::create(
	__String::create("imageReward_ID"),
	__String::create("Upload Image Reward"),
	rewards,
	schedule
);

...

// Assume that saveScreenShot() is a private method that returns
// a string representation of the current screenshot.
std::string screenshotPath = saveScreenshot();

soomla::CCSoomlaProfile::getInstance()->uploadImage(
	soomla::FACEBOOK,                     // Provider
	"I love SOOMLA! http://www.soom.la",  // Message
	screenshotPath.c_str(),               // Name of image file path
	imageReward,                          // Reward for uploading an image
	&profileError                         // Used for error handling
);
```

![alt text](/img/tutorial_img/profile/socialUpload.png "Upload Image")

<div class="info-box">The image to upload should be on the device already; the path supplied needs to be a full path to the image on the device.</div>

<br>
### `getStoredUserProfile`

This function retrieves the user's profile for the given social provider from the **local device storage** (`getStoredUserProfile` does not call any social provider function, it retrieves and returns its information from the storage, contrary to what is depicted in the diagram at the beginning of this section). This function allows you to get user information even if the user is offline.

For example, you could use `getStoredUserProfile` to get the user's `FirstName`, and welcome him to the game.

``` cpp
soomla::CCUserProfile *userProf = soomla::CCSoomlaProfile::getInstance()->getStoredUserProfile(
	soomla::FACEBOOK,                     // Provider
	&profileError                         // Used for error handling
);

// Get the user's first name
userProf->getFirstName();
```

<div class="info-box">This functionality is only available if the user has already logged into the provider.</div>

<br>
### `getContacts`

This function retrieves a list of the user's contacts from the supplied provider.

<div class="info-box">Notice that some social providers (FB, G+, Twitter) supply all of the user's contacts and some supply only the contacts that use your app.</div>

You could use `getContacts` to show your users a personalized screen where they can see which of their friends are also playing your game, or you could offer the contacts that don't play your game to download your game and receive some free coins.

``` cpp
soomla::CCSoomlaProfile::getInstance()->getContacts(
	soomla::FACEBOOK,                     // Provider
	contactsReward,                       // Reward upon success of getting contacts
	&profileError                         // Used for error handling
);
```

<br>
### `openAppRatingPage`

`openAppRatingPage` conveniently opens your application's page on the platform store (for example on an iOS device it'll open your app's page in the App Store) so that it's simple to rate the app. You can offer your users to rate your app after they've completed a level successfully or have progressed significantly in your game.

``` cpp
soomla::CCSoomlaProfile::openAppRatingPage(&profileError);
```

## Auxiliary Model: CCReward [<img class="link-icon" src="/img/tutorial_img/linkImg.png">](https://github.com/soomla/soomla-cocos2dx-core/blob/master/Soomla/rewards/CCReward.h)

A `CCReward` is an entity which can be earned by the user for meeting certain criteria in game progress.

<div class="info-box">Note that `CCReward` is a part of soomla-cocos2dx-core, and not part of the Profile module. However, because `CCReward`s are used very often throughout Profile, it's important that you are familiar with the different `CCReward` types.</div>

`CCReward` itself cannot be instantiated, but there are many types of rewards, all explained below.

<br>
### CCVirtualItemReward [<img class="link-icon-small" src="/img/tutorial_img/linkImg.png">](https://github.com/soomla/cocos2dx-store/blob/master/Soomla/rewards/CCVirtualItemReward.h)

A specific type of `CCReward` that you can use to give your users some amount of a virtual item. **For example:** Give users 100 coins (virtual currency) for liking your page.

``` cpp
CCReward *coinReward = CCVirtualItemReward::create(
	CCString::create("coinReward"),         // ID
	CCString::create("Coin Reward"),        // Name
	CCString::create("coinCurrency_ID")     // Associated item ID
	CCInteger::create(100)                  // Amount
);
```

<br>
### CCBadgeReward [<img class="link-icon-small" src="/img/tutorial_img/linkImg.png">](https://github.com/soomla/soomla-cocos2dx-core/blob/master/Soomla/rewards/CCBadgeReward.h)

A specific type of `CCReward` that represents a badge with an icon. **For example:** Give the user a badge reward for posting a status on his/her wall.

``` cpp
CCReward *goldMedal = CCBadgeReward::create(
	CCString::create("badge_goldMedal"),   // ID
	CCString::create("Gold Medal"),        // Name
);
```

<br>
### CCSequenceReward [<img class="link-icon-small" src="/img/tutorial_img/linkImg.png">](https://github.com/soomla/soomla-cocos2dx-core/blob/master/Soomla/rewards/CCSequenceReward.h)

A specific type of `CCReward` that holds a list of other `CCReward`s in a certain sequence. The rewards are given in ascending order. **For example:** In a Karate game the user can progress between belts and can be rewarded a sequence of: blue belt, purple belt, brown belt, and lastly, black belt.

``` cpp
cocos2d::__Array *belts = cocos2d::__Array::create();
// Assume that the below belts are BadgeRewards that have been defined.
belts->addObject(blueBelt);
belts->addObject(purpleBelt);
belts->addObject(brownBelt);
belts->addObject(blackBelt);

CCReward *beltReward = CCSequenceReward::create(
	CCString::create("beltReward"),         // ID
	CCString::create("Belt Reward"),        // Name
	belts                                   // Sequence of rewards
);
```

<br>
### CCRandomReward [<img class="link-icon-small" src="/img/tutorial_img/linkImg.png">](https://github.com/soomla/soomla-cocos2dx-core/blob/master/Soomla/rewards/CCRandomReward.h)

A specific type of `CCReward` that holds a list of other `CCReward`s. When this `CCReward` is given, it randomly chooses a `CCReward` from the list of `CCReward`s it internally holds. **For example:** Give users a mystery box `CCReward` for uploading an image, that grants him/her a random `Reward`.

``` cpp
cocos2d::__Array *rewards = cocos2d::__Array::create();
rewards->addObject(rewardA);
rewards->addObject(rewardB);

CCReward *mysteryReward = CCRandomReward::create(
	CCString::create("mysteryReward"),        // ID
	CCString::create("Mystery Box Reward"),   // Name
	rewards                                   // Rewards to choose from
);
```
