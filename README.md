Customer Data Cleaning and Standardization

Project Overview

This project focuses on cleaning and standardizing a 1,000-row customer dataset containing inconsistent formats, mixed data representations, currency variations, missing-value indicators, and non-standard categorical values.

The project uses Google Sheets formulas, including ARRAYFORMULA and text-processing functions, to build repeatable transformations and produce a standardized dataset.

Data Quality Challenges

- Multiple representations of missing values, including "NULL", "-", "N/A", and inconsistent spacing.
- Currency values containing "$", "USD", commas, and negative numbers.
- Inconsistent customer segment labels, abbreviations, capitalization, and spelling.
- Mixed representations of customer activity status, including "Y", "N", "yes", "no", "1", "0", "TRUE", and "FALSE".

Cleaning Approach

- Standardized missing-value indicators.
- Normalized currency-formatted values into numeric representations.
- Mapped inconsistent segment labels to consistent categories.
- Converted activity indicators into Boolean values.
- Preserved the original dataset and stored cleaned results separately.
- Flagged unexpected values for further investigation.

Tools and Techniques

- Google Sheets
- ARRAYFORMULA
- IF, IFERROR, and VALUE
- REGEXMATCH and REGEXREPLACE
- Text normalization and category mapping

Project Deliverables

- Original dataset
- Cleaned dataset
- Reusable cleaning formulas
- Documentation of transformation rules and data-quality decisions

Data Quality Considerations

Negative spending values, including "-999", are retained pending investigation because their meaning cannot be established from the value alone. Cleaning decisions should preserve data integrity and avoid unsupported assumptions.

Future Improvements

- Duplicate and missing-value audits
- Automated validation checks
- Before-and-after data-quality metrics
