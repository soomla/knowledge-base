---
layout: "sample"
image: "tune_logo"
title: "TUNE"
text: "Measure SOOMLA in-game events to see which ad networks and publishers send the most engaged users"
position: 12
relates: ["giftgaming", "supersonic", "unity_ads"]
collection: 'samples'
navicon: "nav-icon-tune.png"
backlink: "http://www.tune.com/"
theme: 'samples'
---

# TUNE Integration

<div class="samples-title">This article shows multiple integration samples:</div>

* Measure SOOMLA Store purchase events to see which ad networks and publishers are sending you valuable users making in-app purchases

* Measure SOOMLA Profile social network logins to see which ad networks and publishers are sending you social users, and which social network they login to.

* Measure SOOMLA LevelUp events to see which ad networks and publishers are sending you engaged users that make more in-game progress.

* Measure SOOMLA Insights events to see which ad networks and publishers are sending you paying users.

<br>


The biggest hurdle for marketing a mobile app is integrating SDKs for every ad network and publisher you want to work with. With <a href="http://www.tune.com" target="_blank">TUNE</a>, you never have to integrate another advertising SDK. Not only have we integrated with mobile ad networks and publishers, but you can easily pass conversion information to any third party partner you choose.

The <a href="https://developers.mobileapptracking.com/mobile-sdks/" target="_blank">TUNE SDK</a> provides application session and event logging functionality. To begin measuring sessions and installs, initiate the `measureSession` method. You can then rely on TUNE to log in-app events (such as purchases, game levels, and any other user engagement).

This document will show you how to measure events from all SOOMLA modules - Store, Profile, LevelUp, and Insights - so you can identify which ad networks and publishers send you the most valuable users.

<div>

    <div role="tabpanel" class="tab-pane active active" id="sample-ios-store">
      <pre>
```
#import "ViewController.h"
#import "MarketItem.h"
#import "PurchasableVirtualItem.h"
#import "PurchaseWithMarket.h"
#import "SoomlaStore.h"
#import "StoreEventHandling.h"
#import <MobileAppTracker/MobileAppTracker.h>

@interface ViewController ()

@end

@implementation ViewController

- (void)viewDidLoad {
    [super viewDidLoad];

    // Listen for Soomla EVENT_MARKET_PURCHASED event
    [[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(marketPurchased:)
                                                 name:EVENT_MARKET_PURCHASED object:nil];

    // Initialize Soomla Store with your Store Assets
    [Soomla initializeWithSecret:@"[YOUR CUSTOM GAME SECRET HERE]"];
    [[SoomlaStore getInstance] initializeWithStoreAssets:[[YourStoreAssetsImplementation alloc] init]];
}

// On purchase complete, set purchase info and measure purchase in TUNE
- (void)marketPurchased:(NSNotification *)notification {
    CGFloat revenue;
    NSString *currency;
    NSArray *items;

    PurchaseType *type = [notification.userInfo[DICT_ELEMENT_PURCHASABLE] purchaseType];
    if ([type isKindOfClass:[PurchaseWithMarket class]]) {
        MarketItem *item = ((PurchaseWithMarket *)type).marketItem;
        revenue = (CGFloat)([item marketPriceMicros] / 1000000);
        currency = [item marketCurrencyCode];

        // Create event item to store purchase item data
        MATEventItem *eventItem = [MATEventItem eventItemWithName:[item marketTitle]
                                                       attribute1:[item productId]
                                                       attribute2:nil
                                                       attribute3:nil
                                                       attribute4:nil
                                                       attribute5:nil];
        // Add event item to MATItem array in order to pass to TUNE SDK
        items = @[eventItem];
    }

    // Get transaction ID and receipt data for purchase validation
    NSDictionary *dict = notification.userInfo[DICT_ELEMENT_EXTRA_INFO];
    NSString *transactionId = dict[@"transactionIdentifier"];
    NSString *receipt = dict[@"receiptBase64"];
    NSData *receiptData = [[NSData alloc] initWithBase64EncodedString:receipt options:1];

    // Create a MATEvent with this purchase data
    MATEvent *purchaseEvent = [MATEvent eventWithName:MAT_EVENT_PURCHASE];
    purchaseEvent.revenue = revenue;
    purchaseEvent.currencyCode = currency;
    purchaseEvent.refId = transactionId;
    purchaseEvent.receipt = receiptData;
    purchaseEvent.eventItems = items;

    // Measure "purchase" event
    [MobileAppTracker measureEvent:purchaseEvent];
}

@end
```
      </pre>
    </div>

    <div role="tabpanel" class="tab-pane" id="sample-ios-profile">
      <pre>
