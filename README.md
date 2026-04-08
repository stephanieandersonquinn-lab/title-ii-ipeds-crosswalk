# Title II–IPEDS Crosswalk & Longitudinal Teacher Preparation Dataset
## Overview

This repository contains the open-source methodology and code I created and used to construct a longitudinal crosswalk (also included) between two federal education datasets:

1. Integrated Postsecondary Education Data System (IPEDS)
2. Title II (Sections 205–208) of the Higher Education Act (HEA)

IPEDS and Title II data systems capture different yet complementary characteristics of teacher preparation programs in the U.S. and U.S. Territories. Each data source uses different identifiers and do not directly speak to one another. I developed a transparent and reproducible crosswalk between these sources, allowing for more detailed analysis by institutional and geographic context of where teachers are trained, in what subjects. Below is a description of how I compiled, cleaned, harmonized and merged the data. Also included is guidance to facilitate reuse or adaptation of our methodology.

---
**End-to-End Pipeline:**  
Title II (2012–2024) → Schema Harmonization → Crosswalk Matching (Exact + Fuzzy + Manual Validation) → Merge with IPEDS → Institution Lookup + Value Labels → Final Analytical Dataset

---

## Quick Start

To reproduce the full dataset or adapt this methodology:

1. **Run `notebooks/titleII_schema_harmonization.ipynb`**  
   → Creates the longitudinal Title II dataset (2012–2024)

2. **Run `notebooks/titleII_ipeds_crosswalk.ipynb`**  
   → Generates the initial Title II–IPEDS crosswalk (requires manual validation)  
   → A finalized, validated crosswalk is provided at:  
   `data/outputs/_FINAL_TitleII_with_IPEDS_Matched_UnitID_and_Fuzzy_Details.xlsx`

3. **Run `notebooks/merge_all_years_with_crosswalk.ipynb`**  
   → Produces the final merged dataset with institutional identifiers  
   → This step uses the provided validated crosswalk:  
   `_FINAL_TitleII_with_IPEDS_Matched_UnitID_and_Fuzzy_Details.xlsx`

4. **(Optional) Enrich with institutional attributes**  
   → Use the institution lookup files in `/data/outputs/` to add geographic and institutional characteristics (Read: `ipeds_institution_lookup.md`)

All notebooks are designed to run in Google Colab or a local Python environment.

---

## Compiling the Data 
Here are more details about each data source used:

### 1. Integrated Postsecondary Education Data System (IPEDS)
Data Used: IPEDS, [2023-24]*, General Information; Extract Date: July 2025

IPEDS is the U.S. Department of Education’s primary source of data on postsecondary institutions that participate in federal student aid programs. The IPEDS data used in this project include:

- Enrollment and completion counts by program of study
- Institutional characteristics (public/private control, sector, Land Grant, HBCU, or Tribal College status)
- Geographic identifiers including ZIP codes and county-level FIPS codes

These data provide essential institutional and geographic context but do not distinguish teacher preparation subfields or licensure outcomes.

*Note: At the time of this analysis, 33 institutions reported in Title II had closed; 26 of those institutions closed prior to the 2023-24 IPEDS report year. In those cases, I used the institution's final report data spanning 2011 - 2022 IPEDS General Information reports.

### 2. Title II (HEA Sections 205–208)
Data Used: Report Years 2012 - 2024. Extract Date: July 2025*

Title II reports provide information teacher prep programs that lead to certification or licensure, and consist of

- Data from 50 states, 1 federal district (Washington D.C), and 7 territories (American Somoa, Federal States of Micronesia, Guam, Marshall Islands, Northern Mariana Islands, Puerto Rico, and U.S. Virgin Islands).
- Program-level information by subject area (for example, early childhood, secondary math, special education)
- Distinctions between traditional and alternative route programs
- Characteristics on the demographic composition of candidates and completers
- Enrollment and completer counts

Title II contains limited information at the institutional level or any geographic identifier below the state level. Also, it does not contain a stable institutional identifier, such as an IPEDS Unit ID, that would facilitate direct merging.

*For this project, I accessed and downloaded unsuppressed Title II data prior to its removal from public access. 
- The one exception is Massachusetts' data from Report Year 2021 (Academic Year 2019 - 2020), which was missing all but one university's data. I accessed and added to our data set the missing MA universities from the suppressed Title II report year 2021.

