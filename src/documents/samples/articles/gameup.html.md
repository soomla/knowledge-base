---
layout: "sample"
image: "gameup_logo"
title: "GAMEUP"
text: "Learn how to observe and handle economy events triggered by android-store to customize your game-specific behavior."
position: 6
relates: ["chartboost"]
collection: 'samples'
theme: 'samples'
---

# GameUp + SOOMLA

## Use Case: Cross-device achievements and leaderboards

<div role="tabpanel">

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
        <code class="cs">
using UnityEngine;
using System.Collections;
using System.Collections.Generic;
using System;
using GameUp;
using Soomla;
using Soomla.Profile;
using Soomla.LevelUp;

public class SoomlaGameUpBehaviour : MonoBehaviour
{
  private static readonly String SESSION_KEY = "io.gameup.unity.session";
  private static GameUpSession session;

  void Start ()
  {
    // Initialise the GameUp SDK.
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
    // Listener that submits a particular new Soomla record score to a GameUp
    // leaderboard.
    //
    LevelUpEvents.OnScoreRecordChanged += onScoreRecordChanged;

    //
    // An example of how a GameUp achievement might be triggered. This specific
    // example achievement is triggered by exactly matching the current high
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
    // GameUp session for this user.
    String acc = "io.gameup.accounts.com.soomla.profile." + type + "." + id;
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
    // Drop the GameUp session when the user logs out.
    session = null;
  }


  //
  // Listener that submits a particular new Soomla record score to a GameUp
  // leaderboard.
  //
  private void onScoreRecordChanged(Score score) {
    String scoreId = score.ID;

    // See if we want to report this score. Each GameUp leaderboard is
    // usually only relevant to one score type, but we can have more than
    // one leaderboard and submit different scores to each one!
    if ("soomla-score-id-we-care-about".Equals(scoreId)) {

        // Now check if the user is logged in, otherwise we can't report the
        // score.
        if (session != null) {

            // Finally, report the score to GameUp!
            // Note: GameUp prefers whole numbers as score values, but they
            // can represent anything. For example for a Soomla score of
            // 10.25 you might submit to GameUp the value 1025.
            long latestScore = (long) score.Latest;
            session.UpdateLeaderboard("gameup-leaderboard-id", latestScore, (Rank rank) => {

              // Now we might do something with the rank, such as notify the
              // user if they've got a new best rank.
            }, (int statusCode, string reason) => {
              //handle leaderboard update error
            });
        }
    }
  }

  //
  // An example of how a GameUp achievement might be triggered. This specific
  // example achievement is triggered by exactly matching any current high
  // score, but not beating it.
  //
  private void onScoreRecordReached(Score score) {

    // We can only trigger achievements if we have a session.
    if (session != null) {
      session.Achievements ((AchievementList list) => {
        foreach (Achievement achievement : list) {
          if (achievement.PublicId.Equals("gameup-public-achievement-id")) {
            if (achievement.IsCompleted()) {
              // If this it means the achievement was just
              // unlocked, so we might congratulate the user.
            }
          }
        }
      }, (int statusCode, string reason) => {
        //handle achievement retrieve error
      });
    }
  }
}
        </code>
      </pre>
    </div>
    <!-- <div role="tabpanel" class="tab-pane" id="sample-cocos2dx">...</div> -->
    <!-- <div role="tabpanel" class="tab-pane" id="sample-ios">...</div> -->
    <div role="tabpanel" class="tab-pane" id="sample-android">
      <p>
        First, let's create a static helper class to handle the GameUp session for us:
      </p>

      <pre>
        <code class="java">
import com.soomla.data.KeyValueStorage;
import io.gameup.android.GameUpSession;

public class GameUpSessionHelper {

    // The local storage key to use when persisting the session.
    private static final String SESSION_KEY = "io.gameup.android.session";

    // Static field to cache the session.
    private static GameUpSession session;

    //
    // Get the current session, if one is available. Will try and read a session
    // from local storage if possible.
    //
    // @return A GameUpSession instance if one is available, null otherwise.
    //
    public static GameUpSession getSession() {
        if (session == null) {
            String value = KeyValueStorage.getValue(SESSION_KEY);
            if (!"".equals(value)) {
                session = GameUpSession.fromString(value);
            }
        }
        return session;
    }

    //
    // Instruct the helper to manage a new session. Use null to just erase the
    // current session instead.
    //
    // @param newSession A new session to manage, or null to 'log out'.
    //
    public static void setSession(GameUpSession newSession) {
        if (newSession == null) {
            KeyValueStorage.deleteKeyValue(SESSION_KEY);
            session = null;
        }
        else {
            KeyValueStorage.setValue(SESSION_KEY, newSession.toString());
            session = newSession;
        }
    }
}
        </code>
      </pre>

      <p>
        Finally, here's a complete example activity that catches Soomla events and triggers the correct GameUp actions:
      </p>

      <pre>
        <code class="java">
import android.app.Activity;

