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
