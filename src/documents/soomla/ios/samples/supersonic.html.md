---
layout: "sample"
image: "supersonic_logo"
title: "Supersonic"
text: "Show rewarded video / offer wall to earn coins"
position: 11
relates: ["fyber", "adience", "heyzap"]
collection: 'samples'
navicon: "nav-icon-supersonic.png"
backlink: "http://www.supersonic.com/"
theme: 'samples'
---

# Supersonic Integration

<div class="samples-title">This article shows multiple integration samples:</div>

* Reward coins on RewardedVideo/OfferWall ads completion.

* Show an Interstitial ad when a level ends (Unity only).

* Offer users to watch a video or complete an offer to receive virtual currency when they have insufficient funds (coins) to purchase an item.

<br>

<div>

    <div role="tabpanel" class="tab-pane active" id="sample-ios">
      <pre>
```
#import "ViewController.h"

@implementation ViewController

    NSString *soomlaSecret = @"YOUR_SOOMLA_SECRET";
    NSString *SUPERSONIC_APP_KEY = @"YOUR_SUPERSONIC_APPKEY";
    NSString *userId = @"UNIQUE_USER_ID";

    - (void)viewDidLoad {
        [super viewDidLoad];

        // Initialize SOOMLA
        // Assumes you've implemented your store assets
        [Soomla initializeWithSecret:soomlaSecret];
        id<IStoreAssets> storeAssets = [[YourImplementationAssets alloc] init];
        [[SoomlaStore getInstance] initializeWithStoreAssets:[[storeAssets alloc] init]];

        self.rewardedVideoBtn.enabled = false;
        self.offerwallBtn.enabled = false;

        // Initialize Supersonic
        [[Supersonic sharedInstance] setLogDelegate:self];
        [[Supersonic sharedInstance] setRVDelegate:self];
        [[Supersonic sharedInstance] setOWDelegate:self];
        [SUSupersonicAdsConfiguration getConfiguration].useClientSideCallbacks = [NSNumber numberWithInt:1];
        [[Supersonic sharedInstance] initOWWithAppKey:SUPERSONIC_APP_KEY withUserId:userId];
        [[Supersonic sharedInstance] initRVWithAppKey:SUPERSONIC_APP_KEY withUserId:userId];
    }

    - (IBAction)rewardedVideoBtn:(id)sender {
        [[Supersonic sharedInstance] showRV];
    }

    - (IBAction)offerwallBtn:(id)sender {
        [[Supersonic sharedInstance] showOW];
    }

    - (IBAction)buyShieldBtn:(id)sender {
        @try {
            [StoreInventory buyItemWithItemId:@"shield_good"];
        }
        @catch (InsufficientFundsException *exception) {
            // Not enough coins to buy a shield, show the user some free options to earn more coins
            if([[Supersonic sharedInstance] isAdAvailable])
                [[Supersonic sharedInstance] showRV];
            else if ([[Supersonic sharedInstance] isOWAvailable])
                [[Supersonic sharedInstance] showOW];
        }

    }

    - (void)supersonicRVAdRewarded:(NSInteger)amount{
        [StoreInventory giveAmount:amount ofItem:@"coins_currency"];
    }

    - (BOOL)supersonicOWDidReceiveCredit:(NSDictionary *)creditInfo {
        [StoreInventory giveAmount:[[creditInfo valueForKey:@"credits"] intValue]
                            ofItem:@"coins_currency"];
        // Return true if you successfully rewarded the user
        return true;
    }

    - (void)supersonicOWInitSuccess{
        self.offerwallBtn.enabled = true;
    }
    - (void)supersonicOWInitFailedWithError:(NSError *)error {
        self.offerwallBtn.enabled = false;
    }
    - (void)supersonicRVAdAvailabilityChanged:(BOOL)hasAvailableAds {
        self.rewardedVideoBtn.enabled = hasAvailableAds;
    }

@end
```
      </pre>
    </div>
  
  </div>

</div>


<div class="samples-title">Getting started</div>

1. Sign up to Supersonic <a href="
https://www.supersonic.com/lp/roam/lp_m1/?utm_source=Soomla%20Promotion&utm_medium=Soomla%20Knowledge%20Center&utm_campaign=SDK%20Integration%20Article" target="_blank">here</a>.

2. <a href="https://www.supersonicads.com/partners/login" target="_blank">Login</a> to the Supersonic Dashboard and <a href="http://developers.supersonic.com/hc/en-us/articles/200772061-Adding-Your-App" target="_blank">add your application</a>.

3. Follow the integration instructions for your platform: <br>
    <a href="http://developers.supersonic.com/hc/en-us/articles/201527091-Getting-started-with-the-Supersonic-Unity-Plugin-" target="_blank">Unity</a> |
    <a href="http://developers.supersonic.com/hc/en-us/articles/201328522-Getting-Started-with-the-Supersonic-iOS-SDK" target="_blank">iOS</a> |
    <a href="http://developers.supersonic.com/hc/en-us/articles/201481051-Getting-Started-with-the-Supersonic-Android-SDK" target="_blank">Android</a>

4. Integrate SOOMLA Store and LevelUp.  Follow all steps in the platform specific getting started guides: <br>
    <a href="/unity/store/store_gettingstarted/" target="_blank">Unity Store</a> |
    <a href="/unity/levelup/levelup_gettingstarted/" target="_blank">Unity LevelUp</a> |
    <a href="/android/store/store_gettingstarted/" target="_blank">Android Store</a> |
    <a href="/ios/store/store_gettingstarted/" target="_blank">iOS Store</a>


<div class="samples-title">Additional tips and recommendations</div>

1. Combine 2 or 3 Video ad networks for increased coverage.<br> Working with a single provider could lead to inventory problems where there are no ads available.

2. Adding <a href="/unity/grow/grow_gettingstarted/" target="_blank">GROW</a> will allow your users to backup their balances and will give you more tools to analyze their behavior.
