---
layout: default
parent: IDWise Developers
title: AML Screening Service
nav_order: 7
---

# Standalone AML Screening Service: PEPs, Sanction & More
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

## Overview

AML Check service is an API that allows IDWise customers to search for individuals over Anti Money Laundering (AML) datasets which are updated daily.

## How to use

API only accepts **POST** requests to this url:

```
https://api.idwise.com/aml/search
```


### Authentication

Basic authentication. You will need API Key and API Secret, these need to be passed into the "Authorization" HTTP header. API key (as username) and API secret (use as password) need to be separated by a colon (:) then encoded as base64. API Key and API Secret will be shared with you by IDWise team.


### Request Parameters

**Parameters should be provided as JSON data in request body.**

Request must provide one of the following:

- full_name: it is better to be formatted as Lastnames, Firstnames Middlenames but can be also passed as Firstname Middlenames Lastnames if the order was not known.
- first_name and last_name (both should be provided together).

Other fields are optional which are:

- middle_name.
- birth_date: YYYY-MM-DD ISO formatted date (e.g. 2021-12-30). By default it will search for all values.
- sex: either Male or Female. By default it will search for all values.
- search_countries: a list of ISO country codes that will be used to filter matches (e.g. [”GB”,”US”]).
- match_threshold: the minimum fuzzy matching score to filter out all matches with score lower than it. Defaults to 90, if you want a full match, please pass 100.
- check_type: specify the datasets to search over. User cannot use dataset package above his license but he can use a lower one. check_type can be one of the following:
    - **Basic**: only contains Global Peps and Sanctions
    - **Enhanced**: contains Basic (Gloval PEPs and Sanctions) + Adverse Media, Reputation Risk Databases, Regulatory Enforcement List, and People of Interest. Please contact support@idwise.com for more details about the specific lists we support.
    
### Request Example

```json
{
	"full_name": "",
	"first_name": "Elizabeth",
	"last_name": "Windsor",
	"middle_name": "Alexandra Mary",
	"birth_date": "1926-04-21",
	"sex": "F",
	"search_countries": [
		"GB"
	],
	"match_threshold": "80",
	"check_type": "Enhanced"
}
```

### Response

The result of the query will be similar to the following example:

```json
{
    "matches": [
        {
            "score": 95,
            "name": "Test Test",
            "countries": [
                "GB"
            ],
            "datesOfBirth": [
                "1980"
            ],
            "sex": "Male",
            "notes": [],
            "datasets": [
                "Politically Exposed Person"
            ],
            "aliases": [
                {
                    "firstName": "Test1",
                    "middleName": "",
                    "lastName": "",
                    "type": "Name Spelling Variation"
                }
            ],
            "addresses": [
                {
                    "addressType": "Residential",
                    "countryIsoCode": "GB"
                }
            ]
        }
    ]
}
```

---

## Names of Datasets
```python
[
    "Politically Exposed Person",
    "Linked to a Politically Exposed Persons",
    "Sanctions",
    "Adverse Media",
    "Regulatory Enforcement List",
    "People of Interest",
]
```

## Request Failure Messages

```json
{
    "Invalid Name": "You have to pass either the first and last name or the full name!",
    "Invalid Date": "The date should be YYYY-MM-DD ISO formatted, e.g. 2022-01-01",
    "Invalid Sex": "The sex should be either Male or Female!",
    "Invalid Threshold": "Threshold should be between 0 and 100",
    "Request Failed": "Request Failed!",
    "Invalid Response": "Invalid Response Format!",
    "Invalid Country": "Country should be ISO formatted, e.g. GB",
}
```
