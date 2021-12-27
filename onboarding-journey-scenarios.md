---
layout: default
parent: Android SDK
title: Onboarding Journey Scenarios 
nav_exclude: true
---

# Onboarding Journey Scenarios

IDWise helps customers boost onboarding conversion rate, and enables customers to cater for multiple integration scenarios, here are some examples of how to handle different situations within your app:

## 1. Happy path - completed journey!
The end user starts the onboarding journey, and completes all steps in one go, your client app (mobile frontend) will get the following call backs (you pass them to the startJourney function):
a. Journey Started: This is triggered once journey initialisation has happened, and a journey ID has been allocated to this transaction on IDWise backend (you will get that ID to use it later if you want for some of the edge cases).
b. Journey Completed: This is triggered once the end user reaches the last step of the onboarding workflow, you will also get the journey ID.

Also, on your backend (customer backend), if configured, a webhook will be fired, with journey details (for secure, backend-to-backend integration, where IDWise API can be used to fetch journey details (including extracted information like first name, last name, date of birth,... as well as evaluation of journey rules, face match, and many useful details).
Please reach out to the IDWise support team to configure a webhook on your account.

## 2. Cancelled journey:
This happens when the end user presses the back button, they will get a confirmation message nudging them to not cancel the flow, but if they insist. This also can happen when the user insists on not giving the correct permissions (e.g. camera or access to gallery).
The following callbacks / events will be fired
a. Journey Started.
b. Journey Cancelled: You will get a journey ID as with the other events.

Please note that no webhook will be triggered in this case, but you still have the journey ID from the above callbacks, which you can send to your own backend which can still fetch the partial journey result so far (so that you can understand where the drop rate is).

## 3. Abandoned journey:
This is where the user decided to close the app during the journey (by killing it from recent apps for example).
In this case, the Journey Started callback will still be fired at the beginning, however, no other callbacks will be fired, and no webhook will be called in the backend.


## Reviving partial journeys!
Please note, that there is an optional configuration (turned off by default) that allows abandoned journeys to be resumed from the last step that wasn’t completed, this option uses the “Reference Number” passed to “startJourney” method to determine the end user and to revive an existing partially completed journey. This option will only be viable if you have strictly unique reference numbers passed to IDWise.
