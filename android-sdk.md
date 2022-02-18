---
layout: default
parent: IDWise Developers
title: Android SDK 
has_children: true
nav_order: 2
heading_anchors: true
---
# IDWise Smart Onboarding SDK for Android
{: .no_toc }

<details open markdown="block">
  <summary>
    Table of contents
  </summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

---

This SDK allows you to integrate the IDWise Digital Identity Verification technology inside your app with minimal fuss. 
Whenever your app want's to verify a user, simply initialize the SDK and call a single method i.e. `startJourney(...)`. It's that simple. Isn't it?

Here is how you can setup and start using IDWise SDK.

### Latest Stable Version
3.0.3

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

[`Click here for an example for build.gradle`](https://github.com/idwise/idwise-android-sdk-documentation/blob/main/example%20build.gradle)

## Step 2: Starting an ID verification journey
You can find an example of how to start an ID verification process in the file [`example-activity.kt`](https://github.com/idwise/idwise-android-sdk-documentation/blob/main/example-activity.kt).

**Initialize the SDK**
From inside your Activity or Fragment, You can initialize the like this

	IDWise.initialize("<CLIENT_KEY>") { error: IDWiseSDKError? ->
           error?.printStackTrace()
        }

After successfully initializing the SDK with your `CLIENT_KEY` provided by IDWIse, Your app can start an ID verification process by making a call to the `startJourney` method which takes the following parameters:

* **journeyTemplateId** (also called Journey Definition ID): This is a unique identifier that identifies your journey definition. IDWise shares this with you when you register for using IDWise system.
* **referenceNo**: (Optional) A parameter that you can use to associate an arbitrary identifier (reference number) with the user making the current journey. This is helpful to link the journey back to the user and/or application that started the journey, you will recieve this in the webhook request.

* **locale**: (Optional), iso code of locale (language) for the UI elements (please contact IDWise support for the list of supported locales, we are happy to support more upon reqiest).
* **IDWiseSDKCallback**: An interface implementation with multiple callback events. That are `onJourneyStarted`, `onJourneyCompleted`, `onJourneyCancelled` and `onError`.

The `JourneyInfo.journeyId`, received in `onJourneyStarted` & `onJourneyCompleted`, can then be used by your backend code to securely get the result of the ID verification.

**Here is the Sample for Starting the Journey**


			IDWise.startJourney(
				context,
				"<YOUR_JOURNEY_TEMPLATE_ID>", //Provided by IDWise
				"<REFERENCE_NUMBER>", 
				"en",
				object : IDWiseSDKCallback {
				    override fun onJourneyStarted(journeyInfo: JourneyInfo) {
					Log.d("IDWiseSDKCallback", "onJourneyStarted")
				    }

				    override fun onJourneyCompleted(
					journeyInfo: JourneyInfo,
					isSucceeded: Boolean
				    ) {
					Log.d("IDWiseSDKCallback", "onJourneyCompleted")
				    }

				    override fun onJourneyCancelled(journeyInfo: JourneyInfo?) {
					Log.d("IDWiseSDKCallback", "onJourneyCancelled")
				    }

				    override fun onError(error: IDWiseSDKError) {

					Log.d("IDWiseSDKCallback", "onError ${error.message}")
				    }
				})
			

## Step 3: Customising the UI

The text prompts, images, and colours for both the light and dark modes within an ID verification journey are all customisable. This customisation is performed in our cloud which has the following advantages:

* You do not need to publish a new version of your app to the store each time you customise the journey,
* Your users do not need to do anything in order to pickup any customisations that you apply,

It almost works like magic!

Please reach out to the support team to help with this customisation!

## If you use Proguard
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

## Common Error Codes and their Causes

| IDWiseErrorCode     | Int Value | Cause                                                                              |
|---------------------|-----------|------------------------------------------------------------------------------------|
| INVALID_PARAMETERS  | 11        | clientKey is invalid or empty.                                                     |
| SDK_NOT_INITIALIZED | 22        | Either You haven't Called the IDWise.initialize or you haven't waited for callback |
| WRONG_CREDENTIALS   | 33        | clientKey is incorrect                                                             |
| INTERNAL_ERROR      | 44        | Internal Error occurred while processing the request                               |

## Onboarding Journey Scenarios
Please [`click here`](onboarding-journey-scenarios.html) for detailed explanation of common onboarding journey scanerios and how to deal with each of these scenarios.

## Advanced Dynamic Journey Mode
In some scenarios, your use case might require more flexibility and control over the user journey flow and full control over the user interface / UX above and beyond the wide range of customisations we offer and more than the above sequential flow which simplifies integration, we are introducing: the `Advanced Dynamic Journey` mode, to both of our Android and iOS SDKs to cover some edge advanced use-cases.

From experience, the above simple integration should cover the vast majority of use cases, and allow for a good degree of customisation for the steps and for the UI/UX from the IDWise backend (which applies instantly without the need to update the host app).

If you believe that the simple integration still doesn't cover your use-case, you can find the documentation for this new mode here: [`Advanced Dynamic Journey`](https://idwi.se/android-advanced)

## Keep in touch!
Please get in touch if you want to make any adjustments or customisations to your users journey.
Please feel free to jump on a chat by visiting our website: www.idwise.com
