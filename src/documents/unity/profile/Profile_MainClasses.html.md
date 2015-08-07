---
layout: "content"
image: "Modeling"
title: "Main Classes & Operations"
text: "Read descriptions of the various social entities unity3d-profile provides, and see usage examples of operations that can be done to the different entities."
position: 2
theme: 'platforms'
collection: 'unity_profile'
module: 'profile'
platform: 'unity'
---

# Main Classes & Operations

In this document you'll find descriptions of most of the main classes and interfaces of unity3d-profile. Some of these classes represent the different social elements used in the Profile module, while others contain functionality to perform social-related operations.

![alt text](/img/tutorial_img/soomla_diagrams/Profile.png "Profile Diagram")

<br>
Social actions allow you to entice social engagement by offering your users rewards in exchange for social interactions. For example, you can ask your users to like your page or share a specific status about your game, and give them various rewards, such as a badge of recognition or free virtual items that you normally sell for money/virtual currency. In this win-win situation your users will be pleased, and the network effect will increase the popularity of your game.

<div class="info-box">`Reward`s are a part of SOOMLA's core module and are used in many functions of Profile. Read about the different types of `Reward`s [below](#auxiliary-model-reward).</div>

##Provider <a href="https://github.com/soomla/unity3d-profile/blob/master/Soomla/Assets/Plugins/Soomla/Profile/Domain/Provider.cs" target="_blank"><img class="link-icon" src="/img/tutorial_img/linkImg.png"></a>

This class represents the different social networks that exist today. Currently, SOOMLA supports Facebook, Twitter, and Google+.

The `Provider` class simply holds a string enumeration of the various providers that are currently available, as well as those that will be available in the future.

## SocialActionType <a href="https://github.com/soomla/unity3d-profile/blob/master/Soomla/Assets/Plugins/Soomla/Profile/Domain/SocialActionType.cs" target="_blank"><img class="link-icon" src="/img/tutorial_img/linkImg.png"></a>

This class represents various social actions that can be performed in social networks, such as posting a status or story, or uploading an image.

The `SocialActionType` class simply holds a string enumeration of the different social actions.

## UserProfile <a href="https://github.com/soomla/unity3d-profile/blob/master/Soomla/Assets/Plugins/Soomla/Profile/Domain/UserProfile.cs" target="_blank"><img class="link-icon" src="/img/tutorial_img/linkImg.png"></a>

This class holds information about a user for a specific `Provider`.

<div class="info-box">Note that each social `Provider` (FB, G+, Twitter, etc..) gives access to different information, so you won't necessarily receive all the fields mentioned below.</div>

**A `UserProfile` contains the following elements:**

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
- `Extra` - a Dictionary contains additional info provided by social provider:
  - `Facebook` - access_token(String), permissions(JSONObject of Strings), expiration_date(UNIX timestamp as Double);
  - `Twitter` - access_token(String);
  - `Google+` - access_token(String), refresh_token(String) - not available for Android, expiration_date(UNIX timestamp as Double) - not available for Android.

## SoomlaProfile <a href="https://github.com/soomla/unity3d-profile/blob/master/Soomla/Assets/Plugins/Soomla/Profile/SoomlaProfile.cs" target="_blank"><img class="link-icon" src="/img/tutorial_img/linkImg.png"></a>

