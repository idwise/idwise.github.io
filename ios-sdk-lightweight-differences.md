---
layout: default
parent: iOS SDK
grand_parent: IDWise Developers
title: iOS Lightweight SDK 
nav_exclude: true
---
# iOS Lightweight SDK

## Xcode Supported Version

IDWiseLight SDK always supports latest Xcode version only. The current latest release of IDWiseLight SDK supports below Xcode versions.

|  Xcode  | SDK    |  
| ------- | ------ |
| 14.1    | 3.10.0  |

## Installation

IDWiseSDK is available to install via [CocoaPods package manager](https://cocoapods.org) from IDWiseLight private Cocoapods repository.
To add IDWiseLight SDK to your project, first ensure you have these two lines at the top of your Podfile file:

```ruby
source 'https://cdn.cocoapods.org/'
source 'https://github.com/idwise/ios-sdk-light'
```

This adds IDWiseLight private Cocoapods repository as a source to install packages from

Next add this line also to your Podfile but this time underneath your `target` node for your project:

```ruby
pod 'IDWiseLight'
```

You can have a look at the example `Podfile` provided in the root of this repository to see an example `Podfile` with both the changes above completed

After adding our dependency in your Podfile run:

```
pod install
```

## Usage

Invoking IDWise SDK is very simple. First import IDWise package in your code file:

```swift
import IDWiseLight
```
