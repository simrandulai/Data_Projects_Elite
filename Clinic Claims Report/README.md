# File Title: Clinic Claims Report_TB

This project showcases a Tableau Visualization built to analyze and monitor clinic claims efficiently. The report was designed as part of an initiative to streamline clinic operations and provide actionable insights into claims data. 

### Table of Contents
| File Number | Title | Description |
| :-----------: | ----------- |----------- |
| 1 | README.md | This current page, with all relevant information about the project, just past the Table of contents. |
| 2 | [Claims- Data Source.png](https://github.com/simrandulai/Data_Projects_Elite/blob/main/Clinic%20Claims%20Report/Claims-%20Data%20Source.png) | A png file with relationships for multi-table data analysis. |
| 3 | [Visit Information.png](https://github.com/simrandulai/Data_Projects_Elite/blob/main/Clinic%20Claims%20Report/Visit%20Information.png) | A png file with the Visit Information dashboard. |
| 4 | [ICD-10 & CPT Codes.png](https://github.com/simrandulai/Data_Projects_Elite/blob/main/Clinic%20Claims%20Report/ICD-10%20%26%20CPT%20Codes.png) | A png file with ICD-10 & CPT Codes dashboard. |
| 5 | [Patient Demographic.png](https://github.com/simrandulai/Data_Projects_Elite/blob/main/Clinic%20Claims%20Report/Patient%20Demographics.png) | A png file with the Patient Demographic dashboard. |
| 6 | [Appointment Date.png](https://github.com/simrandulai/Data_Projects_Elite/blob/main/Clinic%20Claims%20Report/Appointment%20Date.png) | A png file with the Appointment Date dashboard. |

| Section Title | Description |
| ----------- |----------- |
| Data | Describes the source of data, included files, tables, and fields. |
| Description | Describes the final products purpose, software, format, and included visuals. |
| Calculations | Used to transform values or members at the data source level of detail or at the visualization level of detail. |
| Process | A general outline of how this project formed from start to finish. |
| Findings | Insights learned from the data analysis. |
| Actionable Reccomendations | Reccomendations made for business impact. |
| Results | List of accomplishments and skills practiced in this project. |

### Data
The analysis leverages the following key tables from the RDS SQL database: 
- clinic_inventory.claims_log: Contains appointment and visit details, forming the core claims data.
- clinic_inventory.claims_log_metadata: Metadata connected to claims with fields: 
    - claims_log_id: Connects to the primary claims log.
    - type: Type of metadata.
    - description: Name or details of the metadata item. 
- clinic_inventory.ExtendedPivotedCPT: Consolidates CPT codes with descriptions and costs for easy reference.
- clinic_inventory.facilities: Includes details on clinic locations: 
    - active: Indicates if the clinic is operational (yes/no).
    - medical_visits: Specifies if the facility performs medical visits (yes/no).
    - Facility Calculation:
```sql  
IF [Active] = "yes" AND [Medical Visits] = "yes" THEN [Name] END
```` 
- clinic_inventory.visit_codes: Visit details including:
    - weight: Used for calculating weighted visits.
    - ne: Specifies if the visit is new or established.
- clinic_inventory.icd_10_codes: Diagnosis codes with descriptions to track conditions. 

### Description:
- The Tableau workbook consists of 22 pages, including 4 dashboards: 
1. Visit Information
2. ICD-10 & CPT Codes
3. Patient Demographics
4. Appointment Date Analysis

Visual elements include: 
- Charts: Pie charts, bar charts, stacked bar charts, trend lines.
- Tables: Detailed breakdowns for visits and claims.
- Filters: Filters include a custom calculation to filter by date range:
```sql  
In Date Range T/F: [Apt Date] >= [Start Date] AND [Apt Date] <= [End Date]
````  
- Parameters: Start Date and End Date. 

### Calculations:
1. Weighted Visits: Calculates visit count weighted by visit_codes.weight:
```sql  
SUM(  
    { FIXED [Id], [Visit Code]:  
        COUNT([Id]) * MAX([Weight]) 
    } 
)
```` 
2. Time Formatting: Converts military time to standard 12-hour format:
```sql  
IF DATEPART('hour', [Military Time]) = 0 THEN  
    "12 AM" 
ELSEIF DATEPART('hour', [Military Time]) < 12 THEN  
    STR(DATEPART('hour', [Military Time])) + " AM" 
ELSEIF DATEPART('hour', [Military Time]) = 12 THEN  
    "12 PM" 
ELSE  
    STR(DATEPART('hour', [Military Time]) - 12) + " PM" 
END 
```` 

### Process:
1. SQL Integration: Connected Tableau to the RDS database, ensuring seamless data extraction.
2. Pivot Table Design: Created the ExtendedPivotedCPT table for unified analysis of CPT codes.
3. Data Cleaning: Standardized collation and resolved null values across all connected fields.
4. Visualization: Designed 4 interactive dashboards to visualize key insights using filters and custom calculations.
- Visit Information: Highlights clinic traffic patterns and weighted visits.
- ICD-10 & CPT Codes: Focuses on claims trends and commonly used codes.
- Patient Demographics: Provides insights into the patient population by age, gender, and visit type.
- Appointment Date Analysis: Tracks visit trends over time, helping identify seasonal or daily patterns.
    - Incorporated filters and parameters, such as start and end dates, to allow dynamic exploration of the data.
5. Validation: Compared calculated metrics with SQL outputs to ensure consistency.
6. Iteration: Refined dashboards and calculations based on user feedback. 

### Findings:
1. Visit Trends: Certain clinics exhibit higher traffic during specific hours and days.
2. Claims Insights: _
3. Diagnosis Patterns: Frequent ICD-10 codes suggest a prevalence of chronic conditions such as diabetes and hypertension.
4. Clinic Utilization: _
5. Patient Profiles: _ 

### Actionable Recommendations:
1. Optimize Operating Hours: Align clinic schedules with peak patient traffic to maximize utilization.
- Extend clinic hours or introduce flexible scheduling options in high-traffic locations to accommodate patient demand during peak times.
- Address the higher no-show rates by implementing reminders or offering virtual visit options. 
2. Outreach Campaigns: Use ICD-10 insights to organize educational health events targeting chronic conditions.
- Organize educational campaigns and health events focused on managing chronic conditions such as diabetes and hypertension.
- Invest in offsite facilities to capture untapped patient demographics, particularly in underserved areas.
- Use demographic data to plan mobile clinic deployments or pop-up events targeting specific communities. 
3. Retention Strategies: Incentivize repeat visits for new patients through loyalty programs.
- Launch loyalty programs for established patients to encourage repeat visits, particularly in clinics with lower volumes.
- Conduct patient feedback surveys to identify areas for service improvement, especially for first-time visitors. 

### Results:
x

This project highlights my strengths in:
Data Analysis & Exploration (understanding and manipulating data)
Data Visualization (creating clear and informative visuals)
Communication & Teamwork (conveying insights and collaborating effectively)

