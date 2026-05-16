# C2i Data Comments — final_data_v4_filtered.csv

Fill rates computed across all 375,827 rows. For HS columns, public fill (n=292,364) and private fill (n=83,463) are shown separately. Private school values of `-10` in public-only columns are sentinel codes meaning "not applicable"; they are counted as filled but should be excluded from any analysis.

---

## Identifiers

| Column | Fill Rate |
| :--- | :--- |
| college\_id | 100.0% |
| cycle | 100.0% |
| school\_type | 100.0% |

---

## High School

| Column | Coverage | Public Fill | Private Fill |
| :--- | :--- | :--- | :--- |
| hs\_id | both | 100.0% | 100.0% |
| hs\_name | both | 100.0% | 100.0% |
| hs\_city | both | 99.2% | 100.0% |
| hs\_state | both | 99.2% | 100.0% |
| hs\_zip | both | 99.2% | 100.0% |
| hs\_ctyname | private only | 0.0% | 100.0% |
| hs\_cty\_fips | both | 99.2% | 100.0% |
| hs\_lat | both | 99.2% | 100.0% |
| hs\_long | both | 99.2% | 100.0% |
| hs\_enrollment | both | 99.4% | 100.0% |
| hs\_pct\_white | both | 94.9% | 100.0% |
| hs\_pct\_black | both | 94.9% | 100.0% |
| hs\_pct\_hispanic | both | 94.9% | 100.0% |
| hs\_pct\_asian | both | 94.9% | 100.0% |
| hs\_pct\_aian | both | 94.9% | 100.0% |
| hs\_pct\_nhpi | both | 94.9% | 100.0% |
| hs\_pct\_two\_or\_more | both | 94.9% | 100.0% |
| hs\_students\_per\_teacher | both | 90.3% | 97.9% |
| hs\_title\_i\_status | public only\* | 61.3% | 100.0%\* |
| hs\_urban\_centric\_locale | public only\* | 100.0% | 100.0%\* |
| hs\_charter | public only\* | 100.0% | 100.0%\* |
| hs\_magnet | public only\* | 61.3% | 100.0%\* |
| hs\_pct\_free\_or\_reduced\_price\_lunch | public only\* | 84.2% | 100.0%\* |
| hs\_school\_level | both | 100.0% | 100.0% |
| hs\_highest\_grade\_offered | both | 100.0% | 100.0% |

\* Private school rows for these columns contain `-10` (not applicable sentinel). The private fill rate reflects this sentinel, not real data. Exclude `-10` before analysis.

**Note on missingness in `hs_title_i_status` and `hs_magnet`:** The CCD directory API does not include these fields for 2022–2023. Values for those years were backfilled from 2021 CCD data in `network_degree_tables.ipynb`. Rows where backfill was not possible remain missing.

---

## College

| Column | Fill Rate |
| :--- | :--- |
| col\_name | 99.9% |
| col\_city | 99.9% |
| col\_st | 99.9% |
| col\_zip | 99.9% |
| col\_type | 99.9% |
| col\_ctyname | 99.9% |
| col\_ctyfips | 99.9% |
| col\_shparea | 99.9% |
| col\_shplength | 99.9% |
| col\_inst\_control | 87.4% |
| col\_inst\_size | 87.4% |
| col\_endow\_total | 37.0% |
| col\_endow\_per\_fte | 34.6% |
| col\_number\_applied | 77.5% |
| col\_number\_admitted | 77.5% |
| col\_number\_enrolled\_total | 77.5% |
| col\_acceptance\_rate | 77.5% |
| col\_enrollment\_rate | 77.5% |
| col\_tuition\_published | 81.5% |
| col\_tuition\_fees\_ft | 84.0% |

---

## Variable Notes and Code Sources

The following columns are coded variables. Code definitions can be found in the sources listed.

### High School

**`hs_title_i_status`** — Coded variable. Values 1–5 indicate different Title I eligibility and program types; 6 = not eligible; negative values = missing/not applicable. Source: CCD directory API (`hs_economic.ipynb`). Full code table in `C2i data comments.md`.

**`hs_urban_centric_locale`** — Coded variable. Values 1–8 (older scheme) or 11–43 (NCES 12-category scheme) indicate city, suburb, town, and rural classifications. Negative values = missing/not applicable. Source: CCD directory API (`hs_economic.ipynb`). Full code table in `C2i data comments.md`.