import com.soomla.BusProvider;
import com.soomla.levelup.data.ScoreStorage;
import com.soomla.levelup.events.ScoreRecordChangedEvent;
import com.soomla.levelup.events.ScoreRecordReachedEvent;
import com.soomla.profile.events.auth.LoginFinishedEvent;
import com.soomla.profile.events.auth.LogoutFinishedEvent;
import com.squareup.otto.Subscribe;

import io.gameup.android.GameUp;
import io.gameup.android.GameUpSession;
import io.gameup.android.entity.Achievement;
import io.gameup.android.entity.Rank;

public class SoomlaGameUpActivity extends Activity {

    @Override
    public void onResume() {
        super.onResume();

        // Initialise the GameUp SDK.
        GameUp.init(this, "your-api-key-here");

        // Register this instance to receive Soomla events.
        BusProvider.getInstance().register(this);
    }

    @Override
    public void onPause() {
        // Unregister from Soomla events
        BusProvider.getInstance().unregister(this);

        super.onPause();
    }

    //
    // Listener that handles login events.
    //
    @Subscribe
    public void onLoginFinished(LoginFinishedEvent event) {
        String type = event.getProvider().toString();
        String id = event.UserProfile.getProfileId();

        // Use the resulting social profile type and ID to request a new
        // GameUp session for this user.
        String acc = "io.gameup.accounts.com.soomla.profile." + type + "." + id;
        GameUpSession session = GameUp.loginAnonymous(acc);

        // Store the session for future use.
        GameUpSessionHelper.setSession(session);
    }

    //
    // Listener that handles logout events.
    //
    @Subscribe
    public void onLogoutFinished(LogoutFinishedEvent event) {
        // Drop the GameUp session when the user logs out.
        GameUpSessionHelper.setSession(null);
    }

    //
    // Listener that submits a particular new Soomla record score to a GameUp
    // leaderboard.
    //
    @Subscribe
    public void onScoreRecordChanged(ScoreRecordChangedEvent event) {
        String scoreId = event.ScoreId;

        // See if we want to report this score. Each GameUp leaderboard is
        // usually only relevant to one score type, but we can have more than
        // one leaderboard and submit different scores to each one!
        if ("soomla-score-id-we-care-about".equals(scoreId)) {

            // Now check if the user is logged in, otherwise we can't report the
            // score.
            GameUpSession session = GameUpSessionHelper.getSession();
            if (session != null) {

                // Finally, report the score to GameUp!
                // Note: GameUp prefers whole numbers as score values, but they
                // can represent anything. For example for a Soomla score of
                // 10.25 you might submit to GameUp the value 1025.
                long score = (long) ScoreStorage.getLatestScore(scoreId);
                Rank rank = session.leaderboard("gameup-leaderboard-id", score);

                // Now we might do something with the rank, such as notify the
                // user if they've got a new best rank.
            }
        }
    }

    //
    // An example of how a GameUp achievement might be triggered. This specific
    // example achievement is triggered by exactly matching any current high
    // score, but not beating it.
    //
    @Subscribe
    public void onScoreRecordReached(ScoreRecordReachedEvent event) {
        // We can only trigger achievements if we have a session.
        GameUpSession session = GameUpSessionHelper.getSession();
        if (session != null) {
            // This particular achievement just requires that any score is
            // matched, so we don't mind what the scoreId was.
            Achievement ach = session.achievement("gameup-achievement-id");
            if (ach != null) {
                // If this is not null, it means the achievement was just
                // unlocked, so we might congratulate the user.
            }
        }
    }
}
        </code>
      </pre>
    </div>
  </div>

</div>

<div class="samples-title">Getting Started</div>

GameUp is a **free** BaaS (backend as a service) for game developers which offers a plethora of features: user accounts, social login, cloud storage, multiplayer, leaderboards, achievements and push notifications.

1. Go to the [GameUp Dashboard](https://dashboard.gameup.io/#/signup) to sign up for free.

2. Get your API key and integrate the code samples above into your game.

3. Initialize SOOMLA Profile and LevelUp.  Follow all steps in the platform specific getting started guides. <br> (<a href="http://know.soom.la/unity/profile/profile_gettingstarted/" target="_blank">Unity Profile</a> | <a href="http://know.soom.la/unity/levelup/levelup_gettingstarted/" target="_blank">Unity LevelUp</a> | <a href="http://know.soom.la/android/profile/profile_gettingstarted/" target="_blank">Android Profile</a>)

3. Check out the [GameUp website](https://gameup.io/) and [documentation](https://gameup.io/docs/) for more details.

4. We're here for you, [get in touch](https://gameup.io/contact/) and we can help you with integration, feature design, and more!


<div class="samples-title">Downloads</div>

* The [GameUp Unity SDK](https://github.com/gameup-io/gameup-unity-sdk) is available to download on [Github](https://github.com/gameup-io/gameup-unity-sdk/releases).

* The [GameUp Android SDK](https://github.com/gameup-io/gameup-android-sdk) is available through [Maven Central](http://search.maven.org/#search%7Cga%7C1%7Cio.gameup.android).
