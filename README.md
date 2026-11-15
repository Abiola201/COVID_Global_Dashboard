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

This project focuses on analysing the global spread and impact of COVID-19 using historical data from the Johns Hopkins University (JHU) CSSE repository.

The objective is to transform raw COVID-19 datasets into an interactive Power BI dashboard that provides meaningful insights into global confirmed cases, deaths, recoveries and trends over time.

### Project Objectives

The dashboard was designed to answer the following questions:

1. What is the global scale of confirmed COVID-19 cases, deaths and recoveries?
2. Which countries recorded the highest number of confirmed cases?
3. How did reported COVID-19 cases change over time?
4. What is the distribution of recorded recoveries and deaths?
5. How did monthly growth patterns change throughout the pandemic?

### Target Audience

The dashboard is designed for individuals, researchers, managers and other stakeholders interested in understanding historical global COVID-19 trends.

### Tools and Technologies

- Power BI Desktop – Dashboard development and visualization.
- Power Query – Data cleaning and transformation.
- DAX – Measures and analytical calculations.
- Microsoft Excel – Dataset preparation and inspection.
> 
![Dashboard Preview](screenshots/Covid-19%20Dashboard.png)
## Data Sources

The analysis is based on historical COVID-19 data obtained from the **Johns Hopkins University Center for Systems Science and Engineering (JHU CSSE)**.

