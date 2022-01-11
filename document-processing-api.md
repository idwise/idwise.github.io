---
layout: default
parent: IDWise Developers
title: Document Processing API 
nav_order: 5
---

# IDWise Document Processing API
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


Integrating with IDWise document processing API couldn't be easier! It is a standard HTTP API, it processes an image and returns the recognition and information extraction results synchronously as part of the HTTP response as JSON.

You can access this API by integrating to a simple app, or simply by invoking it using a tool such as Postman.

## API Details 
### API endpoint: 
Base URL: https://api.idwise.com

Path: /process/document

### Authentication: 
Basic authentication.
You will need API Key and API Secret, these need to be passed into the "Authorization" HTTP header. API key (as username) and API secret (use as password) need to be separated by a colon (:) then encoded as base64.
API Key and API Secret will be shared with you by IDWise team.

### HTTP Method: 
POST

### Parameters:
All paramters are sent as part of POST Body, encoded as form-data (multi-part).

Here are the parameters
-   `front` (or `image`) [Required]: of type "file", this should include the image file to be processed, the image can be of any widely used image format (JPG, PDF, ...)
-   `back`: this parameter is optional, and can be used to process a double-sided document.

## Example
Example on how to invoke the API via (curl) command-line tool, you need to add the full authentication header:

`curl --location --request POST 'https://api.idwise.com/process/document' --header 'Authorization: Basic YmN..........DRi' --form 'front=@"/C:/my-image.jpg"'`

Please contact IDWise at `github-sales (at) idwise.com` to get your hands on a API credintials to take the solution for a wild ride!
