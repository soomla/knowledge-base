---
layout: "content"
image: "InAppPurchase"
title: "Google Play In-app Verification"
text: "Google Play in-app-purchase setup and integration with SOOMLA - set up verification."
position: 8
theme: 'platforms'
collection: 'android_store'
module: 'store'
platform: 'android'
---

# Google Play Purchase Verification

## Getting Started

Google Play purchase verification is a way for you to prevent fraud in your game (_Fraud Protection_). SOOMLA provides support for verifying purchases through the Google Play Billing Service (AndroidStoreGooglePlay.jar). The way it works in that your app connects to SOOMLA's verification server in order to make sure the purchase was actually genuine and not a hack of someone who installed an IAP hacking tool.

In order for the verification to work, you need to follow Google's guidelines and prepare some credentials we can use in order to verify purchases:

1. Create an API Project in https://console.developers.google.com

2. Link your API Project to your game in Google Play Developer Console, follow [this](https://developers.google.com/android-publisher/getting_started#linking_your_api_project) section.

3. Create A Web Application OAuth 2.0 ID in the [Google APIs Console](https://console.developers.google.com). 
  Go to `APIs & Auth -> Credentials` and press `Add credentials -> OAuth 2.0 Client ID`:
   
  ![alt text](/img/tutorial_img/google_play_verification/create_oauth_client.png "Creating OAuth Client")
   
  Fill the values of: `Redirect URIs` and `Authorized JavaScript origins`:

  <div class="info-box">You can use http://www.example.com and http://www.example.com/oauth2callback here as default values.</div>

  ![alt text](/img/tutorial_img/google_play_verification/create_web_application.png "Creating Web App")  

  From the created client id, take `clientId` and `clientSecret`.

  ![alt text](/img/tutorial_img/google_play_verification/get_client_id_secret.png "Get your Client ID and Client Secret")

4. Open in your [Google APIs Console](https://console.developers.google.com) `APIs` section and then open `Google Play Android Developer API` 

  ![alt text](/img/tutorial_img/google_play_verification/find_developer_api.png "Open your APIs page")

  and enable it, if it isn't enabled yet:
  
  ![alt text](/img/tutorial_img/google_play_verification/enable_developer_api.png "Enable your Google Play Android Developer API")  
      

5. Get `refreshToken`:

  <a name="refresh_token_step_1"></a>
  
  1. Put the following URL in a browser on your machine: `https://accounts.google.com/o/oauth2/auth?scope=https://www.googleapis.com/auth/androidpublisher&response_type=code&access_type=offline&redirect_uri=<YOUR_REDIRECT_URI>&client_id=<YOUR_CLIENT_ID>`
  and login if you're ask to.

  2. The browser will be redirected to the `redirect_uri` you've provided. Now Have a look at the address bar, there should be a `code` param
    with a value we need for the next step (underlined on the picture below):
    
    ![alt text](/img/tutorial_img/google_play_verification/get_exchanging_code.png "Get Code to exchange it to request token")

  3. Now what we do is exchanging the `code` from the previous step into a `refresh token`. Initiate a POST request to `https://www.googleapis.com/oauth2/v3/token`
  with the following params:
   ```
    grant_type=authorization_code
    client_id=<YOUR_CLIENT_ID>
    client_secret=<YOUR_CLIENT_SECRET>
    redirect_uri=<YOUR_REDIRECT_URI>
    code=<CODE_FROM_STEP_2>
   ```
   
   The complete request should be the following (request was performed using [HTTPie](https://github.com/jkbrzt/httpie)):
   
   ![alt text](/img/tutorial_img/google_play_verification/get_refresh_token.png "Get Refresh Token")

    <div class="info-box">**NOTE:** If you got a success response but there was no refresh token, you can try to force it: just add `approval_prompt=force` to the URL at the [1st step](#refresh_token_step_1).</div>   

<br>
<br>
<br>
**That's it!** :) Now you have a refresh token you can use to initialize _Fraud Protection_ on SOOMLA's GooglePlay billing service.

## Useful links

- [Quick definition of Google Play Developer API](http://developer.android.com/google/play/developer-api.html#subscriptions_api_overview)

- [Google Play Developer API Getting Started](https://developers.google.com/android-publisher/getting_started)

- [How to get tokens](https://developers.google.com/identity/protocols/OAuth2WebServer)

- [Google Play Developer API: Purchases](https://developers.google.com/android-publisher/api-ref/purchases/products)
