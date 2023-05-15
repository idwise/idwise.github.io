---
layout: default
parent: React Native Integration
grand_parent: IDWise Developers
title: Android Daynamic SDK
nav_order: 1
heading_anchors: true
---

# IDWise SDK for React-Native - Advanced Dynamic Journey Mode

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

{% include cta.md %}

This document will guide you through how to integrate IDWise SDK in your React Native project in Advanced mode (Dynamic journey). This mode allows you to have more control over screen display order and to show your own screens in-between verification steps. If you prefer a simpler integration please check 

## Requirements

The `minSdkVersion` must be 19 or higher and `targetSdkVersion` should be 31 or higher for the application. 

It is recommended to install the latest Android Studio on your development machine.

## Step 1: Integrating with your build scripts

Add the following in your app level `build.gradle` file located at `projectRoot/android/app/build.gradle`

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

Add following repositories in you project-level `build.gradle` file located at `projectRoot/android/build.gradle`

```
repositories {
  maven {
    url 'http://mobile-sdk.idwise.ai/releases/'
    allowInsecureProtocol = true
  }
  maven { url 'https://jitpack.io' }
}
```

Finally, Add the following dependency in your app level `build.gradle` file located at `projectRoot/android/app/build.gradle`.
Replace the `x.y.z` with the latest IDWise SDK version

```
dependencies {
  ...
  implementation 'com.idwise:android-sdk:x.y.z'
}
```

## Step 2: Linking

In order to Link the React code with Android, we need to create a Module class, let's call it `IDWiseModule`. You can find the sample [`IDWiseModule.java`](https://github.com/idwise/idwise-react-native-sdk-samples/blob/main/IDWiseDynamicJourney/android/app/src/main/java/com/idwisereactnativesample/IDWiseModule.java) for sample code.

In order to use `IDWiseModule` we need to create a React Package class, let's call it `IDWisePackage`. You can find the sample [`IDWisePackage.java`](https://github.com/idwise/idwise-react-native-sdk-samples/blob/main/IDWiseDynamicJourney/android/app/src/main/java/com/idwisereactnativesample/IDWisePackage.java) for sample code.

