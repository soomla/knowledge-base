---
layout: "content"
image: "InAppPurchase"
title: "Google Play In-app Billing"
text: "Google Play in-app-purchase setup and integration with SOOMLA - define your game's in-app products."
position: 8
theme: 'platforms'
collection: 'soomla_android_store'
module: 'store'
platform: 'android'
---

# Google Play IAB

## Follow these steps

1. Create your app in Google Play Developer Console. If you’re not sure how to do this, please read Google’s tutorial [Android In-app Billing](http://developer.android.com/guide/google/play/billing/index.html) before continuing.

2. Once you’ve created your app, go to the “Store Listing” tab and fill in all of the mandatory fields marked with ‘*’. Don’t forget to click “Save” when you’re done.

    ![alt text](/img/tutorial_img/google_play_iab/soomlaStoreEx.png "Store Listing")

3. Go to the “In-app Products” tab.

  - Click on “Add new product”.

  - Choose Managed product.

    <div class="info-box">SOOMLA does not support Subscription products, so do not choose this option. Google’s in-app billing v3 treats Unmanaged products as Managed products. Therefore we recommend you make all of your in-app products Managed. If your game already contains Unmanaged products see [Google’s documentation](http://developer.android.com/google/play/billing/billing_overview.html#migration).</div>

  ![alt text](/img/tutorial_img/google_play_iab/managedProduct.png "In-app products")

    To learn more about Google’s Managed/Unmanaged products please see [In-app Billing Version 3 - Product Types](http://developer.android.com/google/play/billing/api.html).

  - If you followed the steps in the [Getting Started](/android/store/Store_GettingStarted) tutorial, you created product IDs for the items you wanted to sell in Google Play. Now you need to insert the EXACT product IDs here. Remember that once you choose a product ID you cannot change it.

    <div class="info-box">For testing use [Google's test product IDs](http://developer.android.com/google/play/billing/billing_testing.html#billing-testing-static) for your product IDs.</div>

  - Once you click “Continue” you will be transferred to a page where you’ll need to fill out more details for your product. Fill in all of the mandatory fields marked with ‘*’. Notice that under Pricing, you can click on “Auto-convert prices now” and that will automatically set local prices for all other countries based on the exchange rate.

  - Click on the “Save” button and go back to the in-app products list.

    <div class="info-box">SOOMLA is all about free-to-play mobile games, so we recommend you choose the “Free” option in the “Pricing & Distribution” tab.</div>

4. Go to the “Services & APIs” tab and make note of your license key. You need to insert it into the code (see step 3 of the Google Play Dev Console section in [Getting Started](/android/store/Store_GettingStarted)).

    ![alt text](/img/tutorial_img/google_play_iab/licenseKey.png "License key")

## Newbie?

**NOTE:** After publishing your game (to alpha or beta), you must wait a few hours (about 1-2 hours) until you can test your in-app products.

  ![alt text](/img/tutorial_img/google_play_iab/iap_testing.png "IAP testing")

### Useful links

- Make sure you have a [test account](http://developer.android.com/google/play/licensing/setting-up.html#test-acct-setup) defined in Google Play and a device using that test account.

- Make sure you know the difference between sandbox testing and real testing. To learn more read [Google’s Testing In-app Billing](http://developer.android.com/google/play/billing/billing_testing.html).
