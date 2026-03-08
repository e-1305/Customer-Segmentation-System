# Customer Segmentation System
### RFM Analysis · K-Means Clustering · Segment Profiling · Lookalike Modeling

> A consulting-grade customer analytics pipeline that transforms raw e-commerce transaction data into actionable business intelligence — built in Google Colab on real UK retail data.

---

## Business Problem

Most retail and e-commerce businesses treat all customers the same. They send the same promotions, the same emails, the same offers — to Champions who buy every two weeks and to customers who haven't purchased in six months.

This project replaces that one-size-fits-all approach with a **data-driven segmentation system** that answers three questions:

1. **Who are my best customers, and what makes them different?**
2. **Which customers are slipping away, and how do I win them back?**
3. **Which new customers look like my best ones, and how do I find more of them?**

---

## Results

### RFM Segmentation — Online Retail II Dataset (541,909 transactions · 4,338 customers)

| Segment | Customers | Revenue Share | Avg Recency | Avg Frequency |
|---|---|---|---|---|
| **Champions** | 1,254 (29%) | ~61% | ~14 days | ~12 orders |
| Loyal Customers | 596 (14%) | ~22% | ~35 days | ~8 orders |
| At Risk | 609 (14%) | ~12% | ~130 days | ~5 orders |
| Lost | 1,879 (43%) | ~5% | ~200+ days | ~1 order |

### Key Business Insight

**29% of customers generate ~61% of revenue.**

This is the Pareto Principle in action. Champions purchase every 14 days on average and place 12+ orders per year. Targeting them with VIP programs, early access, and personalized outreach has a direct, outsized impact on revenue.

**At-Risk customers represent the highest-ROI recovery opportunity.**

These 609 customers were once high spenders — they know the brand and have purchased multiple times. A reactivation campaign with a personalized discount is significantly cheaper than acquiring a new customer with equivalent lifetime value.

![Segment Analysis](results/plots/segment_analysis.png)

---

## Pipeline Architecture

```
Raw Data (Online Retail II .xlsx)
        │
        ▼
[ Data Cleaning ]
  Remove cancellations (Invoice starts with 'C')
  Drop missing Customer IDs
  Remove negative quantities and prices
  Compute line-level spend = Quantity × Price
        │
        ▼
[ RFM Computation ]
  Recency   = days since last purchase
  Frequency = unique invoice count
  Monetary  = total revenue per customer
        │
        ▼
[ RFM Scoring ]
  Quintile scoring (1–5) per metric
  Recency reversed: lower recency = higher score
  Segment labels: Champions · Loyal · At Risk · Lost
        │
        ▼
[ Visualization ]
  Customer volume vs. revenue contribution per segment
```

---

## RFM Methodology

**Snapshot date** = max(InvoiceDate) + 1 day

| Metric | Definition | Scoring |
|---|---|---|
| Recency | Days since last purchase | Lower = better → Score 5→1 |
| Frequency | Unique invoices | Higher = better → Score 1→5 |
| Monetary | Total spend (£) | Higher = better → Score 1→5 |

**Segment Logic**

```python
if r >= 4 and (f >= 4 or m >= 4): → Champions
elif f >= 4:                       → Loyal Customers
elif r <= 2 and (f >= 3 or m >= 3):→ At Risk
else:                              → Lost
```

---

## Actionable Recommendations

| Segment | Strategy |
|---|---|
| **Champions** | VIP loyalty program. Early product access. Personalized thank-you outreach. High retention ROI. |
| **Loyal Customers** | Upsell to higher-margin products. Reward consistency. Nudge toward Champion tier. |
| **At Risk** | Reactivation campaign with time-limited personalized discount. Surveys to understand churn reason. |
| **Lost** | Low-cost win-back email sequence. If no response after 2 touchpoints, deprioritize. |

---

## Dataset

[Online Retail II — UCI Machine Learning Repository](https://archive.ics.uci.edu/dataset/502/online+retail+ii)

Real UK e-commerce transactions from 2009–2011. Contains 541,909 records across invoices, products, quantities, and prices — including canceled orders, missing IDs, and negative quantities that required professional cleaning.

---

## How to Run

1. Open the notebook in Google Colab:
   `notebooks/customer_segmentation_rfm.ipynb`

2. Mount your Google Drive and place `Online_Retail.xlsx` at:
   `MyDrive/customer-segmentation/data/Online_Retail.xlsx`

3. Run all cells in order. The notebook will:
   - Download and clean the dataset
   - Compute RFM metrics
   - Score and segment all customers
   - Generate and save visualizations

---

## Tech Stack

| Layer | Tools |
|---|---|
| Data processing | pandas, numpy |
| Visualization | matplotlib, seaborn |
| Storage | pyarrow (Parquet) |
| Environment | Google Colab |

---

## Future Work

- **K-Means clustering** on log-transformed RFM features with UMAP visualization to discover data-driven segments beyond rule-based scoring
- **Lookalike modeling** with XGBoost to score non-Champion customers by their probability of becoming Champions (AUC-ROC + Precision@K evaluation)
- **Promotion modeling** to quantify revenue lift from campaigns and pre-position inventory before high-demand periods
- **Cohort analysis** to track segment migration over time — which Lost customers were once Champions, and when did they leave?