# 🛒 Customer Shopping Behavior Analysis

## 📌 Project Overview
This project analyzes customer shopping behavior using transactional data from **3,900 purchases** across multiple product categories. The objective is to uncover insights into **spending patterns, customer segments, product preferences, and subscription behavior** to support data-driven business decisions for a retail organization.

---

## 🧩 Business Problem
A leading retail company aims to better understand how customers interact with its products and services. Management observed changes in purchasing patterns across:
- Customer demographics  
- Product categories  
- Sales channels (online vs. offline)

Key interests include understanding how **discounts, reviews, seasons, shipping types, and subscription status** influence customer decisions and repeat purchases.

---

## 📊 Dataset Summary
- **Rows:** 3,900  
- **Columns:** 18  

### Key Features
- **Customer Demographics:** Age, Gender, Location, Subscription Status  
- **Purchase Details:** Item Purchased, Category, Purchase Amount, Season, Size, Color  
- **Shopping Behavior:** Discount Applied, Promo Code Used, Previous Purchases, Frequency of Purchases, Review Rating, Shipping Type  

⚠️ **Missing Data:**  
- 37 missing values in the `Review Rating` column

---


---

## 🧹 Data Preparation & Cleaning

### 1️⃣ Data Loading and Initial Exploration
```python
import pandas as pd

df = pd.read_csv("customer_shopping_behavior.csv")
df.head(20)
df.info()
df.describe(include='all')
df.isnull().sum()
```
---

### 2️⃣  Handling Missing Data

The Review Rating column contained 37 missing values. These were imputed using the median rating per product category.
```python

df['Review Rating'] = df.groupby('Category')['Review Rating'] \
                        .transform(lambda x: x.fillna(x.median()))
```
---

### 3️⃣ Column Standardization
```python
df.columns = df.columns.str.lower().str.replace(' ', '_')
df = df.rename(columns={'purchase_amount_(usd)': 'purchase_amount'})
```
---


### 4️⃣ Feature Engineering
Age Group Categorization
```python

labels = ['Young_adult', 'Adult', 'Middle_aged', 'Senior', 'High']
df['age_group'] = pd.qcut(df['age'], q=5, labels=labels)

Purchase Frequency (in Days)
frequency_mapping = {
    'Weekly': 7,
    'Fortnightly': 14,
    'Monthly': 30,
    'Quarterly': 90,
    'Every 3 Months': 90,
    'Annually': 365
}

df['purchase_frequency_days'] = df['frequency_of_purchases'].map(frequency_mapping)
```
---


 5️⃣ Data Consistency Check
 ```python
(df['discount_applied'] == df['promo_code_used']).all()
df.drop('discount_applied', axis=1, inplace=True)

```
---


### 🗄️ Database Integration (PostgreSQL)
```sql
from sqlalchemy import create_engine

engine = create_engine(
    "postgresql+psycopg2://postgres:postgres@localhost:5432/customer_behaviour"
)

df.to_sql("customer_details", engine, if_exists="replace", index=False)

```
---

### 🧮 SQL Analysis & Business Questions
Revenue by Gender
```sql
SELECT gender, SUM(purchase_amount) AS revenue
FROM customer_details
GROUP BY gender;

High-Spending Discount Users
SELECT customer_id, purchase_amount
FROM customer_details
WHERE promo_code_used = 'Yes'
AND purchase_amount >= (SELECT AVG(purchase_amount) FROM customer_details);

Top 5 Products by Rating
SELECT item_purchased,
       ROUND(AVG(review_rating::numeric), 2) AS avg_rating
FROM customer_details
GROUP BY item_purchased
ORDER BY avg_rating DESC
LIMIT 5;

Subscribers vs Non-Subscribers
SELECT subscription_status,
       COUNT(customer_id) AS total_customers,
       ROUND(AVG(purchase_amount),2) AS avg_spend,
       ROUND(SUM(purchase_amount),2) AS total_revenue
FROM customer_details
GROUP BY subscription_status;
```
---

## 📈 Power BI Dashboard

An interactive Power BI dashboard was created to visualize insights, including:

- Customer demographics
- Purchase behavior trends
- Product performance metrics
- Subscription analysis
- Discount effectiveness

📂 File: `C:\Users\DELL\Personal_Projects\Personal_Projects_new\Customer_behaviour_analysis\analysis\Customer_behaviour_dashboard-1.jpg`

**Optional:** Add a screenshot of your dashboard below by placing the image in your repository and updating the path:

![Power BI Dashboard](power_bi/dashboard_screenshot.png)

---

## 🔍 Key Findings

- Customers can be segmented into **New, Returning, and Loyal**  
- Some customers spend above average even with discounts applied  
- Express shipping users show higher purchase values  
- Subscribers exhibit distinct purchasing patterns  
- Revenue contribution varies across age groups

---

## 💡 Business Recommendations

- Promote subscription benefits to increase customer retention  
- Introduce loyalty rewards for repeat customers  
- Optimize discount strategies to protect profit margins  
- Highlight top-rated and high-performing products  
- Target marketing efforts toward high-value age groups

---

## 🛠️ Technologies Used

- **Python (Pandas)** – Data cleaning & preparation  
- **PostgreSQL** – Data storage  
- **SQL** – Business analysis  
- **Power BI** – Data visualization  
- **GitHub** – Version control & documentation

---

## ▶️ How to Run This Project

```bash
git clone https://github.com/yourusername/customer-shopping-behavior-analysis.git
cd customer-shopping-behavior-analysis
```

- Create a PostgreSQL database named customer_behaviour

- Install dependencies:

```bash
pip install pandas psycopg2-binary sqlalchemy
```
- Run the data preparation script:
```bash
python scripts/data_preparation.py
```
- Execute SQL queries from sql/business_queries.sql

- Open the Power BI dashboard file

## 🚀 Future Work

- Predictive modeling for purchase behavior
- Seasonal trend analysis
- Real-time analytics integration
- Personalized recommendation engine
- Integration of additional data sources

## 📬 Contact

For questions or collaborations, reach out at:  
**your.email@example.com**
