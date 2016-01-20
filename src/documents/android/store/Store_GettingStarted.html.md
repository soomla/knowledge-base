---
layout: "content"
image: "Tutorial"
title: "Getting Started"
text: "Get started with android-store. Here you can find a basic example of initialization, economy framework integration, and links to downloads and IAP setup."
position: 1
theme: 'platforms'
collection: 'android_store'
module: 'store'
platform: 'android'
---

# STORE: Getting Started

Before doing anything, SOOMLA recommends that you go through [Android In-app Billing](http://developer.android.com/guide/google/play/billing/index.html) or [Amazon In App Purchasing](https://developer.amazon.com/public/apis/earn/in-app-purchasing) according to the billing service provider you choose.

## Integrate android-store

1. First, you'll need to either add the jars from the build folder to your project (RECOMMENDED), or clone android-store.

  - RECOMMENDED: Add the jars from the [zip](http://library.soom.la/fetch/android-store/latest?cf=knowledge%20base).

    OR, if you'd like to work with sources:

  - Recursively clone android-store.

    ```
    $ git clone --recursive git@github.com:soomla/android-store.git
    ```

    <div class="info-box">There are some necessary files in submodules linked with symbolic links. If you're cloning the project make sure to include the `--recursive` flag.</div>

2. In the `onCreate()` method of your main activity, initialize Soomla your main activity and secret that you chose to encrypt the user data. (For those who came from older versions, this should be the same as the old "customSec"):

    ``` java
    Soomla.initialize(this, "[YOUR CUSTOM GAME SECRET HERE]");
    ```

    <div class="info-box">This secret is your encryption secret for data saved in the DB.</div>

3. Create your own implementation of `IStoreAssets` in order to describe your game's specific assets.

  - See the brief [example](#example) at the bottom.

  - See a more detailed example, our MuffinRush [example](https://github.com/soomla/android-store/blob/master/SoomlaAndroidExample/src/com/soomla/example/MuffinRushAssets.java).

4. In the `onCreate()` method of your main activity, initialize `SoomlaStore` with the class you just created:

    ``` java
    SoomlaStore.getInstance().initialize(new YourStoreAssetsImplementation());
    ```

    <div class="warning-box">Initialize `SoomlaStore` ONLY ONCE when your application loads.</div>

And that's it! You have storage and in-app purchasing capabilities... ALL-IN-ONE.

Refer to the next section for information on selecting your Billing Service provider and setting it up.

## Select a Billing Service

SOOMLA's android-store can be used on all Android based devices meaning that you might want to use IAP with different billing services.

We've created two billing services for you: Google Play and Amazon (according to your demand).

The billing service is automatically started and stopped for every operation you're running on `SoomlaStore` (`buyWithMarket`, `restoreTransactions`, etc...).

Be careful with that. Don't leave the service running in the background without closing it.

You must select a billing service for android-store to work properly. The integration of a billing service is very easy:

### [Google Play](https://github.com/soomla/android-store-google-play)

Once you complete the following steps, see the [Google Play IAB](/android/store/Store_GooglePlayIAB) tutorial for information about in-app-purchase setup, integration with SOOMLA, and how to define your in-app purchase items.

1. Add `AndroidStoreGooglePlay.jar` from the folder `billing-services/google-play` to your project.

2. Make the following changes in `AndroidManifest.xml`:

  Add the following permission (for Google Play):

  ``` xml
  <uses-permission android:name="com.android.vending.BILLING" />
  ```

  Add the `IabActivity` to your `application` element, the plugin will spawn a transparent activity to make purchases. Also, you need to tell us what plugin you're using so add a meta-data tag for that:

  ``` xml
  <activity android:name="com.soomla.store.billing.google.GooglePlayIabService$IabActivity"
            android:theme="@android:style/Theme.Translucent.NoTitleBar.Fullscreen"/>
  <meta-data android:name="billing.service" android:value="google.GooglePlayIabService" />
  ```

3. After you initialize `SoomlaStore`, let the plugin know your public key from [Google play Developer Console](https://play.google.com/apps/publish/):

  ``` java
  public class StoreExampleActivity extends Activity {
      ...
      protected void onCreate(Bundle savedInstanceState) {
          ...
          String publicKey = "[YOUR PUBLIC KEY FROM GOOGLE PLAY]";
          GooglePlayIabService.getInstance().setPublicKey(publicKey);
      }
  }
  ```

4. If you want to allow Android's test purchases, all you need to do is tell that to the plugin:

  ``` java
  public class StoreExampleActivity extends Activity {
      ...
      protected void onCreate(Bundle savedInstanceState) {
          ...
          GooglePlayIabService.AllowAndroidTestPurchases = true;
      }
  }
  ```

5. In case you want to turn on _Fraud Protection_ you need to get clientId, clientSecret and refreshToken as explained in [Google Play Purchase Verification](/android/store/Store_GooglePlayVerification) and use them like this:

  ``` java
      GooglePlayIabService.getInstance().configVerifyPurchases(new HashMap<String, Object>() {{
          put("clientId", <YOU_CLIENT_ID>);
          put("clientSecret", <YOUR_CLIENT_SECRET>);
          put("refreshToken", <YOUR_REFRESH_TOKEN>);
      }});
  ```

  >  Optionally you can turn on `verifyOnServerFailure` if you want to get purchases automatically verified in case of network failures during the verification process:
  >
  > ``` java
  > GooglePlayIabService.getInstance().verifyOnServerFailure = true;
  > ```

#### **If you have an in-game storefront**

We recommend that you open the IAB Service and keep it open in the background. This how to do that:

When you open the store, call:  
``` java
SoomlaStore.getInstance().startIabServiceInBg();
```

When the store is closed, call:  
``` java
SoomlaStore.getInstance().stopIabServiceInBg();
```

### [Amazon](https://github.com/soomla/android-store-amazon)

Once you complete the following steps, see the [Amazon IAB](/android/store/Store_AmazonIAB) tutorial for information about in-app-purchase setup, integration with SOOMLA, and how to define your in-app purchase items.

1. Add `in-app-purchasing-2.0.1.jar` and `AndroidStoreAmazon.jar` from the folder `billing-services/amazon` to your project.

2. Make the following changes in `AndroidManifest.xml`:

  Add Amazon's `ResponseReceiver` to your `application` element. Also, you need to tell us what plugin you're using so add a meta-data tag for that:

  ``` xml
  <receiver android:name = "com.amazon.inapp.purchasing.ResponseReceiver" >
    <intent-filter>
        <action android:name = "com.amazon.inapp.purchasing.NOTIFY"
            android:permission = "com.amazon.inapp.purchasing.Permission.NOTIFY" />
    </intent-filter>
  </receiver>
  <meta-data android:name="billing.service" android:value="amazon.AmazonIabService" />
  ```

## Example

Create your own implementation of `IStoreAssets`; See the article about [IStoreAssets](/android/store/Store_IStoreAssets), which includes a code example and explanations.

Then initialize `SoomlaStore` with your implementation of `IStoreAssets`:

``` java
public class StoreExampleActivity extends Activity {
    ...

    protected void onCreate(Bundle savedInstanceState) {
        ...

        IStoreAssets storeAssets = new YourImplementationAssets();

        // This value is a secret of your choice.
        // You can't change it after you publish your game.
        Soomla.initialize("[CUSTOM SECRET HERE]");
        SoomlaStore.getInstance().initialize(storeAssets);

        /** The following is relevant only if your Billing Provider is Google Play **/

        // When you create your app in Google play Developer Console,
        // you'll find this key under the "Services & APIs" tab.
        GooglePlayIabService.getInstance().setPublicKey("[YOUR PUBLIC KEY FROM THE MARKET]");
        GooglePlayIabService.AllowAndroidTestPurchases = true;
        ...
    }

    ...
}
```
