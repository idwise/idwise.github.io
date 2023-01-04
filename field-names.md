---
nav_exclude: true
---

Fields Extracted by IDWise
==========================

IDWise engine extracts information from documents in a structured and normalised way, we follow the conventions below.

{% include cta.md %}

Date Fields:
-------------

-   We extract dates always in ISO 8601 format: YYYY-MM-DD.
-   We always convert the digits to English (Western-Arabic) digits regardless to how they are written on the document.
-   If the date is in a different calender, we add a suffix depending on the calender, for example, we add Hijri to the end of each field if the calender is Hijri calender (e.g. "Birth Date Hijri").

Fields in Native Language:
--------------------------

When the field is extracted in the native language of the country (not in English or Latin), we add the suffix "Native" to the field name to ensure that it is not mixed with the English field, e.g. "First Name Native".

ID Number Extraction:
---------------------

When we extract document number or personal number, we always change digits to English (Western-Arabic) digits, and remove any separators (e.g. spaces, dots, dashes,...).

# List of Standard Field Names:


## Key Fields:
- Full Name
- First Name
- Last Name
- Personal Number
- Birth Date
- Sex
- Birth Place
- Nationality
- Nationality Code
- Address
- Document Number
- Expiry Date
- Issue Date


## Supplementary Fields:
- Blood Group
- Eye Colour
- Father Name
- Grandfather Name
- Maiden Name
- Middle Name
- Mother Name
- Spouse Name
- Signature
- Height
- Identifying Marks
- Address Block
- Address Line 1
- Address Line 2
- Barcode
- Country of Stay
- Current Residence City
- Document Validity
- Employer
- Enlistment Date
- Insurance Cover Type
- Insurance Expiry Date
- Insurance Policy Number
- Insurance Provider
- Investor
- Investor Activity
- Investor Name
- Investor Type
- Issuing Authority
- Issuing Country Code
- Licence Category
- Licence Class
- Machine Readable Zone
- Ocr Mask
- Old Passport Number
- Original Residence City
- Passport Expiry Date
- Passport Number
- Photo
- Profession
- Promotion Date
- Rank
- Recruitment date
- Residency Type
- Vehicle Brand
- Vehicle Chassis
- Vehicle Colour
- Vehicle Driver
- Vehicle Driver Personal Number
- Vehicle Empty Weight
- Vehicle Fuel
- Vehicle Full Weight
- Vehicle Gear Type
- Vehicle Identification Number
- Vehicle Model
- Vehicle Motor Number
- Vehicle Origin Country
- Vehicle Owner
- Vehicle Owner Personal Number
- Vehicle Registration
- Vehicle Registration Date
- Vehicle Registration Type
- Vehicle Seats
- Vehicle Type
- Vehicle Year
- Visa Purpose
- Year of Birth
- Car Brand
- Car Chassis
- Car Colour
- Car Driver
- Car Driver Personal Number
- Car Empty Weight
- Car Fuel
- Car Full Weight
- Car Gear Type
- Car Model
- Car Motor Number
- Car Origin Country
- Car Owner
- Car Owner Personal Number
- Car Registration
- Car Registration Type
- Car Seats
- Car Type
- Car Year
- CombinedData
- Company Name
- Activity Code
- Activity Start Date
- First Dose Date
- Third Dose Date
- Second Dose Date
- Fourth Dose Date
- Mission
- Mission Code
