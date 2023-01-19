---
layout: default
parent: Web SDK
grand_parent: IDWise Developers
title: V2.0 Migration Guide
nav_order: 1
---

# Migration Guide

This guide will help you to migrate from version 1 to version 2 of the IDWise SDK.

## Recommendation

We strongly recommend using the most recent version of our web SDK by referencing the assets (JS and CSS) from our
servers rather than using a local copy. This ensures you always have access to the latest improvements, bug fixes, and
security updates. By referencing the files directly from our servers, you can be sure that you are using the most
current version of the SDK.

## Steps

Please follow these steps to migrate to the latest version of the SDK:

1. Update the reference to the style sheet file “https://app.idwise.ai/idwise.min.css”.

   **Replace**
    ```
    <link href="https://app.idwise.ai/idwise.min.css" rel="stylesheet">
    ```

   **With**
    ```
    <link href="https://releases.idwise.com/websdk/latest/idwise.min.css" rel="stylesheet">
    ```

2. Update the reference to the script file “https://app.idwise.ai/idwise.min.js”.

   **Replace**
    ```
    <script href="https://app.idwise.ai/idwise.min.js"></script>
    ```

   **With**
    ```
    <script href="https://releases.idwise.com/websdk/latest/idwise.min.js"></script>
    ```
