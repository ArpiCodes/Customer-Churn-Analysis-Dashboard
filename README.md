# Customer Churn Analysis Dashboard

![img alt](https://github.com/ArpiCodes/Customer-Churn-Analysis-Dashboard/blob/969e9ec5c774627c57f6efcfed0199e46d13accd/Executive%20Overview.png)

## Overview
This repository contains a comprehensive **Power BI Dashboard** designed to analyze customer churn, identify at-risk customers, and provide actionable insights for retention strategies. The dashboard integrates advanced analytics, predictive modeling, and interactive visualizations to help businesses optimize customer engagement.

## Key Metrics & DAX Measures
The following DAX measures were used to calculate important KPIs for analyzing churn trends:

### 1. **Revenue-Based Metrics**
#### **Total Revenue**
```DAX
Total Revenue = SUM('churn_dataset'[Total_Spend])
```
Calculates the total revenue generated from all customers.

#### **Average Revenue Per User (ARPU)**
```DAX
Average Revenue Per User =
DIVIDE(
    SUM('churn_dataset'[Total_Spend]),
    COUNTROWS('churn_dataset'),
    0
)
```
Measures the average revenue generated per customer.

#### **Churned Revenue**
```DAX
Churned Revenue =
SUMX(
    FILTER('churn_dataset', 'churn_dataset'[Churn] = 1),
    'churn_dataset'[Total_Spend]
)
```
Calculates the total revenue lost due to churned customers.

#### **Retained Revenue**
```DAX
Retained Revenue = [Total Revenue] - [Churned Revenue]
```
Represents the revenue retained from non-churned customers.

#### **Revenue Impact**
```DAX
Revenue Impact =
CALCULATE(
    SUM('churn_dataset'[Total_Spend]),
    ALLEXCEPT('churn_dataset', 'churn_dataset'[Churn])
)
```
Shows the revenue impact by filtering through the churn status.

#### **Revenue Impact by Customer**
```DAX
Revenue Impact by Customer =
CALCULATE(
    SUM('churn_dataset'[Total_Spend]),
    ALLEXCEPT('churn_dataset', 'churn_dataset'[Customer_ID])
)
```
Displays revenue contribution per customer.

### 2. **Churn Analysis Metrics**
#### **Churn Rate**
```DAX
Churn Rate =
VAR ChurnedCustomers = COUNTROWS(FILTER('churn_dataset', 'churn_dataset'[Churn] = 1))
VAR TotalCustomers = COUNTROWS('churn_dataset')
RETURN DIVIDE(ChurnedCustomers, TotalCustomers, 0)
```
Calculates the percentage of customers who have churned.

#### **Total Churned Customers**
```DAX
Total Churned Customers =
CALCULATE(
    COUNTROWS('churn_dataset'),
    'churn_dataset'[Churn] = 1
)
```
Counts the total number of churned customers.

#### **Running Churn Count**
```DAX
Running Churn Count =
CALCULATE(
    [Total Churned Customers],
    FILTER(
        ALLSELECTED('churn_dataset'[Last_Purchase_Date]),
        'churn_dataset'[Last_Purchase_Date] <= MAX('churn_dataset'[Last_Purchase_Date])
    )
)
```
Tracks churn count over time.

#### **Running Churn Rate**
```DAX
Running Churn Rate =
DIVIDE(
    [Running Churn Count],
    COUNTROWS(ALL('churn_dataset')),
    0
)
```
Shows churn trends over a selected time period.

### 3. **Customer Lifetime Value (CLV)**
```DAX
Customer Lifetime Value =
VAR AvgMonthlyRevenue = AVERAGEX('churn_dataset', [Total_Spend] / 12)
VAR AvgLifespan = 1 / [Churn Rate]
RETURN AvgMonthlyRevenue * AvgLifespan
```
Predicts the total revenue a business can expect from a customer over their lifetime.

### 4. **Additional Calculated Columns**
#### **Age Group**
```DAX
Age Group =
SWITCH(
    TRUE(),
    'churn_dataset'[Age] <= 18, "Under 18",
    'churn_dataset'[Age] <= 25, "18-25",
    'churn_dataset'[Age] <= 35, "26-35",
    'churn_dataset'[Age] <= 45, "36-45",
    'churn_dataset'[Age] <= 55, "46-55",
    'churn_dataset'[Age] <= 65, "56-65",
    "65+"
)
```
Categorizes customers into different age groups.

#### **Churn Probability**
```DAX
Churn_Probability =
IF(
    DATEDIFF('churn_dataset'[Last_Purchase_Date], TODAY(), DAY) > 180,
    0.8,
    IF(DATEDIFF('churn_dataset'[Last_Purchase_Date], TODAY(), DAY) > 90,
       0.5,
       0.2)
)
```
Predicts the probability of a customer churning based on inactivity.

#### **Risk Category**
```DAX
Risk_Category =
SWITCH(
    TRUE(),
    'churn_dataset'[Churn_Probability] >= 0.7, "High Risk",
    'churn_dataset'[Churn_Probability] >= 0.4, "Medium Risk",
    "Low Risk"
)
```
Classifies customers into risk categories based on their churn probability.

---

## Dashboard Pages & Visualizations
### 1. Executive Overview
![img alt](https://github.com/ArpiCodes/Customer-Churn-Analysis-Dashboard/blob/9a714bb889bd1a4af8749207f86aa665580b59fb/Screenshot%202025-02-14%20211504.png)
**Key Insights:**
- **Churn Rate (80%)**: Displays the percentage of customers lost.
- **Customer Lifetime Value (CLV - 59.85)**: Represents the average revenue a customer is expected to generate.
- **Average Revenue Per User (ARPU - 576.75)**: Measures the revenue earned per customer.
- **Revenue Breakdown (Waterfall Chart)**:
  - Highlights **retained vs. churned revenue**.
  - **Churned revenue** shows a **-0.46M** loss.
  - **Total revenue impact** appears as **-0.34M**.
- **Churn Risk Distribution (Circular Chart)**:
  - **Low Risk:** 488 customers (48.8%)
  - **Medium Risk:** 501 customers (50.1%)
  - **High Risk:** 11 customers (1.1%)
- **Running Churn Rate Trend (Line Chart)**:
  - Shows the **churn rate over time based on last purchase date**.
  - Increases from **0.7% to 10%**, highlighting critical risk periods.
- **Geographical Analysis (Table & Map)**:
  - Displays **churn rate by city** (Chicago & Houston: 0.84, LA: 0.77).
  - **Total customers: 1000**.

### 2. Customer Insights
![img alt](https://github.com/ArpiCodes/Customer-Churn-Analysis-Dashboard/blob/b53ca938ce675184be3166cdb0912c374860e864/Screenshot%202025-02-14%20211535.png)
**Visualizations:**
- **Customer Data Table**:
  - Contains **customer IDs, spend, feedback scores, subscription type**.
  - Average feedback score: **7.8/10**.
- **Engagement Score Indicators (Bar Chart)**:
  - Scores range **24.79 - 29.16**, indicating customer activity levels.
- **Transaction History & Support Interactions**:
  - Shows past **purchases, ticket resolutions, and feedback trends**.
- **Filterable & Customizable View**:
  - Allows dynamic filtering based on **customer segments**.

### 3. Customer Behavior Analysis
![img alt](https://github.com/ArpiCodes/Customer-Churn-Analysis-Dashboard/blob/b53ca938ce675184be3166cdb0912c374860e864/Screenshot%202025-02-14%20211609.png)
**Key Metrics & Visualizations:**
- **Support Tickets vs. Feedback Scatter Plot**:
  - Green dots: **Retained customers**.
  - Red dots: **Churned customers**.
  - Strong correlation between **low feedback & high churn**.
- **Customer Segmentation (Stacked Bar Chart)**:
  - Breakdown by **subscription type & gender**:
    - Monthly: **357 (F), 331 (M), 35 (Other)**.
    - Yearly: **136 (F), 131 (M), 10 (Other)**.
- **Engagement Trends (Heat Map)**:
  - Highest engagement: **56-65 age group**.
  - Lowest engagement: **Under 18** category.

### 4. Predictive Insights
![img alt](https://github.com/ArpiCodes/Customer-Churn-Analysis-Dashboard/blob/b53ca938ce675184be3166cdb0912c374860e864/Screenshot%202025-02-14%20211644.png)
**Advanced ML-Powered Visuals:**
- **Churn Probability Histogram**:
  - Average probability: **0.36**.
  - Clustered risk levels at **0.2 & 0.5**.
- **Risk Categorization Table (Conditional Formatting)**:
  - Customer segmentation by **risk category** (Green: Low, Yellow: Medium, Red: High).
  - Sorted by **last purchase date & probability scores**.

---
## Getting Started
### Prerequisites
- **Power BI Desktop (Latest Version)**
- **Synthetic Customer Dataset (1000+ records included)**
- **Basic DAX Knowledge (for modifications)**

### Installation
1. Clone the repository:
   ```sh
   git clone https://github.com/ArpiCodes/churn-analysis-dashboard.git
   ```
2. Open the `.pbix` file in **Power BI Desktop**.
3. Set up **refresh schedules** if using live data.

### Customization
- **Add Your Data:** Replace the existing dataset with your company data.
- **Modify Visuals:** Adjust colors, filters, and tooltips to match branding.
- **Extend Functionality:** Integrate with **external APIs**, add more ML models.

---
## Implementation Notes
### **Performance Optimization**
- Uses **calculated columns** for static attributes.
- **Measures** dynamically compute KPIs.
- **Time Intelligence Functions** optimize trend analysis.
- Efficient **filtering contexts** using `CALCULATE`.

### **Best Practices**
- Keep report **under 1GB** for efficiency.
- Schedule **daily/weekly refreshes**.
- Implement **Row-Level Security (RLS)** for data protection.

---
## Contributing
- **Fork & clone the repo**
- **Create a feature branch**
- **Submit a Pull Request (PR)**

---
## Acknowledgements
- Power BI Community for **Visualization Best Practices**.
- Synthetic dataset used for **demo purposes**.

## **Conclusion**
This **Customer Churn Analysis Dashboard** provides actionable insights into churn drivers, revenue impact, and customer retention strategies. By leveraging Power BI and DAX, businesses can optimize marketing efforts, personalize engagement, and reduce churn effectively. 🚀

