# Loyalty App Engagement & Performance Overview

### Table of Content
1. [Problem Statement](#problem-statement)
2. [Data Source](#data-source)
3. [Database Structure & Table Relationships](#database-structure--table-relationships)
4. [Tools](#tools)
5. [Dashboard](#dashboard)
6. [SQL Code](#sql-code)
7. [Main Insights](#main-insights)
8. [Recommendations](#recommendations)

### Problem Statement 

Restaurant management needs to understand how effectively the loyalty program is used across different locations. 
This includes:
- total and loyalty sales
- tracking check validation rates
- new user registrations
- redemption activity
- differences in average check value between loyalty and non-loyalty guests.

Previously, similar reporting was done in Excel, which made it difficult to quickly identify trends, compare restaurant performance, and spot potential issues. This project demonstrates how the same type of reporting could be transformed into an interactive Tableau dashboard for clearer and faster decision-making. Below is the previous version of the dashboard visualization:

<img width="1408" height="547" alt="Screenshot 2026-04-08 at 12 18 55" src="https://github.com/user-attachments/assets/ec0cc3b3-d3bb-49a5-baba-0ddb99368d88" />



**The goal of the project** is to create a management-level dashboard that helps evaluate loyalty app engagement, compare restaurants, monitor validation dynamics, and understand guest behavior over time.

**This project is also based on my personal experience in restaurant operations and reporting. It reflects how I would improve a real reporting process I worked with in the past by using data modeling, SQL logic, a synthetic dataset, and Tableau visualization.**

### Data Source

The dataset used in this project is a synthetic AI-generated dataset created based on clearly defined business rules and data structure requirements. It was designed to represent how the final analytical dataset could look after extracting, joining, and transforming data from the restaurant operations database and the external loyalty program database using SQL.

The dataset contains daily restaurant-level aggregated metrics for 7 months in 2025, including total sales, number of checks, loyalty-related sales, new loyalty users, redeemed items, redeemed value, and validation activity. Each row represents one restaurant on one specific date. You can download the original data source here: [restaurant_loyalty_overview_2025_v3.xlsx](https://github.com/user-attachments/files/27287803/restaurant_loyalty_overview_2025_v3.xlsx). 


### Database Structure & Table Relationships

The original reporting process was based on two separate data sources: the restaurant operational database and an external loyalty program database. 
The data models shown below were created by me as a conceptual reconstruction based on my previous work experience.

<img width="887" height="836" alt="Restaurant and Loyalty DB relations drawio" src="https://github.com/user-attachments/assets/03776bbe-6321-4c4e-99bc-5534df736638" />


Note: The diagram does not represent the exact original production databases. Instead, it is designed to illustrate the logic of the data sources, the relationships between key tables, and how restaurant operations data could be connected with loyalty program data for analytical reporting.

### Tools

- **Draw.io** — used to create the conceptual data model
- **SQL** — used to define the query logic and data transformations
- **Tableau** — used to build the interactive dashboard
- **AI** — used to generate a synthetic dataset based on the SQL query logic
  
### [Tableau Dashboard](https://public.tableau.com/app/profile/kseniia.kornienko/viz/Restaurant_17776725293860/LoyaltyAppEngagementPerformanceOverview)
<img width="730" height="726" alt="image" src="https://github.com/user-attachments/assets/243fc0bd-55be-4325-a292-2d0b98c66d33" />
<img width="730" height="400" alt="image" src="https://github.com/user-attachments/assets/b6c7c1b2-cb35-4e26-b3da-f36a3079bc71" />
<img width="730" height="600" alt="image" src="https://github.com/user-attachments/assets/b5c483c6-5387-4f35-a09f-d2b3e2c69e49" />

### SQL Code

```
WITH restaurant_sales AS (
    SELECT 
        t.datetime AS date,
        r.name AS restaurant_name,
        COUNT(DISTINCT t.check_id) AS checks_count,
        SUM(t.amount_spent) AS total_sales_amount
    FROM transactions AS t
    JOIN restaurants AS r
        ON t.restaurant_id = r.id
    WHERE t.datetime >= '2025-01-01'
      AND t.datetime < '2025-08-01'
    GROUP BY 
        t.datetime,
        r.name
),

loyalty_sales AS (
    SELECT
        lt.datetime AS date,
        l.location_name,
        COUNT(DISTINCT lt.id) AS loyalty_checks_count,
        SUM(lt.amount_spent) AS loyalty_sales_amount
    FROM loyalty_transactions AS lt
    JOIN locations AS l
        ON lt.location_id = l.id
    WHERE lt.datetime >= '2025-01-01'
      AND lt.datetime < '2025-08-01'
    GROUP BY 
        lt.datetime,
        l.location_name
),

redeemed_info AS (
    SELECT
        ri.datetime AS date,
        l.location_name,
        COUNT(DISTINCT ri.check_id) AS redeemed_checks_count,
        SUM(ri.quantity) AS redeemed_items_count,
        SUM(ri.product_value * ri.quantity) AS redeemed_value_total
    FROM redeemed_items AS ri 
    JOIN locations AS l
        ON ri.location_id = l.id
    WHERE ri.datetime >= '2025-01-01'
      AND ri.datetime < '2025-08-01'
    GROUP BY 
        ri.datetime,
        l.location_name
),

new_users AS (
    SELECT 
        u.account_registered AS date,
        l.location_name,
        COUNT(DISTINCT u.id) AS new_users_count
    FROM users AS u
    JOIN locations AS l
        ON u.location_id = l.id
    WHERE u.account_registered >= '2025-01-01'
      AND u.account_registered < '2025-08-01'
    GROUP BY 
        u.account_registered,
        l.location_name
)

SELECT 
    rs.date,
    rs.restaurant_name,
    rs.checks_count,
    rs.total_sales_amount,

    COALESCE(ls.loyalty_checks_count, 0) AS loyalty_checks_count,
    COALESCE(ls.loyalty_sales_amount, 0) AS loyalty_sales_amount,

    COALESCE(ri.redeemed_checks_count, 0) AS redeemed_checks_count,
    COALESCE(ri.redeemed_items_count, 0) AS redeemed_items_count,
    COALESCE(ri.redeemed_value_total, 0) AS redeemed_value_total,

    COALESCE(nu.new_users_count, 0) AS new_users_count

FROM restaurant_sales AS rs
LEFT JOIN loyalty_sales AS ls
    ON rs.date = ls.date
    AND rs.restaurant_name = ls.location_name
LEFT JOIN redeemed_info AS ri
    ON rs.date = ri.date
    AND rs.restaurant_name = ri.location_name
LEFT JOIN new_users AS nu
    ON rs.date = nu.date
    AND rs.restaurant_name = nu.location_name
ORDER BY 
    rs.date,
    rs.restaurant_name;
```

### Main Insights:

Overall, revenue has shown a steady upward trend since March. By July, sales had increased by 67% compared to the beginning of the year and by 10% compared to June.

The validation rate remained relatively stable throughout the period. However, the chart shows a noticeable drop in July.

Restaurants can be divided into three main groups based on revenue:

- High revenue: above $3.5M - Park Avenue, Fayna Town, Sun District.
- Medium revenue: between $1.5M and $3.5M - Green Market, River Gate, Old Square.
- Low revenue: under $1.5M - City Harbor.

There is a weak negative correlation between revenue and validation rate. The Spearman correlation coefficient is -0.11. 
The p-value is less than 0.05, meaning the correlation is statistically significant at the 5% significance level.
<img width="550" height="500" alt="image" src="https://github.com/user-attachments/assets/93f55c4f-3d7b-4cb4-97e4-888d11cfb035" />

This suggests that validation rate may be influenced by factors beyond revenue, such as target audience, location type, and customer behavior. 
For example, Park Avenue, Green Market, City Harbor, and Fayna Town are located in residential districts, which may contribute to higher validation rates and potentially stronger customer retention.
However, Sun District also has a lot of residential buildings around but its validation rate is almost the lowest which should be looked into.

<img width="700" height="400" alt="image" src="https://github.com/user-attachments/assets/999671b2-bef1-45cb-b4b9-69d4a894b6e9" />

Average check and revenue:
The Spearman correlation coefficient is 0.85, indicating a strong positive moderate correlation.
The p-value is less than 0.05, meaning the correlation is statistically significant at the 5% significance level.

The p-value is less than 0.05, meaning the correlation is statistically significant at the 5% significance level.
<img width="550" height="500" alt="image" src="https://github.com/user-attachments/assets/b9ba33ee-840d-4913-9f34-80c492abe9a8" />

In July, validated check volume declined significantly, particularly among high-revenue locations. 
For example, Park Avenue saw a 20% decrease in validated checks on Fridays and Sundays, its typically busiest days. This trend is observed among other restaurants where sales increased as well.

Since the validation process depends on staff, it is recommended to review employee schedules, as staffing may be the root cause of the issue.

<img width="750" height="700" alt="image" src="https://github.com/user-attachments/assets/f800a36b-4a17-4310-8e29-d38a5ea47137" />

Although loyalty users can redeem points for certain dishes, their average check is higher than that of non-loyalty guests.
<img width="750" height="350" alt="image" src="https://github.com/user-attachments/assets/2c25b893-8bc4-4865-aac9-b47388616a6e" />

### Recommendations

1. **Investigate the drop in validated checks in July**

The July decline should be reviewed in more detail, especially because it affected high-revenue locations. Since validation depends on staff involvement, management should check whether employee schedules, workload, or operational pressure on busy days contributed to the drop.

2. **Review staffing on peak days as additional measure**

If these are the busiest days, employees may have less time to remind guests about the loyalty app or scan checks properly.

3. **Leverage locations in residential locations to increase loyalty engagement**

Locations such as Park Avenue, Green Market, City Harbor, and Fayna Town already show strong validation rates. Management should use these locations as key opportunities to increase the share of loyalty guests and strengthen customer retention even further.
This can be done through targeted marketing initiatives, personalized loyalty campaigns, and promotions designed to encourage repeat visits. 
Additionally, conduct retention and cohort analysis to better understand which customer groups are most loyal and what initiatives drive repeat visits.

4. **Adjust loyalty strategy by location type**

Validation rate may depend on the target audience and restaurant location. Residential locations may have higher retention and stronger loyalty behavior, while tourist or business-area restaurants may need a different approach, such as clearer app promotion, faster validation process, or staff reminders.

5. **Promote loyalty users as a high-value customer segment**

Since loyalty users have a higher average check than non-loyalty guests, the program appears to attract or encourage more valuable customer behavior. Management should continue promoting loyalty app usage and encourage more guests to register and validate their checks.

6. **Monitor validation rate regularly**

Validation rate should be tracked by restaurant, day of week, and month to quickly identify drops. This would help management react faster when validation activity decreases and understand whether the issue is related to staff, location, seasonality, or customer behavior.
