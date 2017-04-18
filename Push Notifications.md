| # | Section |
| ---- | ---------------- |
| 1 | [iOS Setup](#1-ios-setup) |
| 2 | [Android Setup](#2-android-setup) |

To set up "Push" notifications for your app, please follow the instructions for your specific platform(s). Please note, you'll first have to install either our [iOS](https://github.com/taplytics/taplytics-ios-sdk/blob/master/START.md) or [Android](https://github.com/taplytics/Taplytics-Android-SDK/blob/master/START.md) SDKs before you integrate Push notifications. 

If you want to implement Push Notifications for [React Native](https://github.com/taplytics/taplytics-react-native), due to the nature of push notifications and their dependencies on the specific platforms, setup for each individual platform must be performed natively.

Setting up Push Notifications using Taplytics is simple. Follow the steps below to get started.


# 1. iOS Setup

## Required Code for iOS 9 and Below

For iOS and Taplytics to know that your app accepts Push Notifications, you must implement the following methods on your `UIApplicationDelegate`.

```
// Implement these methods for Taplytics Push Notifications
- (void)application:(UIApplication *)application
didRegisterUserNotificationSettings:(UIUserNotificationSettings *)notificationSettings {
}

- (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken {
}

- (void)application:(UIApplication *)application didFailToRegisterForRemoteNotificationsWithError:(NSError *)error {
}

// Method will be called if the app is open when it recieves the push notification
- (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo {
    // "userInfo" will give you the notification information
}

// Method will be called when the app recieves a push notification
- (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo
fetchCompletionHandler:(void (^)(UIBackgroundFetchResult))completionHandler {
    // "userInfo" will give you the notification information
    completionHandler(UIBackgroundFetchResultNoData);
}
```

## Required Code for iOS 10

For iOS 10, you'll need to implement the new `UserNotification` class to allow Taplytics and iOS to accept Push Notifications. You will need to change your `UIApplicationDelegate` header file to look something like the following

```
#import <UIKit/UIKit.h>
#import <UserNotifications/UserNotifications.h>

@interface AppDelegate : UIResponder <UIApplicationDelegate, UNUserNotificationCenterDelegate>

@end

```

You will also need to add the following methods to your `UIApplicationDelegate`

```
// Implement these methods for Taplytics Push Notifications
- (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken {
}

- (void)application:(UIApplication *)application didFailToRegisterForRemoteNotificationsWithError:(NSError *)error {
}

// Method will be called when the app recieves the push notification
- (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo
fetchCompletionHandler:(void (^)(UIBackgroundFetchResult))completionHandler {
   // "userInfo" will give you the notification information
   completionHandler(UIBackgroundFetchResultNoData);
}

// Method will be called if the app is open when it recieves the push notification
- (void)userNotificationCenter:(UNUserNotificationCenter *)center willPresentNotification:(UNNotification *)notification withCompletionHandler:(void (^)(UNNotificationPresentationOptions))completionHandler{
   // "notification.request.content.userInfo" will give you the notification information
   completionHandler(UNNotificationPresentationOptionBadge);
}

// Method will be called if the user opens the push notification
- (void)userNotificationCenter:(UNUserNotificationCenter *)center didReceiveNotificationResponse:(UNNotificationResponse *)response withCompletionHandler:(void (^)())completionHandler {
    // "response.notification.request.content.userInfo" will give you the notification information
    completionHandler();
}
```
If you want your app to also support lower versions of iOS, you just need to add the missing methods described in the above section.

## Register for Push Notifications

You'll also need to register for push notifications with iOS. When you do so, iOS will ask your users for permission and enable the ability to receive notifications to that device.

You'll need to enable a few capabilities on your app. Go into your project settings and find your project under Targets. Select the Capabilities tab and turn on Push Notifications and Background Modes. Under Background Modes, enable Remote Notifications.

If you are not already registering for push notifications all you have to do is call registerPushNotifications: on Taplytics, and we take care of all the rest!

Please Note that calling this function will show the permission dialog to the user.

```
/* Example usage */
[Taplytics registerPushNotifications];
```

## Register for Location Permissions

For automated push campaigns using location based regions you will need to add the `CoreLocation` framework to your app, and request location permissions from your users. Taplytics will automatically update and manage the monitored regions on your device for your automated push campaigns.

You can handle asking for location permissions yourself, or you can use our provided method as seen below. But make sure that you request `AuthorizedAlways` permissions so that we can set regions.

```
// We will request AuthorizedAlways access to be able to set monitored regions
[Taplytics registerLocationAccess];
```

In order to allow the iOS location manager to successfully display a location request dialog to the user, the following properties must be added to the application's Plist settings:

```
NSLocationAlwaysUsageDescription
NSLocationWhenInUseUsageDescription
```
These values will be used by the OS to display the reason for requesting location access.

# Receiving Push Notifications

To be able to send your users Push Notifications, we'll need you to upload your Apple Push certificates. Please follow this guide to do so.

## Environments (Development and Production)

In order to separate your development devices and production devices, Taplytics automatically buckets devices into Development and Production buckets. We do this by looking at the provisioning profile that the app was built with.

### Development Bucketing

If the app is built using a [Development Provisioning Profile](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppStoreDistributionTutorial/CreatingYourTeamProvisioningProfile/CreatingYourTeamProvisioningProfile.html), we bucket the device into the Development environment. You can change this by forcing an environment.

All devices that fall into the Development environment, Taplytics will use the [Development Push Notification Certificate](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/ConfiguringPushNotifications/ConfiguringPushNotifications.html) to attempt to send push notifications to your devices.

### Production Bucketing

If the app is built using a [Distribution Provisioning Profile](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/TestingYouriOSApp/TestingYouriOSApp.html), we bucket the device into the Production environment. Note that this means that if you're distributing Ad-Hoc or Enterprise builds through a service like [Testflight](https://developer.apple.com/testflight/) or [HockeyApp](http://hockeyapp.net/features/), then all those devices running those builds will fall into the Production environment. You can change this by forcing an environment.

All devices that fall into the Production environment, Taplytics will use the [Production Push Notification Certificate](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/ConfiguringPushNotifications/ConfiguringPushNotifications.html) to attempt to send push notifications to your devices.

### Forcing an Environment

You can use the `options` attribute when you start Taplytics in order to force the environment that your devices are bucketed into. This can be especially useful for any Ad-Hoc and Enterprise distribution you might be doing.

Here's how you can achieve setting the Push environment:
```
// To bucket everything into Production:
[Taplytics startTaplyticsAPIKey:@"SDK_TOKEN_HERE" options:@{@"pushSandbox":@0}];

// To bucket everything into Development:
[Taplytics startTaplyticsAPIKey:@"SDK_TOKEN_HERE" options:@{@"pushSandbox":@1}];
```

# Resetting Users

If you're using our User Attributes feature, you can easily disconnect a user from a device when they log out. This will prevent our system from sending push notifications to that device. It is especially important to reset the user when your push campaigns are targeted to a specific user. You can do this by calling our `resetUser:` function:

```
[Taplytics resetUser:^{
  // Finished User Reset
}];
```

# 2. Android Setup

Setting up Push Notifications using Taplytics is simple. Follow the steps below to get started.

| # | Step |
| ---- | ---------------- |
| 1 | [Android Studio](#1-android-studio) |
| 2 | [Receiving Push Notifications](#2-receiving-push-notifications) |
| 3 | [Push Campaigns] | (#3-push-campaigns)|
| 4 | [Custom Data and Tracking Push Interactions] | (#4-custom-data-and-tracking-push-interactions) |
| 5 | [Special Push Options] | (#5-special-push-options) |
| 6 | [Resetting Users] | (#6-resetting-users) |
| 7 | [Tracking Self Built Notifications] | (#7-tracking-self-built-notifications) |

*New!: Taplytics has updated the way push notifications are handled. [See here!](https://github.com/taplytics/Taplytics-Android-SDK/blob/master/PUSH.md#4-custom-data-and-tracking-push-interactions)*

## 1. Android Studio

If you wish to use Push Notifications on Taplytics, you must add the following permissions (replace com.yourpackagename with your app's package name) to your Android Manifest:

```
<uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
<permission android:name="com.yourpackagename.permission.C2D_MESSAGE"/>
<uses-permission android:name="com.yourpackagename.permission.C2D_MESSAGE" />
<uses-permission android:name="android.permission.WAKE_LOCK" />
```

And you must add the following receiver and service under your application tag:

```
<receiver
    android:name="com.taplytics.sdk.TLGcmBroadcastReceiver"
    android:permission="com.google.android.c2dm.permission.SEND" >
    <intent-filter>
        <action android:name="com.google.android.c2dm.intent.RECEIVE" />
    </intent-filter>

    <intent-filter>
         <action android:name="taplytics.push.OPEN" />
         <action android:name="taplytics.push.DISMISS" />
    </intent-filter>
</receiver>
<service android:name="com.taplytics.sdk.TLGcmIntentService" />
```
Then add the following to your build.gradle:
```
compile("com.google.android.gms:play-services-gcm:9.+")
```
In order to set the notification icon you must add a meta-tag to your manifest specifying the drawable you want to use as the icon:
```
<meta-data android:name="com.taplytics.sdk.notification_icon"
            android:resource="@drawable/notification_icon"/>
```

If this isn't set the application's icon will be used instead.

## 2. Receiving Push Notifications

To send your users Push Notifications, we'll need you to upload your Firebase Cloud Messaging credentials. Please follow [this guide](https://taplytics.com/docs/guides/push-notifications/google-push-certificates) to do so.

### Activity Routing

By default, when a notification sent by Taplytics is clicked, it will open up the main activity of the application. However, you may want to route your users to a different Activity. This can be done on the Taplytics Push page.

Simply add a custom data value to the push with the key `tl_activity` and with the **full** (including package name) class name of your activity. For example:
![activityrouting]

[activityrouting]: https://s3.amazonaws.com/cdn.taplytics.com/Images/push_custom_data.png

### Push Title

By default, the title of a push notification will be the application name.

Currently, the best way to change the title of a push notification is to add a `tl_title` custom key. For Example:

![pushtitle]

[pushtitle]: https://s3.amazonaws.com/cdn.taplytics.com/Images/push_custom_data_2.png

### Getting the Push Token

Sometimes, it can be useful to have the actual token generated by FCM, to target pushes at specific users.

To get this token, use the following method:
```
Taplytics.setTaplyticsPushTokenListener(new TaplyticsPushTokenListener() {
    @Override
    public void pushTokenReceived(String token) {
        //Do something with the push token here.
    }
});
```

## 3. Push Campaigns

Push Campaigns allow you to send pushes in reaction to events, called triggers. Location based triggers use the Google Play Services Location API in order to create geofences for the locations you specify.

For location based triggers add the following two permissions to you manifest:
```
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION"/>
<uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED"/>
```
The `ACCESS_FINE_LOCATION` permission is used to get the devices location, and the `RECEIVE_BOOT_COMPLETED` permission is to re-register the locations which the campaign is triggered in, which get cleared upon reboot.

Also needed is a boot receiver to re-register events, and an intent service to react to geofence events. Both which go in your manifest, under the application tag:
```
<receiver android:name="com.taplytics.sdk.TLBootReceiver">
    <intent-filter>
        <action android:name="android.intent.action.BOOT_COMPLETED"/>
    </intent-filter>
</receiver>

<service android:name="com.taplytics.sdk.TLGeofenceEventService"/>
```
The only additional dependency needed is Google Play Services Location API, which you can add to your module's build.gradle file under dependencies:

`compile 'com.google.android.gms:play-services-location:9.+'`

**Geofences require location services 9.x**

## 4. Custom Data and Tracking Push Interactions

Taplytics has changed as of version 1.9 and push notifications are easier than ever:

To retrieve custom data set in the Taplytics dashboard, as well as to track push interactions (receive, open, dismiss), simply extend the TLBroadcastReceiver and override the function that you need. Then, rplace the TLGcmBroadcastReceiver in your manifest with that one!

Below is an example receiver that explains exactly how this is done. You can put this class directly in your app and start tracking push notifications right away. By default, taplytics will open the LAUNCH activity of your app, but this can be changed by not calling the super (see example below).

**Note that Taplytics automatically tracks the following, however if you would like to do so for internal reasons, this is how.**

Note there is also a TLGgcmBroadcastReceiverNonWakeful.
```
/**
 * Example receiver to take action with push notifications.
 *
 * Make sure to add this to your manifest (see the docs)
 *
 * Overriding any of these is entirely optional.
 *
 * By default, taplytics will open the launch activity of your
 * app when a push notification is clicked.
 *
 */
public class MyBroadcastReceiver extends TLGcmBroadcastReceiver {

    @Override
    public void pushOpened(Context context, Intent intent) {

        //A user clicked on the notification! Do whatever you want here!

        /* If you call through to the super, 
        Taplytics will launch your app's LAUNCH activity. 
        This is optional. */
        super.pushOpened(context, intent);
    }

    @Override
    public void pushDismissed(Context context, Intent intent) {
        //The push has been dismissed :(

    }

    @Override
    public void pushReceived(Context context, Intent intent) {
        //The push was received, but not opened yet!

        /*
        If you add the custom data of tl_silent = true to the push notification,
        there will be no push notification presented to the user. However, this will
        still be triggered, meaning you can use this to remotely trigger something
        within the application!
         */
    }
}
```
And then in your manifest:
```
<receiver
    android:name=".MyBroadcastReceiver"
    android:permission="com.google.android.c2dm.permission.SEND" >
    <intent-filter>
        <action android:name="com.google.android.c2dm.intent.RECEIVE" />
    </intent-filter>

    <intent-filter>
         <action android:name="taplytics.push.OPEN" />
         <action android:name="taplytics.push.DISMISS" />
    </intent-filter>
</receiver>
<service android:name="com.taplytics.sdk.TLGcmIntentService" />
```

## 5. Special Push Options (title, silent push, etc)

The dashboard allows for custom data to be entered into your push notifications. However there are some options that can be added to the custom data for special functionality.

|Name    |Values     |Explanation |
|--------|-----------|------------|
|tl_title|String     |This changes the TITLE of the push notification. By default, it is your application's name. But with this option you can change the title to be anything.|
|tl_silent|boolean: true/false|	Taplytics does give the option to send a SILENT push notification (meaning it will not actually show up in the user's push notifications). It will, however, still triger the pushReceived callback in the custom receiver above!|
|tl_priority|integer|Set the priority of the push notification. For more info see the section 'Correctly set and manage notification priorty' here. The value set must be the integer that is associated with the priorities, which can be found here.|

## 6. Resetting Users
Sometimes, it may be useful to reset an app user for push notifications. For instance, if a user is logged out in your app, you may want them to stop receiving push notifications. If you wish to turn off push notifications for an app user, it can be done as such:
```
TaplyticsResetUserListener listener = new TaplyticsResetUserListener() {
  @Override
  public void finishedResettingUser() {
    // Do stuff
  }
};

Taplytics.resetAppUser(listener);
```
Now, the device that the app is currently running on will no longer receive push notifications until the app user attributes are updated again.

## 7. Tracking Self Built Notifications

You maybe using Taplytics simply to send push notifications. In the event that you already have a system to build notifications, then when extending the Taplytics BroadcastReceiver, you will see duplicates.

To avoid this problem, first, **do not call** `super.onReceive()` where super would be the `TLGCMBroadcastReceiver`.

Now, **Taplytics will not have any push notification tracking if you do this**.

To mitigate this, you must use the Taplytics functions provided. In each function, **you must pass in the tl_id in the notification attempt**.

### Push Open
```
Taplytics.trackPushOpen("tl_id",customKeys);
```
Where tl_id is retrieved from the notification intent. CustomKeys is the metadata passed into the notification. It is optional/nullable

### Push Dismissed
```
Taplytics.trackPushDismissed("tl_id",customKeys);
```
Where tl_id is retrieved from the notification intent. CustomKeys is the metadata passed into the notification. It is optional/nullable

### Push Received
```
Taplytics.trackPushReceived("tl_id",customKeys);
```
Where tl_id is retrieved from the notification intent. CustomKeys is the metadata passed into the notification. It is optional/nullable
