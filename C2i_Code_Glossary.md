# C2i Code and Dataset Glossary

All notebooks are located in `src/`. All datasets are located in `data/`.

| File | Description |
| :--- | :--- |
| `geo.ipynb` | Merges college and private HS geo-location CSVs onto the raw network, pulls public HS geo from the CCD directory API, creates the unified `hs_id` and `school_type` columns, and saves the initial merged dataset. |
| `standardize_hs.ipynb` | Standardizes HS columns so public and private schools share a common schema — fills private school geographic fields from PSS 2019-20 and pulls CCD school names for public schools into a unified `hs_name` column. |
| `pull_hs_race_data.ipynb` | Pulls public HS enrollment by race from the CCD enrollment API (35+ requests across years and race codes), computes percentage-by-race columns, and merges them onto the network. |
| `hs_economic.ipynb` | Pulls public HS economic attributes from the CCD directory API (free/reduced-price lunch counts, Title I status, charter/magnet designation, FTE teachers), derives `hs_pct_free_or_reduced_price_lunch` and `hs_students_per_teacher`, and merges onto the network. |
| `add_college_cols.ipynb` | Pulls IPEDS admissions/enrollment data (2019–2022, forward-filled to 2023) and IPEDS tuition data (2019–2021, forward-filled), derives acceptance and enrollment rates, and merges all admissions and tuition columns onto the network. |
| `fill_endowment_2023.ipynb` | Forward-fills missing 2023 endowment values in `college_out_degree.csv` using 2022 NACUBO data, the last available year from the NACUBO endpoint. |
| `private_school.ipynb` | Merges PSS 2019-20 and PSS 2021-22 attributes (school name, level, county, state) onto private school rows in the network by `ppin`. |
| `fix_fips_codes.ipynb` | Corrects `hs_cty_fips` for public schools (zero-pads 4-digit codes to 5 digits) and looks up FIPS for private schools by concatenating PSS state and county FIPS fields. |
| `data_quality.ipynb` | Computes fill rates for all college and public HS attribute columns across the merged dataset and identifies colleges with no matching college attribute data. |
| `hs_total_vs_visited.ipynb` | Builds a year-by-year table (2019–2023) comparing the total US public and private HS universe to the number of each in our network, using the CCD API for public counts and PSS for private counts. |
| `network_degree_tables.ipynb` | Filters the full dataset to HS grades only, builds per-school degree tables (HS in-degree and college out-degree) for 2019 and 2023, and saves `final_data_v4_filtered.csv`, `hs_in_degree.csv`, and `college_out_degree.csv`. |
| `network_analysis.ipynb` | Models the college–HS recruiting data as a bipartite network using NetworkX, analyzes network statistics, degree distributions, top recruiters and most-visited schools, change in recruiting reach 2019→2023, and the relationship between HS racial composition and recruiting visits. |
| `college_outdegree_correlations.ipynb` | Computes Pearson correlations between college out-degree (and `n_states_visited`) and college-level characteristics for 2019 and 2023, encodes categorical variables, and produces correlation bar charts and scatter plots. |
| `hs_indegree_correlations.ipynb` | Computes Pearson correlations between HS in-degree and all HS-level variables for 2019 and 2023, encodes categorical and ordinal variables, and produces correlation bar charts, scatter plots, and a heatmap. |
| `hs_universe_indegree.ipynb` | Builds a complete universe of all US high schools (grades through 12th) from CCD and PSS, merges with `hs_in_degree.csv`, and assigns in-degree = 0 to unvisited schools, creating a labeled dataset for future modeling. |

---

## Dataset Glossary

### Provided Input Files

| File | Description |
| :--- | :--- |
| `new_data.csv` | Raw input network data containing one row per college–HS visit, with columns for college ID, public HS ID (`ncessch`), private HS ID (`ppin`), and year. |
| `College_geo.csv` | Provided geo-location attributes for colleges, including name, city, state, zip, type, county, shape area, and shape length. |
| `Private_Schools_geo.csv` | Provided geo-location attributes for private high schools, including city, state, zip, county, FIPS, latitude, and longitude. |
| `Public_Schools_geo.csv` | Provided geo-location attributes for public high schools, including city, state, zip, county, FIPS, latitude, and longitude. |
| `pss_2019-20.csv` | Full NCES Private School Universe Survey (PSS) 2019-20 file, downloaded from NCES; used to obtain private school attributes for all cycles. |
| `pss_2021-22.csv` | Full NCES Private School Universe Survey (PSS) 2021-22 file, downloaded from NCES; retained for reference but not used in the main pipeline due to high missingness when joined to our network. |

### Intermediate Pipeline Files

| File | Description |
| :--- | :--- |
| `collegenetwork_fulldata.csv` | Early version of the raw network with an added `hs_type` column distinguishing public and private schools. |
| `merged_geo.csv` | Network dataset after merging college geo, private HS geo, and public HS geo attributes; first file with the `hs_` and `col_` column naming convention. |
| `hs_econ.csv` | CCD economic attributes for public high schools (Title I, charter, magnet, enrollment, free/reduced lunch, teachers) pulled from the Education Data API, before merging onto the network. |
| `hs_race.csv` | Public HS enrollment percentages by race (7 race categories), by school and year, pulled from the CCD enrollment API. |
| `merge_hs_econ.csv` | Network dataset after merging HS economic and race attributes onto `merged_geo.csv`. |
| `updated_data.csv` | Intermediate network snapshot after adding college institutional and endowment attributes. |
| `final_data.csv` | First consolidated version of the fully merged network, before HS column standardization. |
| `final_data_v2.csv` | Network after HS column standardization — public and private schools share a unified schema with `hs_name`, `hs_state`, etc. |
| `final_data_v3.csv` | Network after FIPS code corrections for both public and private schools. |
| `final_data_v4.csv` | Full merged network with all attributes added (admissions, tuition, endowment, private school PSS data); unfiltered. |

### Analysis-Ready Files

| File | Description |
| :--- | :--- |
| `final_data_v4_filtered.csv` | Primary analysis dataset — `final_data_v4.csv` filtered to rows where the high school has grades through 12th and at least some college attributes are present. |
| `hs_in_degree.csv` | Per-high-school in-degree table for 2019 and 2023, containing each visited school's count of unique recruiting colleges along with all HS attributes. |
| `college_out_degree.csv` | Per-college out-degree table for 2019 and 2023, containing each college's count of unique high schools visited along with all college attributes. |
| `college_outdegree_top20.csv` | Subset of `college_out_degree.csv` limited to the top 20 colleges by recruiting reach for 2019 and 2023. |
| `hs_total_vs_visited.csv` | Year-by-year table (2019–2023) comparing the total count of US public and private high schools to the number captured in our network. |
| `hs_universe_indegree.csv` | Full universe of all US high schools with in-degree values — visited schools carry their count, unvisited schools are assigned 0; intended as a labeled dataset for predictive modeling. |
| `college_outdegree_corr.csv` | Pearson correlations between college out-degree (`n_hs_visited`) and college-level characteristics for 2019 and 2023. |
| `college_states_visited_corr.csv` | Pearson correlations between number of states visited (`n_states_visited`) and college-level characteristics for 2019 and 2023. |
| `hs_indegree_corr.csv` | Pearson correlations between HS in-degree (`n_colleges_visiting`) and all HS-level variables for 2019 and 2023. |
| `hs_indegree_corr_2019.csv` | Encoded HS-level dataset used as input for the 2019 in-degree correlation analysis. |
| `hs_indegree_corr_2023.csv` | Encoded HS-level dataset used as input for the 2023 in-degree correlation analysis. |
