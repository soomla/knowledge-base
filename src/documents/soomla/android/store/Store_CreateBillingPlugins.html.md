---
layout: "soomla-content"
image: "Plugins"
title: "Create Your Own Billing Plugin"
text: "Not all Android users pay with Google Play. Learn how to create new billing plugins for android-store to allow alternative payment methods."
position: 10
theme: 'soomla-android'
collection: 'soomla_android_store'
module: 'store'
platform: 'android'
---

# Create Your Own Billing Plugin

SOOMLA's store module is all about game economies and what is more connected to game economies than in-app purchases? IAP in Android is done using the store on the device. On some devices it's Google Play, on others it's Amazon Store and there are lots more.

## Adding Billing Plugins to android-store

Quite some time ago, we've decided to open our gates to more billing services. We already have Google Play and Amazon but the Store module (or more specifically in our case android-store) can easily be extended with more billing services. Want to create a billing service for a provider that dosn't exist yet? This is the article for you. The dependencies for writing any billing service are:

1. *android-store* library - `AndroidStore.jar`

2. *soomla-android-core* library - `SoomlaAndroidCore.jar`

3. Square's *otto* library - `square-otto-1.3.2.jar`

4. Your selected billing service's SDK.

In order to create a billing service you'll have to do 3 things:

* Create an implementation of `IIabService` for your selected billing service.

* Create an implementation of `IabHelper` that will wrap your selected service's implementation.

* Wrap up what you did, add docs and release.

Lets dive into each one of them:

## Implementing IIabService

`IIabService` is the main interface for all billing services. *android-store* knows how to load your implementation of `IIabService` by looking into the meta-data tag called `billing.service` in `AndroidManifest.xml` (more about this below). `SoomlaStore` is the class inside *android-store* that's responsible for loading and calling the billing service in runtime. The methods you'll need to implement in `IIabService` are those that we found to be common in many billing services out there. Billing services that don't support some of the required methods (for example: Amazon doesn't support consumption of items) can just leave the implementation blank or throw a `RuntimeException`, depending on how you want your billing service to behave. Select a name for your billing service's class that implements `IIabService` and make sure you place it under a package that starts with `com.soomla.store.billing`. For example, if your billing service is called `GooglePlayIabService` then the full name with package can be `com.soomla.store.billing.google.GooglePlayIabService` (notice the added `google.GooglePlayIabService`). The `AndroidManifest.xml` meta-data tag for your billing service will have a value containing everything that comes after the SOOMLA billing package prefix. For the example above, the meta-data tag will be:

``` xml
 <meta-data android:name="billing.service" android:value="google.GooglePlayIabService" />
```

### IAB Callbacks

Every operation presented by `IIabService` returns its response using a callback that is received when the operation is invoked. This way we allow the billing service operations to be asynchronous and let the application continue to run even when there's some fetching or sending of data happening in the background. Look in [IabCallbacks.java](https://github.com/soomla/android-store/blob/master/SoomlaAndroidStore/src/com/soomla/store/billing/IabCallbacks.java) to see the different callbacks. They all have `success"` and `fail` handlers, and some of them have other handlers as well. Make sure you call the appropriate handler from the appropriate callback at the right time.

### Add an Activity For Purchasing

Some billing services (like Google Play) need an `Activity` in order to run the purchasing operation. What these services basically do is they launch the IAB `Activity` on top of the given `Activity`. SOOMLA doesn't require the developer to provide an `Activity` on market purchasing operations so there's no `Activity` object to provide to those billing services. To solve this problem, we recommend billing services to create an inner public static class which will extend Android's `Activity` object and will serve as the parent to the service's IAB `Activity`. This custom inner `Activity` will be transparent and will be loaded on top of the Application using the `ApplicationContext` that's saved in `SoomlaApp`. Some guidelines to using an inner `Activity` for purchasing:
* In the custom `Activity`'s `onCreate` method you will call the billing service's purchasing process.

* When the purchasing process finishes, the response will usually be transferred back to the `Activity` in its `onActivityResult(...)` method.

* Keep the `OnPurchaseListener` from the `launchPurchaseFlow` method so you can call the appropriate handler from it when there's a response to the billing operation.

* When you write your "Getting Started", don't forget to tell your users that they need to add this `Activity` to their `AndroidManifest.xml`.  For example this is how it's done with Google Play:

```xml
<activity android:name="com.soomla.store.billing.google.GooglePlayIabService$IabActivity" android:theme="@android:style/Theme.Translucent.NoTitleBar.Fullscreen"/>
```

A good example of having an inner `Activity` for purchasing can be found in *android-store-google-play's* [GooglePlayIabService.java](https://github.com/soomla/android-store-google-play/blob/master/src/com/soomla/store/billing/google/GooglePlayIabService.java).

## Implementing IabHelper

We found out that most billing services have some specific implementation and that it's better to divide this behavior to a separate class. Also, the abstract `IabHelper` takes care of proper running of the billing service operations like making sure that only one runs at a time, or taking care of successful or failed responses (whenever you want them returned). Your `IIabService` is the one who needs to use the helper you'll create. Don't forget to initialize the helper when `IIabService` is started and stop it when it needs to be stopped. Some guidelines:

* You can decide if you want the billing operations to run asynchronously or not. We recommend that everything will run asynchronously on threads.

* Make sure you call the parent's (`abstract IabHelper`'s) `success` and `fail` methods for purchases, restore transactions and other operations (for example: ______). The parent class will know how to deal with that call and propagate it back to `IIabService`.

## Wrapping Up and Deploying

When you finish implementing your billing service and you want to make it available on your public Github repo, there are some steps you'll need to take so your billing service will be usable and understandable.

### Building a jar

Developers usually feel better with using a prebuilt jar file in their project. You will also need to build a jar if you want your project to be used in unity3d-store or other SOOMLA-related game engines. SOOMLA has a build shell script in all its projects. That build script is called `build_all` and it'll build the project to the `build` folder. Create your own `build_all` script and make sure your project builds to the `build` folder correctly. Don't forget to add and push that folder to your public repo.

### Writing the Docs

* Write a nice and clean `README.md` with explanations about your billing service.

* Provide links to the provider's website and tutorials.

* Provide a simple 1-2-3 "Getting Started" with textual guidelines and code blocks. Don't forget to state what should be the name of the meta-data tag in `AndroidManifest.xml`.

* Add inline documentation inside the code. Explain what your methods do and why. See SOOMLA's [Android inline documentation guidelines](https://github.com/soomla/android-store/blob/master/documentation.md).

Your billing service is ready for *android-store*? Great! Now it's time to [Make your Android billing service available on unity3d-store](/soomla/unity/store/Store_CreateBillingPlugins).
