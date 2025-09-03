# zomato-restaurant-analytics
Cleaned and analyzed 50k+ restaurant records to identify cuisine performance, pricing distribution and high-demand neighborhoods; produced SQL reports and an interactive Tableau dashboard. Tools: Python (pandas, geopandas), SQL, Tableau.

# Zomato Pan-India Restaurant Review & Business Analytics

**One-line:** End-to-end analytics on Zomato’s Pan-India dataset: cleaning, enrichment, SQL reporting, geospatial and categorical analysis, and interactive dashboards to surface cuisine performance, pricing, and locality demand.

---

## Table of contents
1. [Project overview](#project-overview)  
2. [Why this project & audience](#why-this-project--audience)  
3. [Datasets & source](#datasets--source)  
4. [Key questions & scope](#key-questions--scope)  
5. [Tech stack](#tech-stack)  
6. [Repository structure](#repository-structure)  
7. [Data cleaning & preprocessing (summary)](#data-cleaning--preprocessing-summary)  
8. [Exploratory data analysis & visualizations](#exploratory-data-analysis--visualizations)  
9. [SQL analyses & sample queries](#sql-analyses--sample-queries)  
10. [Tableau dashboard (how to open & reproduce)](#tableau-dashboard-how-to-open--reproduce)  
11. [Findings & business insights](#findings--business-insights)  
12. [How to reproduce (run locally or Colab)](#how-to-reproduce-run-locally-or-colab)  
13. [Resume bullets & LinkedIn copy](#resume-bullets--linkedin-copy)  
14. [Collaboration & next steps](#collaboration--next-steps)  
15. [License & contact](#license--contact)

---

## Project overview
This repository provides a full, reproducible analytics pipeline for Zomato’s Pan-India restaurant dataset. The goal is to transform raw restaurant metadata into clean, analysis-ready tables and produce actionable insights for stakeholders — restaurant owners, food aggregators, local brokers, market researchers, and recruiters — using robust, production-minded data work:

- Clean and standardize data (ratings, costs, categories, addresses)  
- Run SQL & pandas analytics for business KPIs (avg rating by area, cuisine performance, pricing)  
- Create geospatial & categorical visualizations (boxplots, distribution charts, maps)  
- Build an interactive Tableau dashboard showcasing top insights  
- Package results and methodology clearly for reproducibility and hiring interviews

---

## Why this project & audience
Short-term reasons:
- The online food ecosystem in India is rapidly growing. Aggregators, restaurants, and investors need data-driven insights to make pricing, expansion, and marketing decisions.
- The dataset covers multiple cities, allowing cross-city benchmarking and local market profiling.

Audience:
- **Restaurant owners and managers** — to understand local price positioning & customer sentiment (where available).  
- **Food aggregators / brokers** — to identify high-supply vs high-demand neighborhoods and benchmark partner performance.  
- **Recruiters / hiring managers** — to see clear technical skills (data cleaning, SQL, pandas, geospatial analysis, Tableau).  
- **Urban researchers / consultants** — to study food geography and consumer behavior.

---

## Datasets & source
- **Primary data**: Zomato Pan-India dataset (Kaggle) — restaurant metadata including `restaurant_name`, `area`, `local_address`, `cuisines_type`, `avg_cost_two_people`, `rate_out_of_5`, `num_of_ratings`, `online_order`, `table_booking`.
- **Derived files** included in this repo (after cleaning): `data/cleaned_zomato.csv`.
> Note: raw data is not included due to license/size — download instructions below.

---

## Key questions & scope
- Which **areas** (localities) host the highest number of restaurants (density)?  
- Which **cuisines** have the best customer ratings and which are most widely available?  
- What is the **price distribution** across neighborhoods (affordable vs premium areas)?  
- Do restaurants with **online order** capabilities have different rating patterns?  
- Identify **top candidate restaurants** by rating & review counts for potential partnerships or case studies.

Scope restrictions:
- This project focuses on structured metadata (no line-by-line customer reviews text in this dataset).
- Sentiment analysis is not included here since raw textual reviews are not part of the selected Pan-India dataset variant. The pipeline supports adding review text if available.

---

## Tech stack
- **Python** (pandas, numpy, geopandas optional, matplotlib/seaborn, plotly)  
- **SQLite** for SQL-style queries in the notebook (or PostgreSQL for production)  
- **Tableau Public** for interactive dashboards & heatmaps  
- **Google Colab** (recommended for reproducibility)  
- **Git & GitHub** for version control and publishing

---

## Repository structure

zomato-panindia-restaurant-analytics/
├── notebooks/
│   └── zomato_analysis.ipynb       # Colab-ready notebook: cleaning→EDA→SQL→visuals
├── data/
│   └── cleaned_zomato.csv          # Cleaned dataset for analysis & Tableau
├── visuals/
│   ├── boxplot_cost_by_area.png
│   ├── top_cuisines_bar.png
│   └── online_order_pie.png
├── tableau/
│   └── zomato_dashboard.twbx  

│  
├── README.md
└── LICENSE

---

## Data cleaning & preprocessing (summary)
Cleaning is critical for Tableau and analytics. The notebook performs the following steps (executed in `notebooks/zomato_analysis.ipynb`):

1. **Normalize column names** → `snake_case` (e.g., `restaurant_name`, `rate_out_of_5`, `avg_cost_two_people`).  
2. **Drop duplicates** and irrelevant columns.  
3. **Clean numeric fields**:
   - `rate_out_of_5`: extract numeric (e.g., "4.1/5" → `4.1`), convert to float, constrain to [0,5], fill missing with mean.  
   - `avg_cost_two_people`: strip non-digit chars (₹, commas), convert to int, fill missing with median.  
   - `num_of_ratings`: to integer, fill missing = 0.  
4. **Standardize categorical fields**:
   - `online_order` and `table_booking` → canonical `Yes`/`No`.  
   - `cuisines_type` → take primary cuisine (first listed) and title-case.  
   - `area`, `local_address` → cleaned, title-case.  
5. **Export** cleaned CSV: `data/cleaned_zomato.csv` for Tableau & reporting.

---

## Exploratory data analysis & visualizations
Key visuals included in `/visuals` and demonstrated in the notebook:

1. **Top 10 Cuisines (bar chart)** — frequency of restaurants by cuisine.  
2. **Distribution of Ratings (histogram + KDE)** — overall quality distribution.  
3. **Boxplot: Avg cost for two by Area (top areas)** — shows cost spread and outliers (great for comparing affordability vs luxury).  
4. **Top 10 Areas by Restaurant Count (bar)** — food hub density visualization.  
5. **Pie/Donut Chart: Online Order Support** — delivery penetration.  
6. **Optional Map**: If coordinates are available / geocoded, map restaurants colored by average rating or price.

All visuals are exported as PNGs to `/visuals` for display on GitHub and LinkedIn posts.

---

## SQL analyses & sample queries
The notebook pushes the cleaned DataFrame to an in-memory SQLite DB and runs these sample queries (also stored in `sql/sample_queries.sql`):

- Top cuisines by count:
```sql
SELECT cuisines_type, COUNT(*) AS restaurant_count
FROM zomato_cleaned
GROUP BY cuisines_type
ORDER BY restaurant_count DESC
LIMIT 20;


	•	Average rating by area:

SELECT area, ROUND(AVG(rate_out_of_5),2) AS avg_rating, COUNT(*) AS restaurants
FROM zomato_cleaned
GROUP BY area
HAVING restaurants >= 10
ORDER BY avg_rating DESC;

	•	Average cost by cuisine:

SELECT cuisines_type, ROUND(AVG(avg_cost_two_people),0) AS avg_cost
FROM zomato_cleaned
GROUP BY cuisines_type
ORDER BY avg_cost DESC;

	•	Online order vs average rating:
SELECT online_order, COUNT(*) AS count_restaurants, ROUND(AVG(rate_out_of_5),2) AS avg_rating
FROM zomato_cleaned
GROUP BY online_order;

##These queries are translated into pandas equivalents inside the notebook for convenience and reproducibility