The crosswalk methodology is designed to pull directly from Title II files as they are currently released, even though some program-level details are now suppressed in newer versions. This ensures the code remains reproducible and adaptable while preserving the richer historical record captured in earlier downloads.

## Methodology Summary

### 1. Title II Schema Harmonization (2012–2024)
Our first challenge lay in the drastic change of the Title II reporting schema beginning with the 2019–20 reporting year. For purposes of sustaining consistency within a time series, all historical Title II data have been harmonized to conform to the 2024 Title II schema.
This included:
- Renaming prior report year variables to match 2024 conventions
- Adding placeholder fields where variables did not exist historically
- Reconciling changes in how enrollment and completers were reported across years

#### The `Appendix_TitleII_Column_Harmonization.xlsx` summarizes the Title II variables from earlier reporting years and how they were mapped to their 2024 equivalents.

### 2. Crosswalk Construction
Because IPEDS and Title II do not share a common institutional identifier, the crosswalk is built using text matches of institution names, supported by systematic cleaning and fuzzy matching, as well as manual matching.
#### Name Standardization
The following techniques were applied to standardize institution names in both datasets:
- Convert all texts to lowercase
- Remove punctuation, special characters, and extra spaces or any common format differences
- Expand common abbreviations (e.g., “st” → “saint”)
- Normalize spacing and hyphenation

#### Exact Matching
Exact matches are attempted using:
- State
- Program type
- Standardized program name

#### Fuzzy Matching
When exact matches fail, fuzzy matching is applied using token-based similarity scoring:
- Similarity threshold: 80%
- Title II program names may match against either the IPEDS institution name or alias fields

#### Manual Validation
- All fuzzy matches and unresolved cases are manually reviewed
- Helper fields document match source and confidence
- Program types expected not to have IPEDS UnitIDs (e.g., Alternative, non-IHE-based) are explicitly classified

#### Failsafe Classification
- Programs expected not to map to IPEDS are retained and labeled accordingly
- Remaining unmatched records are isolated for transparency and further review

### 3. Final Merge of Title II Program Data and TitleII - IPEDS Crosswalk
Across the final merged dataset (28,168 Title II program-year records merged to the IPEDS crosswalk of 3,340 institution–program combinations): 
- 25,048 records successfully match to an IPEDS UnitID, with unmatched cases largely attributable to non-IHE programs or unresolved name variations
- A total of 2,705 records correspond to alternative, non-IHE-based programs, (or mislabeled program types) for which an IPEDS Unit ID is not expected and therefore intentionally left blank. 
- The remaining 305 records (approximately 1.1% of all Title II rows) represent IHE-expected programs that could not be automatically matched due to persistent naming variation across years and reporting formats.

## Repository Structure

	├── notebooks/
	│   ├── titleII_schema_harmonization.ipynb
	│   ├── optional_titleii_column_schema_audit.ipynb
	│   ├── titleII_ipeds_crosswalk.ipynb
	│   ├── merge_all_years_with_crosswalk.ipynb
	│ 	└── ipeds_value_labeling.ipynb  # Adds paired label columns to institution lookup
	│	
	├── data/
	│   ├── outputs/      
	│		├── _FINAL_TitleII_with_IPEDS_Matched_UnitID_and_Fuzzy_Details.xlsx   # Final validated Title II + IPEDS Crosswalk
	│ 		├── TitleII_IPEDS_Institution_Lookup.xlsx
	│ 		├── TitleII_IPEDS_Institution_Lookup_With_Labels.xlsx
	│ 		└── IPEDS_Value_Labels_Lookup.xlsx
	├── docs/
	│ 	├── ipeds_institution_lookup.md # How to use lookup + labeling files
	│   ├── crosswalk_methodology.docx
	│   ├── column_mapping_tables.md
	│ 	├── TitleII_2012_2024_Schema_Harmonization.xlsx
	│   ├── TitleII_IPEDS_Data_Dictionary.xlsx
	│   ├── 2013_TitleII_Data_Documentation.pdf
	│   ├── 2018_TitleII_Data_Documentation.pdf
	│	└── 2020_TitleII_Data_Documentation.pdf
	│
	├── CITATION.md
	├── LICENSE
	└── README.md


##### Note: Raw federal data files are not redistributed in this repository. All notebooks are designed to pull directly from official public URLs where possible.

