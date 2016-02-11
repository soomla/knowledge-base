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
    <div role="tabpanel" class="tab-pane" id="sample-android">
      <p>
        First, let's create a static helper class to handle the Heroic Labs session for us:
      </p>

      <pre>
```
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
```
      </pre>

      <p>
        Finally, here's a complete example activity that catches Soomla events and triggers the correct Heroic Labs actions:
      </p>

      <pre>
```
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

public class SoomlaHeroicLabsActivity extends Activity {

    @Override
    public void onResume() {
        super.onResume();

        // Initialise the Heroic Labs SDK.
        GameUp.init(this, "your-api-key-here");

        // Register this instance to receive SOOMLA events.
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
        // Heroic Labs session for this user.
        String acc = "com.heroiclabs.accounts.com.soomla.profile." + type + "." + id;
        GameUpSession session = GameUp.loginAnonymous(acc);

        // Store the session for future use.
        GameUpSessionHelper.setSession(session);
    }

    //
    // Listener that handles logout events.
    //
    @Subscribe
    public void onLogoutFinished(LogoutFinishedEvent event) {
        // Drop the Heroic Labs session when the user logs out.
        GameUpSessionHelper.setSession(null);
    }

    //
    // Listener that submits a particular new Soomla record score to a Heroic Labs
    // leaderboard.
    //
    @Subscribe
    public void onScoreRecordChanged(ScoreRecordChangedEvent event) {
        String scoreId = event.ScoreId;

        // See if we want to report this score. Each Heroic Labs leaderboard is
        // usually only relevant to one score type, but we can have more than
        // one leaderboard and submit different scores to each one!
        if ("soomla-score-id-we-care-about".equals(scoreId)) {

            // Now check if the user is logged in, otherwise we can't report the
            // score.
            GameUpSession session = GameUpSessionHelper.getSession();
            if (session != null) {

                // Finally, report the score to Heroic Labs!
                // Note: Heroic Labs prefers whole numbers as score values, but they
                // can represent anything. For example for a Soomla score of
                // 10.25 you might submit to Heroic Labs the value 1025.
                long score = (long) ScoreStorage.getLatestScore(scoreId);
                Rank rank = session.leaderboard("heroic-labs-leaderboard-id", score);

                // Now we might do something with the rank, such as notify the
                // user if they've got a new best rank.
            }
        }
    }

    //
    // An example of how a Heroic Labs achievement might be triggered. This specific
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
            Achievement ach = session.achievement("heroic-labs-achievement-id");
            if (ach != null) {
                // If this is not null, it means the achievement was just
                // unlocked, so we might congratulate the user.
            }
        }
    }
}
```
      </pre>
    </div>
</div>

<div class="samples-title">Getting Started</div>

Heroic Labs is a **free** game backend for developers which offers a plethora of features: user accounts, social login, cloud storage, multiplayer, leaderboards, achievements and much more...

1. Go to the <a href="https://dashboard.heroiclabs.com/#/signup" target="_blank">Heroic Labs Dashboard</a> to sign up for free.

2. Get your API key and integrate the code samples above into your game.

3. Integrate SOOMLA Profile and LevelUp.  Follow all steps in the platform specific getting started guides. <br>
    <a href="/soomla/unity/store/Profile_GettingStarted/" target="_blank">Unity Profile</a> |
    <a href="/unity/levelup/levelup_gettingstarted/" target="_blank">Unity LevelUp</a> |
    <a href="/soomla/android/profile/profile_gettingstarted/" target="_blank">Android Profile</a>

4. Check out the <a href="https://heroiclabs.com/" target="_blank">Heroic Labs website</a> and <a href="https://heroiclabs.com/docs/" target="_blank">documentation</a> for more details.

5. We're here for you, <a href="https://heroiclabs.com/contact/" target="_blank">get in touch</a> and we can help you with integration, feature design, and more!


<div class="samples-title">Downloads</div>

* The <a href="https://github.com/gameup-io/gameup-unity-sdk" target="_blank">Heroic Labs Unity SDK</a> is available to download on <a href="https://github.com/gameup-io/gameup-unity-sdk/releases" target="_blank">Github</a>.

* The <a href="https://github.com/gameup-io/gameup-android-sdk" target="_blank">Heroic Labs Android SDK</a> is available through <a href="http://search.maven.org/#search%7Cga%7C1%7Cio.gameup.android" target="_blank">Maven Central</a>.
