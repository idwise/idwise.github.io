---
layout: default
parent: IDWise Developers
title: Migration Guide
nav_order: 9
---

# Migration Guide

This guide will help you to migrate from version 1 to version 2 of the IDWise SDK.

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
