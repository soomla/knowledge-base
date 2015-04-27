---
layout: "content"
image: "Tutorial"
title: "About GROW"
text: "Learn about the power of GROW Analytics, how it can benefit game developers, and how to use the GROW dashboard."
position: 1
theme: 'platforms'
collection: 'cocos2dx_grow'
module: 'grow'
platform: 'cocos2dx'
---

# GROW

## About

Soomla GROW is our flagship community-driven analytics dashboard. Developers using GROW can gain valuable insights about their games' performance and compare the data to benchmarks of other games in the GROW community.

GROW analytics uses all of Soomla's modules: [Store](/cocos2dx/cpp/store/), [Profile](/cocos2dx/cpp/profile/) and [LevelUp](/cocos2dx/cpp/levelup/). You may choose to use only specific modules, however, to benefit from the full power of GROW analytics we recommend that you integrate all three.

To get started with GROW, please follow our [Getting Started tutorial](/cocos2dx/cpp/grow/Grow_GettingStarted) for Cocos2d-x.

### Why use GROW?

- With GROW, you can see how you're app is doing in a number of different aspects, including average revenue, average session duration, what percentage of your users are social in your game, and more. These are all explained in detail in the section below.

- GROW is different than other analytics services because it allows data sharing. In the dashboard, you'll be able to see aggregative analytics about other developers' apps as well as your own. This feature makes it possible for you to **compare your game to other indie games** and see how you measure.

## GROW Dashboard

The [GROW dashboard](http://dashboard.soom.la) consists of several screens where you can see different information.

### **Main Page**

![alt text](/img/tutorial_img/unity_grow/demoGame.png "Demo Game Main Screen")

- **ACTIVE USERS** - See how many active users your game has and filter by time (today, last week, last month, or all time).

- **GAME EVENTS** - See how many events have been fired in your game and filter by time (today, last week, last month, or all time).

<br>

<div class="info-box">In the following screens of the dashboard, each statistic represents some type of information about your game. Under each of these there's another statistic in blue, which represents the information about all the other games using GROW. Also notice the color coding: green means that your statistic is better than or equal to the others' statistic, yellow means that you're a bit behind, and red means you're far behind.</div>


### **Overview**

In this part of the dashboard, you can filter by Date, Country, and Device. At the time of writing this article, the Benchmark filter is not yet available, but once it comes out, you'll be able to compare how your users behave in other games.

<br>

#### **NUMBERS:**

![alt text](/img/tutorial_img/unity_grow/overviewNumbers.png "Numbers")


- **ARPU** - Average Revenue Per User

  For example, if you have 100 users, and only 1 of them paid $1.00, your ARPU will be

  $1.00 / 100 users = **$0.01**.

- **ARPPU** - Average Revenue Per **Paying** User

  Following the example above, you have 1 paying user that paid $1.00, so your ARPPU will be

  $1.00 / 1 user = **$1.00**

- **TIMES PLAYED** - The average number of times your game has been played.

- **SESSION DURATION** - The average session duration.

<br>

#### **CONVERSION:**

![alt text](/img/tutorial_img/unity_grow/overviewConversion.png "Conversion")

This shows what percentage of your users make purchases in your game, rate your game, or connect with social networks, all according to the filters you chose at the top. For example, you can see that 1.8% of your users rated your game according to the filters chosen (All time, All countries, All devices). In comparison to the others, you're doing great, because the comparison statistic is 1.4%.


### **Conversion & Progression**

This screen shows the state of your users when they converted (purchased, rated, or 'liked'). You can filter by Conversion action, Date, Country, and Device. In the future, you will be able to filter by benchmark as well.

<br>

#### **CONVERSION MOMENT:**

![alt text](/img/tutorial_img/unity_grow/convMoment.png "Conversion Moment")

- **HAD** - On average, your users perform an action (purchase, rate, or 'like') after they had this number of a specific virtual currency or virtual good. For example, in the image above, the conversion action filter is set to "Purchase" and the filter for "HAD" is set to "Coins" - therefore, on average your users purchase after they had 873 coins.

- **PASSED LEVELS** - On average, your users perform an action (purchase, rate, or 'like') after they passed this number of levels (unique levels and total levels). For example, in the image above, the conversion action filter is set to "Purchase" - therefore, on average your users purchase after they passed 9 unique levels (24 total levels).

- **COMPLETED MISSIONS** - On average, your users perform an action (purchase, rate, or 'like') after they completed this number of missions. For example, in the image above, the conversion action filter is set to "Purchase" - therefore, on average your users purchase after they completed 0 missions.

<br>

#### **DAILY CONVERSION:**

![alt text](/img/tutorial_img/unity_grow/dailyConv.png "Daily Conversion")

This graph presents the number of users who performed the conversion action (purchase, rate, 'like') you chose in the date range you chose.

### **Countries & Devices**

In this part of the dashboard, you can filter by Date and Conversion action (purchase, rate, 'like').

<br>

#### **COUNTRIES:**

![alt text](/img/tutorial_img/unity_grow/countries.png "Countries")

Hover over any country on the map and see what percentage of your users in that country performed the action you defined in the filter. For example, in the image above, you can see that 4.1% of your **United States users** that played **yesterday** performed a **social** action in your game (e.g. Facebook connect, like, share status, etc.). You can see how you compare to other games that use GROW - the other games together have 6.0% for this metric, which means you're a bit behind.  This is a thoughtful insight that can lead you to re-evaluate social engagement in your game.

<br>

#### **DEVICES:**

![alt text](/img/tutorial_img/unity_grow/devices.png "Devices")

See the percentage of your users who performed the action you defined in the filter per device. For example, 4.5% of your iPhone users rated your game. 6.2% of the other games' iPhone users rated their games, so your game is a bit behind the benchmark.
