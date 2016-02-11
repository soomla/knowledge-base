---
layout: "sample"
image: "gameanalytics_logo"
title: "GameAnalytics"
text: "Report SOOMLA Store & LevelUp in-game events to GameAnalytics"
position: 6
relates: ["heroiclabs", "onesignal"]
collection: 'samples'
navicon: "nav-icon-gameanalytics.png"
backlink: "http://www.gameanalytics.com/"
theme: 'samples'
---

# GameAnalytics Integration

<br>

<div>


    <div role="tabpanel" class="tab-pane" id="sample-ios">
      <pre>
```
//
//  AppDelegate.m
//  TestApp
//
//  Copyright (c) 2014 GameAnalytics. All rights reserved.
//

#import "AppDelegate.h"
#import "GameAnalytics.h"
#import "TestAppIAP.h"
#import "GameAnalytics.h"
#import "Soomla.h"
#import "StoreEventHandling.h"
#import "PurchaseWithMarket.h"
#import "PurchasableVirtualItem.h"
#import "MarketItem.h"

@interface AppDelegate ()

@end

@implementation AppDelegate


- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
    [TestAppIAP sharedInstance];

    // Override point for customization after application launch.

    // Configure engine version
    [GameAnalytics configureEngineVersion:@"ios 1.0.0"];

    // Enable log
    [GameAnalytics setEnabledInfoLog:YES];
    [GameAnalytics setEnabledVerboseLog:YES];

    // Configure available virtual currencies and item types
    [GameAnalytics configureAvailableResourceCurrencies:@[@"gems", @"gold"]];
    [GameAnalytics configureAvailableResourceItemTypes:@[@"boost", @"lives"]];

    // Configure available custom dimensions
    [GameAnalytics configureAvailableCustomDimensions01:@[@"ninja", @"samurai"]];
    [GameAnalytics configureAvailableCustomDimensions02:@[@"whale", @"dolphin"]];
    [GameAnalytics configureAvailableCustomDimensions03:@[@"horde", @"alliance"]];

    // Configure build version
    [GameAnalytics configureBuild:@"0.1.0"];

    // Initialize
    [GameAnalytics initializeWithGameKey:@"<game_key>" gameSecret:@"<secret_key>"];

    // Handle SOOMLA events
    [[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(currencyBalanceChanged:) name:EVENT_CURRENCY_BALANCE_CHANGED object:nil];
    [[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(marketPurchased:) name:EVENT_MARKET_PURCHASED object:nil];
    [[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(itemPurchased:) name:EVENT_ITEM_PURCHASED object:nil];
    [[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(goodUpgrade:) name:EVENT_GOOD_UPGRADE object:nil];

    // Initialize SOOMLA Store
    // Assumes you've implemented your store assets.
    [Soomla initializeWithSecret:@"<SECRET_GAME_KEY>"];
    [[SoomlaStore getInstance] initializeWithStoreAssets:[[YourStoreAssetsImplementation alloc] init]];
    return YES;
}

- (void)currencyBalanceChanged:(NSNotification*)notification
{
    NSDictionary* userInfo = [notification userInfo];
    NSNumber* amountAdded = [userInfo objectForKey:DICT_ELEMENT_AMOUNT_ADDED];
    NSString* itemId = [userInfo objectForKey:DICT_ELEMENT_CURRENCY];

    GAResourceFlowType flowType = GAResourceFlowTypeSource;
    if ([amountAdded intValue] <= 0)
    {
        flowType = GAResourceFlowTypeSink;
    }

    [GameAnalytics addResourceEventWithFlowType:flowType currency:@"<virtual_currency_type_goes_here (e.g. Gems, BeamBoosters, Coins)>" amount:amountAdded itemType:@"<item_type_goes_here (e.g. Weapons, IAP, Gameplay, Boosters)>" itemId:itemId];
}

- (void)marketPurchased:(NSNotification*)notification
{
    NSDictionary* userInfo = [notification userInfo];
    PurchasableVirtualItem* purchasableVirtualItem = [userInfo objectForKey:DICT_ELEMENT_PURCHASABLE];
    PurchaseWithMarket* purchaseWithMarket = (PurchaseWithMarket*)[purchasableVirtualItem purchaseType];
    MarketItem* marketItem = [purchaseWithMarket marketItem];
    double price = [marketItem price];
    NSInteger amount = (NSInteger)(price * 100.0);

    [GameAnalytics addBusinessEventWithCurrency:[marketItem marketCurrencyCode] amount:amount itemType:@"<item_type_goes_here (e.g. GoldPacks)>" itemId:[purchasableVirtualItem itemId] cartType:@"<cart_type_goes_here (e.g. The game location of the purchase)>" autoFetchReceipt: YES];
}

- (void)itemPurchased:(NSNotification*)notification
{
    NSDictionary* userInfo = [notification userInfo];
    NSString* itemId = [userInfo objectForKey:DICT_ELEMENT_PURCHASABLE_ID];

    [GameAnalytics addDesignEventWithEventId:[NSString stringWithFormat:@"%@%@", @"Purchased:", itemId]];
}

- (void)goodUpgrade:(NSNotification*)notification
{
    NSDictionary* userInfo = [notification userInfo];
    NSString* itemId = [userInfo objectForKey:DICT_ELEMENT_GOOD];
    NSString* upgradeId = [userInfo objectForKey:DICT_ELEMENT_UpgradeVG];

    [GameAnalytics addDesignEventWithEventId:[NSString stringWithFormat:@"%@%@%@%@", @"Upgrade:", itemId, @":", upgradeId]];
}

- (void)applicationWillResignActive:(UIApplication *)application {
    // Sent when the application is about to move from active to inactive state. This can occur for certain types of temporary interruptions (such as an incoming phone call or SMS message) or when the user quits the application and it begins the transition to the background state.
    // Use this method to pause ongoing tasks, disable timers, and throttle down OpenGL ES frame rates. Games should use this method to pause the game.
}

- (void)applicationDidEnterBackground:(UIApplication *)application {
    // Use this method to release shared resources, save user data, invalidate timers, and store enough application state information to restore your application to its current state in case it is terminated later.
    // If your application supports background execution, this method is called instead of applicationWillTerminate: when the user quits.
}

- (void)applicationWillEnterForeground:(UIApplication *)application {
    // Called as part of the transition from the background to the inactive state; here you can undo many of the changes made on entering the background.
}

- (void)applicationDidBecomeActive:(UIApplication *)application {
    // Restart any tasks that were paused (or not yet started) while the application was inactive. If the application was previously in the background, optionally refresh the user interface.
}

- (void)applicationWillTerminate:(UIApplication *)application {
    // Called when the application is about to terminate. Save data if appropriate. See also applicationDidEnterBackground:.
}

@end
```
      </pre>

    </div>



