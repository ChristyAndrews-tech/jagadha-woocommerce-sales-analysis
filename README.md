# Project 1: Exploratory Sales Analysis (Jagadha by Padanjaly WooCommerce Orders)

A data cleaning and exploratory sales analysis project built in Power BI. It covers 690 orders from the Jagadha by Padanjaly WooCommerce store (January 2023 to May 2026) and ends in a one-page dashboard, a written report with insights, and business recommendations.

### Dashboard View
👉 [Download / View Dashboard PDF](images/sales_dashboard.pdf)

## Project objective

Analyse the sales data to find key patterns and trends, top-performing products and revenue drivers, calculate basic sales KPIs, and point out insights or anomalies for further analysis.

## About the data

| Item | Detail |
| --- | --- |
| Source | WooCommerce orders export of Jagadha by Padanjaly (Padanjali Ayurvedic (P) Ltd, Kerala) |
| Rows | 690 orders (one row per order) |
| Columns | 72 |
| Period | January 2023 to May 2026 |
| Currencies | INR and USD |

> **The raw dataset is not included in this repository.** It contains customer and order details, so only the report and the dashboard image are shared.

## Tools used

- **Power BI** for the dashboard, with **Power Query** for cleaning and **DAX** for measures
- **FBIL USD/INR reference rates** (Financial Benchmarks India) for the currency conversion
- **Microsoft Word** for the report

## Data cleaning

- Checked and corrected the data type of every column.
- Split `order_date` and `paid_date` into separate date and time columns.
- Converted USD orders to INR using the historical FBIL exchange rate for each order's date, and saved the result in a new column.
- Kept the zero-value tax and discount columns, which show that the store charges no tax and offers no discounts.
- Kept `customer_id = 0` as guest customers and counted them separately.
- Verified billing and shipping state and country against the postcode and city, and wrote the full names into new columns.
- Split `line_item_1` and `line_item_2` into Product Item columns (name, id, SKU, quantity, total, subtotal).
- Left null values as they are, because they carry meaning (for example unpaid orders).

## Dashboard

One page with 6 KPI cards, 5 charts and a Key Insights box:

- **KPIs:** Total Revenue, Total Orders, Average Order Value, Guest Order %, Total Revenue (USD), Total Revenue (INR)
- **Charts:** Quarterly revenue and orders (line and bar), top 5 products by units sold, revenue by year for guest vs registered customers, top 5 states by revenue (treemap), orders by payment method (donut)

Example measures:

```DAX
Total Revenue = SUM(Orders[order_subtotal_INR])
Total Orders = DISTINCTCOUNT(Orders[order_id])
Avg Order Value = DIVIDE([Total Revenue], [Total Orders])
Guest Order % = DIVIDE(CALCULATE([Total Orders], Orders[customer_id] = 0), [Total Orders])
Total Revenue USD = CALCULATE(SUM(Orders[order_subtotal]), Orders[order_currency] = "USD")
```

## Key insights

1. **Revenue and order size:** ₹2.52M from 690 orders, with an average order value of about ₹3.66K. A few price points (₹510, ₹2,100, ₹4,200) appear most, so orders are built on a few core products.
2. **Growth:** quarterly revenue and orders rose steadily and peaked in 2025 and 2026. The drop in the last quarter is only partial data.
3. **Best seller:** Agnijith 90 gm sold about 490 units, far ahead of Agnihari (about 190).
4. **Guests:** 49.28% of orders are guest orders, and the guest share of revenue rises every year.
5. **Locations:** Kerala leads, followed by Singapore, Maharashtra, Tamil Nadu and Telangana.
6. **Payments:** 99.13% of orders are paid online, and only 6 were cash on delivery.
7. **Points to check:** the last quarter is incomplete, a few orders are above ₹10,000, and the product chart reads Product Item 1 only.

## Recommendations

1. Offer clearer bulk and repeat-order deals on Agnijith.
2. Bundle the low sellers (Kasmeerini, Kadambini) with the popular products and promote them in their season.
3. Run marketing campaigns where demand is already strong, such as Kerala, Singapore and the other top states.
4. Turn guest customers into registered customers with a sign-up invitation after checkout.
5. Plan campaigns around seasons, and confirm the monthly pattern first.

## Limitations

- There are no cost columns, so profit cannot be analysed.
- The product chart counts units from Product Item 1 only, so products ordered second are not included, and a pack counts as one unit.
- The reasons given for the insights are likely explanations and need further data to confirm.

## Repository contents

```
├── README.md
├── report/
│   └── Jagadha_Sales_Analysis_Final_Report.docx
└── images/
    └── sales_dashboard.jpg
```

