---
layout: default
parent: iOS SDK
grand_parent: IDWise Developers
title: iOS IDWiseNFC SDK 
nav_exclude: true
---
# iOS IDWise NFC SDK

Please use this SDK in conjunction of our [iOS Dynamic Journey integration](https://idwi.se/ios-advanced) (IDWiseNFC SDK is just add-on to enable NFC reading if enabled in your journey flow).

{% include cta.md %}

## Xcode Supported Version

IDWiseNFC SDK always supports latest Xcode version only. The current latest release of IDWiseNFC SDK supports below Xcode versions.

|  Xcode  | SDK    |  
| ------- | ------ |
| 14.2    | 3.11.6  |

## Installation

IDWiseNFC SDK is available to install via [CocoaPods package manager](https://cocoapods.org) from IDWiseNFC private Cocoapods repository.
To add IDWiseNFC SDK to your project, first ensure you have these two lines at the top of your Podfile file:

```ruby
source 'https://cdn.cocoapods.org/'
source 'https://github.com/idwise/ios-sdk-nfc'
```

This adds IDWiseNFC private Cocoapods repository as a source to install packages from

Next add this line also to your Podfile but this time underneath your `target` node for your project:

```ruby
pod 'IDWiseNFC'
```

You can have a look at the example `Podfile` provided in the root of this repository to see an example `Podfile` with both the changes above completed

After adding our dependency in your Podfile run:

```
pod install
```

## Usage

Invoking IDWiseNFC SDK is very simple. First import IDWise package in your code file:

```swift
import IDWiseNFC
```
