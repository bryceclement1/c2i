# C2i Project Narrative
Date: 05/16/2026

## Overview

In this project, I have been collaborating with Dr. McElrath to analyze college recruiting data. The data was obtained by tracking geo-location data from cell phones and identifying those who are likely to be college recruiters. This produced a network dataset representing colleges visiting high schools. The raw columns were college IDs, public high school IDs, private high school IDs, and the year of visit. Our task has been to enhance this dataset by pulling school-level data from various sources, conduct data quality checks, and analyze the network to uncover insights about college recruiting.

---

## Data Enhancement

### Geographic Merging

The first step was to merge geographic information onto the network. We were provided with pre-compiled CSV files containing geo-location attributes for colleges (`College_geo.csv`) and private high schools (`Private_Schools_geo.csv`). I merged these directly onto the raw network data. For public high schools, I pulled geo-location data from the Urban Institute Education Data API's CCD directory endpoint and merged it by school ID and year. To maintain consistency throughout the dataset, I standardized all column names so that high school attributes carry an `hs_` prefix and college attributes carry a `col_` prefix. I also created a unified `hs_id` column (combining `ncessch` for public and `ppin` for private schools) and a `school_type` indicator.

### Standardizing High School Columns

After the initial geo merge, I standardized the high school columns to ensure public and private schools had a consistent shared schema. For private schools, I filled geographic fields (state, zip, county, city, latitude, longitude) from the PSS 2019-20 survey data, since this was the only consistently available source. For public schools, I pulled school names from the CCD directory API by year and merged them into a unified `hs_name` column. Redundant private-school-specific columns were dropped once unified fields were created.

### College Institutional Attributes

I pulled college institution size and control data from the IPEDS endpoint via the Education Data API and merged it onto the network by college ID. Institution control categories are 1 (public), 2 (private not-for-profit), and 3 (private for-profit); institution size is a categorical variable (1 = under 1,000 students through 5 = 20,000 and above).

### Admissions and Enrollment Data

From the same IPEDS endpoint, I pulled year-specific admissions data for 2019–2022. Because 2023 data was not yet available in IPEDS at the time of collection, I forward-filled 2022 values to 2023. From these counts, I derived `col_acceptance_rate` (admitted / applied) and `col_enrollment_rate` (enrolled / admitted) and merged all admissions columns onto the network.

### Tuition Data

I pulled published tuition and total tuition-and-fees data from the IPEDS academic-year-tuition endpoint for 2019–2021, forward-filling 2021 values through 2023. This added `col_tuition_published` and `col_tuition_fees_ft` to the network.

### Endowment Data

I pulled year-specific endowment data from the NACUBO endpoint and merged it by college ID for 2019–2022. Because 2022 was a down year for endowments (roughly an 18% market decline), I noted this limitation when forward-filling 2022 endowment values to cover 2023 rows. A separate notebook (`fill_endowment_2023.ipynb`) handled this forward-fill step for the college out-degree table specifically.

### Public High School Race Enrollment

I pulled public high school enrollment by race from the CCD enrollment API. Because the endpoint requires one request per year and per race code, and covers years 2019–2023 with seven race categories plus a total code, this required 35+ API calls and took significant time to complete. From the raw enrollment counts, I calculated percentage columns for each race group (`hs_pct_white`, `hs_pct_black`, `hs_pct_hispanic`, `hs_pct_asian`, `hs_pct_aian`, `hs_pct_nhpi`, `hs_pct_two_or_more`) and merged these onto the network by school ID and year.

### Public High School Economic Attributes

I pulled additional public high school attributes from the CCD directory API, including number of students on free or reduced-price lunch, Title I eligibility status, charter and magnet school designations, and FTE teacher counts. From these, I derived `hs_pct_free_or_reduced_price_lunch` (free/reduced lunch count divided by enrollment) and `hs_students_per_teacher` (enrollment divided by FTE teachers). Because the CCD API does not include `magnet` or `title_i_status` for 2022–2023, I backfilled those fields using 2021 CCD data, which represents the most recent year with full coverage. School designations rarely change year to year, so this approach is a reasonable approximation.

### Private School Attributes from PSS

Private school attribute data is not available through any public API, so I used the National Center for Education Statistics Private School Universe Survey (PSS). I joined the PSS 2019-20 file to the network by private school ID (`ppin`), applying those records across all cycles. The PSS 2021-22 file was tested but caused significant missingness for 2021–2023 cycles, so the 2019-20 file was used exclusively since it matched 100% of the private schools in our network. This added school name (`hs_name`), school level (`hs_school_level`; 1 = Elementary, 2 = Secondary, 3 = Combined), county name (`hs_ctyname`), and state abbreviation.

### FIPS Code Corrections

I identified two issues with the `hs_cty_fips` field. First, public school FIPS codes were stored as floats and needed to be zero-padded to five digits (e.g., `1555.0` → `01555`). Second, private school FIPS codes were missing entirely and needed to be constructed by concatenating the PSS state ANSI code (`PSTANSI`) and county FIPS code (`PCNTY`) fields. Both fixes were applied and the corrected column was saved in `final_data_v3.csv`.

---

## Data Quality

