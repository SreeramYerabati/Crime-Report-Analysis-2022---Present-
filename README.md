# Crime Analysis & Public Safety Insights Dashboard
> An interactive Power BI dashboard analyzing 752,911 crime reports in Los Angeles (2020–2023), uncovering patterns in crime type, location, timing, and victim demographics.

---

## Project Overview

This project transforms raw crime incident data from the Los Angeles Police Department into an interactive, multi-panel dashboard. It demonstrates end-to-end data analytics competency: from raw CSV ingestion and cleaning, through DAX-powered metric creation, to publication-ready visual storytelling.

**Tools used:** Power BI Desktop · DAX · Power Query (M language)  
**Dataset:** LA Crime Reports 2020–Present (~752K records)  
**Domain:** Public safety analytics / urban crime intelligence

---

## Key Insights

### 1. Crime Volume & Victim Profile
- **752,911** total crime reports recorded across the analysis period
- Male victims (312K) outnumber female victims (278K) by approximately 12%
- Average victim age is **30 years**, indicating young adults bear the highest risk exposure

### 2. Crime Peaked in 2022, Then Dropped Sharply
- Reports rose from **199K (2020)** → **209K (2021)** → **234K (2022)** — a 17.6% increase over two years
- 2023 saw a sharp reversal to **111K**, a 52% single-year decline
- The 2020 baseline likely reflects COVID-19 suppression effects on reported crime
- The 2023 drop warrants further investigation — possible changes in reporting policy, data lag, or genuine reduction

### 3. Vehicle-Related Crime Dominates
- Vehicle theft/tampering is the single most reported crime category (~80K total, weekday + weekend)
- Battery (simple assault) is second at ~60K
- Property crimes (burglary, theft of identity, vandalism) cluster tightly between 43K–49K each, suggesting a broad, persistent baseline of opportunistic offending

### 4. Geography: Central & 77th Street Lead
- **Central** (50K) and **77th Street** (48K) are the highest-crime areas
- Pacific, Southwest, Hollywood, and Southeast follow closely (39K–44K each)
- The tight clustering across top areas suggests systemic, geographically distributed crime rather than isolated hotspots

### 5. Weapon Profile: Physical Force Overwhelmingly Dominant
- **Strong-arm** (physical force, no weapon) accounts for **491K** incidents — 65%+ of all weapon-coded reports
- Firearms (handgun: 16K, semi-automatic: 6K) represent a small but critical subset
- Unknown weapon codes (27K) represent a data quality gap worth flagging

### 6. Weekday vs. Weekend Distribution
- All top crime categories show higher weekday volume — consistent with population activity patterns
- Vehicle theft shows the largest absolute weekday-weekend split (58K vs 23K)
- Weekend crime rates are not dramatically lower, suggesting crime is not primarily opportunity-driven by business hours

---

## DAX Implementation

### Calculated Columns
New columns added to the dataset to support filtering, grouping, and time intelligence.

| Column | Formula | Purpose |
|---|---|---|
| `Victim Gender` | `IF([Vict Gender]="M","Male",IF([Vict Gender]="F","Female","Unknown"))` | Maps raw gender codes to readable labels |
| `Crime Date` | `DATEVALUE([DATE OCC])` | Converts text date to date type — required for time functions |
| `Crime Year` | `YEAR([DATE OCC])` | Extracts year for trend analysis and year slicer |
| `Crime Type` | `[Crm Cd Desc]` | Aliases crime description column for cleaner visual labels |
| `Day Name` | `FORMAT([Crime Date], "dddd")` | Derives full day name (e.g. Monday) from Crime Date |
| `Week Type` | `IF(WEEKDAY([Crime Date], 2) >= 6, "WeekEnd", "WeekDay")` | Classifies each record as weekday or weekend using ISO mode 2 (Mon=1…Sun=7) |

### Measures
Aggregations that evaluate across the full table and power the KPI cards.

| Measure | Formula | Purpose |
|---|---|---|
| `Total Crimes` | `COUNTROWS('Crime_data_2022-Present')` | Total reported incidents — COUNTROWS() used over COUNT() to handle text-format report IDs |
| `No_Of_Male` | `CALCULATE(COUNTROWS(...), [Victim Gender] = "Male")` | Count of male victims, filtered on the calculated column |
| `No_Of_Female` | `CALCULATE(COUNTROWS(...), [Victim Gender] = "Female")` | Count of female victims, filtered on the calculated column |
| `Avg AGE` | `CALCULATE(AVERAGE([Vict Age]), [Vict Age] > 0)` | Average victim age, excluding zero/null entries |

### Column Dependency Chain
```
[DATE OCC]    → Crime Date → Day Name
                           → Week Type
[Vict Gender] → Victim Gender → No_Of_Male
                              → No_Of_Female
[Vict Age]    → Avg AGE (filtered measure)
```
---

## Data Cleaning Steps

- Removed records with invalid victim age (age = 0 or null)
- Filtered out unknown gender codes ("X") for gender-specific KPIs
- Standardized date columns (Date Rptd, DATE OCC) to datetime format
- Removed duplicate DR_NO entries
- Mapped gender, descent, and weapon codes to human-readable labels via lookup tables

---

## Dashboard Panels

| Panel | Visualization Type | Insight Delivered |
|---|---|---|
| KPI Cards | Card visuals | Volume, gender split, avg age |
| Top 10 Areas | Horizontal bar chart | Geographic crime concentration |
| Crime Type Frequency | Horizontal bar chart | Most common offences |
| Weapon Distribution | Horizontal bar chart | Force type breakdown |
| Geographic Map | Bing Maps scatter | Spatial crime distribution |
| Weekday vs Weekend | Stacked bar chart | Temporal behaviour patterns |
| Crime Trend Over Time | Area chart | Year-on-year trend (2020–2023) |

---

## How to Use

1. Clone this repository
2. Open `Crime_Analysis_Dashboard.pbix` in Power BI Desktop
3. Use the **Crime Type by Year** slicer to filter all visuals by year
4. Interact with any chart to cross-filter the entire dashboard

---

## Dataset Source

[Crime Data from 2020 to Present — LA Open Data](Crime_Data_from_2020_to_Present.zip)

---

*Built as part of a data analytics portfolio project demonstrating Power BI, DAX, and data storytelling competency.*
> All figures reflect reported crimes only. Unreported incidents and 
> 2023 data lag may affect year-on-year comparisons.
