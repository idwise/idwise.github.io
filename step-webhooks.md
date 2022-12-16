---
layout: default
parent: Journey API
grand_parent: IDWise Developers
title: IDWise Step Webhooks
nav_order: 1
nav_exclude: true
---

IDWise Step Webhooks
===============
{: .no_toc }

It's important to use IDWise Step Webhooks for integration between backends. Step webhooks are used to send notifications of changes made at each step from the IDWise backend to the server via simple and secure HTTPS calls.

<details open markdown="block">
  <summary>
    Table of contents
  </summary>
  {: .text-delta }
1. TOC
{:toc}
</details>



Configuring step webhooks:
---------------------

If interested, please send the step webhook URL(s) you want to use to <support@idwise.com> to allow the team to add it to your configuration.


How it works?
-----------------

The webhook mechanism is essentially a way for IDWise to inform your backend of updates on a particular journey, for example, when a document is submitted.

The IDWise server will send a JSON HTTP POST request to the URL configured, with the following details:

```javascript
{
  "event": "**EVENT_NAME**",
  "body": {
    "journeyId": "62e6...........5d0c",
    "referenceNo": "840...........23",
    "stepId":"1",
    "stepType":"Document"
  }
}
```

Here are the possible values:

### EVENT_NAME
It represents the event concerned, as mentioned above, we support:

-   Step Submitted

### Body

The webhook body will contains the following:

-   **journeyId**: The journey Id of the submitted step.
-   **referenceNo**: The reference number.
-   **stepId**: The id of the submitted step.
-   **stepType**: The step type it can be either 'Document' or 'Selfie'.


### Experimental webhook (for testing):

You can also use a website called <https://webhook.site/> to get a webhook that you can use during the development and testing phase (on top of your app webhook); IDWise supports adding multiple URLs as webhooks.

* * * * *
