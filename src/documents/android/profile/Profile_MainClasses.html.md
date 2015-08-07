---
layout: "content"
image: "Modeling"
title: "Main Classes & Operations"
text: "Read descriptions of the various social entities android-profile provides, and see usage examples of operations that can be done to the different entities."
position: 2
theme: 'platforms'
collection: 'android_profile'
module: 'profile'
platform: 'android'
---

# Main Classes & Operations

In this document you'll find descriptions of most of the main classes and interfaces of android-profile. Some of these classes represent the different social elements used in the Profile module, while others contain functionality to perform social-related operations.

![alt text](/img/tutorial_img/soomla_diagrams/Profile.png "Profile Diagram")

<br>
Social actions allow you to entice social engagement by offering your users rewards in exchange for social interactions. For example, you can ask your users to like your page or share a specific status about your game, and give them various rewards, such as a badge of recognition or free virtual items that you normally sell for money/virtual currency. In this win-win situation your users will be pleased, and the network effect will increase the popularity of your game.

<div class="info-box">`Reward`s are a part of SOOMLA's core module and are used in many functions of Profile. Read about the different types of `Reward`s [below](#auxiliary-model-reward).</div>

##IProvider <a href="https://github.com/soomla/android-profile/blob/master/SoomlaAndroidProfile/src/com/soomla/profile/domain/IProvider.java" target="_blank"><img class="link-icon" src="/img/tutorial_img/linkImg.png"></a>

This class represents the different social networks that exist today. Currently, SOOMLA supports Facebook, Twitter, and Google+.

The `IProvider` class simply holds a string enumeration of the various providers that are currently available, as well as those that will be available in the future.

## SocialActionType <a href="https://github.com/soomla/android-profile/blob/master/SoomlaAndroidProfile/src/com/soomla/profile/social/ISocialProvider.java#L131" target="_blank"><img class="link-icon" src="/img/tutorial_img/linkImg.png"></a>

A part of the `ISocialProvider` class, this enum represents various social actions that can be performed in social networks, such as posting a status or story, uploading an image, etc.

## UserProfile <a href="https://github.com/soomla/android-profile/blob/master/SoomlaAndroidProfile/src/com/soomla/profile/domain/UserProfile.java" target="_blank"><img class="link-icon" src="/img/tutorial_img/linkImg.png"></a>

This class holds information about a user for a specific `IProvider`.

**A `UserProfile` contains the following elements:**

- `IProvider`
- `ProfileId`
- `Email`
- `Username`
- `FirstName`
- `LastName`
- `Extra` - a map contains additional info provided by social provider:
  - `Facebook` - access_token(String), permissions(JSONArray of Strings);
  - `Twitter` - access_token(String);
  - `Google+` - access_token(String).

## SoomlaProfile <a href="https://github.com/soomla/android-profile/blob/master/SoomlaAndroidProfile/src/com/soomla/profile/SoomlaProfile.java" target="_blank"><img class="link-icon" src="/img/tutorial_img/linkImg.png"></a>

This is the main class that controls the entire SOOMLA Profile module. Use this class to perform various social and authentication operations for users.

**NOTE:** Most of the functions in this class call relevant functions from the social provider's SDK, and do NOT return a value, but rather fire appropriate events that contain the return values. Read more about [Events](/android/profile/Profile_Events).

The diagram below depicts the flow that takes place when a `SoomlaProfile` function is called. In the diagram, the example function shown is `login`, but this principle holds for all functions.

![alt text](/img/tutorial_img/profile/functionFlowAndroid.png "Function Flow")

<br>
### `initialize`

`SoomlaProfile` is the class that needs to be initialized in order to begin using Profile. This is covered in android-profile's [Getting Started](/android/profile/Profile_GettingStarted) tutorial.

``` java
SoomlaProfile.getInstance().initialize();
```

### `login / logout`

The `login` function will log the user into the specified provider, and will give the user a reward if one was provided.

Most of the social actions provided in Profile depend on the user being logged in. Therefore, upon successful login (which means the `onLoginFinished` event has fired), you'll want to enable the rest of your social action buttons. For example, after the user is successfully logged in, you can display "Like" and "Post Status" buttons.

`logout` simply logs the user out of the specified provider. Don't forget to disable the social action buttons in your UI once your user is logged out.

``` java
// If the user clicks on the login button you provide, call the login function:
SoomlaProfile.getInstance().login(
	this                                     // Activity
	IProvider.Provider.FACEBOOK,             // Provider
	gameLoginReward                          // Reward
);

// If the user would like to logout:
SoomlaProfile.getInstance().logout(
	IProvider.Provider.FACEBOOK,             // Provider
);
```

<br>
### `isLoggedIn`

