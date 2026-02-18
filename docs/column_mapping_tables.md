# Title II Column Mapping and Schema Harmonization (2012–2024)

This document describes how Title II variables from earlier reporting years
were mapped, renamed, or excluded to produce a longitudinal dataset
harmonized to the 2024 Title II schema.

## Overview

Title II reporting underwent a substantial schema change beginning in the
2019–20 reporting year. To enable longitudinal analysis, all pre-2020 data
were transformed to match the 2024 column structure.

Where variables did not exist in earlier years, values are recorded as NA.

---

## Pre-2020 to 2024 Column Mapping

| Pre-2020 Column Name | 2024 Column Name | Notes |
|----------------------|-----------------|-------|
| AsianEnrollment | AsianEnrollment | No change |
| BlackEnrollment | BlackEnrollment | No change |
| WhiteEnrollment | WhiteEnrollment | No change |
| FemaleEnrollment | FemaleEnrollment | No change |
| MaleEnrollment | MaleEnrollment | No change |
| SuperAvgForStuTeach | SCEAvgHrsFor_StdntTch | Renamed in 2024 |
| SuperAvgPriorStuTeach | SCEAvgHrsPrior_StdntTch | Renamed in 2024 |
| SuperFTEAdjunct | SCENumAdjunctIHE | Renamed in 2024 |
| Program | Program | No change |
| ProgramType | ProgramType | No change |
| ProgramCode | ProgramCode | No change |
| ReportYear | ReportYear | No change |

---

## Variables Introduced in 2020+

The following variables were introduced in 2020 and later reporting years.
For pre-2020 data, these columns are included and filled as NA.

- AsianCompleters
- BlackCompleters
- HispanicCompleters
- WhiteCompleters
- GrantMajors
- TotalNumPrepPrgs
- SCEAvgHrsPrior_TeachRcd
- SCENumCoopTeachK12
- AssuranceComments

---

## Deprecated or Excluded Pre-2020 Variables

The following pre-2020 variables were not carried forward because they were
discontinued and could not be harmonized consistently across years:

- CompletersPrior
- CompletersSecondPrior
- PGMedGPAEntry
- PGMedGPAExit
- UGMedGPAEntry
- UGMedGPAExit
- SuperAvgForMentoring

These variables are excluded to maintain longitudinal consistency.

---

## Notes on Interpretation

- Enrollment and completer definitions change beginning in 2020.
- Users should rely on `TotalEnrollment_Adj` when conducting
  cross-year comparisons.
- All column names in the final dataset conform to 2024 conventions.
