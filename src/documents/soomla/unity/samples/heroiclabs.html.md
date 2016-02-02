---
layout: "sample"
image: "heroiclabs_logo"
title: "Heroic Labs"
text: "Use SOOMLA Profile & LevelUp events to update sessions, leaderboards and achievements"
position: 8
relates: ["gameanalytics", "onesignal"]
collection: 'samples'
navicon: "nav-icon-heroiclabs.png"
backlink: "https://heroiclabs.com/"
theme: 'samples'
---

# Heroic Labs Integration

<div>

  <!-- Nav tabs -->
  <ul class="nav nav-tabs nav-tabs-use-case-code sample-tabs" role="tablist">
    <li role="presentation" class="active"><a href="#sample-unity" aria-controls="unity" role="tab" data-toggle="tab">Unity</a></li>
    <!-- <li role="presentation"><a href="#sample-cocos2dx" aria-controls="cocos2dx" role="tab" data-toggle="tab">Cocos2d-x</a></li> -->
    <!-- <li role="presentation"><a href="#sample-ios" aria-controls="ios" role="tab" data-toggle="tab">iOS</a></li> -->
    <li role="presentation"><a href="#sample-android" aria-controls="android" role="tab" data-toggle="tab">Android</a></li>
  </ul>

  <!-- Tab panes -->
  <div class="tab-content tab-content-use-case-code">
    <div role="tabpanel" class="tab-pane active" id="sample-unity">
      <pre>
```
using UnityEngine;
using System.Collections;
using System.Collections.Generic;
using System;
using GameUp;
using Soomla;
using Soomla.Profile;
using Soomla.LevelUp;

public class SoomlaHeroicLabsBehaviour : MonoBehaviour
{
  private static readonly String SESSION_KEY = "io.gameup.unity.session";
  private static GameUpSession session;

  void Start ()
  {
    // Initialise the Heroic Labs SDK.
    Client.ApiKey = "your-api-key-here";

    //
    // Listener that handles login events.
    //
    ProfileEvents.OnLoginFinished += onLoginFinished;
    //
    // Listener that handles logout events.
    //
    ProfileEvents.OnLogoutFinished += onLogoutFinished;

    //
    // Listener that submits a particular new Soomla record score to a Heroic Labs
    // leaderboard.
    //
    LevelUpEvents.OnScoreRecordChanged += onScoreRecordChanged;

    //
    // An example of how a Heroic Labs achievement might be triggered. This specific
    // example achievement is triggered by exactly matching any current high
    // score, but not beating it.
    //
    LevelUpEvents.OnScoreRecordReached += onScoreRecordReached;
  }

  //
  // Listener that handles login events.
  //
  private void onLoginFinished(UserProfile userProfile, string payload) {
    string type = userProfile.Provider().ToString();
    string id = userProfile.ProfileId;

    // Use the resulting social profile type and ID to request a new
    // Heroic Labs session for this user.
    String acc = "com.heroiclabs.accounts.com.soomla.profile." + type + "." + id;
    Client.LoginAnonymous(acc, (SessionClient sessionClient) => {

      // Store the session for future use.
      session = sessionClient;
    }, (int statusCode, string reason) => {
      //handle login error
    });

  }

  //
  // Listener that handles logout events.
  //
  private void onLoginFinished(string message) {
    // Drop the Heroic Labs session when the user logs out.
    session = null;
  }


  //
  // Listener that submits a particular new Soomla record score to a Heroic Labs
  // leaderboard.
  //
  private void onScoreRecordChanged(Score score) {
    String scoreId = score.ID;

    // See if we want to report this score. Each Heroic Labs leaderboard is
    // usually only relevant to one score type, but we can have more than
    // one leaderboard and submit different scores to each one!
    if ("soomla-score-id-we-care-about".Equals(scoreId)) {

        // Now check if the user is logged in, otherwise we can't report the
        // score.
        if (session != null) {

            // Finally, report the score to Heroic Labs!
            // Note: Heroic Labs prefers whole numbers as score values, but they
            // can represent anything. For example for a Soomla score of
            // 10.25 you might submit to Heroic Labs the value 1025.
            long latestScore = (long) score.Latest;
            session.UpdateLeaderboard("heroic-labs-leaderboard-id", latestScore, (Rank rank) => {

              // Now we might do something with the rank, such as notify the
              // user if they've got a new best rank.
            }, (int statusCode, string reason) => {
              //handle leaderboard update error
            });
        }
    }
  }

  //
  // An example of how a Heroic Labs achievement might be triggered. This specific
  // example achievement is triggered by exactly matching any current high
  // score, but not beating it.
  //
  private void onScoreRecordReached(Score score) {

    // We can only trigger achievements if we have a session.
    if (session != null) {
      session.Achievement("heroic-labs-achievement-id", () => {
        session.Achievements ((AchievementList list) => {
        foreach (Achievement achievement : list) {
          if (achievement.PublicId.Equals("heroic-labs-public-achievement-id")) {
            if (achievement.IsCompleted()) {
              // If this it means the achievement was just
              // unlocked, so we might congratulate the user.
            }
          }
        }
      }, (int statusCode, string reason) => {
        //handle achievement retrieve error
      }
      }, , (int statusCode, string reason) => {
        //handle achievement submit error
      });
    }
  }
}
```
      </pre>
    </div>
  </div>
</div>

<div class="samples-title">Getting Started</div>

Heroic Labs is a **free** game backend for developers which offers a plethora of features: user accounts, social login, cloud storage, multiplayer, leaderboards, achievements and much more...

1. Go to the <a href="https://dashboard.heroiclabs.com/#/signup" target="_blank">Heroic Labs Dashboard</a> to sign up for free.

2. Get your API key and integrate the code samples above into your game.

3. Integrate SOOMLA Profile and LevelUp.  Follow all steps in the platform specific getting started guides. <br>
    <a href="/unity/profile/profile_gettingstarted/" target="_blank">Unity Profile</a> |
    <a href="/unity/levelup/levelup_gettingstarted/" target="_blank">Unity LevelUp</a> |
    <a href="/android/profile/profile_gettingstarted/" target="_blank">Android Profile</a>

4. Check out the <a href="https://heroiclabs.com/" target="_blank">Heroic Labs website</a> and <a href="https://heroiclabs.com/docs/" target="_blank">documentation</a> for more details.

5. We're here for you, <a href="https://heroiclabs.com/contact/" target="_blank">get in touch</a> and we can help you with integration, feature design, and more!


<div class="samples-title">Downloads</div>

* The <a href="https://github.com/gameup-io/gameup-unity-sdk" target="_blank">Heroic Labs Unity SDK</a> is available to download on <a href="https://github.com/gameup-io/gameup-unity-sdk/releases" target="_blank">Github</a>.

* The <a href="https://github.com/gameup-io/gameup-android-sdk" target="_blank">Heroic Labs Android SDK</a> is available through <a href="http://search.maven.org/#search%7Cga%7C1%7Cio.gameup.android" target="_blank">Maven Central</a>.