This is the main class that controls the entire SOOMLA Profile module. Use this class to perform various social and authentication operations for users. The Profile module will work with the social and authentication plugins, as you supply in the SOOMLA settings, described in step 3b of the [Getting Started](/docs/platforms/unity/Profile_GettingStarted#getting-started) tutorial.

**NOTE:** Most of the functions in this class call relevant functions from the social provider's SDK, and do NOT return a value, but rather fire appropriate events that contain the return values. Read more about [Events](/docs/platforms/unity/Profile_Events).

The diagram below depicts the flow that takes place when a `SoomlaProfile` function is called. In the diagram, the example function shown is `Login`, but this principle holds for all functions.

![alt text](/img/tutorial_img/unity-profile/functionFlow.png "Function Flow")

<br>
###`Initialize`

`SoomlaProfile` is the class that needs to be initialized in order to begin using Profile. This is covered in unity3d-profile's [Getting Started](/unity/profile/Profile_GettingStarted) tutorial.

```cs
SoomlaProfile.Initialize();
```

###`Login / Logout`

The `Login` function will log the user into the specified provider, and will give the user a reward if one was provided.

Most of the social actions provided in Profile depend on the user being logged in. Therefore, upon successful login (which means the `OnLoginFinished` event has fired), you'll want to enable the rest of your social action buttons. For example, after the user is successfully logged in, you can display "Like" and "Post Status" buttons.

`Logout` simply logs the user out of the specified provider. Don't forget to disable the social action buttons in your UI once your user is logged out.

``` cs
// If the user clicks on the login button you provide, call the Login function:
SoomlaProfile.Login(
	Provider.FACEBOOK                        // Provider
);

// If you'd like to give your users a reward for logging in, use:
SoomlaProfile.Login(
	Provider.FACEBOOK,                       // Provider
	"",                                      // Payload
	new BadgeReward("reward", "Logged In!")  // Reward
);

// If the user would like to logout:
SoomlaProfile.Logout(
	Provider.FACEBOOK                        // Provider
);
```

<br>
###`IsLoggedIn`

Checks if the user is logged in and returns a boolean value.

``` cs
if (SoomlaProfile.IsLoggedIn(Provider.FACEBOOK)) {
    // Here you can (and should) set the screen to match the logged-in state.
    // For example display the logout button, like button, share button, etc.
}
```

<div class="info-box">If the user is not logged in, please notice that `IsLoggedIn` will not log the user in, you'll need to call the `Login` function yourself. </div>

<br>
###`Like`

This function opens up the provider page to "like" (a web page in a browser), and grants the user the supplied reward. For example, give the user 100 coins for liking your page.

``` cs
public const string COIN_ID = "coin_ID";

// Virtual currency, coin
VirtualCurrency coin = new VirtualCurrency("coin", "", COIN_ID);

// A reward of 100 virtual coins.
Reward hundredCoinReward = new VirtualItemReward("hundredCoinReward_ID", "100 Coin Reward", COIN_ID, 100);

// If the user clicks the "Like" button provided in your game, call the Like function and reward him/her with 100 coins.
SoomlaProfile.Like(
	Provider.FACEBOOK,                 // Provider  
	"The.Soomla.Project",              // Page id to like
	hundredCoinReward                  // Reward for liking
);
```

The 2nd parameter received by the `like` function is a pageId. This can either be a page-id or page-name.

######For iOS Applications:
When using the `like` function, you should pass the page-id in most cases. There reason for that is that the page-name will only work correctly when the Facebook App is not installed on the device. In which case, SOOMLA tries to open the "like" page in the browser.

Page-id can be fetched from sites like http://findmyfacebookid.com/ , or Services like https://graph.facebook.com/requested_page_name, which once queried with a valid pageName will return a JsonString that contains the pageId value under the key "id".

<div class="info-box">Note that the user is given the reward just for clicking `Like` from the application. The `Like` function opens the page to like, but does not track if the user *actually* liked the page or not.</div>

<br>
###`UpdateStatus`

This function updates the user's status, which is simply a message, on the supplied social provider. Upon a successful update, the user will receive the supplied reward. For example, reward users that post a specific status with a `SingleUseVG`, such as a sword.

``` cs
// Virtual currency, coin
VirtualCurrency coin = new VirtualCurrency("Coin", "", "coin_ID");

// A single-use virtual item, sword, that can be purchased for 200 virtual coins.
VirtualGood sword = new SingleUseVG("Sword", "", "sword_ID", new PurchaseWithVirtualItem(coin.ID, 200));

// A reward of 1 FREE sword (no need to purchase it for 200 coins!)
Reward swordReward = new VirtualItemReward("swordReward_ID", "Sword Reward", sword.ID, 1);

// When a user updates his/her status, they'll receive a statusReward (free sword).
SoomlaProfile.UpdateStatus(
	Provider.FACEBOOK,                      // Provider
	"I LOVE SOOMLA!  http://www.soom.la",   // Message to post as status
	"",                                     // Payload
	swordReward                             // Reward for updating status
);
```

![alt text](/img/tutorial_img/unity-profile/socialStatus.png "Update Status")

<br>
### `UpdateStatusWithConfirmation`

Works the same as `UpdateStatus` only here a confirmation dialog will be shown before the operation is performed.

``` cs
SoomlaProfile.UpdateStatusWithConfirmation(
	Provider.FACEBOOK,                      // Provider
	"I LOVE SOOMLA!  http://www.soom.la",   // Message to post as status
	"",                                     // Payload
	null,                                   // Reward
	customMessage							// Message to show in the confirmation dialog
);
```
**NOTE:** This confirmation dialog is not supported in FB.
<br>
###`UpdateStory`
This function posts a story (which is a detailed status) on the user's wall in the supplied social provider. Upon a successful update, the user will receive the supplied reward.

For example, once your user reaches a high score, you could display a popup that allows them to share their high score on Facebook with a click of a button. Once he/she shares the story, you can give them a reward such as a free character.

**NOTE:** This functionality is only **fully** supported in Facebook, since not all social network provide this type of customization in a post.

``` cs
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
	soombot.ID,
	1
);

// When a user updates this story, they'll receive a soombotReward (free "Soombot").
SoomlaProfile.UpdateStory(
	Provider.FACEBOOK,                          // Provider
	"This is the story.",                       // Text of the story to post
	"The story of SOOMBOT (Profile Test App)",  // Name
	"SOOMBOT Story",                            // Caption
	"Hey! It's SOOMBOT Story",                 	// Description
	"http://about.soom.la/soombots",            // Link to post
	"http://about.soom.la/.../spockbot.png",    // Image URL
	"",                                         // Payload
	soombotReward                               // Reward for posting a story
);
```

![alt text](/img/tutorial_img/unity-profile/socialStory.png "Post Story")

<br>
### `UpdateStoryWithConfirmation`

Works the same as `updateStory` only here a confirmation dialog will be shown before the operation is performed.

``` cs
SoomlaProfile.UpdateStoryWithConfirmation(
	Provider.FACEBOOK,                          // Provider
	"This is the story.",                       // Text of the story to post
	"The story of SOOMBOT (Profile Test App)",  // Name
	"SOOMBOT Story",                            // Caption
	"Hey! It's SOOMBOT Story",                 	// Description
	"http://about.soom.la/soombots",            // Link to post
	"http://about.soom.la/.../spockbot.png",    // Image URL
	"",                                         // Payload
	null,                                       // Reward
	customMessage								// Message to show in the confirmation dialog
);
```
**NOTE:** This confirmation dialog is not supported in FB.

<br>
###`UploadImage`

This function uploads an image to the user's wall in the social provider. Upon a successful upload, the user will receive the supplied reward.

For example, when your user finishes a level in your game, you can offer him/her to upload an image (perhaps a screenshot of the finished level) and receive a reward.

``` cs
// A random reward that is selected from the given list - in this case it'll be one of the rewards defined above.
Reward mysteryReward = new RandomReward(
	"mysteryReward_ID",
	"Mystery Reward",
	new List<Reward>{hundredCoinReward, swordReward, soombotReward}
);

// Once the user uploads the image, give him/her a mysteryReward.
SoomlaProfile.UploadImage(
	Provider.FACEBOOK,                    // Provider
	(a texture),                          // The image as a texture
	"Image title",                        // Name of image
	"I love SOOMLA! http://www.soom.la",  // Message to post with image
	"",                                   // Payload
	mysteryReward                         // Reward for posting a story
);
```

There is another function - `UploadImageWithConfirmation`. Works the same as `UploadImage` only here a confirmation
dialog will be shown before the operation is performed.

**NOTE:** This confirmation dialog is not supported in FB.

![alt text](/img/tutorial_img/unity-profile/socialUpload.png "Upload Image")

<br>
###`GetStoredUserProfile`

This function retrieves the user's page for the given social provider from the **local device storage** (`GetStoredUserProfile` does not call any social provider function, it retrieves and returns its information from the storage, contrary to what is depicted in the diagram at the beginning of this section). This function allows you to get user information even if the user is offline.

For example, you could use `GetStoredUserProfile` to get the user's `FirstName`, and welcome him to the game.

``` cs
UserProfile userProf = SoomlaProfile.GetStoredUserProfile(Provider.FACEBOOK);

string firstName = userProf.FirstName;
```

<div class="info-box">This functionality is only available if the user has already logged into the provider once.</div>

<br>
###`GetContacts`

This function retrieves a list of the user's contacts from the supplied provider.

<div class="info-box">Notice that some social providers (G+, Twitter) supply all of the user's contacts and some (FB) supply only the contacts that use your app.</div>

You could use `GetContacts` to show your users a personalized screen where they can see which of their friends are also playing your game, or you could offer the contacts that don't play your game to download your game and receive some free coins.

``` cs
SoomlaProfile.GetContacts(Provider.FACEBOOK);
```

#### Pagination

Note that the results will contain only part of the list. In order to get more items you should call the method again with `fromStart` param set to `false` (it's a default value for overloaded methods). You can use the following workflow:

```cs
public void GetContacts() {
    ProfileEvents.OnGetContactsFinished += onGetContactsFinished;

    // request for the 1st page
    SoomlaProfile.GetContacts(Provider.FACEBOOK, true);
}


// your handler:
public void onGetContactsFinished(Provider provider, SocialPageData<UserProfile> userProfiles, string payload) {

    // ... handle page results ...
    if (userProfiles.HasMore) {
        SoomlaProfile.GetContacts(Provider.FACEBOOK, false);
    } else {
        // no pages anymore
    }
}

```

<br>
###`OpenAppRatingPage`

`OpenAppRatingPage` conveniently opens your application's page on the platform store (for example on an iOS device it'll open your app's page in the App Store) so that it's simple to rate the app. You can offer your users to rate your app after they've completed a level successfully or have progressed significantly in your game.

``` cs
SoomlaProfile.OpenAppRatingPage();
```

<br>
### `MultiShare`

`MultiShare` Shares text and/or image using native sharing functionality of your target platform.
The user will be shown a screen where he selects where he wants to share.

``` cs
SoomlaProfile.MultiShare(
    "I'm happy. I can be shared everywhere.",
    "path/to/file/you/want/to/share"
);
```

<br>
##Auxiliary Model: Reward <a href="https://github.com/soomla/soomla-unity3d-core/blob/master/Soomla/Assets/Plugins/Soomla/Core/rewards/Reward.cs" target="_blank"><img class="link-icon" src="/img/tutorial_img/linkImg.png"></a>

A `Reward` is an entity which can be earned by the user for meeting certain criteria in game progress.

<div class="info-box">Note that `Reward` is a part of soomla-unity3d-core, and *not* part of the Profile module. However, because `Reward`s are used very often throughout Profile, it's important that you are familiar with the different `Reward` types.</div>

`Reward` is an abstract class. Below are several types of rewards that implement `Reward`.

### VirtualItemReward <a href="https://github.com/soomla/unity3d-store/blob/master/Soomla/Assets/Plugins/Soomla/Store/rewards/VirtualItemReward.cs" target="_blank"><img class="link-icon-small" src="/img/tutorial_img/linkImg.png"></a>

A specific type of `Reward` that you can use to give your users some amount of a virtual item. **For example:** Give users 100 coins (virtual currency) for liking your page.

<div class="info-box">`VirtualItemReward` is a part of `unity3d-store`. In case you want to use it, you'll need to import unity3d-store as well.</div>

``` cs
VirtualCurrency coin = new VirtualCurrency("Coin", "", "coin_currency");

Reward coinReward = new VirtualItemReward(
	"vReward",                            // ID
	"Coin Reward",                        // Name
	coin.ID,                              // Associated item ID
	100                                   // Amount
);
```

<br>
### BadgeReward <a href="https://github.com/soomla/soomla-unity3d-core/blob/master/Soomla/Assets/Plugins/Soomla/Core/rewards/BadgeReward.cs" target="_blank"><img class="link-icon-small" src="/img/tutorial_img/linkImg.png"></a>

A specific type of `Reward` that represents a badge with an icon. **For example:** Give the user a badge reward for posting a status on his/her wall.

``` cs
Reward goldMedal = new BadgeReward(
	"badge_goldMedal",                    // ID
	"Gold Medal"                          // Name
);
```

<br>
### SequenceReward <a href="https://github.com/soomla/soomla-unity3d-core/blob/master/Soomla/Assets/Plugins/Soomla/Core/rewards/SequenceReward.cs" target="_blank"><img class="link-icon-small" src="/img/tutorial_img/linkImg.png"></a>

A specific type of `Reward` that holds a list of other `Reward`s in a certain sequence. The rewards are given in ascending order. **For example:** In a Karate game the user can progress between belts and can be rewarded a sequence of: blue belt, purple belt, brown belt, and lastly, black belt.

``` cs
Reward blueBelt = new BadgeReward(
	"blueBelt",                           // ID
	"Karate blue belt"                    // Name
);
// Assume the same instantiation for the rest of the belts.

Reward beltReward = new SequenceReward(
	"beltReward",                         // ID
	"Belt Reward",                        // Name
	new List<Reward>() {                  // Rewards in sequence
		blueBelt,
		purpleBelt,  
		brownBelt,  
		blackBelt }
);
```

<br>
### RandomReward <a href="https://github.com/soomla/soomla-unity3d-core/blob/master/Soomla/Assets/Plugins/Soomla/Core/rewards/RandomReward.cs" target="_blank"><img class="link-icon-small" src="/img/tutorial_img/linkImg.png"></a>

A specific type of `Reward` that holds a list of other `Reward`s. When this `Reward` is given, it randomly chooses a `Reward` from the list of `Reward`s it internally holds. **For example:** Give users a mystery box `Reward` for uploading an image, that grants him/her a random `Reward`.

``` cs
Reward mysteryReward = new RandomReward(
	"mysteryReward",                      // ID
	"Mystery Box Reward",                 // Name
	new List<Reward>() {                  // Rewards to choose from
		goldMedal,
		coinReward }
);
```
