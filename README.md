# Loyalty App Engagement & Performance Overview

### Table of Content
1. [Problem Statement](#problem-statement)
2. [Data Source](#data-source)
3. [Database Structure & Table Relationships](#database-structure--table-relationships)
4. [Tools](#tools)
5. [Dashboard](#dashboard)
6. [SQL Code](#sql-code)
7. [Main Insights](#main-insights)

### Problem Statement 

Restaurant management needs to understand how effectively the loyalty program is used across different locations. 
This includes:
- total and loyalty sales
- tracking check validation rates
- new user registrations
- redemption activity
- differences in average check value between loyalty and non-loyalty guests.

Previously, similar reporting was done in Excel, which made it difficult to quickly identify trends, compare restaurant performance, and spot potential issues. This project demonstrates how the same type of reporting could be transformed into an interactive Tableau dashboard for clearer and faster decision-making.

The goal of the project is to create a management-level dashboard that helps evaluate loyalty app engagement, compare restaurants, monitor validation dynamics, and understand guest behavior over time.

**This project is also based on my personal experience in restaurant operations and reporting. It reflects how I would improve a real reporting process I worked with in the past by using data modeling, SQL logic, a synthetic dataset, and Tableau visualization.**

### Data Source

The dataset used in this project is a synthetic AI-generated dataset created based on clearly defined business rules and data structure requirements. It was designed to represent how the final analytical dataset could look after extracting, joining, and transforming data from the restaurant operations database and the external loyalty program database using SQL.

The dataset contains daily restaurant-level aggregated metrics for 7 months in 2025, including total sales, number of checks, loyalty-related sales, new loyalty users, redeemed items, redeemed value, and validation activity. Each row represents one restaurant on one specific date. You can download the original data source here: [restaurant_loyalty_overview_2025_v3.xlsx](https://github.com/user-attachments/files/27287803/restaurant_loyalty_overview_2025_v3.xlsx). 


### Database Structure & Table Relationships

The original reporting process was based on two separate data sources: the restaurant operational database and an external loyalty program database. 
The data models shown below were created by me as a conceptual reconstruction based on my previous work experience.

<img width="851" height="836" alt="Restaurant and Loyalty DB relations drawio (2)" src="https://github.com/user-attachments/assets/e967edff-469c-43d6-90ce-a1e16050f8a6" />


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

### Main Insights:
