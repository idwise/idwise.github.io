---
layout: default
parent: Home
title: Android SDK Documentation 
nav_order: 2
---
# IDWise Smart Onboarding SDK for Android
This SDK allows you to integrate the IDWise Digital Identity Verification technology inside your app with minimal fuss. Whenever your app wants to verify a user, it simply calls a single function in our SDK (`startJourney`) which will start an ID verification journey for that user. This will present a highly customisable UI that guides the user through a series of steps that prompts them for their ID documents and/or biometrics depending on how you have configured your journey flow in IDWise backend systems. At the end of this process your app will receive a `journeyId` (via callback functions) which your backend code can use to securely get the result of this verification process. It's that simple!

### Our latest stable SDK version is:
2.0.17

## Step 1: Integrating with your build scripts
- In your `build.gradle` file, add `multiDexEnabled true` and `dataBinding true` in these sections:
```
android {
	...
	defaultConfig {
		...
		multiDexEnabled true
	}

	buildFeatures {
		...
		dataBinding true
	}
}
```
- Add the following repositories:
```
repositories {
	maven { 
		url 'http://mobile-sdk.idwise.ai/releases/' 
            	allowInsecureProtocol = true
	}
	maven { url 'https://jitpack.io' }
}
```
- Add the following dependency inside your `dependencies` section: `implementation 'com.idwise:android-sdk:VERSION_NUMBER'`
  For example, to use **vx.y.z** of our SDK, you would do the following:
```
dependencies {
	...
	implementation 'com.idwise:android-sdk:x.y.z'
}
```
- Change your `minSdkVersion` to **19** or higher

## Step 2: Starting an ID verification journey:
You can find an example of how to start an ID verification process in the file [`example-activity.kt`](https://github.com/idwise/idwise-android-sdk-documentation/blob/main/example-activity.kt).

Your app can start an ID verification process by making a call to the `startJourney` method which takes the following parameters:

* **journeyTemplateId** (also called Journey Definition ID): This is a unique identifier that identifies your journey definition. IDWise shares this with you when you register for using IDWise system.
* **referenceNo**: (Optional) A parameter that you can use to associate an arbitrary identifier (reference number) with the user making the current journey. This is helpful to link the journey back to the user and/or application that started the journey, you will recieve this in the webhook request.
* **completionCallback**: This callback has one parameter (journeyId), and is called when the journey has been completed.
* **cancelCallback**: This callback has one parameter (journeyId) and is called when the customer cancels the journey (clicks back and confirms cancellation).
* **startedCallback**: This callback has one parameter (journeyInfo) and is called when the user starts the journey.
* **locale**: (Optional), iso code of locale (language) for the UI elements (please contact IDWise support for the list of supported locales, we are happy to support more upon reqiest).

The `journeyId` can then be used by your backend code to securely get the result of the ID verification.

## Step 3: Customising the UI for the ID verification journey:

The text prompts, images, and colours for both the light and dark modes within an ID verification journey are all customisable. This customisation is performed in our cloud which has the following advantages:

* You do not need to publish a new version of your app to the store each time you customise the journey,
* Your users do not need to do anything in order to pickup any customisations that you apply,

It almost works like magic!

## Note: If you use Proguard for obfuscation or shrinking:
You need to update your build as follow:

	// generate release apk
	buildTypes {
		release {
			signingConfig signingConfigs.release

				proguardFile '../proguard.pro'
			minifyEnabled true
			//enableR8 code Shrinking & Obfuscation
			shrinkResources true
		}
	}

And add the following file to your app [`proguard.pro`](https://github.com/idwise/idwise-android-sdk-documentation/blob/main/proguard.pro), in case the first simpler configuration doesn't work, please try the second more comprehensive configuration in [`proguard-2.pro`](https://github.com/idwise/idwise-android-sdk-documentation/blob/main/proguard-2.pro)

Please reach out to our IDWise support team for details on how to customise the UI for the ID verification journey.

## Onboarding Journey Scenarios:
Please [`click here`](onboarding-journey-scenarios.html) for detailed explanation of common onboarding journey scanerios and how to deal with each of these scenarios.



## Keep in touch!
Please get in touch if you want to make any adjustments or customisations to your users journey.
Please feel free to jump on a chat by visiting our website: www.idwise.com
