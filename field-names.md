---
nav_exclude: true
---

# Fields Extracted by IDWise
IDWise engine extracts information from documents in a structured and normalised way, we follow the conventions below:

## Dates Fields:
* We extract dates always in **ISO 8601** format: YYYY-MM-DD.
* We always convert the digits to **English (Western-Arabic)** digits regardless to how they are written on the document.
* If the date is in a different calender, we add a **suffix depending on the calender**, for example, we add Hijri to the end of each field if the calender is Hijri calender (e.g. "Birth Date Hijri").

## Fields in Native Language:
When the field is extracted in the native language of the country (not in English or Latin), we add the suffix "Native" to the field name to ensure that it is not mixed with the English field, e.g. "First Name Native".

## ID Number Extraction:
When we extract document number or personal number, we always change digits to English (Western-Arabic) digits, and remove any separators (e.g. spaces, dots, dashes,...).

## List of Standard Field Names:
* Birth Date
* Issue Date
* Expiry Date
* Address
* Birth Place
* Country of Stay
* Document Number
* Father Name
* Grandfather Name
* First Name
* Full Name
* Maiden Name
* Identifying Marks
* Issuing Authority
* Issuing Country Code
* Last Name
* Licence Class
* Licence Category
* Machine Readable Zone
* Middle Name
* Mother Name
* Nationality
* Nationality Code
* Personal Number
* Photo
* Profession
* Sex
* Signature
* Spouse Name
* Car Owner
* Car Owner Personal Number
* Car Driver
* Car Driver Personal Number
* Vehicle Identification Number
* Car Registration
* Car Model
* Car Brand
* Car Type
* Car Year
* Car Gear Type
* Car Seats
* Car Fuel
* Car Colour
* Car Motor Number
* Car Chassis
* Car Origin Country
* Car Empty Weight
* Car Full Weight
* Car Registration Type
* Document Validity
* Passport Number
* Old Passport Number
* Residency Type
* Current Residence City
* Original Residence City
* Employer
* Blood Group
* Address Line 1
* Address Line 2
* Address Block
* Insurance Provider
* Insurance Policy Number
* Insurance Cover Type
* Insurance Expiry Date
* Passport Expiry Date
* Vehicle Registration Date
* Eye Colour
* Company Name
* Visa Purpose
