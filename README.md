# COVID-19 Global Overview Dashboard

## Executive Summary

The COVID-19 Global Overview Dashboard is an interactive Power BI project developed to analyse global COVID-19 trends using historical data from Johns Hopkins University (JHU).

The project transforms raw, cumulative COVID-19 datasets into meaningful insights through data cleaning, transformation, data modelling and DAX calculations.

The dashboard provides an overview of:
- Global confirmed cases, recoveries and deaths.
- Countries with the highest reported confirmed cases.
- COVID-19 trends over time.
- Recovery and death rates.
- Monthly growth patterns.

**Key Highlight:** During data preparation, I identified and resolved a many-to-many merge issue that significantly inflated the reported totals. By refining the merge keys and implementing appropriate DAX measures, I improved the accuracy and reliability of the dashboard.

This project demonstrates practical skills in Power BI, Power Query, DAX, data cleaning, data modelling and data visualization.

*Note: The dashboard uses historical data covering January 2020 to March 2023 and does not represent current COVID-19 statistics.*


## 📌 Project Overview

> **Note:** This is a historical analysis of the COVID-19 pandemic, not a live tracking dashboard. Recovery data availability is limited because Johns Hopkins CSSE discontinued its global recovery time-series reporting in mid-2021.
> 
![Dashboard Preview](screenshots/Covid-19%20Dashboard.png)
## 📊 Dataset

- **Source:** [Johns Hopkins CSSE COVID-19 Data Repository](https://github.com/CSSEGISandData/COVID-19)
- **Files used:** `time_series_covid19_confirmed_global.csv`, `time_series_covid19_deaths_global.csv`, `time_series_covid19_recovered_global.csv`
- **Format:** Daily cumulative case counts by country/province, in wide format (one column per date)
- **Timeframe:** January 2020 onward (Recovered data reporting ends mid-2021, per JHU's own discontinuation of that file)

## 🎯 Business Questions Answered

- What is the global scale of confirmed cases, deaths, and recoveries?
- Which countries have been most affected by total case count?
- How did case growth trend and wave over time?
- What is the relationship between recovery and death outcomes?
- How did month-over-month growth rate change across the pandemic?

## 🧮 Key KPIs

| KPI | Description |
|---|---|
| Total Confirmed (Latest) | Latest known cumulative confirmed case count |
| Total Deaths (Latest) | Latest known cumulative death count |
| Total Recovered (Latest) | Latest known cumulative recovery count |
| Death Rate | Total Deaths ÷ Total Confirmed |
| Recovery Rate | Total Recovered ÷ Total Confirmed |
| Daily/Monthly Growth Rate | Period-over-period change in confirmed cases |
| New Confirmed / Deaths / Recovered | Daily new counts, derived from cumulative deltas |

## 📈 Visuals

- **Global Case Trend** — cumulative and new daily case trend line
- **Total Confirmed by Country** — bar chart, top 25 countries
- **Recovery vs. Death Analysis** — donut chart
- **Monthly Growth Rate** — bar chart by month
- **Country-Level Detail Table** — sortable table with per-country totals
- **Slicers** — Country/Region, Month

## 🛠️ Tools Used

- Microsoft Power BI (Power Query + DAX)
- Data source: JHU CSSE GitHub repository

## 🧠 Data Modeling Notes

The raw JHU files are in **wide format** (one column per date) and had to be:
1. Unpivoted in Power Query to a long format (`Country/Region`, `Date`, `Value`)
2. Renamed per metric (`Confirmed`, `Deaths`, `Recovered`)
3. Merged into a single master table (`COVID_Glob`), matched on **three keys** — `Country/Region`, `Province/State`, and `Date` — since matching on only two keys caused a many-to-many join for countries with multiple province-level rows (e.g. UK, France, China), inflating totals into the billions
4. A custom Date Table was built and marked for time-intelligence functions

### Key DAX pattern used
Because the source data is **cumulative**, a simple `SUM()` across all dates massively over-counts. All headline totals instead use a "latest known value" pattern:

```DAX
Total Confirmed (Latest) = 
CALCULATE(
    SUM(COVID_Glob[Confirmed]),
    LASTDATE(DateTable[Date])
)
```

For `Total Recovered`, since JHU stopped updating that file before Confirmed/Deaths, the "latest" date had to be found separately:

```DAX
Total Recovered (Latest) = 
VAR LastRecoveredDate =
    CALCULATE(
        MAX(COVID_Glob[Date]),
        COVID_Glob[Recovered] > 0
    )
RETURN
    CALCULATE(
        SUM(COVID_Glob[Recovered]),
        COVID_Glob[Date] = LastRecoveredDate
    )
```

## 🔑 Key Insights

- The US, India, and Brazil consistently led in total confirmed cases throughout the pandemic
- Global case growth showed clear wave patterns rather than a steady climb, with visible spikes and slowdowns
- Recovery reporting became sparse globally after mid-2021 — a real limitation of the JHU dataset, not a data-cleaning error

## ⚠️ Known Data Limitations

- JHU discontinued the Recovered time-series file in mid-2021, so global recovery figures understate true recoveries post-2021
- Some countries revised prior-day cumulative figures downward on rare occasions, which can produce a small negative value in daily "new case" calculations — expected, not a bug

## 🚧 Challenges Faced

- **Date parsing errors** — resolved with locale-specific date conversion in Power Query
- **DAX syntax errors from combining multiple formulas in one entry box** — resolved by adding measures/columns one at a time
- **Chronological sorting of month labels** — fixed using Sort by Column against numeric month fields
- **Merge inflating totals into the billions** — root-caused to a two-key merge causing many-to-many joins on province-level countries; fixed by merging on three keys (Country/Region, Province/State, Date)

## 📁 Files in This Repo

- `Covid dashboard 2.pbix` — Power BI dashboard file
- `Covid-19 clean dataset.pbix` — Power BI project file
- `screenshots/Covid-19 Dashboard.png` — Dashboard preview image
- `confirmed_global.xlsx` — Confirmed cases dataset
- `deaths_global.xlsx` — Deaths dataset
- `recovered_global.xlsx` — Recovered cases dataset

## 👤 Author

Built by Abiola as part of the AnalystLab Africa Data Analytics internship program.
