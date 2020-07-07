---
ID: 14179
post_title: >
  Automatically place an Android Phone on
  Vibrate at Work
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2016/07/21/automatically-place-an-android-phone-on-vibrate-at-work/
published: true
post_date: 2016-07-21 07:30:06
---
Whether or not your place of employment requires that your cellphone be placed on silent or not while in the office, it's simply a common courtesy to prevent interruptions and distractions to your co-workers when your twenty-seven different social media, email, phone, voicemail, and shopping applications that are installed on your phone constantly chime or play your favorite song every thirty seconds.

The problem is, who can remember to silence their phone after arriving in the office before it makes a half dozen or so of these sounds and while a half dozen notifications may not sound like a big deal compound them by a hundred or more people in the office and it has become a major problem in society, especially when your co-workers within hearing distance are on business calls with customers.

The solution is to automate the task of silencing your phone when arriving at work. On my Android phone, I use a program named "<a href="https://play.google.com/store/apps/details?id=net.dinglisch.android.taskerm&amp;hl=en" target="_blank">Tasker</a>" to setup this sort of automation and it's capable of all sorts of automation. The app is $2.99 in the Google Play store (I paid for Tasker personally and no one sponsored this blog article).

Open Tasker on your Android phone or tablet and click the "+" at the bottom of the application:

<a href="http://mikefrobbins.com/wp-content/uploads/2016/07/tasker1c.png"><img class="alignnone size-full wp-image-14222" src="http://mikefrobbins.com/wp-content/uploads/2016/07/tasker1c.png" alt="tasker1c" width="300" height="480" /></a>

<hr />

Select State:

<a href="http://mikefrobbins.com/wp-content/uploads/2016/07/tasker2c.png"><img class="alignnone size-full wp-image-14223" src="http://mikefrobbins.com/wp-content/uploads/2016/07/tasker2c.png" alt="tasker2c" width="300" height="265" /></a>

<hr />

Net:

<a href="http://mikefrobbins.com/wp-content/uploads/2016/07/tasker3c.png"><img class="alignnone size-full wp-image-14224" src="http://mikefrobbins.com/wp-content/uploads/2016/07/tasker3c.png" alt="tasker3c" width="300" height="343" /></a>

<hr />

Wifi Connected:

<a href="http://mikefrobbins.com/wp-content/uploads/2016/07/tasker4c.png"><img class="alignnone size-full wp-image-14225" src="http://mikefrobbins.com/wp-content/uploads/2016/07/tasker4c.png" alt="tasker4c" width="300" height="361" /></a>

<hr />

Enter the name of the access points you connect to at work. In the following scenario, two possible networks are available, one with the SSID of network1 and another with network2. If your employer doesn't allow connections to their network but networks are available, you can select "Wifi Near" in the previous step. You can also optionally narrow down the scope of the wireless network by selecting specific MAC or IP addresses of the access points:

<a href="http://mikefrobbins.com/wp-content/uploads/2016/07/tasker5d.png"><img class="alignnone size-full wp-image-14244" src="http://mikefrobbins.com/wp-content/uploads/2016/07/tasker5d.png" alt="tasker5d" width="300" height="307" /></a>

<hr />

New Task:

<a href="http://mikefrobbins.com/wp-content/uploads/2016/07/tasker6d.png"><img class="alignnone size-full wp-image-14245" src="http://mikefrobbins.com/wp-content/uploads/2016/07/tasker6d.png" alt="tasker6d" width="300" height="158" /></a>

<hr />

Enter a task name. I entered "Vibrate" since that's what mode this task will place my phone in:

<a href="http://mikefrobbins.com/wp-content/uploads/2016/07/tasker7d.png"><img class="alignnone size-full wp-image-14246" src="http://mikefrobbins.com/wp-content/uploads/2016/07/tasker7d.png" alt="tasker7d" width="300" height="163" /></a>

<hr />

Click the "+" at the bottom of the application:

