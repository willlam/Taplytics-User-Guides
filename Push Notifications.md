| # | Section |
| ---- | ---------------- |
| 1 | [Apple Certificates](#1-apple-certificates) |
| 2 | [Goolge Push Certificates](#2-google-push-certificates) |

To set up "Push" notifications for your app, please follow the instructions for your specific platform(s). Please note, you'll first have to install either our [iOS](https://github.com/taplytics/taplytics-ios-sdk/blob/master/START.md) or [Android](https://github.com/taplytics/Taplytics-Android-SDK/blob/master/START.md) SDKs before you integrate Push notifications. 

If you want to implement Push Notifications for [React Native](https://github.com/taplytics/taplytics-react-native), due to the nature of push notifications and their dependencies on the specific platforms, setup for each individual platform must be performed natively.

Setting up Push Notifications using Taplytics is simple. Follow the steps below to get started.


# 1. Apple Certificates

## Upload your Apple Push certificates so you can send push notifications using Taplytics.

Before you can use Taplytics push notifications, you need to upload your **Apple Push Notification** certificates. If you don’t already have your certificates created, don't worry. We will help you through this three step process.

1. Create a certificate request
2. Generate your push certificates
3. Upload your certificates to Taplytics

## Create a certificate request

To begin, create a certificate signing request file. This is used to authenticate the creation of the certificate. On your Mac, launch the **Keychain Access** application. In **Keychain Access**, click the "Certificate Assistant", and select "Request a Certificate From a Certificate Authority".

![certificateauthority](https://taplytics.com/assets/docs/push/apple/cert-request.png)

Enter your name and email and make sure to click "Saved to disk". Next, press "Continue". Your ".certSigningRequest" file will save to your Mac.

![certinfo](https://taplytics.com/assets/docs/push/apple/cert-create.png)

## Generate your push certificates

Navigate to the "Apple Developer Member Center" website and select "Certificates, Identifiers & Profiles".
![generatepushcerts](https://taplytics.com/assets/docs/push/apple/member-center-hl.png)

Select "Identifiers" from the **iOS Apps** section.
![identifiers](https://taplytics.com/assets/docs/push/apple/identifiers-ar.png)

You will see a list of your iOS App IDs. If your app is not on the list, you can click the "+" button to create a new app ID. If you need some help check out the [Apple doc](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/MaintainingProfiles/MaintainingProfiles.html) for further information.

![appids](https://taplytics.com/assets/docs/push/apple/add-new.png)

Click on your app to open the "Settings" menu.

![settings](https://taplytics.com/assets/docs/push/apple/edit.png)

Scroll down to the bottom of the page and click "Edit".

Now that you are on the "Edit" screen, scroll down until you see "Push Notifications". **Click the box to enable them**. We will now create certificates for both development and production.

![enable](https://taplytics.com/assets/docs/push/apple/setting-push-hl.png)

## Let's start with development!

Click "Create Certificate" under **Development SSL Certificate** to begin generating your cert. When you're on the next screen, press "Continue".

![developmentssl](https://taplytics.com/assets/docs/push/apple/dev-ssl-cont.png)

Now, you need to upload the CSR file you created earlier. Press "Choose File" and find the ".certSigningRequest" file saved to your Mac.

![generatecert](https://taplytics.com/assets/docs/push/apple/cert-upload.png)

Once the upload completes, press "Generate File" to create your certificate. Download and open the file to save your development certificate to your keychain.

![certready](https://taplytics.com/assets/docs/push/apple/cert-download.png)

Now that you have your development certificate generated, you to need to create one for production. Click "Add another" and select "Apple Push Notification service SSL" under Production. Repeat the steps above to create a production SSL certificate.

![production](https://taplytics.com/assets/docs/push/apple/prod-cert.png)

## We're almost done!

Venture back to your Keychain so you can export your certs and upload them to Taplytics.

When you are in Keychain, go to "My Certificates". Find your newly created development certificate, right click, and hit "Export".

![almostdone](https://taplytics.com/assets/docs/push/apple/export-dev-cert.png)

**Save the ".p12" file to your computer**. The modal that appears will ask you to create a password to protect your exported items. In order to upload to Taplytics properly, leave the password field empty. Instead, just press "OK".

![savep12](https://taplytics.com/assets/docs/push/apple/skip-password.png)

Enter your Keychain password in order to export your files.

Locate your production certificate in the same list. Once you locate it, export it the same way as the one for development. Remember to change the name of your export file so you don't replace the one you just created for development.

If **Keychain Access** is giving you grief and your production certificate is not in your list, try searching for "production" in the search window. Once you find it, drag your file to "login" and you should now be able to export your production certificate from **Login—My Certificates**.

![keychainaccess](https://taplytics.com/assets/docs/push/apple/cert-prod-move-hl.png)

## Upload your certificates to Taplytics

Go to your Taplytics profile and navigate to the "Settings" tab where you will find the **Push Notifications** settings. Click the upload button and select your development and production certificates to upload.

![pushsettings](https://github.com/taplytics/Taplytics-User-Guides/blob/master/Image%20Assets/Apple_Push_Certificate_Upload.jpg?raw=true)

Once the upload completes, your "Settings" screen will display both of your certificates.

![uploadcomplete](https://github.com/taplytics/Taplytics-User-Guides/blob/master/Image%20Assets/pushcertuploadsuccess.jpg?raw=true)

Great Job! You can now send push notifications using Taplytics!


# 2. Google Push Certificates

### Upload your Google Push certificates so you can send push notifications using Taplytics.

Before you can get started using Taplytics push notifications, we need to upload your Google Push Notification credentials.

1. Getting your Google Credentials
2. Upload your credentials to Taplytics

## Getting your Google Credentials

First, head over to the [Firebase Console](https://console.firebase.google.com/). If your project is already in Firebase, simply enter that project. Otherwise, click `CREATE NEW PROJECT`. Then navigate to your project's settings:

![Firebaseconsole](https://github.com/taplytics/Taplytics-Android-SDK/blob/master/Google%20Certs/settings.png?raw=true)

Keep this browser tab open and open up a Taplytics tab.

## Uploading your Google Credentials

Now, on your Taplytics project, navigate to your project Settings tab. On the left, you wll see a smaller tab for Push Notification Settings. Click that, then go down to the 'Google Cloud Messaging' section. In the input fields for 'Sender ID' and 'GCM API Key', enter the keys in the appropriate fields. Finally, click 'Save Credentials' and you're on your way to sending Push Notifications to Android!

![GCMsettings](https://github.com/taplytics/Taplytics-User-Guides/blob/master/Image%20Assets/Taplytics_Example_%E2%80%93_Overview_%E2%80%93_Firebase_console.jpg?raw=true)