**Original Data Repository:** [Johns Hopkins CSSE COVID-19 Data Repository](https://github.com/CSSEGISandData/COVID-19)

### Dataset Description

Three datasets were used for this project:

| Dataset | Description |
|---|---|
| Confirmed Cases | Records of confirmed COVID-19 cases across countries and regions. |
| Deaths | Records of reported COVID-19 deaths. |
| Recoveries | Records of reported COVID-19 recoveries. |

### Data Characteristics

- **Data Format:** CSV files, converted into Excel format for preparation.
- **Geographic Coverage:** Countries and provinces/regions worldwide.
- **Time Coverage:** 22 January 2020 – 9 March 2023.
- **Data Structure:** Daily cumulative COVID-19 records.
- **Data Type:** Historical data; not automatically updated.

### Important Data Note

The recovery dataset contains records only up to 4 August 2021 because Johns Hopkins discontinued recovery reporting. Therefore, recovery figures should be interpreted with this limitation in mind.

The dashboard represents historical reported statistics and should not be interpreted as current COVID-19 figures.

## Data Cleaning & Transformation

Data preparation was carried out using **Power Query in Power BI** to ensure the three datasets were properly structured, consistent and ready for analysis.

### Data Preparation Steps

1. **Data Import:** Imported the confirmed cases, deaths and recoveries datasets into Power BI.

2. **Data Restructuring:** Used the Unpivot Columns feature to transform the datasets from wide format into long format, making the daily records easier to analyse.

3. **Data Cleaning:** Removed duplicates, trimmed unnecessary spaces, cleaned text fields and standardized country names.

4. **Date Transformation:** Applied locale-specific date conversion to resolve date parsing inconsistencies.

5. **Data Integration:** Merged the three datasets into a single table named `COVID_Glob` using three matching keys:
   - Country/Region
   - Province/State
   - Date

6. **Date Table Creation:** Created a custom date table to support time-based calculations and trend analysis.

### Data Integration Challenge

During the initial merging process, I encountered inflated totals running into billions due to a many-to-many relationship.

The issue occurred because the datasets contained multiple provincial records for certain countries, and the initial merge used only Country/Region and Date.

**Solution:** I introduced Province/State as a third matching key, ensuring that records were correctly matched across all three datasets.

This resolved the duplication issue and improved the accuracy of the final dataset.

### Outcome

The cleaning and transformation process produced a consolidated dataset suitable for DAX calculations, time-based analysis and interactive Power BI visualizations.

## 🧠 Data Modeling Notes

The raw JHU files are in **wide format** (one column per date) and had to be:
1. Unpivoted in Power Query to a long format (`Country/Region`, `Date`, `Value`)
2. Renamed per metric (`Confirmed`, `Deaths`, `Recovered`)
3. Merged into a single master table (`COVID_Glob`), matched on **three keys** — `Country/Region`, `Province/State`, and `Date` — since matching on only two keys caused a many-to-many join for countries with multiple province-level rows (e.g. UK, France, China), inflating totals into the billions
4. A custom Date Table was built and marked for time-intelligence functions

### Key DAX Patterns Used
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

## Analysis and Visualizations

The Power BI dashboard combines different visual elements to present global COVID-19 statistics in an interactive and easy-to-understand format.

### Dashboard Components

| Visualization | Purpose |
|---|---|
| KPI Cards | Display global confirmed cases, recoveries, deaths and death rate. |
| Recovered vs Deaths | Compares recorded recoveries and deaths. |
| Monthly Growth Rate | Highlights changes in reported cases across months. |
| Global Case Trend | Shows how reported COVID-19 cases evolved over time. |
| Top 25 Countries Table | Provides country-level details of confirmed cases, deaths and recoveries. |
| Confirmed Cases by Country | Identifies countries with the highest reported confirmed cases. |
| Interactive Slicers | Allow users to filter the dashboard by country and month. |

### Analytical Measures

The dashboard uses DAX measures to support its analysis, including:

- Total Confirmed Cases (Latest)
- Total Deaths (Latest)
- Total Recoveries (Latest)
- Death Rate
- Recovery Rate
- Daily Growth Rate
- Monthly Growth Rate
- New Confirmed Cases
- New Deaths
- New Recoveries

These measures help transform historical cumulative records into meaningful analytical insights while supporting interactive exploration of the data.

## Key Findings

The analysis revealed several important insights from the historical global COVID-19 dataset.

### 1. Global COVID-19 Statistics
- Approximately **677 million confirmed cases** were recorded.
- Recorded recoveries reached approximately **129 million**.
- Reported deaths totalled approximately **7 million**.
- The calculated death rate was approximately **1.02%** of confirmed cases.

### 2. Countries with the Highest Confirmed Cases

The United States recorded the highest number of confirmed cases, followed by India, France, Germany and Brazil.

| Country | Confirmed Cases (Approx.) |
|---|---:|
| United States | 104M |
| India | 45M |
| France | 40M |
| Germany | 38M |
| Brazil | 37M |

### 3. Concentration of Reported Cases

The United States accounted for approximately 15% of global confirmed cases, while the five countries with the highest reported cases accounted for roughly 39%.

### 4. Recovery Data Limitations

The recovery dataset ended on 4 August 2021, while confirmed cases and deaths continued through March 2023. This creates a reporting gap that affects recovery comparisons.

### Overall Insight

The analysis highlights the scale of the global pandemic, differences in reported cases across countries and the importance of understanding data limitations when interpreting public health statistics.

*All figures represent historical reported data, not current COVID-19 statistics.*

## Recommendations

Based on the analysis and findings, the following recommendations are proposed:

1. **Interpret Death Rates Carefully:** Death rates should be understood as a proportion of reported confirmed cases, not as a definitive measure of the virus's severity across countries.

2. **Consider Data Completeness:** Recovery figures should be interpreted cautiously because reporting ended in August 2021.

3. **Include Population-Based Analysis:** Future versions of the dashboard could incorporate cases and deaths per 100,000 people to support fairer comparisons between countries.

4. **Expand the Analysis:** Integrating vaccination data and additional public health indicators could provide a broader understanding of the pandemic.

5. **Maintain Data Accuracy:** When working with cumulative datasets, use appropriate merge keys and latest-value DAX calculations to prevent duplicate counting and inflated totals.

## Limitations

Although the dashboard provides useful insights into historical global COVID-19 trends, some limitations should be considered when interpreting the results.

1. **Historical Dataset:** The analysis covers 22 January 2020 to 9 March 2023. It does not include data beyond this period.

2. **Incomplete Recovery Records:** Johns Hopkins discontinued recovery reporting on 4 August 2021, making recovery comparisons across the entire analysis period unreliable.

3. **Differences in Testing and Reporting:** Confirmed cases depend on testing availability and reporting practices, which varied across countries.

4. **Cumulative Data Revisions:** Some countries revised previously reported figures, resulting in occasional negative daily changes.

5. **Country-Level Detail:** The dashboard's detailed country table displays only the top 25 countries, rather than every country in the dataset.

6. **Interpretation of Death Rate:** The calculated death rate represents reported deaths as a proportion of confirmed cases. It should not be interpreted as the actual probability of death from COVID-19.

These limitations highlight the importance of considering data completeness, reporting differences and context when interpreting public health statistics.

## Challenges and Solutions

During the development of this dashboard, I encountered several technical challenges that required troubleshooting and problem-solving.

| Challenge | Solution |
|---|---|
| Inflated totals during data merging | Identified a many-to-many merge issue and resolved it by using three matching keys: Country/Region, Province/State and Date. |
| Date parsing inconsistencies | Applied locale-specific date conversion in Power Query. |
| Incorrect calculations from cumulative data | Developed DAX measures to retrieve the latest cumulative values instead of summing repeated historical totals. |
| Month names sorting alphabetically | Used the Sort by Column feature with a numeric month field. |
| Incomplete recovery records | Created a separate DAX calculation to identify the last available recovery date. |
| DAX formula errors | Created and tested measures individually to identify and resolve calculation issues. |

### Key Learning

This project strengthened my understanding of data transformation, data modelling, DAX calculations and troubleshooting in Power BI.

It also reinforced the importance of validating data before building visualizations, as incorrect data relationships can produce misleading analytical results.


## Project Files and Repository Structure

This repository contains the Power BI dashboard, source datasets and supporting project materials.

### Power BI Files

| File | Description |
|---|---|
| `Covid dashboard 2.pbix` | Final Power BI dashboard containing the corrected latest-value calculations. |
| `Covid-19 clean dataset.pbix` | Earlier development version retained for reference. |

**Recommended file:** `Covid dashboard 2.pbix` is the final version for viewing and analysis.

### Repository Structure

```text
COVID_Global_Dashboard/
│
├── dashboard/
│   ├── Covid dashboard 2.pbix
│   └── Covid-19 clean dataset.pbix
│
├── data/
│   ├── confirmed_global.xlsx
│   ├── deaths_global.xlsx
│   └── recovered_global.xlsx
│
├── screenshots/
│   └── Covid-19 Dashboard.png
│
└── README.md
```

## About the Author

**Abiola Damilola Mercy**  
Data Analyst | Power BI | SQL | Excel

I am a data analytics enthusiast interested in transforming raw data into meaningful insights that support informed decision-making.

I enjoy working on projects involving data cleaning, analysis, visualization and dashboard development.

### Connect With Me

- **LinkedIn:** [Abiola Damilola](https://www.linkedin.com/in/abiola-damilola-b57728406/)
- **GitHub:** [Abiola201](https://github.com/Abiola201)

