# IPEDS Institution Lookup and Value Labeling

## Overview

The Title II–IPEDS crosswalk enables program-level linkage between Title II teacher preparation data and IPEDS institutions via `UnitID`. However, the merged dataset does not include the full set of IPEDS institutional attributes (e.g., sector, control, geography).

To support downstream analysis, this repository provides a set of lookup and labeling files that allow researchers to:

- Attach institutional characteristics (e.g., sector, control, HBCU status)
- Add geographic identifiers (e.g., ZIP code, county FIPS)
- Convert coded IPEDS variables into human-readable labels

---

## Files and Their Purpose

### 1. `TitleII_IPEDS_Institution_Lookup.xlsx`

This file serves as the **primary institutional reference table**.

It contains one row per institution (`UnitID`) and includes:

- Institutional identifiers (`UnitID`)
- Institution name
- Geographic fields (ZIP code, state FIPS, county FIPS)
- IPEDS-coded variables (e.g., sector, control, level)
- Additional institutional attributes used in analysis

This file is designed to be **joined to the merged dataset**.

---

### 2. `TitleII_IPEDS_Institution_Lookup_With_Labels.xlsx`

This file extends the lookup table by adding **paired label columns** for coded IPEDS variables.

For each coded variable, a corresponding `_Label` column is included. For example:

| Original Column | Label Column |
|----------------|-------------|
| Sector of institution | Sector of institution_Label |
| Control of institution | Control of institution_Label |
| Status of institution | Status of institution_Label |

This allows users to:

- Preserve original coded values for reproducibility  
- Use human-readable labels for analysis and reporting  

---

### 3. `IPEDS_Value_Labels_Lookup.xlsx`

This file provides the **mapping between coded values and human-readable labels**.

It includes:

- `VariableName` — the column name in the lookup dataset  
- `Value` — the coded value (numeric or string)  
- `ValueLabel` — the corresponding description  

This file is used to dynamically generate labeled columns in the lookup dataset.

---

### 4. `IPEDS_Institution_Data_Dictionary.xlsx`

This file provides detailed definitions for all IPEDS variables included in the institution lookup file.

It includes:

- Variable names  
- Definitions and descriptions  
- Source context from IPEDS  
- Notes on interpretation where applicable  

This file is intended to help users:

- Understand the meaning of coded and labeled variables  
- Interpret institutional characteristics correctly  
- Ensure consistent use of variables in analysis  

Researchers should reference this file when working with unfamiliar IPEDS variables or when validating analysis outputs.

---

## How to Use These Files

### Step 1: Merge institutional attributes into your dataset

Join the merged Title II–IPEDS dataset with the institution lookup file using `UnitID`.

```python
df_merged = df_merged.merge(
    df_lookup,
    on="UnitID",
    how="left"
)
```

### Step 2: Choose between coded or labeled variables

You can use either:

- Coded variables (e.g., `Sector of institution`)
- Labeled variables (e.g., `Sector of institution_Label`)

For most analysis and reporting, labeled columns are recommended.

### Step 3: Use geographic fields for spatial analysis

The lookup file includes:

- `zip_code_TS`  — institution ZIP code (5-digit string)
- `Fips County code`  — county FIPS code (5-digit string)
- State identifiers

These fields allow integration with external datasets such as:

- Census data
- County-level labor market data
- Urbanicity classifications

### Step 4: Reference variable definitions

Use the IPEDS data dictionary to understand the meaning and structure of variables included in the lookup file.

This is especially important when working with:

- Sector classifications
- Institutional control
- Distance education indicators
- Federal designations (e.g., HBCU, Land Grant)

Refer to:

`docs/IPEDS_Institution_Data_Dictionary.xlsx`

## Notes on Data Formatting
### ZIP Codes and FIPS Codes

ZIP codes and county FIPS codes are stored as zero-padded strings (e.g., "01234").

This ensures:

- Consistent joins across datasets
- No reliance on spreadsheet formatting
- Reproducibility across Python, Excel, and CSV workflows

---

### Value Labeling Approach

Label columns are created programmatically using the IPEDS_Value_Labels_Lookup.xlsx file.

This approach:

- Avoids hard-coded mappings
- Makes the pipeline extensible to additional variables
- Ensures transparency and reproducibility

---

### Reproducibility

The labeling process is implemented in the repository notebook:

`notebooks/ipeds_value_labeling.ipynb`

Researchers can rerun this step to:

- Regenerate labeled columns
- Apply updated mappings
- Extend labeling to additional variables

## Summary

These files provide a flexible and reproducible way to:

- Attach institutional metadata to Title II programs
- Interpret coded IPEDS variables
- Support geographic and policy-relevant analysis

They are intended to be used together as part of a broader workflow linking teacher preparation programs to institutional and geographic context.
