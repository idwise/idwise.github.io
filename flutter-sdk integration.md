---
layout: default
parent: IDWise Developers
title: Flutter Integration
nav_order: 4.5
heading_anchors: true
---
# IDWise Smart Onboarding SDK for Flutter

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

This git repository contains the official IDWiseSDK meant to be used in Flutter applications. This README contains all the needed information to test and integrate the IDWiseSDK in your flutter project.

{% include cta.md %}

## Requirements

### iOS ###

The minimum deployment target for IDWiseSDK is iOS 12.0. In order to use the SDK your application minimum deployment target should be iOS 12.0 or higher.
On your development machine you need to have XCode and CocoaPods installed. Both Intel and M1 (Apple Sillicon) based machines are supported. When working with Cocoapods you might have to run some commands through Rosetta Stone compatibility mode.

If you are building iOS project on VSCode and using an M1 machine then It's recommended to use Xcode for iOS builds because VSCode does not support Roestta mode and we need Rosetta option on both terminal and Xcode to run our project without any Issues of linking architecture.
Flutter also has disabled support for builds through VSCode starting from iOS 14, so we need Xcode for iOS builds anyway.

### Android ###

The `minSdkVersion` is 16 and `targetSdkVersion` is set to 31 in IDWise Android SDK. It is recommended to install the Latest Android Studio on your development machine.

## iOS Installation

To install pods in your flutter project, first go to projectfolder/ios. You can create a podfile If does not exist already by running command "pod init" on terminal by going into this ios directory. If you already have a podfile, you can follow following steps.