Checks if the user is logged in and returns a boolean value.

``` java
if (SoomlaProfile.getInstance().isLoggedIn(this, IProvider.Provider.FACEBOOK) {
    // Here you can (and should) set the screen to match the logged-in state.
    // For example display the logout button, like button, share button, etc.
}
```

<div class="info-box">If the user is not logged in, please notice that `isLoggedIn` will not log the user in, you'll need to call the `login` function yourself.</div>

<br>
### `like`

This function opens up the provider page to "like" (a web page in a browser), and grants the user the supplied reward. For example, give the user 100 coins for liking your page.

``` java
// Virtual currency, coin
VirtualCurrency coin = new VirtualCurrency("Coin", "", "coin_ID");

// A reward of 100 virtual coins.
Reward hundredCoinReward = new VirtualItemReward("hundredCoinReward_ID", "100 Coin Reward", 100, "coin_ID");

// If the user clicks the "Like" button provided in your game,
// call the Like function and reward him/her with 100 coins.
SoomlaProfile.getInstance().like(
	this,                              // Activity
	IProvider.Provider.FACEBOOK,       // Provider
	"The.Soomla.Project",              // Page Id to like
	hundredCoinReward                  // Reward
);
```

The 3rd parameter received by the `like` function is a pageId. This can either be a page-id or page-name.

<div class="info-box">Note that the user is given the reward just for clicking `like` from the application. The `like` function opens the page to like, but does not track if the user *actually* liked the page or not.</div>

<br>
### `updateStatus`

This function updates the user's status, which is simply a message, on the supplied social provider. Upon a successful update, the user will receive the supplied reward. For example, reward users that post a specific status with a `SingleUseVG`, such as a sword.

``` java
// Virtual currency, coin
VirtualCurrency coin = new VirtualCurrency("Name", "", "coin_ID");

// A single-use virtual item, sword, that can be purchased for 200 virtual coins.
VirtualGood sword = new SingleUseVG(
	"Sword", "", "sword_ID",
	new PurchaseWithVirtualItem(coin.getItemId(), 200));

// A reward of 1 FREE sword (no need to purchase it for 200 coins!)
Reward swordReward = new VirtualItemReward(
	"swordReward_ID", "Sword Reward", 1,
	sword.getItemId());

// When a user updates his/her status, they'll receive a statusReward (free sword).
SoomlaProfile.getInstance().updateStatus(
	IProvider.Provider.FACEBOOK,            // Provider
	"I LOVE SOOMLA!  http://www.soom.la",   // Message to post as status
	"",                                     // Payload
	swordReward                             // Reward
);
```

![alt text](/img/tutorial_img/unity-profile/socialStatus.png "Update Status")

<br>
### `updateStatusWithConfirmation`

Works the same as `updateStatus` only here a confirmation dialog will be shown before the operation is performed.

``` java
SoomlaProfile.getInstance().updateStatusWithConfirmation(
	IProvider.Provider.FACEBOOK,            // Provider
	"I LOVE SOOMLA!  http://www.soom.la",   // Message to post as status
	"",                                     // Payload
	null,                             		// Reward
	this,                             		// Activity
	customMessage							// Message to show in the confirmation dialog
);
```

There is also an overloaded version without `customMessage`. In this case default message will be used.  


<br>
### `updateStory`
This function posts a story (which is a detailed status) on the user's wall in the supplied social provider. Upon a successful update, the user will receive the supplied reward.

For example, once your user reaches a high score, you could display a popup that allows them to share their high score on Facebook with a click of a button. Once he/she shares the story, you can give them a reward such as a free character.

**NOTE:** This functionality is supported in Facebook only.

``` java
// Equippable virtual good - a soombot character with super powers
// that can be purchased for 2.99.
VirtualGood soombot = new EquippableVG(
	EquippableVG.EquippingModel.GLOBAL,
	"Soombot character",
	"Soombot has super powers!",
	"soombot_ID",
	new PurchaseWithMarket("productID", 2.99)
);

// A reward of receiving "Soombot" for FREE (no need to pay 2.99!)
Reward soombotReward = new VirtualItemReward(
	"soombotReward_ID",
	"Soombot Reward",
	1,
	soombot.getItemId()
);

// When a user updates this story, they'll receive a soombotReward (free "Soombot").
SoomlaProfile.getInstance().updateStory(
	IProvider.Provider.FACEBOOK,                // Provider
	"This is the story.",                       // Text of the story to post
	"The story of SOOMBOT (Profile Test App)",  // Name
	"SOOMBOT Story",                            // Caption
	"http://about.soom.la/soombots",            // Link to post
	"http://about.soom.la/.../spockbot.png",    // Image URL
	"",                                         // Payload
	soombotReward                               // Reward
);
```