**`hs_charter`** — Binary coded variable. 0 = not a charter school, 1 = charter school; negative values = missing/not applicable. Source: CCD directory API (`hs_economic.ipynb`).

**`hs_magnet`** — Binary coded variable. 0 = not a magnet school, 1 = magnet school; negative values = missing/not applicable. Source: CCD directory API (`hs_economic.ipynb`).

**`hs_school_level`** — Coded variable; encoding differs by school type. Public: 0 = Pre-K, 1 = Primary, 2 = Middle, 3 = High, 4 = Other, 5 = Ungraded, 6 = Adult Ed, 7 = Secondary; negative = missing/not applicable. Private (PSS): 1 = Elementary, 2 = Secondary, 3 = Combined. Source: CCD directory API for public (`geo.ipynb`, `standardize_hs.ipynb`); PSS for private (`private_school.ipynb`). Full code table in `C2i data comments.md`.

**`hs_highest_grade_offered`** — Coded variable. -1 = Pre-K, 0 = Kindergarten, 1–12 = grade level, 13 = grade 13/adult ed continuation, 14 = adult education, 15 = ungraded; negative = missing/not applicable. Source: CCD directory API (`geo.ipynb`). Full code table in `C2i data comments.md`.

### College

**`col_inst_control`** — Coded variable. 1 = Public, 2 = Private not-for-profit, 3 = Private for-profit; negative = missing/not applicable. Source: IPEDS via Education Data API (`add_college_cols.ipynb`). Full code table in `C2i data comments.md`.

**`col_inst_size`** — Coded variable. 1 = Under 1,000, 2 = 1,000–4,999, 3 = 5,000–9,999, 4 = 10,000–19,999, 5 = 20,000 and above; negative = missing/not applicable. Source: IPEDS via Education Data API (`add_college_cols.ipynb`). Full code table in `C2i data comments.md`.

**`col_type`** — Institutional type label (string). Source: provided college geo file (`College_geo.csv`).

---

## Data Sources by Column Group

| Column Group | Source | Notebook |
| :--- | :--- | :--- |
| `col_name`, `col_city`, `col_st`, `col_zip`, `col_type`, `col_ctyname`, `col_ctyfips`, `col_shparea`, `col_shplength` | Provided `College_geo.csv` | `geo.ipynb` |
| `col_inst_control`, `col_inst_size` | IPEDS via Education Data API | `add_college_cols.ipynb` |
| `col_endow_total`, `col_endow_per_fte` | NACUBO via Education Data API | `add_college_cols.ipynb`, `fill_endowment_2023.ipynb` |
| `col_number_applied`, `col_number_admitted`, `col_number_enrolled_total`, `col_acceptance_rate`, `col_enrollment_rate` | IPEDS admissions via Education Data API | `add_college_cols.ipynb` |
| `col_tuition_published`, `col_tuition_fees_ft` | IPEDS tuition via Education Data API | `add_college_cols.ipynb` |
| `hs_city`, `hs_state`, `hs_zip`, `hs_cty_fips`, `hs_lat`, `hs_long` (public) | CCD directory via Education Data API | `geo.ipynb`, `standardize_hs.ipynb`, `fix_fips_codes.ipynb` |
| `hs_city`, `hs_state`, `hs_zip`, `hs_ctyname`, `hs_cty_fips`, `hs_lat`, `hs_long` (private) | PSS 2019-20 | `standardize_hs.ipynb`, `fix_fips_codes.ipynb` |
| `hs_name` (public) | CCD directory via Education Data API | `standardize_hs.ipynb` |
| `hs_name`, `hs_school_level` (private) | PSS 2019-20 (used for all cycles) | `private_school.ipynb` |
| `hs_pct_white` through `hs_pct_two_or_more` (public) | CCD enrollment by race via Education Data API | `pull_hs_race_data.ipynb` |
| `hs_pct_white` through `hs_pct_two_or_more` (private) | PSS 2019-20 (used for all cycles; P_WHITE etc.) | `standardize_hs.ipynb` |
| `hs_title_i_status`, `hs_urban_centric_locale`, `hs_charter`, `hs_magnet`, `hs_enrollment`, `hs_pct_free_or_reduced_price_lunch`, `hs_students_per_teacher` | CCD directory via Education Data API | `hs_economic.ipynb` |
| `hs_school_level`, `hs_highest_grade_offered` (public) | CCD directory via Education Data API | `standardize_hs.ipynb` |
| `hs_school_level`, `hs_highest_grade_offered` (private) | PSS 2019-20 | `private_school.ipynb` |