IDWiseSDK is available to install via [CocoaPods package manager](https://cocoapods.org) from IDWise private Cocoapods repository.
To add IDWise SDK to your project, first ensure you have these two lines at the top of your Podfile file:

```ruby
source 'https://cdn.cocoapods.org/'
source 'https://github.com/idwise/ios-sdk'
```

This adds IDWise private Cocoapods repository as a source to install packages from

Next add this line also to your Podfile but this time underneath your `target` node for your project:

```ruby
pod 'IDWise'
```

You can have a look at the example `Podfile` provided in the root of this repository to see an example `Podfile` with both the changes above completed

After adding our dependency in your Podfile run:

```
pod install
```

## Android Installation

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

**If you use Proguard:** You need to update your build as follow:

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

## Flutter Usage

Invoking IDWise SDK is very simple. First import IDWise package in your .dart file from where you want to invoke IDWise SDK:

```flutter
import IDWise
```

### Flutter Bridging to native platforms

We need to create a MethodChannel class object with some unique name assigned to channel. To use MethodChannel class, we need to import following dart services to our project.

```flutter
import 'dart:async';
import 'package:flutter/services.dart';
```

We will create object of MethodChannel class in our widget class as a variable. METHOD_CHANNEL_NAME can be any unique string.

```flutter
static const platformChannel = MethodChannel("<METHOD_CHANNEL_NAME>");
```

Now, we will use methodChannel object to invoke our native platform code. Calling the native code is an asynchronous operation so we need to create a method which returns a Future and is an async method like below. This is all the code that we need to do on flutter side and everything else will be handeled in native platform classes.

Initializing the SDK with your CLIENT_KEY provided by IDWise and an optional theme parameter, Your app can start an ID verification process by making a call to the startJourney method which takes the following parameters:

* **journeyTemplateId** (also called Journey Definition ID): This is a unique identifier that identifies your journey definition. IDWise shares this with you when you register for using IDWise system.
* **referenceNo**: (Optional) A parameter that you can use to associate an arbitrary identifier (reference number) with the user making the current journey. This is helpful to link the journey back to the user and/or application that started the journey, you will recieve this in the webhook request.
* **locale**: (Optional), iso code of locale (language) for the UI elements (please contact IDWise support for the list of supported locales, we are happy to support more upon request).

**MethodChannel.MethodCallHandler**: Add an implementation for method call handler to receive the callback events from the native code. That are `onJourneyStarted`, `onJourneyResumed`, `onJourneyFinished`, `onJourneyCancelled` and `onError`.

The `journeyId`, received in `onJourneyResumed` & `onJourneyStarted`, can then be used by your backend code to securely get the result of the ID verification.

```
  Future<void> _startSDK() async {
    try {
      /**
       * You can call initialize either in initState() of your Page
       * where you are going to start the verification, to pre-initialize the SDK.
       * Or you can call it along with startJourney(). Which suits best for your
       * usecase. Further implementation is done in MainActivity.kt for Android
       * and AppDelegate.swift for iOS
       */

      const initializeArgs = {
        "clientKey": "<YOUR_CLIENT_KEY>", // Replace from client key here
        "theme": "SYSTEM_DEFAULT", // Values [LIGHT, DARK, SYSTEM_DEFAULT] 
      };
      platformChannel.invokeMethod('initialize', initializeArgs);

      /**
       * You can call the startJourney when you wan to start the verification
       * process. Further implementation is done in MainActivity.kt for Android
       * and AppDelegate.swift for iOS
       */

      const startJourneyArgs = {
        "journeyDefinitionId": "<YOUR_JOURNEY_DEFINITION_ID>", // Replace from journey definition id
        "referenceNo": null, //Put your reference number here
        "locale" : "en"
      };
      platformChannel.invokeMethod('startJourney', startJourneyArgs);

      //MethodCallHandler : Callback events from the native code
      platformChannel.setMethodCallHandler((handler) async {
        switch (handler.method) {
          case 'onJourneyStarted':
            print("Method: onJourneyStarted, ${handler.arguments.toString()}");
            break;
          case 'onJourneyFinished':
            print("Method: onJourneyFinished");
            break;
          case 'onJourneyCancelled':
            print("Method: onJourneyCancelled");
            break;
          case 'onJourneyResumed':
            print("Method: onJourneyResumed, ${handler.arguments.toString()}");
            break;
          case 'onError':
            print("Method: onError, ${handler.arguments.toString()}");
            break;
          default :
            print('Unknown method from MethodChannel: ${handler.method}');
            break;
        }
      });

    } on PlatformException catch (e) {
      print("Failed : '${e.message}'.");
    }
  }
```

### Native Code to start Journey (iOS)

#### Step 1

In Flutter project, we will navigate to projectfolder/ios/Runner and will work in AppDelegate.swift class. In this file, we will declare a FlutterMethodChannel variable to user later in code.

```swift
 var channel: FlutterMethodChannel? = nil
```

#### Step 2

We will work in AppDelegate.swift class as we did above in step 1. In this file, we will write our code in **didFinishLaunchingWithOptions** method. Just before returning from this method, we will add our code.

To start a new journey just provide the UIViewController from which you want the flow to start then call `IDWiseSDK.initialize` method first with client key passed from Dart code and then you can call `IDWise.startJourney` method. If initialization is failed for any reason, you will get an error object with a code and a message explaining the reason of the error. In the following example, we called initialize method and then called startJourney method.

As in flutter, we don't have a UIViewController so we will create controller object using FlutterViewController class as done in code below. Then we will create a FluterMethodChannel object with same channel name as we assigned on dart side while creating the channel.
Finally, we will implement channel's handler method and depending on method name called from flutter class end, we will take action and invoke our native methods. As we invoked two methods from flutter class so we are handling for two methods in our AppDelegate.swift file.

```swift
    // Native code bridging Swift -> Dart , calling iOS SDK here
      let controller : FlutterViewController = window?.rootViewController as! FlutterViewController
      let channel = FlutterMethodChannel(name: "com.idwise.fluttersampleproject/idwise",
                                                binaryMessenger: controller.binaryMessenger)
      self.channel = channel
      channel.setMethodCallHandler({ [self]
          (call: FlutterMethodCall, result: @escaping FlutterResult) -> Void in
          
          switch call.method {
          case "initialize":
              // receiving arguments from Dart side and consuming here
              
              var clientKey: String = "" 
              var sdkTheme: IDWiseSDKTheme = IDWiseSDKTheme.systemDefault 
              if let parameteres = call.arguments as? [String:Any] {
                  if let clientkey = parameteres["clientKey"] as? String {
                      clientKey = clientkey
                  }
                  if let theme = parameteres["theme"] as? String {
                      if theme == "LIGHT" {
                          sdkTheme = IDWiseSDKTheme.light
                      } else if theme == "DARK" {
                          sdkTheme = IDWiseSDKTheme.dark
                      } else  {
                          sdkTheme = IDWiseSDKTheme.systemDefault
                      }
                  }
                
              }
              IDWise.initialize(clientKey: clientKey,theme: sdkTheme) { error in
                  result("got some error")
                  if let err = error {
                      channel.invokeMethod(
                        "onError",
                        arguments: ["errorCode": err.code,"message": err.message] as [String : Any])
                  }
              }

          case "startJourney":
              // receiving arguments from Dart side and consuming here

              var referenceNo: String = "" // optional parameter
              var locale: String = "en"
              var journeyDefinitionId = ""
              if let parameteres = call.arguments as? [String:Any] {
                  if let refNo = parameteres["referenceNo"] as? String {
                      referenceNo = refNo
                  }
                  if let loc = parameteres["locale"] as? String {
                      locale = loc
                  }
                  if let journeyDefId = parameteres["journeyDefinitionId"] as? String {
                      journeyDefinitionId = journeyDefId
                  }
              }
              IDWise.startJourney(journeyDefinitionId: journeyDefinitionId,referenceNumber: referenceNo,locale: locale, journeyDelegate: self)
              result("successfully started journey")
          default:
              result(FlutterMethodNotImplemented)
          }
          
      })
```

This will make IDWise SDK show a UI with a wizard to guide the user through completing the onboarding journey

#### Step 3

Outside of our AppDelegate class, we will paste this code which is an extension to AppDelegate file. This code is used to conform to `IDWiseSDKJourneyDelegate` protocol and then to invoke callbacks back to Dart.

For example we can implement the protocol as an extension on the AppDelegate class like so:

```swift
 extension AppDelegate:IDWiseSDKJourneyDelegate {
    func onJourneyResumed(journeyID: String) {
        channel?.invokeMethod("onJourneyResumed", arguments: journeyID)
    }
    
    
    func onError(error : IDWiseSDKError) {
        channel?.invokeMethod("onError",
                    arguments: ["errorCode": error.code,"message": error.message] as [String : Any])
    }
    
    func JourneyStarted(journeyID: String) {
        channel?.invokeMethod( "onJourneyStarted", arguments: journeyID)
    }
    
    func JourneyFinished() {
        channel?.invokeMethod( "onJourneyFinished", arguments: nil)
    }
    
    func JourneyCancelled() {
        channel?.invokeMethod("onJourneyCancelled", arguments: nil)
    }
   
}
```

When the journey is started it is assigned a unique id called Journey ID in IDWise system and this is provided as a parameter, `journeyID` with the triggering of `JourneyStarted` event.
This identifier can be used to fetch the data and status of the journey from IDWise Journey Fetch API once you get the [webhook call](https://idwi.se/webhooks) to your backend.

The steps that compose part of the journey and the prompts that user see are all cutomisable through IDWise cloud system.

### Native Code to start Journey (Android)

Inside your `Activity` which is extended from `FlutterActivity`, you need to `override` the `configureFlutterEngine(flutterEngine: FlutterEngine)` method. Add the following code inside this method to handle the `initialize` and `startJourney` requests from your flutter code e.g `main.dart`.

```
private var methodChannel: MethodChannel? = null

methodChannel = MethodChannel(
            flutterEngine.dartExecutor.binaryMessenger,
            "YOUR_CHANNEL_NAME" //Example: "com.idwise.fluttersampleproject/idwise"
)

methodChannel?.setMethodCallHandler { call, result ->
      when (call.method) {
        "initialize" -> {
            val clientKey = call.argument<String>("clientKey")
            val theme = when (call.argument<String>("theme")) {
                "LIGHT" -> IDWiseSDKTheme.LIGHT
                "DARK" -> IDWiseSDKTheme.DARK
                else -> IDWiseSDKTheme.SYSTEM_DEFAULT
            }

            IDWise.initialize(clientKey!!, theme /*Optional*/) { error ->
                Log.d("IDWiseSDKCallback", "onError ${error?.message}")
                result.error("ERROR", error!!.message, null)
                methodChannel?.invokeMethod("onError", Gson().toJson(error))
            }
        }

        "startJourney" -> {
            val journeyDefinitionId = call.argument<String>("journeyDefinitionId")
            val referenceNo = call.argument<String>("referenceNo")
            val locale = call.argument<String>("locale")
            
            IDWise.startJourney(
                this,
                journeyDefinitionId!!,
                referenceNo,
                locale,
                callback = object : IDWiseSDKCallback {
                    override fun onJourneyStarted(journeyInfo: JourneyInfo) {
                        Log.d("IDWiseSDKCallback", "onJourneyStarted")
                        methodChannel?.invokeMethod("onJourneyStarted", journeyInfo.journeyId)
                    }

                    override fun onJourneyResumed(journeyInfo: JourneyInfo) {
                        Log.d("IDWiseSDKCallback", "onJourneyResumed")
                        methodChannel?.invokeMethod("onJourneyResumed", journeyInfo.journeyId)
                    }

                    override fun onJourneyCompleted(
                        journeyInfo: JourneyInfo,
                        isSucceeded: Boolean
                    ) {
                        Log.d("IDWiseSDKCallback", "onJourneyCompleted")
                        methodChannel?.invokeMethod("onJourneyFinished", null)
                    }

                    override fun onJourneyCancelled(journeyInfo: JourneyInfo?) {
                        Log.d("IDWiseSDKCallback", "onJourneyCancelled")
                        methodChannel?.invokeMethod("onJourneyCancelled", null)
                    }

                    override fun onError(error: IDWiseSDKError) {
                        Log.d(
                            "IDWiseSDKCallback",
                            "onError ${error.message}"
                        )
                        //Strigify error using GSON or with any other approach.
                        methodChannel?.invokeMethod("onError", Gson().toJson(error))
                    }
                }
            )

        }

        else -> result.error("NO_SUCH_METHOD", "NO SUCH METHOD", null)
    }
}

```

## Android Code Example

You can find the sample [`MainActivity.kt`](https://github.com/idwise/idwise-flutter-example/tree/main/android/app/src/main/kotlin/com/example/sample_project/MainActivity.kt) that showcases the integration with IDWise Android Native Framework.

## iOS Code Example

You can find the sample [`AppDelegate.swift`](https://github.com/idwise/idwise-flutter-example/blob/main/ios/Runner/AppDelegate.swift) that showcases the integration with IDWise iOS Framework.