Add both classes to your android application module. Now we can add `IDWisePackage` to our packages, to do this, we need to edit the `MainApplication.java`, located at `projectRoot/android/your/package/name/../MainApplication.java`. In `getPackages()` method, we need to add our Package like below. You can find the sample [`MainApplication.java`](https://github.com/idwise/idwise-react-native-sdk-samples/blob/main/IDWiseDynamicJourney/android/app/src/main/java/com/idwisereactnativesample/MainApplication.java) for sample code.

```java
@Override
protected List<ReactPackage> getPackages() {
  List<ReactPackage> packages = new PackageList(this).getPackages();

  //add IDWisePackage here
  packages.add(new IDWisePackage());

  return packages;
}
```

## Step 3: React-Native Usage

Invoking IDWise SDK is very simple. First reterieve `IDWiseModule` from `NativeModules` in your .js file from where you want to invoke IDWise SDK:

```javascript
const { IDWiseModule } = NativeModules;
```

### Initializing the SDK

In order to use the SDK, we need to initialize it first with the `<CLIENT_KEY>` provided by IDWise and a theme parameter. You can pre-load this on `componentDidMount()` if you want to. Here is how we can initialize the SDK.

```javascript
const theme = 'SYSTEM_DEFAULT'; //[ LIGHT, DARK, SYSTEM_DEFAULT ]
IDWiseModule.initialize("<CLIENT_KEY>", theme);
```

### Starting the Journey

Now we can start the verfication journey by calling `startDynamicJourney(..)` of the SDK which takes the following parameters. 

- `journeyDefinitionId`: (also called Journey Definition ID): This is a unique identifier that identifies your journey definition. IDWise shares this with you when you register for using IDWise system.
- `referenceNo`: (Optional) A parameter that you can use to associate an arbitrary identifier (reference number) with the user making the current journey. This is helpful to link the journey back to the user and/or application that started the journey, you will recieve this in the webhook request.
- `locale`: (Optional), iso code of locale (language) for the UI elements (please contact IDWise support for the list of supported locales, we are happy to support more upon reqiest).

This creates a new journey and makes the SDK ready to start steps in the verification journey as explained in next section.

```javascript
IDWiseModule.startDynamicJourney("<JOURNEY_DEFINITION_ID>", "<REFERENCE_NO>", "en");
```

### Starting the Steps

When `onJourneyStarted(...)` or `onJourneyResumed(...)` is triggered successfuly, you can call the `IDWise.startStep(...)` to start the specific verfication Step. `IDWise.startStep` takes the following parameters:
* **stepId:** ID of the step you want to start. (Will be provided by IDWise for each step)

Here is the sample implementation of starting a step

```javascript
IDWiseModule.startStep("<STEP_ID>");
```

The above code will show IDWise UI to take the user through capturing the step from the camera.

Instead of using the camera to capture, you can also pass a file directly as a `ByteArray`. Supported file formats are JPG, PNG or PDF.

`Important Note`: Size of the byteArray should be less then 6MB. Following is an example

```javascript
IDWiseModule.startStepFromFileUpload("<STEP_ID>","<BYTE_ARRAY>");
```
The step events (`stepCaptured` and `stepResult`) will be triggered the step is handled and processed.

### Resuming the Journey

We can resume the verfication journey by calling `resumeDynamicJourney(..)` of the SDK like below. This will resume the verification process.

```javascript
IDWiseModule.resumeDynamicJourney("<JOURNEY_DEFINITION_ID>", "<JOURNEY_ID>", "en");
```

### Events (Callbacks)

Throughout the journey, IDWise SDK sends back some events to your app. Here are examples how to listen to these events:

These are the supported events:

* **onJourneyStarted:**  Triggered when the journey is started and the Journey ID is assigned.
* **onJourneyFinished:**  Triggered when the journey is completed by the user.
* **onJourneyCancelled:**  Triggered when the user cancels the journey and doesn't finish it.
* **onJourneySummary:**  Triggered as the callback response for the journey summary.
* **onError:**  Triggered when an error occurs for example a network connectivity error and lack of permission to access the camera.
* **onStepCaptured:**  Triggered after capturing the image from the camera for a step.
* **onStepResult:**  Triggered after the backend processing of a step is competed.

<br />

```javascript
useEffect(() => {
  const eventEmitter = new NativeEventEmitter(NativeModules.IDWiseModule);

  eventEmitter.addListener("onError", (event) => {
    console.log(
      `An Error has occured  ${event.errorCode} : ${event.errorMessage}`
    );
  });

  eventEmitter.addListener("onJourneyStarted", (event) => {
    console.log(`Journey Started with id ${event.journeyId}`);
  });

  eventEmitter.addListener('onJourneyResumed', event => {
    console.log(`Journey Resumed with id ${event.journeyId}`);
  });

  eventEmitter.addListener("onJourneyFinished", (event) => {
    console.log(`Journey Completed with id ${event.journeyId}`);
  });

  eventEmitter.addListener("onJourneyCancelled", (event) => {
    console.log(`Journey Cancelled with id ${event.journeyId}`);
  });

  eventEmitter.addListener('onJourneySummary', event => {
    console.log(`Journey Summary for id ${event.journeyId}`);
    console.log(`Journey Step Summaries ${event.journeyStepSummaries}`);
    console.log(`Journey Result ${event.journeyResult}`);
    console.log(`Journey Is Complete ${event.journeyIsComplete}`);
  });

  eventEmitter.addListener('onStepCaptured', event => {
      console.log(`Step Captured with id ${event.stepId}`);
      console.log(`Step Captured Bitmap Base64 ${event.bitmapBase64}`);
  });

  eventEmitter.addListener('onStepResult', event => {
    console.log(`Step Result with id ${event.stepId} : ${event.stepResult}`);
  });
}, []);
```

### Get Summary of the Verification Journey
You can get the Status of the journey anytime by calling the following function. You will receive the response against `onJourneySummary` event having the fields `journeyId`, `journeyStepSummaries`, `journeyResult` and `journeyIsComplete`.

```javascript
IDWiseModule.getJourneySummary("<JOURNEY_ID>");
```

## Step 5: Customising the UI

The text prompts, images, and colours for both the light and dark modes within an ID verification journey are all customisable. This customisation is performed in our cloud which has the following advantages:

* You do not need to publish a new version of your app to the store each time you customise the journey,
* Your users do not need to do anything in order to pickup any customisations that you apply,

It almost works like magic!

Please reach out to the support team to help with this customisation!

## Sample Project

You can find the [`Dynamic Journey Sample Project`](https://github.com/idwise/idwise-react-native-sdk-samples) for sample code for React-Native Integration.