---

## Outputs

Key outputs produced by this repository include:

1. Longitudinal Title II dataset (2012–2024), harmonized to 2024 schema (Run: `titleII_schema_harmonization.ipynb`)
2. Validated Title II–IPEDS crosswalk (initial output requires final manual validation; Run: `titleII_ipeds_crosswalk.ipynb`)
3. Merged Title II + IPEDS dataset with institutional identifiers (Run: `merge_all_years_with_crosswalk.ipynb`)
4. Audit tables identifying unmatched or non-IHE programs (Run: `merge_all_years_with_crosswalk.ipynb`)
5. (Optional) Add institutional and geographic characteristics to the merged dataset (Read: `ipeds_institution_lookup.md`; Run: `ipeds_value_labeling.ipynb`)

---

## Key Files Explained

### Crosswalk File
`_FINAL_TitleII_with_IPEDS_Matched_UnitID_and_Fuzzy_Details.xlsx`

This file contains the validated mapping between Title II programs and IPEDS institutions.  
It includes:

- Matched IPEDS UnitIDs  
- Match methodology indicators (exact, fuzzy, manual)  
- Flags for expected non-matches  

---

### Institution Lookup Files

These files allow researchers to enrich the merged dataset with institutional and geographic characteristics:

- `TitleII_IPEDS_Institution_Lookup.xlsx`  
  → Base institutional reference table (coded values)

- `TitleII_IPEDS_Institution_Lookup_With_Labels.xlsx`  
  → Includes human-readable label columns for IPEDS variables

- `IPEDS_Value_Labels_Lookup.xlsx`  
  → Mapping between coded IPEDS values and labels

---

### Data Dictionary
`docs/TitleII_IPEDS_Data_Dictionary.xlsx`
- Provides definitions and descriptions for variables included in the crosswalk and institution lookup datasets.

`2013_TitleII_Data_Documentation.pdf`, `2018_TitleII_Data_Documentation.pdf`, `2020_TitleII_Data_Documentation.pdf`
- Provides variable definitions for each tab of the Title II annual reports.
- We used these dictionaries to identify changes in variables across report years (e.g. naming conventions, additions, deletions, etc.) 

---

### Schema Harmonization Reference
`docs/TitleII_2012_2024_Schema_Harmonization.xlsx`

Documents how pre-2020 Title II variables were mapped to the 2024 schema.

---

## How to Use the Data

To conduct analysis using this repository:

1. Use the merged dataset (from `merge_all_years_with_crosswalk.ipynb`) as your base.

2. Join institutional characteristics using the lookup file:

```python
df = df.merge(df_lookup, on="UnitID", how="left")
```

3. Use _Label columns for interpretation and reporting (e.g., sector, control).
4. Use ZIP and FIPS fields to merge with external datasets such as Census or labor market data.

This workflow enables analysis of teacher preparation programs by:

- Institution type
- Geography
- Program characteristics
- Demographic composition

---

## Known Limitations

- Title II does not include a stable institutional identifier, requiring text-based matching.
- Some institutions appear under multiple name variations across years.
- Approximately 305 records (~1.1%) remain unmatched due to persistent naming inconsistencies and had to be manually matched.
- Alternative, non-IHE-based programs (2,705 records) are not expected to map to IPEDS institutions.
- IPEDS does not distinguish teacher preparation subfields or licensure outcomes.
- Title II data after recent releases include suppressed fields, limiting comparability with earlier years.

Users should interpret unmatched records and crosswalk results accordingly.

---

## Reproducibility
All notebooks are designed to run in Google Colab or a local Python environment.
Where possible, intermediate outputs are cached to reduce repeated downloads and ensure consistent results.

## MIT License
This project is released under the MIT License. You are free to use, adapt, modify, and redistribute the code and derived outputs with attribution. See the LICENSE file for details.

## Citation
### If you use this methodology, code, or crosswalk in published work, please cite:

Quinn, S. (2026). Title II–IPEDS Crosswalk & Longitudinal Teacher Preparation Dataset (Version v2). [Online] Western Governors University - Craft Education. Zenodo. Available at https://github.com/stephanieandersonquinn-lab/title-ii-ipeds-crosswalk. DOI: [https://doi.org/10.5281/zenodo.18676589](https://doi.org/10.5281/zenodo.18676588)

