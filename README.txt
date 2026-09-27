💶 Macroeconomic Analysis: Eurostat Short-Term Tourism vs. Housing Rent IndexAn end-to-end data engineering and business intelligence portfolio project analyzing the correlation 
between short-term rental tourism activity and residential housing rent inflation across European markets, with dedicated focus on Spain (ES) and Italy (IT).📌 Executive SummaryThis 
project investigates whether fluctuations in short-term tourism nights (driven by platforms like Airbnb, Booking.com, and local accommodations) exert upward pressure on residential 
actual housing rents.By pulling raw time-series data directly from Eurostat APIs, cleaning and de-seasonalizing metrics in Python, and modeling the data in Power BI using a Star Schema, 
this project evaluates:Pre-2020 Baseline: Steady-state historical relationship between tourism and rent inflation.2020–2021 Pandemic Shock: The decoupling event where tourism nights 
collapsed by up to $-100\%$, while residential rents maintained sticky upwards momentum.Post-2021 Recovery: The return to seasonal equilibrium and the persistent trajectory of actual 
housing costs.📂 Repository StructurePlaintexthousing-tourism-analysis/
│
├── .gitignore
├── LICENSE
├── README.md
│
├── data/
│   └── processed/
│       ├── cleaned_rent_data.csv          # HICP actual rent indices by geo/date
│       └── cleaned_tourism_data.csv         # Tourist nights spent by geo/date
│
├── notebooks/
│   └── eurostat_etl_analysis.ipynb        # Cleaned & refactored Python ETL notebook
│
├── power_bi/
│   └── tourism_vs_rent_dashboard.pbix     # Interactive Power BI report
│
└── images/
    └── dashboard_overview.png             # Visual previews for documentation
🛠️ Data Pipeline & Architecture[ Eurostat SDMX API ] 
         │
         ▼
[ Python ETL Notebook ] ──► (Pandas / Refactoring / De-seasonalizing)
         │
         ▼
[ Processed CSV Layer ] ──► (data/processed/)
         │
         ▼
[ Power BI Data Model ] ──► Star Schema (Calendar, Dim_Geo ──< Fact Tables)
         │
         ▼
[ Interactive Dashboard ] ──► YoY Calculations, Scatter Plots, Trendlines & Slicers

1. Data Ingestion & Cleaning (notebooks/)Eurostat Endpoints:Tourism Nights: tour_ce_omd / tour_occ_nimActual Rent Index: prc_hicp_midx (COICOP code CP0411)Implemented defensive 
parsing with pd.to_numeric(..., errors='coerce') to clean Eurostat confidentiality flags (: c, : z).Standardized date formatting into unified YearMonth strings (YYYY-MM) and Date 
types.2. Data Modeling (power_bi/)Star Schema Design:Fact Tables: cleaned_rent_data, cleaned_tourism_dataDimension Tables: Calendar (generated via CALENDARAUTO()), Dim_Geo 
(lookup table mapping 43+ Eurostat 2-letter codes to full country names via DAX SWITCH()).Key DAX Measures:YoY Rent Growth (%):Fragment koduRent_Index_YoY_% = 
VAR CurrentValue = AVERAGE(cleaned_rent_data[rent_index])
VAR PriorYearValue = CALCULATE(AVERAGE(cleaned_rent_data[rent_index]), SAMEPERIODLASTYEAR('Calendar'[Date]))
RETURN DIVIDE(CurrentValue - PriorYearValue, PriorYearValue, 0) * 100
Pandemic Shock Legend Categorization:Fragment koduPeriod_Category = 
SWITCH(
    TRUE(),
    'Calendar'[Year] < 2020, "Pre-2020 (Baseline)",
    'Calendar'[Year] IN {2020, 2021}, "2020-2021 (Pandemic Shock)",
    'Calendar'[Year] >= 2022, "Post-2021 (Recovery)",
    "Other"
)

📊 Key FindingsSticky Rent Dynamics: While tourism nights dropped sharply during the 2020–2021 lockdowns, housing rent indices exhibited downward rigidity, continuing a positive 
trajectory across key markets like Spain and Italy. De-seasonalized Correlation: The trendline on YoY tourism vs. YoY rent growth remains virtually flat, proving that short-term 
seasonal tourism surges are not the sole macro driver of long-term rental market escalation.
Key Insight: These findings suggest that tourism is unlikely to be the primary macro driver of rising rent prices in Spain and Italy. While localized tourism surges may exert 
upward pressure on specific neighborhood rental markets, seasonal travel alone cannot account for the broader national rent escalation. Instead, long-term rental market trends 
are predominantly driven by structural supply-demand imbalances, such as population growth fueled by net migration and a constrained supply of long-term rental housing stock.

🚀 How to Run LocallyPrerequisitesPython 3.10+Power BI Desktop (Latest Version)1. Clone 
the RepositoryBashgit clone https://github.com/YOUR_GITHUB_USERNAME/housing-tourism-analysis.git
cd housing-tourism-analysis

2. Run Python ETL NotebookBashpip install pandas matplotlib eurostatapiclient
jupyter notebook notebooks/eurostat_etl_analysis.ipynb

3. Open Power BI DashboardOpen power_bi/tourism_vs_rent_dashboard.pbix in Power BI Desktop.If prompted for data source location, point the files to data/processed/cleaned_rent_data.csv 
and data/processed/cleaned_tourism_data.csv.📜 License & Data Source AttributionCode License: Distributed under the MIT License. See LICENSE for details.Data Source & Reuse Policy:Source: 
Eurostat (European Commission).Datasets: tour_ce_omd, tour_occ_nim, prc_hicp_midx.Reused under the European Commission's official open data policy (Commission Decision 2011/833/EU).