![alt text](/img/tutorial_img/unity-profile/socialStory.png "Post Story")

<br>
### `updateStoryWithConfirmation`

Works the same as `updateStory` only here a confirmation dialog will be shown before the operation is performed.

``` java
SoomlaProfile.getInstance().updateStoryWithConfirmation(
	IProvider.Provider.FACEBOOK,                // Provider
	"This is the story.",                       // Text of the story to post
	"The story of SOOMBOT (Profile Test App)",  // Name
	"SOOMBOT Story",                            // Caption
	"Hey! It's SOOMBOT Story",                 	// Description
	"http://about.soom.la/soombots",            // Link to post
	"http://about.soom.la/.../spockbot.png",    // Image URL
	"",                                         // Payload
	null,                                       // Reward
	this,                             			// Activity
	customMessage								// Message to show in the confirmation dialog
);
```

There is also an overloaded version without `customMessage`. In this case default message will be used.  


<br>
### `uploadImage`

This function uploads an image to the user's wall in the social provider. Upon a successful upload, the user will receive the supplied reward.

For example, when your user finishes a level in your game, you can offer him/her to upload an image (perhaps a screenshot of the finished level) and receive a reward.

**NOTE:** This functionality is supported in Facebook only.

``` java
// A badge reward, gold coin.
Reward goldMedal = new BadgeReward(
	"badge_goldMedal",                    // ID
	"Gold Medal"                          // Name
);

// Once the user uploads the image, give him/her a badgeReward.
SoomlaProfile.getInstance().uploadImage(
	IProvider.Provider.FACEBOOK,          // Provider
	"I love SOOMLA! http://www.soom.la",  // Message to post with imag
	"someFileName",                       // File path
	"",                                   // Payload
	goldMedal                             // Reward
);
```

![alt text](/img/tutorial_img/unity-profile/socialUpload.png "Upload Image")

<br>
### `uploadImageWithConfirmation`

Works the same as `uploadImage` only here a confirmation dialog will be shown before the operation is performed.

``` java
SoomlaProfile.getInstance().uploadImageWithConfirmation(
	IProvider.Provider.FACEBOOK,          	// Provider
	"I love SOOMLA! http://www.soom.la",  	// Message to post with imag
	"someFileName",                       	// File path
	"",                                   	// Payload
	null,                             		// Reward
	this,                             		// Activity
	customMessage							// Message to show in the confirmation dialog
);
```

There is also an overloaded version without `customMessage`. In this case default message will be used.  

<br>
### `getStoredUserProfile`

This function retrieves the user's page for the given social provider from the **local device storage** (`getStoredUserProfile` does not call any social provider function, it retrieves and returns its information from the storage, contrary to what is depicted in the diagram at the beginning of this section). This function allows you to get user information even if the user is offline.

For example, you could use `getStoredUserProfile` to get the user's first name, and welcome him to the game.

``` java
UserProfile userProf = SoomlaProfile.getInstance().getStoredUserProfile(
	IProvider.Provider.FACEBOOK           // Provider
);

String firstName = userProf.getFirstName();
```

<div class="info-box">This functionality is only available if the user has already logged into the provider once.</div>

<br>
### `getContacts`

This function retrieves a list of the user's contacts from the supplied provider.

<div class="info-box">Notice that some social providers (G+, Twitter) supply all of the user's contacts and some (FB) supply only the contacts that use your app.</div>

You could use `getContacts` to show your users a personalized screen where they can see which of their friends are also playing your game, or you could offer the contacts that don't play your game to download your game and receive some free coins.

``` java
SoomlaProfile.getInstance().getContacts(
	IProvider.Provider.FACEBOOK,          // Provider
	false,                                // fromStart
	"",                                   // Payload
	someReward                            // Reward
);
```

#### Pagination

