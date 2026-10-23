Customer Data Cleaning Formulas

Project Overview

This document describes the Google Sheets formulas used to clean and standardize a customer dataset containing approximately 1,000 rows. The formulas handle customer IDs, email addresses, dates, country names, spending values, customer segments, and active-status values.

Tools Used

- Google Sheets
- "ARRAYFORMULA"
- "MAP" and "LAMBDA"
- "IF" and "IFERROR"
- "REGEXMATCH", "REGEXEXTRACT", and "REGEXREPLACE"
- "SUBSTITUTE", "TRIM", and "LOWER"
- "VALUE", "TEXT", "DATE", and "SPLIT"
- "SWITCH" and "PROPER"

1. Customer ID Standardization

Formula:

="CUST-"&VALUE(SUBSTITUTE(A2,"CUST-",""))

Purpose:

- Removes the existing "CUST-" prefix from the original ID.
- Converts the remaining value to a number.
- Adds the standardized "CUST-" prefix.

Example:

Original ID| Standardized ID
CUST-101| CUST-101
CUST-205| CUST-205

Note: This formula assumes the ID suffix is numeric. Non-numeric suffixes may cause an error.

2. Email Cleaning

Formula:

=ARRAYFORMULA(IF(C2:C="","",LOWER(TRIM(IFERROR(REGEXEXTRACT(SUBSTITUTE(SUBSTITUTE(C2:C,"[at]","@"),"[AT]","@"),"[\w.+%-]+@[\w.-]+\.[A-Za-z]{2,}"),"")))))

Purpose:

- Processes email values across the column.
- Replaces "[at]" and "[AT]" with "@".
- Extracts text matching the specified email pattern.
- Removes surrounding whitespace and converts the result to lowercase.
- Returns a blank when no matching pattern is found or the source cell is blank.

Example:

Original Email| Cleaned Email
JOHN.SMITH@EXAMPLE.COM| john.smith@example.com
jane[at]example.com| jane@example.com

Note: Pattern matching does not confirm that an email address exists or is deliverable.

3. Date Standardization

Formula:

=MAP(D2:D,LAMBDA(d,IF(TRIM(d&"")="","",IF(REGEXMATCH(LOWER(TRIM(d&"")),"^(null|none|n/a)$"),"",IFERROR(IF(ISNUMBER(d),TEXT(d,"yyyy-mm-dd"),IF(REGEXMATCH(TRIM(d),"/"),TEXT(DATE(VALUE(INDEX(SPLIT(d,"/"),1,3)),VALUE(INDEX(SPLIT(d,"/"),1,1)),VALUE(INDEX(SPLIT(d,"/"),1,2))),"yyyy-mm-dd"),TEXT(DATE(VALUE(INDEX(SPLIT(d,"."),1,3)),VALUE(INDEX(SPLIT(d,"."),1,2)),VALUE(INDEX(SPLIT(d,"."),1,1))),"yyyy-mm-dd"))),"REVIEW")))))

Purpose:

- Processes dates row by row using "MAP" and "LAMBDA".
- Converts numeric date values to "yyyy-mm-dd".
- Treats blank cells and the markers "NULL", "NONE", and "N/A" as missing.
- Interprets slash-separated dates as month/day/year.
- Interprets dot-separated dates as day.month.year.
- Returns "REVIEW" when conversion produces an error.

Example:

Original Date| Standardized Date
12/25/2025| 2025-12-25
25.12.2025| 2025-12-25
NULL| Blank

Note: The assumed date formats should be confirmed against the source dataset. Ambiguous or impossible dates may require additional validation.

4. Country Standardization

Formula:

=ARRAYFORMULA(IF(E2:E="","",SWITCH(UPPER(REGEXREPLACE(TRIM(E2:E),"\.","")), "UK","United Kingdom","GB","United Kingdom","GBR","United Kingdom","DE","Germany","DEU","Germany","FR","France","BR","Brazil","FRA","France","US","United States","USA","United States","UNITED STATES","United States","UNITED KINGDOM","United Kingdom","GERMANY","Germany","FRANCE","France",PROPER(TRIM(E2:E)))))

Purpose:

- Removes periods and trims surrounding whitespace.
- Converts country codes and recognized country names to standardized names.
- Uses "PROPER" to format unrecognized values when no mapping matches.

Standardization mappings:

