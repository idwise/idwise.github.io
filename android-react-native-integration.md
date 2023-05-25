---
layout: default
parent: React Native Integration
grand_parent: IDWise Developers
title: Android Simple Journey
nav_order: 1
heading_anchors: true
---
# IDWise Simple SDK for React-Native
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

In an increasingly digital world, relying solely on conventional and manual remote validation methods involving database checks, IP addresses and credit bureau checks is cumbersome, inefficient and opens your customers' sensitive data to a variety of data breach security risks. IDWise’s cutting-edge document verification, face match and liveness detection solution establishes a robust layer of trust between your business and your prospective customer so that you can focus your effort on developing your business faster and with more confidence while stopping even the most sophisticated fraudsters in their tracks.

This git repository contains the official IDWiseSDK meant to be used in React-Native applications. This README contains all the needed information to test and integrate the IDWiseSDK in your React-Native project.

## Requirements

The `minSdkVersion` is 16 and `targetSdkVersion` is set to 31 in IDWise Android SDK. It is recommended to install the Latest Android Studio on your development machine. 


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

**To support RTL**, add the following attribute in application tag in your project's `AndroidManifest.xml` file located at `projectRoot/android/app/src/main/AndroidManifest.xml`

```
android:supportsRtl="true"
```

## Step 2: Linking

In order to Link the React Code with android, we need to create a Module, let's call it `IDWiseModule`. You can find the sample [`IDWiseModule.java`](https://github.com/idwise/idwise-react-native-sdk-samples/blob/main/IDWiseSimpleJourney/android/app/src/main/java/com/idwisereactnativesample/IDWiseModule.java) for sample code.

In order to use `IDWiseModule` we need to create a React Package, let's call it `IDWisePackage`. You can find the sample [`IDWisePackage.java`](https://github.com/idwise/idwise-react-native-sdk-samples/blob/main/IDWiseSimpleJourney/android/app/src/main/java/com/idwisereactnativesample/IDWisePackage.java) for sample code.

Add both classes to your android application module. Now we can add `IDWisePackage` to our packages, to do this, we need to edit the `MainApplication.java`, located at `projectRoot/android/your/package/name/../MainApplication.java`. In `getPackages()` method, we need to add our Package like below. You can find the sample [`MainApplication.java`](https://github.com/idwise/idwise-react-native-sdk-samples/blob/main/IDWiseSimpleJourney/android/app/src/main/java/com/idwisereactnativesample/MainApplication.java) for sample code.

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
Now we can start the verfication journey by calling `startJourney(..)` of the SDK which takes the following parameters. 

- `journeyDefinitionId`: (also called Journey Definition ID): This is a unique identifier that identifies your journey definition. IDWise shares this with you when you register for using IDWise system.
- `referenceNo`: (Optional) A parameter that you can use to associate an arbitrary identifier (reference number) with the user making the current journey. This is helpful to link the journey back to the user and/or application that started the journey, you will recieve this in the webhook request.
- `locale`: (Optional), iso code of locale (language) for the UI elements (please contact IDWise support for the list of supported locales, we are happy to support more upon reqiest).

This will start the verification process and that's it.

```javascript
IDWiseModule.startJourney("<JOURNEY_DEFINITION_ID>","<REFERENCE_NO>","en");
```

### Events (Callbacks)
Throughout the journey, IDWise SDK sends back some events to the Hosting app. Here we can listen to those events:

These are the supported events:

* **onJourneyStarted:**  Triggered when the journey is started and the Journey ID is assigned.
* **onJourneyFinished:**  Triggered when the journey is completed by the user.
* **onJourneyCancelled:**  Triggered when the user cancels the journey and doesn't finish it.
* **onError:**  Triggered when an error occurs for example a network connectivity error and lack of permission to access the camera.

<br />

```javascript
useEffect(() => {
  const eventEmitter = new NativeEventEmitter(NativeModules.IDWiseModule);

  eventEmitter.addListener('onError', (event) => {
    console.log(`An Error has occured  ${event.errorCode} : ${event.errorMessage}`); 
  });

  eventEmitter.addListener('onJourneyStarted', (event) => {
      console.log(`Journey Started with id ${event.journeyId}`); 
  });

  eventEmitter.addListener('onJourneyFinished', (event) => {
    console.log(`Journey Completed with id ${event.journeyId}`);
  });

  eventEmitter.addListener('onJourneyCancelled', (event) => {
    console.log(`Journey Cancelled with id ${event.journeyId}`); 
  });
}, []);
```

## Sample Project
You can find the [`Simple Journey Sample Project`](https://github.com/idwise/idwise-react-native-sdk-samples/tree/main/IDWiseSimpleJourney) for sample code for React-Native Integration.

