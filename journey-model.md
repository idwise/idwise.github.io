---
layout: default
parent: Journey API
grand_parent: IDWise Developers
title: IDWise Journey Model Documentation
nav_order: 1
---



# IDWise Journey Model Documentation
{: .no_toc }

<details open markdown="block">
  <summary>
    Table of contents
  </summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

API Version: V2 - Model Version: 3.0

This documentation details the various elements of the result graph after processing a journey in IDWise system. A journey is comprised of documents and biometrics submitted by an applicant. After processing these documents and biometrics, you can access these results by calling IDWise API.

## Journey Model Top-Level Elements

First, here are the top-level elements of the graph (with explanation for each element below):

```json
{
	"journey_id": "",
	"customer_id": "",
	"reference_number": "",
	"start_time": "",
	"end_time": "",
	"system_decision": "",
	"final_decision":"",
	"model_version": "",
	"document": {},
	"selfie": {},
	"rule_results": {},
	"applicant": {},
	"aml":{}
}
```

-   `journey_id`: The unique identifier of the journey. This identifier is used across IDWise systems to refer to the journey and is auto-generated at the first moment the user starts the journey.
-   `customer_id`: The identifier of your account within the system. All journeys within your account share this identifier.
-   `reference_number`: This is a custom identifier you could attach to the journey when you start it and it will be available as part of the result graph. This is useful for example to link an identifier of the user in your system to the journey or journeys made by this user thus making it easier locate the user in your system after they finish the journey (this used to be called user_id).
-   `start_time`: The date time stamp when this journey was initiated. This is formatted as a standard ISO 8601 Date Time string commonly used in JSON ( For example: `2010-07-23T18:25:43.511Z` ).
-   `end_time`: The date time stamp when this journey was finished. This field can be `null` if the journey is not finished yet. The format of this field is also ISO 8601 Date Time string like in `start_time` field.
-   `system_decision`: An enum value (encoded as a string) representing the outcome of the journey as evaluated by IDWise system. This decision accounts for the result of steps and the business rules configured on the journey to reach a conclusion on whether the journey passes automatically or needs referral to manual review. Available values are:
    -   `Incomplete`: The journey is not completed yet so a decision cannot be drawn yet.
    -   `Complete`: The journey was completed and passed all the configured checks and rules.
    -   `Refer`: The journey was completed but has failed one or more of the configured checks or rules.
-   `final_decision`: is the manual review final decision. the default value will be `null` if there is no manual review done. otherwise will be:
    -   `Approved` : if the journey approved by the reviewer.
    -   `Rejected` : if the journey rejected by the reviewer.
