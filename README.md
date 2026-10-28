# Project 1: Exploratory Sales Analysis (Jagadha by Padanjaly WooCommerce Orders)

A data cleaning and exploratory sales analysis project built in Power BI. It covers 690 orders from the Jagadha by Padanjaly WooCommerce store (January 2023 to May 2026) and ends in a one-page dashboard, a written report with insights, and business recommendations.

### Dashboard View
👉 [Download / View Dashboard PDF - Page 1](Padanjaly_Sales_Dashboards.pdf#page=1)

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
└── Padanjaly_Sales_Dashboards.pdf
```

## Author

**Christy Andrews**
Data analysis project, October 2026

---

# Project 2: Location Sales Analysis

A second Power BI dashboard, built on the same data, that looks at **where** the sales come from: by country, state and city.

### Dashboard View
👉 [Location Sales Analysis dashboard - Page 2](Padanjaly_Sales_Dashboards.pdf#page=2) 

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
└── Padanjaly_Sales_Dashboards.pdf
```
# Project 3: Product Performance Analysis

A third Power BI dashboard, built on the same data, that looks at **which products drive revenue, how pack types perform over time, and which products sell best across different regions.**

### Dashboard View
👉 [Product Performance Analysis dashboard - Page 3](Padanjaly_Sales_Dashboards.pdf#page=3) 

## Objective

Analyse product revenue and quantity sold to identify top-performing products, compare single packs, multi-packs and combo packs, track quarterly revenue trends, and understand which products perform best across Indian states and international markets.

## Data preparation

Product details were spread across four sets of columns in the original orders table. These were combined into one product-level table in Power Query.

- **`Products_Item1` to `Products_Item4`:** Referenced the original orders query, selected the relevant order and product columns, and renamed the product fields consistently.
- **`Products` table:** Appended all four queries into one table containing 822 rows (690 from Item 1, 108 from Item 2, 22 from Item 3 and 2 from Item 4).
- **Data types:** Set order IDs and quantities to whole numbers, order dates to dates, product IDs to text, and totals and subtotals to decimal numbers.
- **`product_total_INR` and `product_subtotal_INR`:** Converted USD values to INR using the exchange rate stored with each order, while retaining INR values as they were.
- **`product_name`:** Trimmed product names and corrected spelling variations, including Agnihari and the Agnijith–Kasmeerini combo pack.
- **`pack_type`:** Classified products as **Single**, **Multi-pack** or **Combo** based on their names.
- **Query loading:** Disabled loading for the four helper queries and kept the appended `Products` table loaded into the model.
- **`Region Type`:** Classified orders as India or International for comparing product performance across Indian states and other regions.

## Dashboard

One page with 5 KPI cards, 5 main visuals, 2 product tables and a Key Insights box:

- **KPIs:** Quantity Sold, Avg Revenue per Pack, Top Product, Top Product Share %, Total Products.
- **Visuals:** Revenue by product, quarterly revenue by pack type, revenue by pack type, top product in each Indian state, and top product in other regions.
- **Tables:** Product performance by Indian state and by international region, showing the leading product, quantity sold and total revenue.

Example measures:

```DAX
Total Revenue (Products) = SUM(Products[product_subtotal_INR])
Quantity Sold = SUM(Products[quantity])
Total Products = DISTINCTCOUNT(Products[product_name])
Top Product = CONCATENATEX(TOPN(1, VALUES(Products[product_name]), [Total Revenue (Products)], DESC), Products[product_name], ", ")
Top Product Share % = DIVIDE(MAXX(VALUES(Products[product_name]), [Total Revenue (Products)]), [Total Revenue (Products)])
Avg Revenue per Pack = DIVIDE([Total Revenue (Products)], [Quantity Sold])
Top Product in State = CONCATENATEX(TOPN(1, VALUES(Products[product_name]), [Quantity Sold], DESC), Products[product_name], ", ")
```

## Key insights

1. **Top product:** Agnijith 90 gms generates approximately ₹1.24M in revenue, nearly four times the next product.
2. **Volume vs revenue:** Agnijith 90 gms contributes about 47% of units sold and 49.05% of revenue.
3. **Revenue trend:** Product revenue has grown since 2023, with single-pack revenue reaching approximately ₹260K and multi-pack revenue approximately ₹218K in the strongest period shown.
4. **Pack type:** Single packs contribute 58.05% of revenue, multi-packs 39.24%, and combo packs 2.71%.
5. **Top state:** Kerala leads its state table with 355 units for its leading product, Agnihari 90 gms. Agnijith 90 gms leads in most of the other Indian states shown.
6. **International sales:** Singapore leads the other-region table, with 18 units and approximately ₹2.11L in revenue from the Agnijith two-pack.

## Recommendations

1. Maintain availability of Agnijith 90 gms and test targeted promotions to sustain its strong performance.
2. Promote multi-packs to increase their contribution, particularly in international markets.
3. Test bundles and targeted offers to explore opportunities for combo packs.
4. Tailor regional campaigns around Agnihari in Kerala and Agnijith 90 gms in other strong Indian states.
5. Track quarterly revenue by pack type to identify trends and plan inventory and promotions.

## Limitations

- Product quantities and revenue are analysed using the appended product-level table rather than treating each order as a single product.
- Top-product measures depend on the current filter context.
- The dashboard shows product performance and trends but does not establish why demand changes or whether promotions would generate additional sales.

## Files for this project

```text
├── report/
│   └── Product_Performance_Analysis_Final_Report.docx
└── Padanjaly_Sales_Dashboards.pdf
```

# Project 4: Customer Behaviour Analysis

A fourth Power BI dashboard, built on the same data, that looks at **who is buying, how often they come back, and where they are**: registered customers, guest orders, repeat buying and customer locations.

### Dashboard View
👉 [Customer Behaviour Analysis dashboard - Page 4](Padanjaly_Sales_Dashboard.pdf#page=4)

## Objective

Analyse customer behaviour to measure registered customers, repeat buying and spend per customer, compare guest and registered orders across Indian states and Kerala cities, and find opportunities to improve customer retention.

## Data preparation

The orders table records every order but has no table of customers, and guest orders carry a customer ID of 0. A separate customer table was built for registered customers only.

- **`Customers` table:** Created as a calculated table with one row per registered customer (customer ID other than 0). For each customer it holds the number of orders, total spend in INR, first order date and last order date.
- **Relationship:** Joined `Customers[customer_id]` to the orders table's `customer_id`, with one customer linked to many orders.
- **`Order Frequency`:** Added a column that marks each registered customer as **One-time** (1 order) or **Repeat** (2 or more orders).
- **Guest vs registered:** Used the existing `customer_type` column to split orders in the state and city charts. Guest Order % was reused from the sales dashboard.
- **Slicers:** Added Year and Country slicers that filter the whole page.

## Dashboard

One page with 4 KPI cards, 2 slicers, 4 visuals and a Key Insights box:

- **KPIs:** Registered Customers, Repeat Registered Customers %, Guest Order %, Avg Spend per Registered Customer.
- **Visuals:** Guest vs registered orders by Indian state, guest vs registered orders in Kerala cities, registered customers and revenue by quarter, and a donut chart of one-time vs repeat registered customers. The tooltips of the two bar charts also show product revenue.

Example measures:

```DAX
Customers =
ADDCOLUMNS(
    FILTER(VALUES('Orders (2)'[customer_id]), 'Orders (2)'[customer_id] <> 0),
    "Orders", CALCULATE(DISTINCTCOUNT('Orders (2)'[order_id])),
    "Total Spend", CALCULATE(SUM('Orders (2)'[order_subtotal_INR])),
    "First Order", CALCULATE(MIN('Orders (2)'[order_date])),
    "Last Order", CALCULATE(MAX('Orders (2)'[order_date]))
)

Registered Customers = CALCULATE(DISTINCTCOUNT('Orders (2)'[customer_id]), 'Orders (2)'[customer_id] <> 0)
Repeat Registered Customers % = DIVIDE(COUNTROWS(FILTER(CALCULATETABLE(VALUES('Orders (2)'[customer_id]), 'Orders (2)'[customer_id] <> 0), CALCULATE(DISTINCTCOUNT('Orders (2)'[order_id])) >= 2)), [Registered Customers])
Avg Spend per Registered Customer = DIVIDE(CALCULATE(SUM('Orders (2)'[order_subtotal_INR]), 'Orders (2)'[customer_id] <> 0), [Registered Customers])
Order Frequency = IF(Customers[Orders] = 1, "One-time", "Repeat")
```

## Key insights

1. **Low repeat buying:** Of 323 registered customers, only 6.50% (about 21) ordered again and 93.5% ordered once.
2. **Guest orders:** 49.28% of the 690 orders (about 340) are guest orders that cannot be linked to a customer. The sales dashboard shows the guest share of revenue rising every year.
3. **Customer value:** A registered customer spends about ₹4.19K in total, close to one average order (₹3.66K), so most of a customer's value comes from the first purchase.
4. **Top state:** Kerala has about 235 orders, more than three times Maharashtra (about 73), Tamil Nadu (about 70) and Karnataka (about 68).
5. **Guest vs registered by state:** About 145 of Kerala's 235 orders are guest orders, while registered customers place the larger share in Maharashtra, Tamil Nadu, Karnataka and Telangana.
6. **Kerala cities:** Thiruvananthapuram (about 29) and Thrissur (about 28) lead, followed by Kozhikode (about 18). Kannur, Kasaragod, Kottayam and Malappuram have only about 5 to 7 orders each.
7. **Trend:** Registered customers peaked in Qtr 3 2024 (about 47), while revenue peaked later, at about ₹0.4M in Qtr 4 2025 and Qtr 2 2026, when registered customers were about 31 and 39. Revenue includes guest orders, so its growth is no longer matched by registered customers.

## Recommendations

1. Bring one-time buyers back with a follow-up message after delivery, reorder reminders and a small repeat-order offer.
2. Encourage account creation at checkout, for example with order tracking or a first-registration discount, so more guest orders can be linked to customers.
3. Collect a phone number or email from guest buyers so returning guests can be recognised.
4. Run local campaigns in Thiruvananthapuram, Thrissur and Kozhikode, and try to convert Kerala guest buyers into registered customers.
5. Use repeat-order offers in Maharashtra, Tamil Nadu, Karnataka and Telangana, where registered customers are the larger group, and test the response.
6. Use the first and last order dates in the `Customers` table to measure the days between orders and time reminders.

## Limitations

- Registered customers means customer ID other than 0, so guests are not counted as customers.
- The repeat rate may be understated, because a guest who orders again cannot be recognised.
- Repeat means two or more orders in the data, so recent customers may not have had time to reorder.
- The revenue line in the quarterly chart is total revenue, including guest orders, not revenue from registered customers only.
- The last quarter (Qtr 3 2026) is incomplete, so its drop is not treated as a decline.
- Order counts for states and cities are read from the charts and are approximate.
- The reasons given for the insights are likely explanations and need more data to confirm.

## Files for this project

```text
├── report/
│   └── Customer_Behaviour_Analysis_Final_Report.docx
└── Padanjaly_Sales_Dashboard.pdf
```
