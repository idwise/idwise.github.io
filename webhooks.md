---
layout: default
parent: Journey API
grand_parent: IDWise Developers
title: IDWise Webhooks
nav_order: 1
---

IDWise Webhooks
===============
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
It is essential that you use IDWise webhooks for your backend-to-backend integration. Webhooks are used to send notifications from the IDWise backend to your server via simple secure HTTPS calls.

* * * * *

Types of webhooks:
------------------

There are two types of webhooks supported by IDWise:

### Journey Completed Webhook:

This webhook triggers when the journey has been completed and processing is finalised on the IDWise backend.

### Manual Review Webhook:

This webhook triggers once a manual review has been completed on the IDWise dashboard (where the system decision was either confirmed or overridden by a dashboard user).

### AML Monitor Update Webhook:

This webhook triggers when there is an update on a monitored AML record (for example, the person was added to a sanctions list or is no longer deemed to be politically exposed).

* * * * *

Configuring webhooks:
---------------------

If interested, please send the webhook URL(s) you want to use to <support@idwise.com> to allow the team to add it to your configuration.

### Experimental webhook (for testing):

You can also use a website called <https://webhook.site/> to get a webhook that you can use during the development and testing phase (on top of your app webhook); IDWise supports adding multiple URLs as webhooks.

* * * * *

How does it work?
-----------------

The webhook mechanism is essentially a way for IDWise to inform your backend of updates on a particular journey, for example, when the journey is complete.

The IDWise server will send a JSON HTTP POST request to the URL configured, with the following details:

```javascript
{
  "event": "**EVENT_NAME**",
  "body": {
    "journeyId": "62e6...........5d0c",
    "referenceNo": "840...........23",
    "systemDecision": "**SYSTEM_DECISION**",
    "finalDecision": "**FINAL_DECISION**"
  }
}
```

### Where EVENT_NAME is the event concerned, as mentioned above, we support:

-   Finished Journey
-   Manually Reviewed
-   AML Monitor Update

### SYSTEM_DECISION (Code) can be one of the following:

-   **Complete**: The journey has been successfully completed (it shows on the UI as passed).
-   **Incomplete**: The journey has not been completed yet (the user didn't finish all steps).
-   **Refer**: At least one of the steps has failed, and the journey needs to be referred for a manual review.

### FINAL_DECISION:

-   **Approved**: The journey has been approved by a manual reviewer.
-   **Rejected**: The journey has been rejected by a manual reviewer.