Note that the results will contain only part of the list. In order to get more items you should call the method again with `fromStart` param set to `false` (it's a default value for overloaded methods). You can use the following workflow:

```java
public void getContacts() {

    // request for the 1st page
    SoomlaProfile.getInstance().getContacts(
        IProvider.Provider.FACEBOOK,          // Provider
        true,                                 // fromStart
        "",                                   // Payload
        null                                  // Reward
    );
}

@Subscribe
public void onGetContactsFinished(GetContactsFinishedEvent getContactsFinishedEvent) {

    // ... handle page results ...

    if (getContactsFinishedEvent.HasMore) {
        SoomlaProfile.getInstance().getContacts(
            IProvider.Provider.FACEBOOK,          // Provider
            false,                                // fromStart
            "",                                   // Payload
            null                                  // Reward
        );
    } else {
        // no pages anymore
    }
}

```

<br>
### `getFeed`

This function retrieves a list of the user's feed entries from the supplied provider.

<div class="info-box">G+ does not support this.</div>

``` java
SoomlaProfile.getInstance().getFeed(
	IProvider.Provider.FACEBOOK,          // Provider
	false,                                // fromStart
	"",                                   // Payload
	someReward                            // Reward
);
```

#### Pagination

Note that the results will contain only part of the list. In order to get more items you should call the method again with `fromStart` param set to `false` (it's a default value for overloaded methods). You can use the following workflow:

```java
public void getFeed() {

    // request for the 1st page
    SoomlaProfile.getInstance().getFeed(
        IProvider.Provider.FACEBOOK,          // Provider
        true,                                 // fromStart
        "",                                   // Payload
        null                                  // Reward
    );
}

@Subscribe
public void onGetFeedFinished(GetFeedFinishedEvent getFeedFinishedEvent) {

    // ... handle page results ...

    if (getFeedFinishedEvent.HasMore) {
        SoomlaProfile.getInstance().getFeed(
            IProvider.Provider.FACEBOOK,          // Provider
            false,                                // fromStart
            "",                                   // Payload
            null                                  // Reward
        );
    } else {
        // no pages anymore
    }
}
```

<br>
### `openAppRatingPage`

`openAppRatingPage` conveniently opens your application's page on the platform store (Google Play Store, Amazon Appstore, etc) so that it's simple to rate the app. You can offer your users to rate your app after they've completed a level successfully or have progressed significantly in your game.

``` java
SoomlaProfile.getInstance().openAppRatingPage(
	context                               // Context
);
```

<br>
### `multiShare`

`multiShare` Shares text and/or image using native sharing functionality of your target platform.
The user will be shown a screen where he selects where he wants to share.

``` java
SoomlaProfile.getInstance().multiShare(
    "I'm happy. I can be shared everywhere.",
    "path/to/file/you/want/to/share"
);
```

<br>
## Auxiliary Model: Reward

A `Reward` is an entity which can be earned by the user for meeting certain criteria in game progress.

<div class="info-box">Note that `Reward` is a part of soomla-android-core, and *not* part of the Profile module. However, because `Reward`s are used very often throughout Profile, it's important that you are familiar with the different `Reward` types.</div>

`Reward` is an abstract class. Below are several types of rewards that implement `Reward`.

### **VirtualItemReward**

A specific type of `Reward` that you can use to give your users some amount of a virtual item. **For example:** Give users 100 coins (virtual currency) for liking your page.

<div class="info-box">`VirtualItemReward` is a part of `android-store`. In case you want to use it, you'll need to import android-store as well.</div>

``` java
VirtualCurrency coin = new VirtualCurrency("Coin", "", "coin_ID");

Reward hundredCoinReward = new VirtualItemReward(
	"vReward_ID",                         // ID
	"Coin Reward",                        // Name
	100,                                  // Amount
	coin.getItemId()                      // Associated item ID
);
```

<br>
### **BadgeReward**

A specific type of `Reward` that represents a badge with an icon. **For example:** Give the user a badge reward for posting a status on his/her wall.

``` java
Reward goldMedal = new BadgeReward(
  "badge_goldMedal",                    // ID
  "Gold Medal"                          // Name
);
```

<br>
### **SequenceReward**

A specific type of `Reward` that holds a list of other `Reward`s in a certain sequence. The rewards are given in ascending order. **For example:** In a Karate game the user can progress between belts and can be rewarded a sequence of: blue belt, purple belt, brown belt, and lastly, black belt.

``` java
Reward blueBelt = new BadgeReward(
	"blueBelt",                           // ID
	"Karate blue belt"                    // Name
);
// Assume the same instantiation for the rest of the belts.

List<Reward> beltRewards = new ArrayList<Reward>();
list.add(blueBeltReward);
...
list.add(blackBeltReward);

Reward beltReward = new SequenceReward(
	"beltReward",                         // ID
	"Belt Reward",                        // Name
	beltRewards                           // Rewards in sequence
);
```

<br>
### **RandomReward**

A specific type of `Reward` that holds a list of other `Reward`s. When this `Reward` is given, it randomly chooses a `Reward` from the list of `Reward`s it internally holds. **For example:** Give users a mystery box `Reward` for uploading an image, that grants him/her a random `Reward`.

``` java
List<Reward> rewards = new ArrayList<Reward>();
list.add(goldMedal);
list.add(coinReward);

Reward mysteryReward = new RandomReward(
	"mysteryReward",                      // ID
	"Mystery Box Reward",                 // Name
	rewards                               // Rewards to choose from
);
```
