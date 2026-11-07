# 🛒 Grocery Stores Sales & Customer Analysis Dashboard

An end-to-end data analytics project that takes raw grocery store transaction data from a CSV file, cleans it in **MongoDB**, exports it as **JSON**, and visualizes it in **Tableau** through two interactive dashboards covering sales performance and customer behavior.

---

## 📌 Table of Contents

- [Project Overview](#-project-overview)
- [Objectives](#-objectives)
- [Tech Stack](#-tech-stack)
- [Project Workflow](#-project-workflow)
- [Dashboard Preview](#-dashboard-preview)
  - [Dashboard 1: Grocery Stores Sales Analysis](#dashboard-1-grocery-stores-sales-analysis)
  - [Dashboard 2: Grocery Stores Customers Overview](#dashboard-2-grocery-stores-customers-overview)
- [Key Insights](#-key-insights)
- [Interactive Features](#-interactive-features)
- [How to Use](#-how-to-use)
- [Future Improvements](#-future-improvements)
- [Author](#-author)

---

## 📖 Project Overview

This project analyzes transaction data from a chain of grocery stores operating across 15 Indian cities. The raw data was imported into MongoDB, cleaned and standardized there, exported as JSON, and connected to Tableau to build two dashboards:

1. **Grocery Stores Sales Analysis**: revenue, quantity, payment methods, categories, stores and monthly trends.
2. **Grocery Stores Customers Overview**: customer counts, demographics, payment preferences, and category and store-wise customer behavior.

---

## 🎯 Objectives

- Clean and prepare raw CSV data using MongoDB.
- Track key sales KPIs such as total sales, quantity sold and average quantity per order.
- Understand which categories, stores and payment methods drive revenue.
- Analyze customer distribution by gender, payment method, category and store.
- Identify monthly and yearly sales trends (2023 – 2025).
- Give business users an interactive, filter-driven dashboard for decision making.

---

## 🛠 Tech Stack

| Tool | Purpose |
|------|---------|
| **CSV** | Raw data source |
| **MongoDB** | Data import, cleaning and transformation |
| **JSON** | Intermediate format for exporting cleaned data |
| **Tableau** | Data visualization and dashboard design |

---

## 🔄 Project Workflow

```
CSV File  →  MongoDB (Import & Clean)  →  JSON Export  →  Tableau  →  Dashboards
```

1. **Data Collection**: Grocery store sales data obtained in CSV format.
2. **Data Import**: CSV imported into a MongoDB collection.
3. **Data Cleaning**: Duplicates, null values, inconsistent formats and data types handled in MongoDB *(queries are maintained in a separate file)*.
4. **Data Export**: Cleaned data exported from MongoDB in JSON format.
5. **Visualization**: JSON connected to Tableau to build calculated fields, charts and dashboards.
6. **Dashboard Design**: Layout, color themes, filters and navigation buttons added between the two dashboards.

---

## 📊 Dashboard Preview

### Dashboard 1: Grocery Stores Sales Analysis

<img width="3774" height="2324" alt="Grocessory Store Sales Analysis" src="https://github.com/user-attachments/assets/d84c16b9-74af-4b0f-b2d5-c150668fb43b" />


#### 🔹 KPIs

| Metric | Value |
|--------|-------|
| Total Sales | 30,016,588 |
| Total Quantity Sold | 260,266 |
| Average Quantity | 2 |
| Unit Price | 13833664 |

#### 🔹 Visualizations

- **Sales by Payment Method**: donut chart showing the revenue share of Cash, Credit Card, Debit Card, Net Banking, UPI and Wallet.
- **Sales by Gender**: bar chart comparing male and female spending.
- **Sales by Category**: revenue across 9 product categories.
- **Monthly Sales Trend**: line chart comparing January–December sales across 2023, 2024 and 2025.
- **Stores by Sales**: horizontal bar chart ranking all stores by total revenue.

---

### Dashboard 2: Grocery Stores Customers Overview

<img width="3024" height="2324" alt="Grocessory Stores Customers Overview" src="https://github.com/user-attachments/assets/d7ffa7d5-c462-4e06-8638-0ded3e81f3da" />


#### 🔹 KPIs

| Metric | Value |
|--------|-------|
| Total Customers | 17,975 |
| Average Customer Spending | 1,670 |
| Total Categories | 9 |
| Total Subcategories | 29 |

#### 🔹 Visualizations

- **Customers by Gender**: male vs. female customer distribution.
- **Customers by Payment Method**: customer count per payment type.
- **Category by Customers**: number of customers buying from each category.
- **Stores by Customers**: customer footfall across stores.
- **Category by Quantity**: total units sold per category.

---

## 💡 Key Insights

### Sales Insights

- **Staples** is the top-selling category by a wide margin (≈7.4M), followed by **Dairy** (≈4.6M) and **Beverages** (≈4.5M).
- **Frozen**, **Produce** and **Snacks** contribute the least revenue (roughly 2M or below each).
- **GreenBasket** and **FreshMart Superstore** are the top-performing stores (≈2.7M+ each), while **Sunrise Stores** and **MegaFoods** are at the lower end.
- Male customers contribute slightly higher sales than female customers.
- Monthly sales stay fairly stable throughout the year, hovering around 800K, with no extreme seasonal spikes.

### Customer Insights

- **FreshMart Superstore** (4,358) and **GreenBasket** (4,175) also lead in customer count, showing that high sales are backed by strong footfall.
- **Beverages** (11,883 customers) and **Dairy** (11,509) are the most widely purchased categories; **Frozen** (5,491) is the least.
- **Beverages** lead in units sold (42,331), followed by Dairy (39,441) and Staples (39,424).
- Customers are spread almost evenly across all six payment methods, so no single payment mode dominates.
- Staples earn the highest revenue even though they rank below Beverages and Dairy in customer count and units, which points to higher-value purchases.

---

## 🎛 Interactive Features

- **Payment Method filter**: Cash, Credit Card, Debit Card, Net Banking, UPI, Wallet.
- **Year filter**: 2023, 2024, 2025.
- **City filter**: Ahmedabad, Bengaluru, Bhopal, Chandigarh, Chennai, Delhi, Hyderabad, Indore, Jaipur, Kochi, Kolkata, Lucknow, Mumbai, Pune, Surat.
- **Dashboard navigation buttons**: switch between the Sales Analysis and Customers Overview dashboards.
- Filters apply across all charts on the dashboard for quick drill-down.

---

## ▶️ How to Use

1. Clone or download this repository.
2. Open the Tableau workbook (`.twb` / `.twbx`) in **Tableau Desktop** or **Tableau Public**.
3. If prompted, re-point the data source to the cleaned JSON file.
4. Use the filters on the left panel to explore the data by payment method, year and city.
5. Use the navigation buttons to move between dashboards.

> **Note:** The MongoDB queries used for importing and cleaning the data are available in a separate file in this repository.

---

## 🚀 Future Improvements

- Add profit and margin analysis if cost data becomes available.
- Add customer segmentation (e.g., high-value vs. occasional buyers).
- Include a city-wise map view for geographic sales analysis.
- Add forecasting for upcoming months' sales.
- Publish the dashboards on Tableau Public / Tableau Server.

---


## 👨‍💻 Author

**Subhajit Chatterjee**  
**Data Analyst** at Farmershop Tech India Pvt. Ltd.

- **Role:** Data Analyst
- **Company:** Farmershop Tech India Pvt. Ltd.
- **Skills:** Google Sheets, SQL, Power BI, Data Cleaning, Data Visualization, and Business Intelligence


⭐ If you found this project useful, consider giving it a star!