```
#import "ViewController.h"
#import "SoomlaProfile.h"
#import "ProfileEventHandling.h"
#import "UserProfile.h"
#import "UserProfileUtils.h"
#import <MobileAppTracker/MobileAppTracker.h>

@interface ViewController ()

@end

@implementation ViewController

- (void)viewDidLoad {
    [super viewDidLoad];

    // Register for Soomla login finished event
    [[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(loginFinished:) name:EVENT_UP_LOGIN_FINISHED object:nil];

    // Initialize Soomla Profile
    [Soomla initializeWithSecret:@"[YOUR CUSTOM GAME SECRET HERE]"];
    [[SoomlaProfile getInstance] initialize:providerParams];
}

// Set user ID and measure login event upon login finished
- (void)loginFinished:(NSNotification*)notification {
    UserProfile *userProfile = notification.userInfo[DICT_ELEMENT_USER_PROFILE];
    NSString *userId = [userProfile profileId];

    // Set different user IDs in TUNE SDK based on provider
    NSString *provider = [UserProfileUtils providerEnumToString:[userProfile provider]];
    if ([provider isEqualToString:@"facebook"]) {
        [MobileAppTracker setFacebookUserId:userId];
    } else if ([provider isEqualToString:@"google"]) {
        [MobileAppTracker setGoogleUserId:userId];
    } else if ([provider isEqualToString:@"twitter"]) {
        [MobileAppTracker setTwitterUserId:userId];
    } else {
        [MobileAppTracker setUserId:userId];
    }
    // Measure a login event for this user ID
    [MobileAppTracker measureEventName:MAT_EVENT_LOGIN];
}

@end
```
      </pre>
    </div>
    <div role="tabpanel" class="tab-pane" id="sample-android-profile">
      <pre>
```
import android.app.Activity;
import android.os.Bundle;

import com.mobileapptracker.MATEvent;
import com.mobileapptracker.MobileAppTracker;
import com.soomla.BusProvider;
import com.soomla.Soomla;
import com.soomla.SoomlaConfig;
import com.soomla.profile.SoomlaProfile;
import com.soomla.profile.domain.IProvider;
import com.soomla.profile.domain.UserProfile;
import com.soomla.profile.events.auth.LoginFinishedEvent;
import com.squareup.otto.Subscribe;

public class MainActivity extends Activity {
    private MobileAppTracker mobileAppTracker;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        // Initialize the TUNE SDK
        mobileAppTracker = MobileAppTracker.init(
                getApplicationContext(),
                "tune_advertiser_id",
                "tune_conversion_key");

        // Initialize Soomla and Soomla Profile
        Soomla.initialize("[YOUR CUSTOM GAME SECRET HERE]");
        SoomlaProfile.getInstance().initialize();
    }

    @Override
    protected void onResume() {
        super.onResume();
        // Register to receive Soomla events
        BusProvider.getInstance().register(this);
        // Measure an app open in TUNE
        mobileAppTracker.setReferralSources(this);
        mobileAppTracker.measureSession();
    }

    @Override
    protected void onPause() {
        // Unregister from Soomla events
        BusProvider.getInstance().unregister(this);
        super.onPause();
    }

    @Subscribe
    public void onLoginFinished(LoginFinishedEvent loginFinishedEvent) {

        // On login finished, set the user ID in TUNE based on provider, and measure a login
        UserProfile user = loginFinishedEvent.UserProfile;
        if (user != null) {
            IProvider.Provider provider = user.getProvider();
            String userId = user.getProfileId();

            // Set different user IDs in TUNE SDK based on provider
            if (provider == IProvider.Provider.FACEBOOK) {
                mobileAppTracker.setFacebookUserId(userId);
            } else if (provider == IProvider.Provider.GOOGLE) {
                mobileAppTracker.setGoogleUserId(userId);
            } else if (provider == IProvider.Provider.TWITTER) {
                mobileAppTracker.setTwitterUserId(userId);
            } else {
                mobileAppTracker.setUserId(userId);
            }
            // Measure a login event for this user ID
            mobileAppTracker.measureEvent(MATEvent.LOGIN);
        }
    }
}
```
      </pre>
    </div>

</div>

<div class="samples-title">Getting started</div>


1. <a href="https://platform.mobileapptracking.com/#!/advertiser" target="_blank">Sign up</a> with TUNE to get started on your home for attribution and analytics.

2. Download and integrate the <a href="https://developers.mobileapptracking.com/mobile-sdks/" target="_blank">TUNE SDK</a>.

3. Integrate SOOMLA Store and LevelUp.  Follow all steps in the platform specific getting started guides: <br>
    <a href="/unity/" target="_blank">Unity</a> |
    <a href="/ios/" target="_blank">iOS</a> |
    <a href="/android/" target="_blank">Android</a>

4. To use Grow Insights, follow the <a href="/unity/grow/grow_insights/" target="_blank">Grow Insights Unity Instructions</a>.


<div class="samples-title">Sample projects</div>

For sample projects of TUNE + SOOMLA integration, please see in GitHub:

<a href="https://github.com/john-gu/TuneSoomlaUnity" target="_blank">Unity</a> |
<a href="https://github.com/john-gu/TuneSoomlaIOS" target="_blank">iOS</a> |
<a href="https://github.com/john-gu/TuneSoomlaAndroid" target="_blank">Android</a>


<div class="samples-title">Support</div>

* For more information on SDK implementation and events to measure, please visit our <a href="https://developers.mobileapptracking.com/mobile-sdks/" target="_blank">documentation</a>.

* If you have further questions, please feel free to contact us at [support@mobileapptracking.com](mailto:support@mobileapptracking.com).
