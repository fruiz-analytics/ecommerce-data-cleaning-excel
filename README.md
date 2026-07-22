[Uploading README_excel_cleaning.md…]()
# E-commerce Sales Data Cleaning | Excel + Power Query

<img width="2195" height="478" alt="audit_picture" src="https://github.com/user-attachments/assets/e08c0752-56ec-4b81-9a9b-679f77053a0b" />


**Data cleaning case study — formulas and automated pipeline**
Author: Franklin Manuel Ruiz Guadamuz · Tools: Excel (formulas), Power Query · Source: Kaggle — messy e-commerce sales data

---

## 1. Overview
A raw e-commerce sales export (103 records, 11 columns) was audited and cleaned end to end. The file contained inconsistent text, mixed date formats, US decimal notation, hidden nulls, duplicate records, and logically impossible values.

The project delivers **two independent solutions**:
- **Formula-based** — transparent and auditable; every transformation is visible in the sheet.
- **Power Query** — automated and refreshable; the same result in a repeatable pipeline.

Both produced identical results (100 clean records, same 3 flagged discrepancies), which served as a cross-validation of the work.

## 2. Workbook structure
| Sheet | Purpose |
|---|---|
| `00_README` | Objective, scope, method, deliverables |
| `01_Raw` | Original data, untouched |
| `02_Audit` | Issue log with severity and decisions |
| `03_Clean_Formulas` | Cleaning with formulas (raw and clean side by side) |
| `04_Clean_Final` | Final deliverable (values only) |
| `05_Clean_PowerQuery` | Automated pipeline output |

## 3. Issues found and resolved

<img width="1152" height="2497" alt="raw" src="https://github.com/user-attachments/assets/d4ccfe27-0641-4b8c-a81f-91bd5799ce52" />

<img width="1284" height="2425" alt="clean_final" src="https://github.com/user-attachments/assets/fb01975b-aa0d-418c-b3b3-9edc859424aa" />


19 distinct data quality issues were catalogued (full log in `02_Audit`). Highlights:

**Critical**
- **Silent date misparse** — Excel read US-format dates (`M/D/YYYY`) as `D/M/YYYY`, silently converting `7/5/2025` to May 7 instead of July 5. Dates were parsed explicitly and standardized to ISO `YYYY-MM-DD`.
- **Duplicate IDs with conflicting data** — validated against `Quantity × Price` rather than removed automatically. In one case the *correct* record was the later duplicate, so a default "remove duplicates" would have retained corrupted data.
- **Total ≠ Quantity × Price in 3 records** — each exactly **70%** of the calculated value. A consistent pattern suggesting an applied discount not captured in a separate field. Flagged for client confirmation, not overwritten.

**High**
- Empty strings stored instead of true blanks (`ISBLANK` failed; used `LEN(TRIM())=0`).
- US decimal notation requiring conversion before numeric parsing.
- Negative quantities and prices — technically valid numbers, logically impossible. Flagged, not altered.

**Medium / Low**
- 9 category variants reduced to 5 (casing + one misspelling).
- Nulls stored as the literal string `"nan"` (source export artifact).
- Currency symbol embedded in a value (`300$`) — recovered as 300.
- One recoverable Total recalculated and flagged as such.

## 4. Guiding principles
- **Never delete client data silently.** Missing and invalid values are labeled, not removed.
- **Distinguish missing from invalid.** An empty cell and corrupted text are different problems with different fixes.
- **Flag, don't guess.** Outliers and impossible values are surfaced for client review rather than "corrected" on assumption.
- **Validate against business logic, not just data types.** A negative quantity passes every type check and is still impossible.

## 5. Results
| Metric | Value |
|---|---|
| Records in | 103 |
| Records out | 100 |
| Duplicates resolved | 3 |
| Issues catalogued | 19 |
| Records flagged for review | 3 (discrepancy) + 4 (impossible values) |
| Fields recovered | 1 Total, 1 Price |

## 6. Formulas vs Power Query
<img width="1867" height="2423" alt="clean_formulas" src="https://github.com/user-attachments/assets/5cb8c8b4-9f01-424d-aad8-a13c011a7537" />

<img width="1331" height="2425" alt="clean_powerquery" src="https://github.com/user-attachments/assets/268a41d2-d22c-4f10-b638-c110073b4437" />


| Task | Formulas | Power Query |
|---|---|---|
| Date parsing (locale) | Nested FIND / LEFT / MID / RIGHT / DATE | One locale-aware type change |
| Decimal separator | SUBSTITUTE in every numeric column | Included in the same step |
| Trim all text | Column by column | One step, all columns |
| Fixing a mistake | Rework, risk of row misalignment | Delete the step |
| Reusable next month | Rebuild | Refresh |

**Takeaway:** Power Query is far faster for systematic transformations, but duplicate resolution still required case-by-case human judgment. Automation executes decisions — it doesn't make them.

## 7. Skills demonstrated
`TRIM` · `PROPER` · `IF` / nested `IF` · `AND` / `OR` · `IFERROR` · `VALUE` · `SUBSTITUTE` · `LEN` · `FIND` · `LEFT` / `MID` / `RIGHT` · `DATE` · `ROUND` · `ISNUMBER` · `COUNTIF` · Power Query (locale-aware types, replace values, format, custom columns, M language, applied steps)

---
**Files:** `ecommerce_cleaning_project.xlsx` (all sheets) · `messy_ecommerce_sales_data.csv` (source) · this README
