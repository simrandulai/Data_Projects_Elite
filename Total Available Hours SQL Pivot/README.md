This project highlights my work on creating a SQL pivot to calculate Total Available Hours for clinics and their providers. This pivot table was used as the base table for the Clinic Utilization Report I created in Tableau. The objective was to showcase advanced SQL skills by deriving meaningful insights from operational clinic data.

### Table of Contents
| File Number | Title | Description |
| :-----------: | ----------- |----------- |
| 1 | README.md | This current page, with all relevant information about the project, just past the Table of contents. |

### Data
This project uses operational data from a clinic management database, including the following key tables: 
- clinic_inventory.claims_log: Records of clinic appointments with the following fields: 
    - Apt_Date: The date of the appointment. 
    - Clinic_Id: The clinic where the appointment was scheduled. 
- elite_dashboard.clinic_hours_normalized: Information on clinic operating hours, including: 
- Clinic_Id: Identifier for the clinic. 
    - Day_of_Week: Operating day (e.g., Monday, Tuesday). 
    - Event: Specific events such as opening, lunch start, and closing times. 
    - Time: Timestamp associated with each event. 
- elite_dashboard.pno_dates: Records of clinic holidays, including: 
    - Start_Date: Start of the holiday. 
    - Description: Reason for the holiday. 
-clinic_inventory.facilities: Information on clinic providers, including: 
    - Id: Identifier for the clinic. 
- Providers_Per_Hour: Number of providers available per hour. 

### Description:
- This project demonstrates the creation of a pivoted SQL view that calculates clinic hours by day, adjusts for holidays, and incorporates provider availability. 
- The project focuses on building a SQL pivot to compute Total Available Hours for clinics by day, adjusting for holidays and provider availability. The goal is to provide an efficient way to measure clinic capacity and plan resources effectively. 
- It showcases a multi-step approach to SQL queries, involving data transformation, collation adjustments, and performance optimization to ensure compatibility across fields. 

### Assumptions: 
1. Data Integrity: All necessary columns and values are correctly populated in the source tables. Null or inconsistent collation issues were resolved during query design. 
2. Holiday Adjustment: Clinic hours are set to zero for holidays listed in the pno_dates table. 
3. Provider Availability: The number of available providers per hour (Providers_Per_Hour) is consistent across all operating hours of the clinic. 
4. Date Ranges: The Calendar_Date column in the pivot table is derived from the Apt_Date field in claims_log and represents valid operating days. 

### Process: 
1. Data Exploration: 
- Explored various tables, including clinic operating hours, provider schedules, and holiday data. 
- Identified collation inconsistencies across fields and resolved them for seamless joins. 
2. Base Pivot Creation: 
- Generated daily clinic hours using a cross-join of calendar dates and clinic IDs. 
- Applied conditional logic to extract opening, lunch, and closing hours. 
3. Adjustment for Holidays: 
- Integrated holiday data to exclude clinic hours for specific dates using SQL joins. 
4. Provider Hour Calculations: 
- Multiplied adjusted clinic hours with the number of available providers per hour to derive total provider hours. 
5. Data Validation: 
- Verified the accuracy of results by comparing SQL output to operational records. 
- Adjusted filters and date parameters for specific time periods. 

### Findings:
Key insights derived from the SQL pivot include: 
- Daily Operational Hours: The query accurately calculates total operating hours per clinic, split between morning and afternoon shifts. 
- Holiday Adjustments: Days marked as holidays in the pno_dates table effectively reduce clinic hours to zero. 
- Provider Hours: By incorporating Providers_Per_Hour, the pivot calculates the total capacity for each clinic in terms of provider availability. 
- Flexibility for Analysis: The pivot table can be filtered by date range or specific clinics for targeted analysis. 

Key SQL Query 
Here’s the core SQL query for calculating Total Available Hours: 
```sql 
WITH  
Date_Range AS ( 
SELECT DISTINCT  
        c.Clinic_Id, 
        d.Calendar_Date, 
        MIN(CASE WHEN ch.Event = 'Open' THEN ch.Time END) AS Open_Time, 
        MIN(CASE WHEN ch.Event = 'Lunch Start' THEN ch.Time END) AS Lunch_Start_Time, 
        MIN(CASE WHEN ch.Event = 'Lunch End' THEN ch.Time END) AS Lunch_End_Time, 
        MIN(CASE WHEN ch.Event = 'Close' THEN ch.Time END) AS Close_Time 
    FROM ( 
        SELECT DISTINCT Apt_Date AS Calendar_Date  
        FROM clinic_inventory.claims_log 
    ) d 
    CROSS JOIN  
        (SELECT DISTINCT Clinic_Id FROM elite_dashboard.clinic_hours_normalized) c 
    LEFT JOIN  
        elite_dashboard.clinic_hours_normalized ch 
    ON  
        CONVERT(c.Clinic_Id USING utf8mb4) = CONVERT(ch.Clinic_Id USING utf8mb4) 
        AND DAYNAME(d.Calendar_Date) COLLATE utf8mb4_general_ci = ch.Day_of_Week COLLATE utf8mb4_general_ci 
    GROUP BY c.Clinic_Id, d.Calendar_Date 
), 
Daily_Hours AS ( 
    SELECT  
        dr.Clinic_Id, 
        dr.Calendar_Date, 
        TIMESTAMPDIFF(MINUTE, dr.Open_Time, dr.Lunch_Start_Time) / 60.0 AS Open_To_Lunch_Hours, 
        TIMESTAMPDIFF(MINUTE, dr.Lunch_End_Time, dr.Close_Time) / 60.0 AS Lunch_End_To_Close_Hours, 
        TIMESTAMPDIFF(MINUTE, dr.Open_Time, dr.Close_Time) / 60.0 AS Total_Available_Hours 
    FROM Date_Range dr 
), 
Holiday_Excluded AS ( 
    SELECT  
        dh.Clinic_Id, 
        dh.Calendar_Date, 
        dh.Total_Available_Hours, 
        CASE 
            WHEN pd.start_date COLLATE utf8mb4_general_ci IS NOT NULL THEN 0 
            ELSE dh.Total_Available_Hours 
        END AS Adjusted_Available_Hours 
    FROM  
        Daily_Hours dh 
    LEFT JOIN  
        elite_dashboard.pno_dates pd 
    ON  
        CONVERT(dh.Calendar_Date USING utf8mb4) = CONVERT(pd.start_date USING utf8mb4) 
), 
Provider_Hours AS ( 
    SELECT  
        hae.Clinic_Id, 
        hae.Calendar_Date, 
        hae.Adjusted_Available_Hours, 
        f.providers_per_hour, 
        hae.Adjusted_Available_Hours * f.providers_per_hour AS Total_Provider_Hours 
    FROM  
        Holiday_Excluded hae 
    LEFT JOIN  
        clinic_inventory.facilities f 
    ON  
        CONVERT(hae.Clinic_Id USING utf8mb4) = CONVERT(f.id USING utf8mb4) 
) 
SELECT  
    Clinic_Id, 
    Calendar_Date, 
    Total_Provider_Hours 
FROM Provider_Hours;
```` 
 
