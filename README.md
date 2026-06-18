# Retail-Customer-Segmentation-

# 🛍️ Retail Customer Segmentation Analysis

![Python](https://img.shields.io/badge/Python-9B59B6?style=for-the-badge&logo=python&logoColor=white)
![Tableau](https://img.shields.io/badge/Tableau-A569BD?style=for-the-badge&logo=tableau&logoColor=white)
![Scikit-Learn](https://img.shields.io/badge/Scikit--Learn-7D3C98?style=for-the-badge&logo=scikitlearn&logoColor=white)

## 📚 Table of Contents
- [Project Overview](#project-overview)
- [Data Source](#data-source)
- [Data Cleaning](#data-cleaning)
- [RFM Analysis](#rfm-analysis)
- [Customer Segments](#customer-segments)
- [Limitations](#limitations)
- [Business Recommendations](#business-recommendations)
- [Dashboard](#dashboard)

---

## Project Overview

Using real transaction data from a UK-based online retailer, this project segments over 5,800 customers into distinct groups using RFM analysis and KMeans clustering. The goal is to help marketing teams identify which customers to target, retain, and re-engage.

---

## Data Source

[UCI Online Retail II Dataset](https://www.kaggle.com/datasets/mashlyn/online-retail-ii-uci) via Kaggle

- **Raw dataset:** 1,067,371 rows · 8 columns
- **Columns used:** Invoice, Customer ID, InvoiceDate, Quantity, Price

---

## Data Cleaning

The raw dataset contained cancelled orders, missing customer IDs, and negative quantities that needed to be removed before analysis.

```python
# Drop missing Customer IDs
df_clean = df.dropna(subset=['Customer ID'])

# Remove cancelled orders
df_clean = df_clean[~df_clean['Invoice'].astype(str).str.startswith('C')]

# Remove negative or zero quantity and price
df_clean = df_clean[df_clean['Quantity'] > 0]
df_clean = df_clean[df_clean['Price'] > 0]
```

#### Before vs After Cleaning:

| | Rows |
|---|---|
| **Before** | 1,067,371 |
| **After** | 805,549 |

![Before Cleaning](https://github.com/kswl4/Retail-Customer-Segmentation-/blob/main/Screenshot%202026-06-17%20at%208.43.46%20PM.png)
![After Cleaning](charts/after_cleaning.png)

---

## RFM Analysis

RFM stands for **Recency, Frequency, and Monetary** — a proven marketing framework for understanding customer behavior.

```python
reference_date = df_clean['InvoiceDate'].max() + pd.Timedelta(days=1)

rfm = df_clean.groupby('Customer ID').agg(
    Recency=('InvoiceDate', lambda x: (reference_date - x.max()).days),
    Frequency=('Invoice', 'nunique'),
    Monetary=('TotalSpend', 'sum')
).reset_index()
```

#### Steps:
- **Recency** — days since the customer's last purchase
- **Frequency** — number of unique orders placed
- **Monetary** — total amount spent

KMeans clustering was applied to scaled RFM values. The elbow method identified **4** as the optimal number of clusters.

![Elbow Chart](charts/elbow_chart.png)

---

## Customer Segments

```python
kmeans = KMeans(n_clusters=4, random_state=42)
rfm['Cluster'] = kmeans.fit_predict(rfm_scaled)
```

#### Answer:

| Segment | Customers | Avg Recency | Avg Frequency | Avg Spend |
|---|---|---|---|---|
| **Champion** | 4 | 4 days | 213 orders | $436,836 |
| **VIP** | 35 | 26 days | 104 orders | $83,086 |
| **Loyal** | 3,841 | 67 days | 7 orders | $3,009 |
| **Lost** | 1,998 | 463 days | 2 orders | $765 |

![Customer Segments](charts/customer_segments.png)
![Spend by Segment](charts/spend_by_segment.png)

---

## Limitations

- **No demographic data** — we don't know age, location, or gender of customers
- **UK-based retailer** — findings may not generalize to other markets
- **KMeans assumes equal cluster sizes** — real customer distributions are rarely equal, which is why Champions (4) and VIPs (35) are very small groups
- **Snapshot in time** — segments are based on historical data and will shift over time

---

## Business Recommendations

**1. Protect your Champions and VIPs**
Only 39 customers generate the highest revenue. These customers need white-glove treatment — early access to new products, dedicated account managers, and exclusive loyalty perks.

**2. Move Loyal customers up the ladder**
3,841 loyal customers are your biggest growth opportunity. Targeted campaigns with personalized product recommendations and volume discounts could increase their frequency and spend.

**3. Win back Lost customers**
1,998 customers haven't purchased in over a year. A re-engagement campaign with a discount or "we miss you" email could recover a meaningful portion of this group at low cost.

---

## Dashboard

🔗 [View Live Tableau Dashboard](https://public.tableau.com/app/profile/kiasha.williams/viz/RetailCustomerSegmentation_17817475071620/Dashboard1)