Input Values| Standardized Value
UK, GB, GBR| United Kingdom
DE, DEU| Germany
FR, FRA| France
BR| Brazil
US, USA| United States
United Kingdom| United Kingdom
Germany| Germany
France| France

Note: The formula standardizes listed values but does not validate every possible country code. Unrecognized values may remain in the output in title case and should be reviewed.

5. Spend Cleaning

Formula:

=ARRAYFORMULA(IF(TRIM(F2:F&"")="","",IF(REGEXMATCH(UPPER(TRIM(F2:F&"")),"^(NULL|-|N\s*/\s*A)$"),"",IFERROR(VALUE(REGEXREPLACE(SUBSTITUTE(SUBSTITUTE(SUBSTITUTE(UPPER(TRIM(F2:F&"")),"USD",""),",",""),"$",""),"\s","")),"CHECK"))))

Purpose:

- Handles blank cells and recognized missing-value markers.
- Removes "USD", dollar signs, commas, and whitespace.
- Converts valid numeric text into numeric values.
- Returns "CHECK" when conversion fails.

Examples:

Original Value| Cleaned Result
$1,250| 1250
USD 2,500| 2500
750| 750
NULL| Blank
N/A| Blank
Invalid text| CHECK

Note: Negative values are retained if they can be converted to numbers. They should be investigated against the dataset's business rules rather than automatically removed.

6. Customer Segment Standardization

Formula:

=ARRAYFORMULA(IF(G2:G="","",IF(REGEXMATCH(LOWER(TRIM(G2:G)),"^ent$|^enterprise$"),"Enterprise",IF(REGEXMATCH(LOWER(TRIM(G2:G)),"^mid[\s-]*market$"),"Mid-Market",IF(REGEXMATCH(LOWER(TRIM(G2:G)),"^smb$|^small\s*buisness$|^small\s*business$"),"Small Business",IF(LOWER(TRIM(G2:G))="consumer","Consumer","CHECK"))))))

Purpose:

- Removes surrounding whitespace.
- Standardizes variations in customer segment names.
- Flags unrecognized nonblank values as "CHECK".

Standardization mappings:

Original Values| Standardized Value
ENT, Enterprise| Enterprise
Mid-Market, midmarket, Mid Market| Mid-Market
SMB| Small Business
Small Buisness| Small Business
Small Business| Small Business
Consumer| Consumer
Unrecognized category| CHECK

Note: The formula intentionally includes the source spelling "Buisness" so that this variation is standardized.

7. Active Status Standardization

Formula:

=ARRAYFORMULA(IF(TRIM(H2:H&"")="","",IF(REGEXMATCH(UPPER(TRIM(H2:H&"")),"^(Y|YES|1|TRUE)$"),TRUE,IF(REGEXMATCH(UPPER(TRIM(H2:H&"")),"^(N|NO|0|FALSE)$"),FALSE,"CHECK"))))

Purpose:

- Standardizes different representations of active status.
- Converts recognized positive values to Boolean "TRUE".
- Converts recognized negative values to Boolean "FALSE".
- Leaves blank cells blank and flags unrecognized nonblank values as "CHECK".

Standardization mappings:

Original Values| Standardized Value
Y, YES, 1, TRUE| TRUE
N, NO, 0, FALSE| FALSE
Blank| Blank
Unrecognized value| CHECK

8. Cleaning Workflow

1. Preserve the original raw dataset.
2. Apply formulas to the relevant columns.
3. Review cells marked "CHECK" or "REVIEW".
4. Verify standardized country names, dates, customer segments, and active-status values.
5. Investigate unusual numeric values using the dataset's business context.
6. Copy the validated cleaned results into a separate sheet using Paste special → Values only.
7. Compare raw and cleaned row counts and check for unintended data loss.
8. Save the raw data, cleaned data, and formula documentation separately.

9. Data Quality Notes

- Blank outputs may represent missing source values or values for which no matching email pattern was found.
- "CHECK" indicates a value that could not be converted or standardized according to the formula's rules.
- "REVIEW" indicates that a date conversion produced an error.
- Negative spending values are preserved unless a separate validation rule determines that they are invalid.
- Formula outputs should be checked against the original dataset before being treated as final.
- Standardization improves consistency but does not independently prove that the underlying information is accurate.

Conclusion

This project demonstrates practical data cleaning and standardization using Google Sheets. It addresses inconsistent formatting, missing-value markers, mixed data types, country variations, and non-standard categorical values while keeping the original dataset available for comparison.
