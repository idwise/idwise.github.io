---
layout: default
parent: iOS SDK
grand_parent: IDWise Developers
title: Dynamic Journey - iOS
nav_order: 1
---

# IDWise Smart Onboarding SDK for iOS - Advanced Dynamic Journey Mode

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
Alternatively, if you want to use Simple Journey mode which would get you up and running quicker and with less code if you do not need advanced control over the journey please check [`Simple Journey Mode here`](https://idwi.se/ios)

## Xcode Supported Version

IDWise SDK always supports latest Xcode version only. The current latest release of IDWise SDK supports below Xcode versions.

|  Xcode  | SDK    |  
| ------- | ------ |
| 14.1    | 3.10.0 |
| 14.0    | 3.9.1  |

### Dynamic Journey Mode

In dynamic journey mode, IDWise provides full control to the hosting application to show its own UI and handle journey and step events more flexibly. This makes IDWise journey more configurable for the hosting application to not only show its own UI, control when to start each step and in what order and subscribe to events for progress of each step.
Here is how you can setup and start using IDWise SDK.

## Requirements

The minimum deployment target for IDWiseSDK is iOS 12.0. In order to use the SDK your application minimum deployment target should be iOS 12.0 or higher.
On your development machine you need to have XCode and CocoaPods installed. Both Intel and M1 (Apple Sillicon) based machines are supported. When working with Cocoapods you might have to run some commands through Rosetta Stone compatibility mode.

## Installation

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

## Usage

Invoking IDWise SDK is very simple. First import IDWise package in your code file:

```swift
import IDWise
```

### Dynamic Journey

The rest of this document explains how to set up and start using IDWise SDK in Dynamic Journey mode.
Alternatively, if you want to use Simple Journey mode which would get you up and running quicker and with less code if you do not need advanced control over the journey please check Simple Journey Mode [`here`](https://github.com/idwise/idwise.github.io/blob/main/ios-sdk.md)

### Starting a dynamic user journey

IDWise SDK is designed to start on top of a UIViewController in your application. Each user onboarding or verification transaction is named a user journey.

To start a new dynamic journey just provide the UIViewController from which you want the flow to start then call `IDWiseSDK.initialize` method first with your provided client key and then you can call `IDWise.startDynamicJourney` method. If initialization is failed for any reason, you will get an error object with a code and a message explaining the reason of the error. In the following example, we called initialize method and then called startJourney method.

```swift
        IDWise.initialize(clientKey: "<YOUR_CLIENT_KEY>") { error in
                // Deal with error here
                print(error?.message)
        }
        
        IDWise.startDynamicJourney(journeyDefinitionId: "<YOUR_CUSTOMER_ID>", referenceNumber: "<YOUR_REFERENCE_NO>", locale: "en", journeyDelegate: self, stepDelegate: self)
        
```

This will make IDWise SDK start a journey and make it the current journey. You can next start steps of this journey and guide the user through completing the necessary steps in the desired order.

This method takes following parameters:

- `journeyDefinitionId`: Specifies the journey definition (aka template) to base this new journey on. Journey definitions are created based on your requirements and specify what documents and biometrics to collect from the user and in what order. JourneyDefinitionId is shared with you by IDWise team as part of your use-case and requirements discussions.
- `referenceNo` : A custom identifier to associate with this journey to enable you to link it back easily or associate it with a user on your system.
- `locale` (Optional) : Language code of the language to be used to display the journey user interface. This is either an ISO 639-1 (2-letter for example en) or IETF BCP 47 (2-letter plus extended language specified for example zh-HK or zh-CN)
- `journeyDelegate`: This parameter is used to provide a set of event handlers to handle the different events that are triggered from IDWise SDK. These events indicate the lifetime of a journey and provide oppurtunity for your application to react to certain journey events.
- `stepDelegate`: This parameter is used to provide a set of event handlers to handle the different events that are triggered from IDWise SDK. These events indicate the lifetime of a verification step and provide oppurtunity for your application to react to certain step events.

### Resuming an existing journey

You can resume the exiting, incompleted journey at any time. Following is the sample to Resume an existing journey.

```swift
IDWise.resumeDynamicJourney(journeyDefinitionId: "<YOUR_CUSTOMER_ID>",journeyId: "<JOURNEY_ID>",locale: "en", journeyDelegate: self, stepDelegate: self)
```

This method takes following parameters:

- `journeyDefinitionId`: Specifies the journey definition (aka template) to base this new journey on. Journey definitions are created based on your requirements and specify what documents and biometrics to collect from the user and in what order. JourneyDefinitionId is shared with you by IDWise team as part of your use-case and requirements discussions.
- `journeyId`: journeyId of the journey you want to resume. which you got in onJourneyStarted callback when you started the journey first time.
- `locale` (Optional) : Language code of the language to be used to display the journey user interface. This is either an ISO 639-1 (2-letter for example en) or IETF BCP 47 (2-letter plus extended language specified for example zh-HK or zh-CN)
- `journeyDelegate`: This parameter is used to provide a set of event handlers to handle the different events that are triggered from IDWise SDK. These events indicate the lifetime of a journey and provide oppurtunity for your application to react to certain journey events.
- `stepDelegate`: This parameter is used to provide a set of event handlers to handle the different events that are triggered from IDWise SDK. These events indicate the lifetime of a verification step and provide oppurtunity for your application to react to certain step events.

For example we can implement the protocol 'IDWiseSDKJourneyDelegate' as an extension on the ViewController like so:

```swift
extension ViewController:IDWiseSDKJourneyDelegate {
    func JourneyStarted(journeyID: String) {
        
    }

    func JourneyCancelled() {
        
    }
    
    func onJourneyResumed(journeyID: String) {

    }
    
    func JourneyFinished() {
        
    }

    func onError(error: IDWiseSDKError) {
       
    }
}
```

When the journey is started it is assigned a unique id called Journey ID in IDWise system and this is provided as a parameter, `journeyID` with the triggering of `JourneyStarted` event.
We can use this `journeyID` when we need to resume the journey.
This identifier can be used to fetch the data and status of the journey from IDWise Journey Fetch API at any time.

### Starting Journey Steps

After calling `startJourney` method and subsequently when `JourneyStarted` method is triggered successfully, you can call the `IDWise.startStep` method.

```swift
IDWise.startStep(stepId: String)
```

`IDWise.startStep` method takes the following parameter:

stepId: ID of the step you want to start. (Will be provided by IDWise for each step)

You can also pass a PDF or an Image as `Data` and for that you can use following method named `startStepFromFileUpload`.
 Important Note: Size of the Data should be less then or equal to 4Mb. Following is an example

```swift
   IDWise.startStepFromFileUpload(stepId: String, data: Data)
```

`IDWise.startStepFromFileUpload` method takes the following parameter:

stepId: ID of the step you want to start. (Will be provided by IDWise for each step)

data: Data representation of an image file or a PDF file ( Data bytes must be less than or equal to 4Mb )

### Confirming the Step

You can confirm the step that sucessfully started and completed by calling method `IDWise.confirmStep(stepId: String)` and passing the stepID as a parameter. StepId passed should be the stepID of the step that has been completed.
The `confirmStep(stepId)` method should be called after we complete the step. For that prupose, we are calling this method in `onStepResult()` method because we know the step is completed now and we can confirm it. If the `confirmStep(stepId)` is called before the completion of step, we will wait until the step completes but It is reccommended to call it after step completion.

The methods in `IDWiseStepDelegate`  will be triggered as step is handled and processed

We can implement the protocol `IDWiseSDKStepDelegate` as an extension on the ViewController same way as above to recieve the step events:

```swift

extension ViewController:IDWiseSDKStepDelegate {
    
    // This method will be triggered when user has captured the image/selfie from the camera
    func onStepCaptured(stepId: Int, capturedImage: UIImage) {
        // An example of showing custom UI
        LoadingView.show()
    }
    
    //This method will be triggered when Image processing is completed at the backend.
    //stepId will be the Id of the step that is just completed
    func onStepResult(stepId: Int,stepResult: StepResult?) {
        // An example of showing custom UI
        LoadingView.hide()
        if let result = stepResult {
          print(result.document?.documentType)
        }
        LoadingView.show()
        IDWise.confirmStep(stepId: lastProcessedStepId)
        // lastProcessedStepId means the stepID that you last started 

    }

     func onStepConfirmed(stepId: String) {
        LoadingView.hide()
    }

}
```

- `onStepCaptured` : This handler will be triggered when user has captured the document or selfie. Control will be returned to your application by this point and you can show any UI or run business logic to guide the user through the rest of the journey. At this point the captured image will have started uploading to backend in the background but would have not finished processing

- `onStepResult` : This handler will be triggered when step has finished processing. stepResult will contain information about the corresponding step. Your application can show any UI or can perform any business logic in this method

- `onStepConfirmed` : This handler will be called when step has been confirmed successfully you will get back stepID of the step that has been confirmed now

## Error Codes

|  Error Code  |            Error Description                                                                                                                   |  
| -----------  | ------------------------------------                                                                                                           |
| 11           |   Invalid/Empty Parameter (Client key is empty or Invalid)                                                                                     |
| 22           |   SDK is not initialized. Please Call IDWise.initialize(:) first                                                                               |
| 33           |   Wrong API credentials (Client Key) provided                                                                                                  |
| 44           |   Empty journeyId is provided to resume journey.                                                                                               |
| 45           |   Provided file size is exceeding the maximum file size                                                                                        |
| 66           |   No step found against provided stepId to start.                                                                                              |
| 70           |   Invalid step Id provided, please provide a valid stepId.                                                                                     |
| -101         |   An unexpected error occurred while processing the request.Make sure you have Internet connected                                              |
| -102         |   Network seems to be not connected, Please try again with network connected.                                                                  |
| 55           |   This method is not supported in this Journey Mode                                                                                            |
| 78           |   Step is not started, please trigger startStep(...) and call confirmStep(...) after onStepResult(...)                                         |

## Code Example

Please find the [`following example`](https://github.com/idwise/idwise-ios-sdk-documentation/tree/main/IDWiseExample) for an XCode project that showcases the integration with IDWise iOS Framework.
