---
layout: default
parent: Android SDK
grand_parent: IDWise Developers
title: Advanced Mode - Android
nav_order: 1
---

# IDWise Smart Onboarding SDK for Android - Advanced Dynamic Mode
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

This document explains how to set up and start using IDWise SDK in Dynamic Journey mode.
Alternatively, if you want to use Simple Journey mode which would get you up and running quicker and with less code if you do not need advanced control over the journey please check [`Simple Journey Mode here`](https://idwi.se/android)

### Dynamic Journey Mode


In dynamic journey mode, IDWise provides full control to the hosting application to show its own UI and handle journey and step events more flexibly. This makes IDWise journey more configurable for the hosting application to not only show its own UI, control when to start each step and in what order and subscribe to events for progress of each step.
Here is how you can setup and start using IDWise SDK.

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

## Step 2: Starting an ID verification  journey
You can find an example of how to start an ID verification process in the file [`example-activity.kt`](https://github.com/idwise/idwise-android-sdk-documentation/blob/main/example-activity.kt).

**Initialize the SDK**
From inside your Activity or Fragment, You can initialize the like this

	IDWise.initialize("<CLIENT_KEY>") { error: IDWiseSDKError? ->
           error?.printStackTrace()
        }

After successfully initializing the SDK with your `CLIENT_KEY` provided by IDWIse, Your app can start the journey by making a call to the `startDynamicJourney` method which takes the following parameters:

* **journeyTemplateId** (also called Journey Definition ID): This is a unique identifier that identifies your journey definition. IDWise shares this with you when you register for using IDWise system.
* **referenceNo**: (Optional) A parameter that you can use to associate an arbitrary identifier (reference number) with the user making the current journey. This is helpful to link the journey back to the user and/or application that started the journey, you will recieve this in the webhook request.

* **locale**: (Optional), iso code of locale (language) for the UI elements (please contact IDWise support for the list of supported locales, we are happy to support more upon reqiest).
* **IDWiseSDKCallback**: An interface implementation with multiple callback events. That are `onJourneyStarted`, `onJourneyCompleted`, `onJourneyCancelled` and `onError`.
* **IDWiseStepCallback**: A callback interface to notify the Step Events like `onStepCaptured` and `onStepResult`.

The `JourneyInfo.journeyId`, received in `onJourneyStarted` & `onJourneyCompleted`, can then be used by your backend code to securely get the result of the ID verification.

**Here is the Sample for Starting the Dynamic Journey**


          IDWise.startDynamicJourney(
              context,
              "<YOUR_JOURNEY_TEMPLATE_ID>", //Provided by IDWise
              "<REFERENCE_NUMBER>", 
              "en",
              journeyCallback,
              stepCallback
           )
 
Following is the sample implementation of `journeyCallback` and `stepCallback`

        val journeyCallback = object : IDWiseSDKCallback {
              override fun onJourneyStarted(journeyInfo: JourneyInfo) {
                Log.d("IDWiseSDKCallback", "onJourneyStarted")
              }

              override fun onJourneyCompleted(journeyInfo: JourneyInfo,isSucceeded: Boolean) {
                Log.d("IDWiseSDKCallback", "onJourneyCompleted")
              }

              override fun onJourneyCancelled(journeyInfo: JourneyInfo?) {
                Log.d("IDWiseSDKCallback", "onJourneyCancelled")
              }

              override fun onError(error: IDWiseSDKError) {
                Log.d("IDWiseSDKCallback", "onError ${error.message}")
              }
          })
        
        
        val stepCallback = object : IDWiseSDKStepCallback {
            override fun onStepCaptured(stepId: Int, bitmap: Bitmap?) {
                //This event triggers when User has captured the image from the camera
            }

            override fun onStepResult(stepId: Int, stepResult: StepResult?) {
                //This event is triggered when Image processing is completed at the backend.
                //stepResult contains the details of the processing output
            }

         }

## Step 3: Starting the Steps

When `onJourneyStarted(...)` triggered successfuly, you can call the `IDWise.startStep(...)` to start the specific verfication Step. `IDWise.startStep` takes the following parameters:
* **context:** `Activity` or `Fragment` context
* **stepId:** ID of the step you want to start. (Will be provided by IDWise for each step)

The step events (provided in `IDWiseSDKStepCallback` parameter provided to  `startDynamicJourney` method) will be triggered as step is handled and processed.


## Step 4: Customising the UI

The text prompts, images, and colours for both the light and dark modes within an ID verification journey are all customisable. This customisation is performed in our cloud which has the following advantages:

* You do not need to publish a new version of your app to the store each time you customise the journey,
* Your users do not need to do anything in order to pickup any customisations that you apply,

It almost works like magic!

Please reach out to the support team to help with this customisation!

## If you use Proguard
You need to update your app level `build.gradle` as follow:

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
| INVALID_PARAMETERS  | 11        | apiKey or apiSecret are null or empty                                              |
| SDK_NOT_INITIALIZED | 22        | Either You haven't Called the IDWise.initialize or you haven't waited for callback |
| WRONG_CREDENTIALS   | 33        | Either apiKey or apiSecret or both are incorrect                                   |
| INTERNAL_ERROR      | 44        | Internal Error occurred while processing the request                               |

## Onboarding Journey Scenarios
Please [`click here`](onboarding-journey-scenarios.html) for detailed explanation of common onboarding journey scanerios and how to deal with each of these scenarios.



## Keep in touch!
Please get in touch if you want to make any adjustments or customisations to your users journey.
Please feel free to jump on a chat by visiting our website: www.idwise.com
