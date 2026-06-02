# 🏥 Hospital Patient Analytics Dashboard — Power BI PR 2

![Power BI](https://img.shields.io/badge/Power%20BI-F2C811?style=for-the-badge&logo=powerbi&logoColor=black)
![Excel](https://img.shields.io/badge/Excel-217346?style=for-the-badge&logo=microsoftexcel&logoColor=white)
![Status](https://img.shields.io/badge/Status-Completed-brightgreen?style=for-the-badge)
![Institute](https://img.shields.io/badge/Institute-Red%20%26%20White%20Skill%20Education-red?style=for-the-badge)

---

## 📌 Project Overview

| Field | Detail |
|-------|--------|
| **Institute** | Red & White Skill Education |
| **Subject** | Power BI |
| **Project** | Practical Report 2 (PR 2) |
| **Total Marks** | 10 |
| **Dataset** | Healthcare Dataset — Patient Hospital Records (Kaggle) |
| **Skill Focus** | Power Query: Storage Modes · Data Profiling · Text, Number & Date Tools · Conditional Columns · Group By · Pivot & Unpivot · Merge · Append · Parameters |

---

## 📊 Dashboard Preview

![Dashboard](D:\Power%20BI\pr2\Screenshots\Dashboard.png)

---

## 📁 Dataset

| Field | Detail |
|-------|--------|
| **Dataset Name** | Healthcare Dataset |
| **Author** | Prasad Patil (prasad22) on Kaggle |
| **Kaggle URL** | [Healthcare Dataset](https://www.kaggle.com/datasets/prasad22/healthcare-dataset) |
| **Rows** | 55,500 patient records |
| **Columns** | 15 (Name, Age, Gender, Blood Type, Medical Condition, Date of Admission, Doctor, Hospital, Insurance Provider, Billing Amount, Room Number, Admission Type, Discharge Date, Medication, Test Results) |


---

## 🛠️ Steps Applied — Power Query Tasks

### ✅ Task 1: Connect to Data Sources & Choose Storage Mode

1. Created `Condition_Dept_Lookup.csv` manually mapping 6 Medical Conditions to Departments and saved it in the same folder as `healthcare_dataset.csv`.
2. Opened Power BI Desktop → Home → Get Data → Text/CSV → selected `healthcare_dataset.csv`.
3. Studied both Import and DirectQuery options in the Connection Settings dialog.
4. Selected **Import mode** — justified because `healthcare_dataset.csv` is a static local file; DirectQuery requires a live SQL/cloud database source.
5. Loaded the second file: Get Data → Text/CSV → selected `Condition_Dept_Lookup.csv`. Both queries confirmed in the Queries pane.
6. Verified all 15 columns visible in Power Query Editor.

---

### ✅ Task 2: Power Query Editor — Data Profiling

1. Enabled **Column Quality** (View tab) — observed % Valid, % Error, % Empty for all 15 columns.
2. Enabled **Column Distribution** — confirmed Medical Condition: 6 distinct values, Gender: 2, Insurance Provider: 5, Admission Type: 3, Test Results: 3, Medication: 5, Blood Type: 8.
3. Enabled **Column Profile** → clicked 'Age' column (Min: 13, Max: 89); clicked 'Billing Amount' → observed negative values (data quality issue).
4. Changed to **"Column profiling based on entire dataset"** (bottom-left status bar) to profile all 55,500 rows.
5. Clicked 'Name' column → observed 55,500 distinct values with mixed casing (e.g. `Bobby JacksOn`) — documented as data quality issue.

---

### ✅ Task 3: Text, Number & Date/Time Transformations

| Step | Action | Details |
|------|--------|---------|
| 3.1 | **Proper Case – Patient Names** | Transform → Format → Capitalize Each Word; applied Trim & Clean |
| 3.2 | **UPPERCASE – Hospital Names** | Transform → Format → UPPERCASE |
| 3.3 | **Proper Case – Doctor Names** | Transform → Format → Capitalize Each Word; applied Trim & Clean |
| 3.4 | **Fix Negative Billing** | Add Column → Custom Column: `Billing_Amount_Fixed = Number.Abs([Billing Amount])` |
| 3.5 | **Round Billing** | Add Column → Custom Column: `Billing_Rounded = Number.Round([Billing_Amount_Fixed], 0)` |
| 3.6 | **Change Date Column Types** | Changed `Date of Admission` and `Discharge Date` to Date type |
| 3.7 | **Extract Admission Year & Month** | Added `Admission_Year`, `Admission_Month`, `Admission_Month_Num` columns |
| 3.8 | **Calculate Length of Stay** | Custom Column: `Length_of_Stay_Days = Duration.Days([Discharge Date] - [Date of Admission])` |
| 3.9 | **Remove Original Billing Amount** | Removed original `Billing Amount` column (contained negatives) |

---

### ✅ Task 4: Index Columns & Conditional Columns

| Step | Column Added | Rule / Configuration |
|------|-------------|----------------------|
| 4.1 | `Patient_ID` | Index Column from 1 (unique sequential identifier) |
| 4.2 | `Age_Category` | Age ≥ 65 → Senior · Age ≥ 18 → Adult · else Minor |
| 4.3 | `Billing_Tier` | ≥ 40,000 → Platinum · ≥ 25,000 → Gold · ≥ 10,000 → Silver · else Standard |
| 4.4 | `Stay_Category` | ≥ 20 days → Long Stay · ≥ 10 → Medium Stay · ≥ 3 → Short Stay · else Day Case |
| 4.5 | `Risk_Flag` | Abnormal → High Risk · Inconclusive → Monitor · else Low Risk |

---

### ✅ Task 5: Grouping & Aggregation

| Query Name | Group By | Aggregations |
|------------|----------|--------------|
| `Condition_Summary` | Medical Condition | Patient_Count (Count), Avg_Billing (Avg), Avg_LOS (Avg) |
| `Hospital_Summary` | Hospital | Total_Patients (Count), Total_Revenue (Sum), Avg_Billing (Avg) |
| `Insurance_Summary` | Insurance Provider | Covered_Patients (Count), Total_Claims (Sum), Avg_Claim (Avg) |
| `Monthly_Admissions` | Admission_Year + Admission_Month_Num | Monthly_Count (Count), Monthly_Revenue (Sum) |

---

### ✅ Task 6: Pivot, Unpivot & Merge Queries

1. **Merge Dept Lookup into Main** — Main query → Merge Queries → matched `Medical Condition` (main) to `Medical_Condition` (lookup) using **Left Outer Join** → expanded and kept `Department` column only. Verified 6 department values (Oncology, Endocrinology, Cardiology, Bariatrics, Pulmonology, Rheumatology), no nulls.

2. **Pivot — Admission Type as Columns** — Referenced `Condition_Summary` → renamed `Condition_Pivot` → added Admission Type grouping → Transform → Pivot Column → Values: Patient_Count → Aggregate: Sum. Result: one row per Medical Condition with columns Elective, Emergency, Urgent.

3. **Unpivot — Demonstrate on Pivot** — On `Condition_Pivot`, selected the 3 admission type columns → Transform → Unpivot Selected Columns. Collapsed into 2 columns: `Attribute` (admission type name) and `Value` (patient count). Demonstrates how wide tables convert back to tall format required by Power BI visuals.

---

### ✅ Task 7: Append Queries, Folder Connector & Data Source Management

1. **Split data by Admission Year** — Referenced main query twice: `Admissions_Pre2022` (Admission_Year < 2022) and `Admissions_2022Plus` (Admission_Year ≥ 2022).
2. **Append the two sub-queries** — Home → Append Queries → Append as New → selected both queries → named result `Admissions_Full` → verified total = 55,500 rows.
3. **Demonstrated Folder Connector** — Home → New Source → Folder → explained how it auto-detects and combines multiple CSVs (e.g., one CSV per year) into one unified table.
4. **Configured Data Source Settings** — File → Options and Settings → Data Source Settings → demonstrated changing file paths for sharing the `.pbix` file.
5. **Created Parameter: `MinAge`** — Type: Whole Number · Current Value: 18. Applied as filter on main query (Age ≥ MinAge). Changing to 65 restricts dataset to senior patients.
6. **Created Parameter: `AdmissionType`** — Type: Text · Suggested Values: Elective, Emergency, Urgent, All · Current Value: All. Used to dynamically filter Admission Type.
7. **Refreshed all queries** — Home → Refresh Preview → Close & Apply → Refresh on Report canvas. Confirmed all 6 tables in Fields pane.

---

### ✅ Task 8: Close & Apply — Load Data into Model

1. Reviewed Applied Steps pane — confirmed full transformation pipeline in order (Source → Promoted Headers → all transformation steps → Merged Department).
2. Renamed all Applied Steps with clear descriptive names (e.g., "Fix negative billing amounts", "Add Length of Stay column").
3. Clicked Home → **Close & Apply** to load all queries.
4. Confirmed Fields pane shows all 6 tables with correct column type icons (Σ numeric, Calendar dates, Abc text).

---

### ✅ Task 9: Build KPI Cards & Core Visuals

| Visual | Configuration |
|--------|--------------|
| KPI Card: Total Patients | Patient_ID → Count → Label: "Total Patients" → Shows 55,500 |
| KPI Card: Total Billing | Billing_Amount_Fixed → Sum → Label: "Total Billing (₹/$)" |
| KPI Card: Avg Billing | Billing_Amount_Fixed → Average → Label: "Avg Billing per Patient" |
| KPI Card: Avg Length of Stay | Length_of_Stay_Days → Average → Label: "Avg Length of Stay (Days)" |
| Bar Chart: Patients by Condition | Y-axis: Medical Condition · X-axis: Patient_Count · Sorted descending |
| Bar Chart: Revenue by Hospital | Y-axis: Hospital · X-axis: Total_Revenue · Top 10 filter |
| Line Chart: Monthly Admissions | X-axis: Admission_Month_Num · Values: Monthly_Count · Legend: Admission_Year |
| Donut Chart: Insurance Mix | Legend: Insurance Provider · Values: Covered_Patients |

All visuals formatted with bold 12pt titles, Red `#CC0000` primary bars, aligned using Format → Align.

---

### ✅ Task 10: Slicers, Filters & Visual Interactions

1. **Slicer: Admission Type** — Tile style (Elective / Emergency / Urgent) filters entire dashboard.
2. **Slicer: Medical Condition** — Dropdown style (6 conditions) filters all visuals.
3. **Slicer: Admission Year** — Between (slider) style for year-range filtering.
4. **Page-level filter** — Added `Age_Category` to Filters pane → unchecked 'Minor' by default.
5. **Visual-level filters** — Revenue by Hospital: Total_Revenue > 0; Monthly Admissions: Admission_Month is not blank.
6. **Configured Interactions** — Medical Condition slicer: Cross-Filter on bar charts and line chart; None on KPI cards. Admission Type slicer: Cross-Filter on all 4 main visuals.
7. **Tested interactions** — Clicking 'Cancer' in condition slicer correctly filters all charts and KPI cards.

---

### ✅ Task 11: Report Page Formatting & Additional Pages

1. Set all 3 pages to **16:9 canvas size** (1280 × 720 px).
2. Set canvas background to `#F5F5F5`, Transparency 0%.
3. Enabled Snap to Grid and Gridlines (View tab).
4. Added Page 1 header: **"Hospital Patient Analytics Dashboard — Healthcare Dataset"** — Bold.
5. Added Page 1 sub-header *Patient Demographics · Admissions · Billing · Condition Analysis · Power Query Analytics Project*.
6. Aligned all Page 1 visuals using Format → Align → top edges, distribute horizontally.
7. **Page 2 — Patient Detail Table** — Table visual with all patient fields including Department, Stay_Category, Risk_Flag; slicers for Medical Condition and Admission Type.
8. **Page 3 — Billing Analysis** — Clustered Column Chart (Department × Avg Billing by Billing_Tier) + Matrix visual (Medical Condition × Insurance Provider × Sum of Billing) + Admission_Year slicer.

---

### ✅ Task 12: Apply Theme & Final Polish

1. Applied built-in theme: View → Themes → **Accessible Default / Executive**.
2. Verified theme consistency across all 3 pages; re-applied Red `#CC0000` to primary bars after theme override.
3. Formatted KPI cards: Bold values, White background, 1pt grey border, 11pt grey category labels.
4. Added descriptive chart subtitles (e.g., "Monthly patient admissions across all conditions · Kaggle Healthcare Dataset · 55,500 records").
5. Removed vertical gridlines from bar and column charts; retained horizontal gridlines.
6. Confirmed all Applied Steps renamed with clear descriptive names in Power Query Editor.

---

## 🧰 Tools Used

- **Power BI Desktop** — Data modeling, Power Query transformations, DAX, report building
- **Microsoft Excel** — Dataset preparation and lookup table creation
- **Power Query (M Language)** — All data transformations and custom columns
- **Kaggle** — Source dataset ([Healthcare Dataset by Prasad Patil](https://www.kaggle.com/datasets/prasad22/healthcare-dataset))

---

## 📹 Video Walkthrough

> 🎥 _Video link to be added here (Google Drive / YouTube Unlisted)_

---

## 📂 Repository Structure

```
📦 PR2-Healthcare-PowerBI
 ┣ 📂 Excel_Dataset
 ┃ ┣ 📄 healthcare_dataset.xlsx
 ┃ ┗ 📄 Condition_Dept_Lookup.xlsx
 ┣ 📂 Assets
 ┃ ┗ 🖼️ All images used in dashboard 
 ┣ 📂 Screenshots
 ┃ ┣ 🖼️ Dashboard.png
 ┃ ┣ 🖼️ Billing_Analysis.png
 ┃ ┗ 🖼️ Table_Preview.png
 ┣ 📄 Healthcare_Dashboard.pbix
 ┗ 📄 README.md
```

---

<div align="center">

## 👨‍💻 Author

### 🎓 RENSEE GAJIPARA

![Author](https://img.shields.io/badge/Student-Rensee%20Gajipara-blueviolet?style=for-the-badge&logo=graduation-cap&logoColor=white)
![Institute](https://img.shields.io/badge/Red%20%26%20White-Skill%20Education-red?style=for-the-badge)
![Subject](https://img.shields.io/badge/Power%20BI-Practical%20Report%202-F2C811?style=for-the-badge&logo=powerbi&logoColor=black)

</div>