After completing the data enhancement pipeline, I ran a data quality check to assess coverage across the merged dataset. I calculated fill rates for every college and high school attribute column, broken down by school type. College attributes had a mismatch rate below 3% for most fields, with endowment data being the notable exception due to many colleges not reporting to NACUBO. High school attributes were mostly within 5% of full coverage for public schools. A few columns had higher missingness — `hs_title_i_status` and `hs_magnet` for public schools and `col_endow_total` for colleges — which we flagged for further investigation.

---

## Dataset Filtering

To prepare for analysis, I filtered the full network to remove rows where high schools did not have grades through 12th grade (`hs_highest_grade_offered` not in [12, 13]) and rows where all key college attributes were missing. This produced `final_data_v4_filtered.csv`, which is the primary analysis dataset. `final_data_v4.csv` is the unfiltered version.

---

## Coverage Summary Table

I built a year-by-year summary table comparing the total universe of public and private high schools in the US to the number represented in our network. Using the CCD API for public schools and PSS for private schools, I counted unique schools for each year from 2019–2023. The network captured roughly 60% of all public high schools and roughly 50% of all private high schools in each year.

---

## Network Analysis

### Degree Tables

I computed per-school degree tables for 2019 and 2023. For high schools, the in-degree represents the number of unique colleges that visited that school in a given year. For colleges, the out-degree represents the number of unique high schools visited. These tables were saved as `hs_in_degree.csv` and `college_out_degree.csv` and serve as the basis for all subsequent analysis.

### Bipartite Network Analysis

Using NetworkX, I modeled the college–high school recruiting data as a bipartite network and compared the 2019 and 2023 networks. I analyzed network-level statistics (number of nodes, edges, density), degree distributions for colleges and high schools, the top colleges by recruiting reach (out-degree), and the most-visited high schools (in-degree). I also examined which high schools were never visited versus highly visited, and how college recruiting footprints changed between 2019 and 2023. Network visualizations were produced showing the top 50 colleges and their most-visited high schools, and were saved to the `data` folder.

### Race Composition and Recruiting Visits

As part of the network analysis, I examined whether high schools with different racial compositions receive different levels of recruiting attention. This included scatter plots of in-degree against race percentage variables, majority-race category breakdowns, and quartile analyses for 2019 and 2023. Outputs were saved as PNG files in the `data` folder.

### Correlation Analysis — College Out-Degree

I computed Pearson correlations between college out-degree (`n_hs_visited`) and college-level characteristics for 2019 and 2023. Prior to correlating, categorical variables were encoded: `col_inst_control` was split into `is_public` and `is_forprofit` binary columns, and `col_inst_size` was treated as ordinal. I also computed `n_states_visited` (the number of unique HS states each college visited) and included it as an additional outcome. Correlation bar charts and scatter plots for continuous variables were produced and saved. Results were saved to `college_outdegree_corr.csv` and `college_states_visited_corr.csv`.

### Correlation Analysis — High School In-Degree

I computed Pearson correlations between high school in-degree (`n_colleges_visiting`) and all HS-level variables for 2019 and 2023. Encoding was applied to prepare categorical variables: `school_type` was converted to `is_public`, negative sentinel codes in `hs_charter` and `hs_magnet` were treated as NaN, `hs_title_i_status` was binarized to an `is_title_i` flag, and `hs_urban_centric_locale` was collapsed into `locale_city`, `locale_suburb`, `locale_town`, and `locale_rural` indicator columns. Correlation bar charts, scatter plots, and a correlation matrix heatmap were produced and saved.

### Full HS Universe for Modeling

To enable future predictive modeling, I built a complete universe of all US high schools (those with `highest_grade_offered` of 12 or 13). For public schools, I pulled the full CCD directory and enrollment by race for 2019 and 2023. For private schools, I used PSS 2019-20 for both the 2019 and 2023 snapshots, consistent with the rest of the pipeline. I then merged this universe with `hs_in_degree.csv`, assigning an in-degree of 0 to any school not present in the network. This produces `hs_universe_indegree.csv`, a dataset of positive (visited) and negative (not visited) examples suitable for training a classification model.

---

## Next Steps

The most natural next step for this project is predictive modeling. With `hs_universe_indegree.csv` providing a labeled dataset of all US high schools — both those visited and those not visited by any college in our network — the groundwork exists to train a binary classifier predicting which high schools a college is likely to recruit at. Features such as racial composition, socioeconomic indicators (Title I status, free/reduced lunch rate), urban/rural locale, school size, and geographic proximity to recruiting colleges are all available in the dataset. A logistic regression or gradient boosting model would be a reasonable starting point, and cross-validation across years (training on 2019, testing on 2023) would provide a meaningful evaluation framework.

On the data side, there are a few gaps worth addressing before modeling proceeds. First, private school economic data is sparser than public — while the PSS provides enrollment-by-race breakdowns, it lacks the economic indicators available for public schools through CCD, such as free/reduced-price lunch rates, Title I status, and student-to-teacher ratios. Exploring whether any commercial data sources or supplementary federal surveys could fill these gaps would improve equity of coverage across school types. Second, the endowment data has a fill rate of only ~37%, which limits its utility as a predictor. Cross-checking against Integrated Postsecondary Education Data System (IPEDS) finance survey data or the Department of Education's endowment reporting may yield better coverage. Finally, considering whether additional years of data could be incorporated (e.g., 2024 recruiting cycles) would strengthen the temporal analysis and help assess whether patterns observed between 2019 and 2023 are persistent trends or artifacts of COVID-era disruption.
