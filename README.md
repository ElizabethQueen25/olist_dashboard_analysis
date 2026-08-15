# Olist Brazilian E-Commerce Analytics Dashboard

## 1. Project Overview & Company Background
**Olist** is a major Brazilian e-commerce enabler and marketplace integrator operating across Brazil. It allows small and medium-sized merchants (SMBs) to list and sell their product inventories across Brazil's largest online marketplaces (such as Mercado Livre, B2W, Amazon, and Magazine Luiza) through a single unified platform.

Managing logistics, payment gateways, product variety, and fulfillment times across continental-scale Brazilian territory introduces critical operational challenges. This Power BI analytics project delivers an end-to-end business intelligence solution—designed in **Figma** and modeled in **Power BI**—to track commercial performance, logistic SLAs, customer lifetime value, catalog economics, and customer satisfaction.

---

## 2. Dataset & Data Architecture
The dataset was obtained from the official **Brazilian E-Commerce Public Dataset by Olist on Kaggle**, comprising real commercial records between **2016 and 2018**.

### Relational Schema Tables
* **`olist_orders_dataset`**: Core transaction table tracking order status, order creation, approval timestamp, carrier pickup, and delivery timestamps.
* **`olist_order_items_dataset`**: Line-item details containing item price, freight value, product IDs, and seller IDs.
* **`olist_customers_dataset`**: Customer identifiers, geographic state (`customer_state`), and city.
* **`olist_products_dataset`**: Product dimensions (weight in grams, length, height, width) and category names.
* **`product_category_name_translation`**: English translations for Portuguese category names.
* **`olist_order_payments_dataset`**: Transaction payment types (*credit card, boleto, voucher, debit card*), installments, and payment values.
* **`olist_order_reviews_dataset`**: Customer feedback scores (1 to 5 stars), review survey creation, and response timestamps.
* **`olist_sellers_dataset`**: Merchant registry and location coordinates.
* **`olist_geolocation_dataset`**: Brazilian zip code prefix coordinates (latitude and longitude) used for spatial mapping.

---

## 3. Detailed Page-by-Page Analysis

---

### Page 1: Sales Overview

#### Business Context & Hypotheses
Commercial leadership needs to evaluate overall revenue performance, average order size, freight burdens, and geographic concentration.
* **Hypothesis:** Revenue is heavily centralized in the industrialized Southeast region (São Paulo, Rio de Janeiro, Minas Gerais), while smaller peripheral markets bear higher relative freight costs.

#### Key Visuals & Role
* **KPI Header Cards:** Track aggregate `GROSS SALES`, `TOTAL ORDERS SOLD`, `AVG. ORDER VALUE (AOV)`, `FREIGHT-TO-PRICE`, and `MoM SALES GROWTH`.
* **Monthly Revenue Trend (Line Chart):** Identifies revenue seasonality, peak months, and baseline sales trajectory.
* **Top 5 Product Categories (Bar Chart):** Highlights the strongest revenue-generating sectors (`health_beauty`, `watches_gifts`, `bed_bath_table`).
* **Regional Revenue Distribution (Treemap):** Visualizes sales concentration across Brazilian federative units (UF).
* **Order Status & Granular Details:** Monitors delivered volume versus cancellations and provides transactional auditability.

#### Key Findings & Insights
* **State Dominance:** São Paulo (`SP`) represents the single largest market share (>40% of total gross sales), followed by `RJ` and `MG`.
* **Healthy Basket Size:** Platform Average Order Value (AOV) stabilizes around **R$ 136.75**.
* **Freight Burden:** Freight costs account for ~**17%** of total product prices across sales transactions.

#### Core DAX Measures & Purpose
```dax
-- Total Gross Revenue generated from item sales
GROSS SALES = SUM(olist_order_items_dataset[price])

-- Average basket size per distinct transaction
AVG. ORDER VALUE (AOV) = 
DIVIDE([GROSS SALES], DISTINCTCOUNT(olist_orders_dataset[order_id]), 0)

-- Ratio of freight costs over total product merchandise value
FREIGHT-TO-PRICE = 
DIVIDE(SUM(olist_order_items_dataset[freight_value]), [GROSS SALES], 0)

-- Month-over-Month growth rate conditioned on month selection
MOM GROWTH % = 
IF(
    HASONEVALUE(calendar_table[Month]),
    VAR INCOME_CURRENT_MONTH = SUM(olist_order_items_dataset[price])
    VAR INCOME_PREV_MONTH = 
        CALCULATE(
            SUM(olist_order_items_dataset[price]),
            PREVIOUSMONTH(calendar_table[Fecha])
        )
    RETURN
        DIVIDE(INCOME_CURRENT_MONTH - INCOME_PREV_MONTH, INCOME_PREV_MONTH),
    BLANK()
)
