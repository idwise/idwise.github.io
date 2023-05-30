---
layout: default
parent: React Native Integration
grand_parent: IDWise Developers
title: iOS Dynamic Journey
nav_order: 1
heading_anchors: true
---
# IDWise Smart Onboarding iOS SDK for React-Native

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

This git repository contains the official IDWiseSDK meant to be used in React-Native applications. This README contains all the needed information to test and integrate the IDWiseSDK in your React-Native project.

{% include cta.md %}

## Xcode Supported Version

IDWise SDK always supports latest Xcode version only. The current latest release of IDWise SDK (IDWise 3.9.2) supports below Xcode version.

|  Xcode  | SDK    |  
| ------- | ------ |
| 14.3    | 4.1.6  |

## Requirements

The minimum deployment target for IDWiseSDK is iOS 12.0. In order to use the SDK your application minimum deployment target should be iOS 12.0 or higher.
On your development machine you need to have XCode and CocoaPods installed. Both Intel and M1 (Apple Sillicon) based machines are supported. When working with Cocoapods you might have to run some commands through Rosetta Stone compatibility mode.

## Step 1: Installation

IDWiseSDK is available to install via [CocoaPods package manager](https://cocoapods.org) from IDWise private Cocoapods repository. IDWise is a dynamic framework and requires the podfile to use `use_frameworks!` directive to be used so dynamic pods/frameworks can install and run successfully.

React Native’s native iOS dependencies are compiled as static libraries and many react-native node modules are only ever compiled and tested as static libraries. When trying to incorporate a dynamic framework (which Swift dependencies are often bundled as), compilation problems often ensue.

As aforementioned, Cocoapods builds pods by default as static libraries. To use a framework like this we normally use the `use_frameworks!` Cocoapods directive at the top of our Podfile. However, this causes ALL pods to be compiled as dynamic frameworks. Not only will some React Native pods fail, but a large number of 3rd party React Native libraries will as well.

If you can find a way where you can use `use_frameworks!` directive and still able to install `IDWise` pod and other React Native native iOS dependecies (Flipper, Folly, YogaKit etc) and successfully run the react native project then It's fine. Otherwise, the workaround is to use a plugin over cocoapods which can dynamically install specific libraries as static or dynamic during pod installation process.

You can use this plugin to install pods `https://github.com/joncardasis/cocoapods-user-defined-build-types` which enables us to dictate the specific build type of a pod ( dynamic in our case ). You can go through the README section of this plugin as well.
We can easily install this plugin by typing this command in our terminal `gem install cocoapods-user-defined-build-types`. It will install this plugin over our cocoapods. You can use our sample project's podfile in which we used this plugin for `IDWise` pod's dynamic installation.

### Without cocoapods-user-defined-build-types Plugin

To add IDWise SDK to your project, first ensure you have these two lines at the top of your Podfile file:

```ruby
source 'https://cdn.cocoapods.org/'
source 'https://github.com/idwise/ios-sdk'
```

In this case, you should use `use_frameworks!` directive in your podfile.

This adds IDWise private Cocoapods repository as a source to install packages from

Next add this line also to your Podfile but this time underneath your `target` node for your project:

```ruby
pod 'IDWise'
```

Also, add this configuration underneath your `target` node for your project:

```ruby
  post_install do |installer|
    installer.pods_project.build_configurations.each do |config|
      config.build_settings['CODE_SIGNING_ALLOWED'] = 'NO'
    end
  end
```

After adding our dependency in your Podfile run:

```
pod install
```

### With cocoapods-user-defined-build-types Plugin

To add IDWise SDK to your project, first ensure you have these two lines at the top of your Podfile file:

```ruby
source 'https://cdn.cocoapods.org/'
source 'https://github.com/idwise/ios-sdk'
```

```ruby
plugin 'cocoapods-user-defined-build-types', {
  verbose: true
}
enable_user_defined_build_types!
```

This adds IDWise private Cocoapods repository as a source to install packages from

Next add this line also to your Podfile but this time underneath your `target` node for your project:

```ruby
 pod 'IDWise', :build_type => :dynamic_framework
```

You can have a look at the example `Podfile` provided in the ios folder of this repository to see an example `Podfile` with both the changes above completed

After adding our dependency in your Podfile run:

```
pod install
```

## Step 2: Linking

In order to Link the React Code with iOS, we need to create a swift class with objective-c bridging, let's call it `IDWiseModule`. You can find the sample [`IDWiseModule.swift`] (<https://github.com/idwise/idwise-react-native-example/blob/main/ios/IDWiseModule.swift>) for sample code.This class will act as a bridge between swift and react native, because react native's ios bridging code is written in objectve-c so we have to mark our swift class with `@objc` annotation.

We also need to create a header `(.h)` and an implementation file `(.m)` for our swift class with same name as our swift class. In header file, we do some native module imports and in implementation file we export our swift class and It's method's as module and method's respectively. You can find the sample of both of these files [`IDWiseModule.h`,`IDWiseModule.h`] at (<https://github.com/idwise/idwise-react-native-example/blob/main/ios/IDWiseModule.h>) and (<https://github.com/idwise/idwise-react-native-example/blob/main/ios/IDWiseModule.m>) respectively.

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

Now we can start the verfication journey by calling `startJourney(..)` of the SDK like below. This will start the verification process and that's it.

```javascript
IDWiseModule.startJourney("<JOURNEY_DEFINITION_ID>","<REFERENCE_NO>","<LOCALE>");
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

## Sample Project

You can find the [`Dynamic Journey Sample Project`](https://github.com/idwise/idwise-react-native-sdk-samples/tree/main/IDWiseDynamicJourney) for sample code for React-Native Integration.