## Author

**Christy Andrews**
Data analysis project, October 2026

---

# Project 2: Location Sales Analysis

A second Power BI dashboard, built on the same data, that looks at **where** the sales come from: by country, state and city.

### Dashboard View
👉 [Location Sales Analysis dashboard](images/location_dashboard.pdf)

## Objective

Analyse sales by country, state and city to identify high and low performing locations, and create geographical insights and visuals that show regional sales performance.

## Data preparation

The location columns were typed by customers, so they were cleaned in Power Query. The original columns were kept next to the new ones.

- **`billing_state_full`:** short codes (KL, TN, TS and so on) converted to full names using the country, then checked against the postcode and city. Wrong entries were corrected (for example Karaikal to Puducherry, Vadodara to Gujarat, Coimbatore to Tamil Nadu, Sugar Hill to Georgia). UK rows were grouped as England, Wales or Northern Ireland.
- **`billing_country_full`:** country codes converted to full names, with the city and postcode used when the code was blank.
- **`billing_city_full` and `shipping_city_full`:** one reusable Power Query function merged spelling variants (Trivandrum and Thiruvananthapuram, Bangalore and Bengaluru, Calicut and Kozhikode) and used the postcode to fix wrong cities.
- **`state_match`, `country_match` and `city_match`:** each order is marked **Same** or **Different** when comparing billing and shipping locations.
- **`Region Type` :** each region is marked as **India** or **International**.

## Dashboard

One page with 5 KPI cards, 6 visuals and a Key Insights box:

- **KPIs:** Countries Served, States and Regions Served, International Revenue %, Top Country Revenue (India), Top State Share %
- **Visuals:** world map of revenue by billing country, top 5 states by revenue, bottom 5 states by revenue, treemap of top cities, matrix of units sold by product in the top states, and a donut chart of billing vs shipping city

Example measures:

```DAX
Countries Served = DISTINCTCOUNTNOBLANK('Orders (2)'[billing_country_full])
States & Regions Served = DISTINCTCOUNTNOBLANK('Orders (2)'[billing_state_full])
Top State Share % = DIVIDE(MAXX(VALUES('Orders (2)'[billing_state_full]), [Total Revenue]), [Total Revenue])
International Revenue % = DIVIDE(CALCULATE([Total Revenue], 'Orders (2)'[billing_country_full] <> "India" && NOT ISBLANK('Orders (2)'[billing_country_full])), [Total Revenue])
```

## Key insights

1. **International sales:** 46.49% of revenue comes from outside India, with Singapore as the strongest overseas market. India accounts for ₹1.35M.
2. **Top Indian states:** Kerala leads (14.39% of revenue), then Maharashtra, Tamil Nadu and Telangana. No state dominates, and orders came from 24 countries and 73 states and regions.
3. **Low performers:** the bottom states (Chandigarh, Jammu and Kashmir, Jharkhand and others) earned only about ₹2,100 to ₹2,600 each, so they are untapped markets and not failures.
4. **Top cities:** Singapore, Hyderabad and Bengaluru lead, then Thiruvananthapuram and Mumbai. Two US cities, Sugar Hill and Spring, also rank in the top eight.
5. **Top products:** Agnijith 90 gm sells in all five top states (94 units in Kerala, 54 in Maharashtra, 53 in Tamil Nadu and 27 in Telangana), while Agnihari leads only in Kerala (126 units).
6. **Billing vs shipping:** 682 of 690 orders (98.84%) ship to the billing city.

## Recommendations

1. Defend Kerala with repeat-order offers, and grow the south Indian metros (Hyderabad, Bengaluru, Chennai).
2. Build the overseas market, starting with Singapore, then the UAE, UK and US, after checking each country's import rules.
3. Lead campaigns outside Kerala with Agnijith 90 gm, and use Agnihari as the lower-priced entry product.
4. Test the low performing states with small, cheap campaigns before spending more.
5. Use drop-down lists for state and city at checkout, so the data needs less cleaning.

## Limitations

- The product matrix reads Product Item 1 only, and its Telangana column is beyond the visible width of the matrix (scroll to see it).
- Singapore ranks second in revenue but shows few units in the matrix, so its orders probably include packs or products outside the top five listed.
- "States and Regions" includes non-Indian regions such as Singapore and England.
- The reasons given for the insights are likely explanations and need more data to confirm.

## Files for this project

```
├── report/
│   └── Location_Sales_Analysis_Final_Report.docx
└── images/
    └── location_dashboard.pdf
```