-   `model_version`: Represents which version of the result graph this journey follows. Different versions of the graph might have different elements.
-   [`documents`](#document-element): The processing results of documents submitted as part of the journey. This is a dictionary (JSON object) where the *key* represents the `step_id` associated with a given document and the *value* represents the `document` object itself. See the following sections for details on the format of `document` elements.
-   [`selfie`](#selfie-element): Represents the processing results of the live selfie taken during the journey. If the journey is configured with no `Selfie` step then this element will be `null`. See the following sections for details on the format of the `selfie` element.
-   [`rule_results`](#rule-result-element): The business rules that were applied on this journey and their outcomes. These rules can configure the acceptance criteria for a journey to pass. Examples include: Acceptable types of documents to be provided, ensure the documents are non-expired.
-   [`applicant`](#applicant-element): Common data representing the applicant (user) who made the journey.
-   [`aml`](#aml-element): Holds the results of carrying out AML (Anti-Money Laundering) and background checks. If the journey is not configured with AML checks enabled then this element will be `null`. See the following sections for details on the format of the `aml` elements.

## Document Element

This object represents the set of documents in this journey. Each child element inside `documents` contains the results of processing a document that was submitted as part of the journey.

Document processing result includes the type of the document, the data extracted from the document and detailed validation check details carried out while processing the document.

This is an example for documents element (with detailed explanation below):

```json
"documents": {
		"0": {
			"step_title": "UAE Passport",
			"progress": "Completed",
			"has_passed_rules": true,			
			"document_type": "Passport",
			"issuing_country": "United Arab Emirates",
			"issuing_country_code": "ARE",
			"extracted_fields": {
				"First Name": {
					"value": "Sami",
					"read_accuracy": "Low"
					"type": "string"
				},
				"Last Name": {
					"value": "Adam",
					"read_accuracy": "High"
					"type": "string"
				},
				"Photo": {
					"value": null,
					"read_accuracy": null
					"type": "image"
				}
			},			
			"validation_result": {
				"validation_outcome": "Failed",
				"validation_checks": [
					{
						"name": "Document Classification (Front)",
						"description": "Verifies supportability of the type of document and its ability to be fully authenticated.",
						"result": "Failed",
						"result_description": "The document type could not be determined"
					}
				]
			},
			"face_match_result": {
				"is_matched": true,
				"score": 99,
				"images": [
					{
						"image_path": "95602fb54c...............1f181b9",
						"image_source": "Selfie"
					},
					{
						"image_path": "05602fb54cf...............f181b0",
						"image_source": "Document"
					}
				]
			},
			"images": {
				"input_image_path": "98682fb54c...............1f181b9",
				"back_side_image_path": null,
				"front_side_cropped_image_path": "95602fb54...............81f181b9",
				"back_side_cropped_image_path": null
			}
		}
	}

```

Here are the elements that constitute a `document` element:

-   `step_title`: UI specific name of the step for this document it is helpful to distinguish if you have multiple documents (e.g UAE Driving Licence and International Passport).
-   `progress`: Reflects the step progress, its value could be `Completed`, `Started` and `NotStarted`.
-   `has_passed_rules`: Reflects the document validation, and rules. Its value could be either `true` or `false`.
-   `document_type`: The type of this document. The main possible values are: `Passport`, `Driving Licence`, `Visa` and `Identity Card`, there are some other special documents supported as well.
-   `issuing_country`: The name of the country which issued this document.
-   `issuing_country_code`: The 3-letter ISO country code of the country which issued this document.
-   `extracted_fields`: A dictionary (aka object) containing the data fields that were read from the document. Each element of this dictionary has the following:
    -   key: is the name of the field (e.g `First Name`, `Last Name`, `Birth Date` ..etc). For a list of all supported fields, please check [this page](https://idwi.se/fields).
    -   value: is an object that has the following attributes:
        -   `value`: The field value encoded as a string. For all date fields, the value will be encoded in ISO 8601 date format. if the field is of type `Photo` (e.g `Signature`), `value` will be `null`.
        -   `read_accuracy`: It represents the expected accuracy of the reading value, and it can either `High` or `Low`.
        -   `type`: An enum representing the type of the value (i.e `string`, `image`).
-   `validation_result`: A dictionary (aka object) containing the list of the checks that were run to check the authenticity of the document. This element contains:
    -   `validation_outcome`: The conclusive results of all document authenticity checks (doesn't include the business rules in `rule_results`). its value can be either `Passed` or `Failed`.
    -   `validation_checks`: A list of the detailed checks.
-   `face_match_result`: An object that represents the face match result between the portrait photo on the document with the submitted selfie image. this object contains:
    -   `is_matched`: The conclusive face matching decision, it can be either `true` or `false`.
    -   `images` is an array of the images that are used in face matching.
-   `images`: A list of image identifiers for both input and output images:
    -   `front_image_path`: The path of the front image was submitted by the user.
    -   `back_image_path`: The path of the back image was submitted by the user. if the back image wasn't submitted the value will be `null`
    -   `front_cropped_image_path`: The path of the front cropped image.
    -   `back_cropped_image_path`: The path of the back cropped image. if the back image wasn't submitted the value will be `null`

### Selfie Element

This an example for the selfie result object:

```json
"selfie": {		
		"status" : "Complete",
		"is_live": true,
		"image_path": "95652fb54cfa6c15d477a44781f171b8"		
	}

```

This object represents the face liveness status, it has the following attributes:

-   `status`: The conclusive status of the selfie step value could be `Complete`, `Refer`, and `Not Started`. This element is the main element to be used to confirm whether the selfie passed or not.
-   `is_live`: A boolean indicates whether the submitted selfie image is live or not. The liveness is already reflected on `status` element.
-   `image_path`: The image identifier for the submitted selfie image which can be used to retrieve the image through Image Retrieval API.

### Rule Result Element

This object contains the business rule results. business rules are set of checks that can be defined by customers to apply some constraints on the onboarding journeys like what are acceptable document types for each step or prevent a user from submitting expired documents ..etc.

This is an example for the rule result object

```json
"rule_results": {
		"proof_policy": {
			"name": "Proof Policy",
			"result": "Passed",
			"description": "Confirms that the document provided is of the accepted types for this journey step",
			"information": "The document type and issuing country of the document provided by the user is checked against a list of allowed document types and/or issuing countries configured as accepted on this step. If the step is configured to accept a driving licence only but the user provided another type of document, this check would fail",						
			"tips": "Check the type of the document provided and the issuing country and confirm if they are accepted for this type of application",
			"details": [
				{
					"title":"Passport - Front",
					"result": "Passed",
					"result_description": "Document is confirmed to be of one of the accepted types",
					"reference_object": {"object_key":"documents", "instance_key":"passport_step"}					
				},
				{
					"title":"Driving Licence - Front",
					"result": "Passed",
					"result_description": "Document is confirmed to be of one of the accepted types",
					"reference_object": {"object_key":"documents", "instance_key":"0"}					
				}
			]
		}
	}

```

Each item in this object represents one rule, Here are the elements that constitute a `rule` element:

-   `name`: The rule name.
-   `result`: An enum value (encoded as a string) summarises the result of applied rules on each document, It can be either `Passed` or `Failed`.
-   `description`: The description of the applied rule.
-   `information`: The information on the applied rule.
-   `tips`: An advice for the user on how to handle the result of this rule.
-   `details`: Provides a list of the checks that were run on different provided documents and data, each element has the following attribute:
    -   `title`: the title of the check that presents the name of the document and the side to which the rule was applied against.
    -   `result`: the result of the check it can be either `Passed` or `Failed`.
    -   `result_description`: A tailored description massage for each result status.
    -   `reference_object`: Shows the related object for the check.

here is the full list of [Rules](#supported-rules-checks)

### Applicant Element

Contains general information for the applicant.

```json
"applicant": {
		"applicant_id":"",
		"profile_image_path":"95652fb54cfa6c15d477a44781f171b8",		
		"full_name": "Adam Sami",		
		"personal_number": "123456789",
		"birth_date": "1960-05-12",
	}

```

### AML Element

```json
	"aml": {
		"applicability": "Applicable",
		"matches": [
			{
				"score": 82,
				"name": "Sami Adam",
				"countries": [
					"EA"
				],
				"datesOfBirth": [1986],
				"notes": [],
				"datasets": [
					"Politically Exposed Person"
				],
				"aliases": [
					{
						"firstName": "Sami",
						"middleName": "",
						"lastName": "Adam",
						"type": "Name Spelling Variation"
					},
					{
						"firstName": "Sami",
						"middleName": "Adam",
						"lastName": "",
						"type": "Name Spelling Variation"
					}
				],
				"addresses": [
					{
						"addressType": "Business",
						"line1": "Gamal",
						"line2": "Gaza",
						"postcode": "",
						"city": "Cairo",
						"county": "",
						"countryIsoCode": "EA"
					}
				],
				"sex": "Male"
			}
		]
	}

```

Shows the applicant's records over Anti Money Laundering (AML) databases if exist. Here are the attributes that constitute an `aml` element:

-   `applicability`: Shows the applicability of this check for the journey and it can be

    -   `Not Ready Yet`: When the journey is not completed.
    -   `Skipped`: When the personal information is not extracted properly.
    -   `Disabled`: When this check is not enabled.
    -   `Applicable`: When all prerequisites for this check are satisfied and the check was performed. if there are matches will be reflected in `matches` element.
-   `matches`: Presents the list of matched records in the datasets for the applicant, each record of matches has the following sub-elements

    -   `score`: The matching score of the profile. The score in the range of [0-100] the higher the value the closer the match.
    -   `name`: The name of the profile found in the associated database entry.
    -   `countries`: The list of countries from the addresses and nationalities of this profile. It has the format of 2-letter ISO country code.
    -   `datesOfBirth`: Date in either YYYY-MM-DD or YYYY-MM or YYYY ISO format.
    -   `notes`: Notes.
    -   `datasets`: The name of databases in which this match was found.
    -   `aliases`: Other names of profile.
    -   `addresses`: The list of addresses.
    -   `sex`: The gender of the person.

## Appendix

### Supported Rules (Checks):

    | Key | Title | Description |
    | --- | --- | --- |
    | document_validation | Authentic Document | Confirms the authenticity of the submitted documents, likely free of any evidence of manipulation. |
    | same_subject | Same Person | Verifies that all steps in the journey belong to the same person. |
    | no_duplicate_documents | Non-duplicate Document | Confirms that the same document is not submitted more than once. |
    | no_duplicate_applicant | Unique Applicant | Confirms whether the applicant is matched with already existing applicants. |
    | no_aml_records | Datasets (e-KYC/AML) | Confirms whether the applicant has records in electronic Know Your Customer (e-KYC) and Anti Money Laundering (AML) databases, screening for Politically Exposed Persons (PEPs) and Sanctions. |
    | unrecognized_document | Recognised Document | Verifies the recognition of the submitted documents as a precisely supported document type. |
    | proof_policy | Proof Policy | Confirms that the document provided is of the accepted type for each particular journey step. |
    | expired_document | Un-expired Document | Confirms that the documents provided are not expired. |

    The rules you will get depend on the configuration of your journey definition. So some of these rules might not apply in your case. If you need to make adjustments to the rules on your journey definition please contact IDWise support team.

[Extracted Fields](https://idwi.se/fields)