<div class="samples-title">Getting started</div>

1. Sign up to <a href="http://www.gameanalytics.com/signup" target="_blank">GameAnalytics</a> for free.

2. Create your account, studio and game.

3. Follow instructions on how to integrate your platform: <br>
    <a href="https://guides.gameanalytics.com/content/sdk?page=unity" target="_blank">Unity</a> |
    <a href="https://guides.gameanalytics.com/content/sdk?page=ios" target="_blank">iOS</a> |
    <a href="https://guides.gameanalytics.com/content/sdk?page=android" target="_blank">Android</a>

4. Integrate SOOMLA Store and LevelUp. Follow all steps in the platform specific getting started guides: <a href="http://know.soom.la/soomla/unity/store/Store_GettingStarted/" target="_blank">Unity Store</a> | <a href="http://know.soom.la/soomla/ios/store/store_gettingstarted/" target="_blank">iOS Store</a> | <a href="http://know.soom.la/soomla/android/store/store_gettingstarted/" target="_blank">Android Store</a> | <a href="http://know.soom.la/unity/levelup/levelup_gettingstarted/" target="_blank">Unity LevelUp</a>

5. Integrate Grow Insights - follow the <a href="/unity/grow_insights/" target="_blank">Grow Insights Unity Instructions</a>.
