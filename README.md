Exploratory Analysis of Google Play Store Apps (R)
Data Analytics Lab – CA3 Mini Project.
An end-to-end exploratory data analysis in R of the Kaggle Google Play Store dataset (10,841 rows × 13 columns). A single script cleans the raw data, engineers features, computes descriptive and correlation statistics, draws 16 plots, and exports the results to CSV, TXT and Excel.
Project structure
```
PlayStore/
├── project.R                  # Full analysis pipeline (run this)
├── PlayStore.Rproj            # RStudio project file
├── googleplaystore.csv        # Raw dataset (input)
├── plots/                     # 16 generated PNG charts
└── output/
    ├── playstore_clean.csv    # Cleaned dataset (9,639 apps)
    ├── descriptive_stats.txt  # Descriptive statistics table
    └── analysis_results.xlsx  # Multi-sheet workbook of results
```
Requirements
R 4.x (RStudio recommended)
Packages: `ggplot2`, `dplyr`, `writexl`, `readxl`, `scales` (`rstudioapi` is optional)
```r
install.packages(c("ggplot2", "dplyr", "writexl", "readxl", "scales"))
```
How to run
Open `PlayStore.Rproj` in RStudio (or set the working directory to the `PlayStore` folder).
Make sure `googleplaystore.csv` is in that folder (or in a `data/` subfolder).
Run `project.R` (`source("project.R")`). The script creates `plots/` and `output/` if needed and prints progress for each section.
Dataset
Columns (renamed in the script): `app, category, rating, reviews, size, installs, type, price, content_rating, genres, last_updated, current_ver, android_ver`.
What the script does
Section	Description
1. Setup & load	Loads the CSV, inspects structure, missing values, duplicates; draws a raw missing-value map
2. Cleaning	Removes one malformed row (shifted columns), converts blanks to `NA`, drops exact duplicates and repeated app names (keeps the most-reviewed copy), converts `installs`/`price`/`size` (to MB) to numeric, parses dates, imputes `type`, `content_rating` and `size` (median copy), and removes novelty apps priced above $100
2B. Outliers	Flags review-count outliers per category with the IQR rule (flagged, not removed)
3. Transformation	`log10` transforms, z-score and min-max scaling, install tiers, rating groups, 7 broad category groups, size bins, rating matrix (category group × type)
4. EDA	Descriptive statistics, Pearson/Spearman correlations, category rankings, group comparisons (type, content rating, size, update recency)
5–7. Visualisation	Base R and `ggplot2` charts: histograms, boxplots, scatter plots, bar charts, line chart, heatmap, violin, facets, pairs matrix
6. Export	Writes the clean CSV, descriptive stats and a 5-sheet Excel workbook, then re-reads them to verify
Design choice: `rating` is not imputed. A missing rating means the app has too few reviews, so a filled-in value would be invented. Rating analysis uses rated apps only (8,181 apps).
Outputs
`output/playstore_clean.csv` and the `clean_data` sheet contain, per app: category, category group, type, content rating, rating and rating group, reviews, installs and install tier, price, size (MB) and size bin, days since update, review rate, log-transformed values and the outlier flag.
`output/analysis_results.xlsx` sheets: `clean_data`, `descriptive`, `correlation`, `category_stats`, `group_x_type`.
Key findings
Cleaning: 10,841 raw rows reduced to 9,639 apps (about 11% removed), spanning 33 categories.
Free vs paid: 92.4% free, 7.6% paid. Median installs are 100,000 (free) vs 1,000 (paid); mean rating is 4.17 (free) vs 4.27 (paid).
Install tiers: Low (<10K) 3,132 · Medium 3,109 · High (1M–100M) 3,165 · Mega (100M+) 233.
Ratings: Median rating is 4.3. 1,458 apps are unrated, and only 277 are rated "Poor" (<3.0).
Largest categories: Family (1,870), Game (946), Tools (829), Business (420), Medical (394).
Rating by category: Events (4.44), Art & Design (4.36) and Education (4.35) rank highest; Dating (3.98), Maps & Navigation (4.04) and Tools (4.04) rank lowest.
Correlations: `log10(reviews)` and `log10(installs)` are almost perfectly related (r ≈ 0.96). Rating has only a weak link to popularity (r ≈ 0.08 with installs) and a small negative link to days since update (r ≈ −0.13).
Outliers: 8 apps flagged as review-count outliers within their category, all retained.
Plots
All charts are saved in `plots/`:
#	File	Chart
01	`01_missing_raw.png`	Missing values in raw data
02	`02_hist_rating_installs.png`	Histograms of rating and log installs
03	`03_boxplot_rating_tier.png`	Rating by install tier
04	`04_scatter_reviews_installs.png`	Reviews vs installs (base R)
05	`05_gg_scatter_reviews_installs.png`	Reviews vs installs by type
06	`06_gg_bar_categories.png`	Top 10 categories by app count
07	`07_gg_stacked_tier_group.png`	Install tier mix by category group
08	`08_gg_grouped_group_type.png`	Median installs by group and type
09	`09_gg_category_rating.png`	Highest vs lowest rated categories
10	`10_gg_line_sizebin.png`	Median installs across size bands
11	`11_gg_box_type.png`	Log installs by type
12	`12_gg_corr_heatmap.png`	Correlation heatmap
13	`13_pairs_matrix.png`	Scatterplot matrix (sample of 2,000)
14	`14_gg_hist_density.png`	Histogram with density curve
15	`15_gg_violin_type.png`	Rating distribution by type
16	`16_gg_facet_group.png`	Rating vs reviews by category group
Notes
`install` counts in the source data are lower-bound bins (e.g. "1,000,000+"), so `review_rate` (reviews per 1,000 installs) is an approximation.
`size_mb` is missing for apps listed as "Varies with device"; `size_mb_imp` (median-filled) is used only for z-score standardisation.
The dataset snapshot date is the latest `last_updated` value in the data, and `days_since_update` is measured against it.
`.RData`, `.Rhistory` and `.Rproj.user/` are local RStudio files and can be ignored or added to `.gitignore`.
Dataset source
Google Play Store Apps, Kaggle (`googleplaystore.csv`).
