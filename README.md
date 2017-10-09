Android Push Notificstion Learning Notes
============================================

#### Changes 
1. Change package name to "org.sunnycorp.sunnypn" to work with my Firebase notfication project;
1. Add click_action handling for both background and foreground so that it can launch activity other than the default launcher. 

#### Payload
Not sure why; the payload can have "notification" node or "data" node and they work differently. When the payload contains "data" node, "FirebaseMessagingService" is always triggered, no matter whether the app is launched or not, or whether it is in the foreground or background. It works very consistently and may be the best way to go. It does require extra work by building a notification UI after receiving it (otherwise it would not show the notification in the notification center), on the other hand, it is super flexible. 

If we use "notification" node in the payload, it seems that it would fire up "FirebaseMessagingService#onMessageReceived" only if the app is already launched and in the foreground; if not(either the app is not launched yet, or it is launched but in background), the notification would show in notification center (without the need to build notification interface through notification builder). 

In summary, seems like "notification" node as payload is an old way of doing things. It is not preferred for 2 reasons: 1) It is not as consisent as "data" would allow handling in one place, that is, "FirebaseMessagingService#onMessageReceived". 2) It is not as flexible, because we cannot create our own notification interface. Using "data" would allow us to fire up notification interfaces built through notification builder, in many default styles (big text, big picture, inbox, etc) and even customized RemoteViews). 

#### Firebase and AWS SNS integration

##### Firebase
* [Firebase Console](https://console.firebase.google.com/project/firebase-sunnypn/notification). It seems that Firebase Console only sends the payload through "notification" node, unless we use Firebase CLI as [Mastering Firebase Notifications](https://medium.com/@Miqubel/mastering-firebase-notifications-36a3ffe57c41).
* [AWS SNS Console](https://us-west-2.console.aws.amazon.com/sns/v2/home?region=us-west-2#/applications/arn:aws:sns:us-west-2:221118179913:app/GCM/SunnyPNAndroid). AWS SNS Console sends out the payload through "data" node, and the payload shall be something like below: 

```
{
"GCM": "{ \"data\": { \"title\": \"Sunny Message\",\"text\": \"test message\",\"sound\":\"default\",\"click_action\":\"org.sunnycorp.sunnypn.Main2Activity\"} }"
}

```
Also got to set TTL time to some positive value say 8. Another note is that the GCM API key can be found as instructed here as [Firebase Server Key](https://stackoverflow.com/questions/38300450/fcm-with-aws-sns). 

#### References 
* [Android Developers Training - Notifying the User](https://developer.android.com/training/notify-user/index.html)
* How to show custom layout for remote push notificsation, see [StackOverflow](https://stackoverflow.com/questions/36945702/how-to-show-custom-ui-for-a-gcm-push-notification), like [Google Cloud Messaging Overview](http://www.androiddocs.com/google/gcm/gcm.html), `It does not provide any built-in user interface or other handling for message data. GCM simply passes raw message data received straight to the Android application, which has full control of how to handle it. For example, the application might post a notification, display a custom user interface, or silently sync data`.
* [How to create a Custom Notification Layout in android?](https://stackoverflow.com/questions/41888161/how-to-create-a-custom-notification-layout-in-android)
* [Android Notification API Guide](https://developer.android.com/guide/topics/ui/notifiers/notifications.html)
* [Getting Started With Push Notifications on Android](https://github.com/hathibelagal/GCM-Push-Notifications)
* [Firebase Cloud Messaging](https://firebase.google.com/docs/cloud-messaging/)
* [Amazon Simple Notification Service (SNS)](https://aws.amazon.com/sns/)
* [Tuts+ tutorial about Android O's notification channels](https://github.com/chikecodes/tutsplus-Android-o-notification-channels) and a similar one at Android Authority, [Creating an Android 8.0 Oreo app: Implementing Notification Channels](http://www.androidauthority.com/android-8-0-oreo-app-implementing-notification-channels-801097/)

Firebase Cloud Messaging Quickstart
==============================

The Firebase Cloud Messaging Android Quickstart app demonstrates registering
an Android app for notifications and handling the receipt of a message.
**InstanceID** allows easy registration while **FirebaseMessagingService** and **FirebaseInstanceIDService**
enable token refreshes and message handling on the client.

Introduction
------------

- [Read more about Firebase Cloud Messaging](https://firebase.google.com/docs/cloud-messaging)

Getting Started
---------------

- [Add Firebase to your Android Project](https://firebase.google.com/docs/android/setup).
- Run the sample on Android device or emulator.

Sending Notifications
---------------------

Use Firebase console to send FCM messages to device or emulator.

## Send to a single device

- From Firebase console Notification section, click **New Message**.
- Enter the text of your message in the Message Text field.
- Set the target to **Single Device**.
- Check the logs for the **InstanceID** token, copy and paste it into the Firebase console Token field.
  - If you cannot find the token in your logs, click on the **LOG TOKEN** button in the application and the token will
  be logged in **logcat**.
- Click on the **Send Message** button.
- If your application is in the foreground you should see the incoming
  message printed in the logs. If it is in the background, a system notification should be
  displayed. When the notification is tapped, the application should return to the quickstart application.

## Send to a topic

- From Firebase console Notification section, click **New Message**.
- Enter the text of your message in the Message Text field.
- Click on the **SUBSCRIBE TO NEWS** button to subscribe to the news topic.
- Set the target to **Topic**.
- Select the news topic from the list of topics ("news" in this sample). 
  You must subscribe from the device or emulator before the topic will will be visible in the console.
- Click on the **Send Message** button.
- If your application is in the foreground you should see the incoming
  message printed in the logs. If it is in the background, a system notification should be
  displayed. When the notification is tapped, the application should return to the quickstart application.

Best Practices
--------------

## Android notification channels

### Set default channel

If incoming FCM messages do not specify an Android notification channel, you can indicate
to FCM what channel should be used as the default by adding a metadata element to your
application manifest. In the metadata element specify the ID of the channel that should
be used by default by FCM.

    <meta-data
        android:name="com.google.firebase.messaging.default_notification_channel_id"
        android:value="default_channel_id"/>

Note: You are still required to create a notification channel in code with an ID that
matches the one defined in the manifest. See the Android [docs](https://goo.gl/x9fh5X) for more.

## Customize default notification

### Custom default icon

Setting a custom default icon allows you to specify what icon is used for notification
messages if no icon is set in the notification payload. Also use the custom default
icon to set the icon used by notification messages sent from the Firebase console.
If no custom default icon is set and no icon is set in the notification payload,
the application icon (rendered in white) is used.

### Custom default Color

You can also define what color is used with your notification. Different android
versions use this settings in different ways: Android < N use this as background color
for the icon. Android >= N use this to color the icon and the app name.

See the [docs](https://goo.gl/sPggnS) for more.

Result
-----------
<img src="app/src/screen.png" height="534" width="300"/>

Support
-------

- [Stack Overflow](https://stackoverflow.com/questions/tagged/firebase-cloud-messaging)
- [Firebase Support](https://firebase.google.com/support/)

License
-------

Copyright 2016 Google, Inc.

Licensed to the Apache Software Foundation (ASF) under one or more contributor
license agreements.  See the NOTICE file distributed with this work for
additional information regarding copyright ownership.  The ASF licenses this
file to you under the Apache License, Version 2.0 (the "License"); you may not
use this file except in compliance with the License.  You may obtain a copy of
the License at

  http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS, WITHOUT
WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.  See the
License for the specific language governing permissions and limitations under
the License.
