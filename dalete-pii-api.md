
---
layout: default
title: DELETE Journey API 
nav_exclude: true
---

Delete Personal Information API
==================

This API allows you to remove all Personally Identifiable Information (PII) associated with a specific journey using its journey-id.

{% include cta.md %}

Endpoint
--------

`https://api.idwise.com/journey/{{journey-id}}`

HTTP Method
-----------

`DELETE`

Authentication
--------------

Use the "Authorization" HTTP header to send your API Key and API Secret using basic authentication. Encode both values separated by a colon (:) in base64, and prepend the encoded string with "Basic" (the same way you authenticate for all other API endpoints).

Path Variable
-------------

-   `journey-id`: The unique identifier of the journey you want to delete.

Response
--------
Upon successful deletion, the API will return a 2xx status code. Otherwise, you will get a non-success status code depending on the error (e.g. 4xx or 5xx).