<a href="http://mikefrobbins.com/wp-content/uploads/2016/07/tasker8c.png"><img class="alignnone size-full wp-image-14229" src="http://mikefrobbins.com/wp-content/uploads/2016/07/tasker8c.png" alt="tasker8c" width="300" height="479" /></a>

<hr />

Audio:

<a href="http://mikefrobbins.com/wp-content/uploads/2016/07/tasker9c.png"><img class="alignnone size-full wp-image-14230" src="http://mikefrobbins.com/wp-content/uploads/2016/07/tasker9c.png" alt="tasker9c" width="300" height="513" /></a>

<hr />

Silent Mode:

<a href="http://mikefrobbins.com/wp-content/uploads/2016/07/tasker10c.png"><img class="alignnone size-full wp-image-14231" src="http://mikefrobbins.com/wp-content/uploads/2016/07/tasker10c.png" alt="tasker10c" width="300" height="482" /></a>

<hr />

Vibrate:

<a href="http://mikefrobbins.com/wp-content/uploads/2016/07/tasker11c.png"><img class="alignnone size-full wp-image-14232" src="http://mikefrobbins.com/wp-content/uploads/2016/07/tasker11c.png" alt="tasker11c" width="300" height="155" /></a>

<hr />

The profile to automatically place your phone into vibrate mode anytime it's connected to a wireless network with the SSID of network1 or network2 is now complete:

<a href="http://mikefrobbins.com/wp-content/uploads/2016/07/tasker12d.png"><img class="alignnone size-full wp-image-14247" src="http://mikefrobbins.com/wp-content/uploads/2016/07/tasker12d.png" alt="tasker12d" width="300" height="151" /></a>

<hr />

You'll need to create another profile to turn the ringer of your phone back on anytime you leave work. Repeat the same process again except check the "Invert" checkbox:

<a href="http://mikefrobbins.com/wp-content/uploads/2016/07/tasker13d.png"><img class="alignnone size-full wp-image-14248" src="http://mikefrobbins.com/wp-content/uploads/2016/07/tasker13d.png" alt="tasker13d" width="300" height="311" /></a>

<hr />

Create another new task:

<a href="http://mikefrobbins.com/wp-content/uploads/2016/07/tasker14d.png"><img class="alignnone size-full wp-image-14249" src="http://mikefrobbins.com/wp-content/uploads/2016/07/tasker14d.png" alt="tasker14d" width="300" height="270" /></a>

<hr />

Name this one "Ringer" or something descriptive:

<a href="http://mikefrobbins.com/wp-content/uploads/2016/07/tasker15d.png"><img class="alignnone size-full wp-image-14250" src="http://mikefrobbins.com/wp-content/uploads/2016/07/tasker15d.png" alt="tasker15d" width="300" height="244" /></a>

<hr />

Set the mode to Off for this one instead of Vibrate (it turns silent mode off):

<a href="http://mikefrobbins.com/wp-content/uploads/2016/07/tasker16c.png"><img class="alignnone size-full wp-image-14237" src="http://mikefrobbins.com/wp-content/uploads/2016/07/tasker16c.png" alt="tasker16c" width="300" height="154" /></a>

<hr />

Be sure to save your profiles before exiting Tasker:

<a href="http://mikefrobbins.com/wp-content/uploads/2016/07/tasker17d.png"><img class="alignnone size-full wp-image-14251" src="http://mikefrobbins.com/wp-content/uploads/2016/07/tasker17d.png" alt="tasker17d" width="300" height="312" /></a>

<hr />

Once you've saved the profiles and exited Tasker, you'll see an icon for Tasker in the top left corner of your phone that looks like a lightning bolt. You can also see active profiles in the notifications section of your phone or by opening the Tasker application:

<a href="http://mikefrobbins.com/wp-content/uploads/2016/07/tasker18d.png"><img class="alignnone size-full wp-image-14252" src="http://mikefrobbins.com/wp-content/uploads/2016/07/tasker18d.png" alt="tasker18d" width="300" height="189" /></a>

This is just the tip of the iceberg as far as automating your Android phone goes.

µ