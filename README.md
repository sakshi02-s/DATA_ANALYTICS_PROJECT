# Understanding Order Value in an Indian Online Shopping Store

A data analysis project on what drives the rupee value of an online order, and which orders are too small to be worth the delivery and marketing cost.

| | |
|---|---|
| **Student** | Sakshi Sharma |
| **Admission No** | 2026B01011196 |
| **Branch / Section** | CSE 12 |
| **Project type** | Data Analyst Training Project, B.Tech / BCA First Year |
| **Date** | 21-11-2026 |

---

## 1. Business Problem

An online store sells to customers in five Indian cities. Its sales manager wants to know **what drives the rupee value of each order** and **which orders are too small to be worth the delivery and marketing cost**. This analysis helps the team decide where to focus promotions, product categories and customer attention.

## 2. The Four Questions

1. What does `Order_Value_Rs` look like overall (typical value, spread, smallest and largest orders, shape of the distribution)?
2. Do `Items_In_Order` and `Time_On_Site_Min` affect `Order_Value_Rs`? (`Customer_Age` and `Discount_Percent` are checked too, for comparison.)
3. Does `Order_Value_Rs` differ by `City`, `Product_Category`, `Payment_Mode` and `Customer_Type`?
4. Which orders need attention? An order is a **Low-Value Order** if `Order_Value_Rs` is below Rs 1,500, and **Normal** otherwise. How many are there, and what do they have in common?

## 3. Dataset

| Item | Value |
|---|---|
| File | `shopping_data.csv` (in `data/raw`) |
| Rows / columns | 255 rows, 11 columns (250 rows after removing duplicates) |
| Period | 2024-11-19 to 2026-11-17 |
| Outcome column | `Order_Value_Rs` |
| Source | Synthetic data created with an AI tool |

| Column | Meaning |
|---|---|
| `Order_Id` | Unique order code (O1001 to O1250) |
| `Order_Date` | Date the order was placed |
| `City` | City the order was delivered to (Delhi, Mumbai, Bengaluru, Kolkata, Chennai) |
| `Product_Category` | Electronics, Fashion, Home_Kitchen, Beauty, Grocery |
| `Payment_Mode` | UPI, Card, COD, Wallet |
| `Customer_Type` | New, Returning, Premium |
| `Customer_Age` | Age in years (18 to 65) |
| `Items_In_Order` | Number of items (1 to 8) |
| `Time_On_Site_Min` | Minutes spent on the site before ordering |
| `Discount_Percent` | Discount given, in percent (0 to 100) |
| `Order_Value_Rs` | Final order value in rupees (the outcome) |

## 4. Data Cleaning

| Column | Problem found | How many | Fix |
|---|---|---|---|
| All columns | Duplicate rows | 5 | Removed |
| `City` | Inconsistent text (extra spaces, different capital letters) | 7 | Trimmed and made the spelling the same |
| `Payment_Mode` | Missing values | 5 | Filled with `Unknown` |
| `Customer_Age` | Missing values | 5 | Filled with the median |
| `Time_On_Site_Min` | Missing values | 4 | Filled with the median |
| `Discount_Percent` | Missing values | 6 | Filled with the median |
| `Discount_Percent` | Impossible values (-20 and 135) | 2 | Marked as missing, then filled with the median |

After cleaning: 250 rows, no missing values, no duplicates. The cleaned file is saved as `data/processed/online_shopping_cleaned.csv`.

## 5. New Columns

| New column | How it is made |
|---|---|
| `Value_Per_Item` | `Order_Value_Rs` divided by `Items_In_Order` |
| `Customer_Segment` | `Customer_Type` joined with the discount level (small discount = 0 to 10%, big discount = above 10%), for example "Premium - Small Discount" |
| `Age_Group` | `Customer_Age` placed in bands 18-25, 26-35, 36-45 and 46+ |
| `Order_Class` | "Low-Value Order" if `Order_Value_Rs` is below 1,500, otherwise "Normal" |

---

## 6. Findings

### Q1. What does order value look like?

![Order value distribution](images/q1_order_value_distribution.png)

- The **typical order is about Rs 2,564** (median); the average is Rs 2,606.
- The middle half of orders lies between **Rs 1,938 and Rs 3,136**; the standard deviation is Rs 899.
- The **smallest order is Rs 557** and the **largest is Rs 5,827**.
- The shape is roughly bell-shaped with a slightly longer tail on the high side (skewness 0.50). Only 4 orders are unusually large (above about Rs 4,933).

### Q2. Do items and time on site affect order value?

![Items and time on site against order value](images/q2_items_time_scatter.png)
![Correlation with order value](images/q2_correlation_bar.png)

- **More items go with higher value** (correlation 0.52). The average order is Rs 1,927 with 1 item and Rs 3,438 with 6 items.
- **Longer visits go with higher value** (correlation 0.48). Orders after up to 10 minutes on the site average Rs 2,212, while orders after more than 30 minutes average Rs 3,450.
- **Customer age (-0.11) and discount (-0.08) have a very weak link** with order value. Orders with a discount of more than 25% average Rs 2,411, which is lower than orders with up to 10% (Rs 2,696). A bigger discount did not bring a bigger order in this data.
- Correlation shows that two columns move together; it does not prove that one causes the other.

### Q3. Does order value differ by group?

![Average order value by group](images/q3_value_by_group.png)

| Group | Highest average order value | Lowest average order value |
|---|---|---|
| Product category | Electronics, Rs 3,195 | Grocery, Rs 2,004 |
| Customer type | Premium, Rs 3,001 | Returning, Rs 2,520 |
| City | Bengaluru, Rs 2,763 | Mumbai, Rs 2,464 |
| Payment mode | Card, Rs 2,688 (leaving out `Unknown`) | COD, Rs 2,461 |

- **Product category and customer type make the biggest difference.** Electronics orders are worth about Rs 1,200 more than Grocery orders, and Premium customers spend about Rs 480 more than Returning customers.
- **City and payment mode make a smaller difference**, about Rs 230 to Rs 300 between the highest and lowest group.
- Some groups are small (Grocery has 15 orders, Premium 33, and `Unknown` payment mode 5), so their averages should be read with care.

### Q4. Which orders need attention?

![Low-value and normal orders](images/q4_low_value_count.png)
![Low-value share by group](images/q4_low_value_share.png)

- There are **26 Low-Value Orders (10.4% of all orders)** and 224 Normal orders. Low-value orders range from Rs 557 to Rs 1,481 and average Rs 1,232.
- **They have few items.** 18 of the 26 (69%) have just 1 or 2 items. Nearly 3 in 10 single-item orders (29%) are low-value, compared with none among orders of 4 items.
- **Customers spend less time on the site** before ordering: 12.4 minutes on average, compared with 19.4 minutes for Normal orders.
- **Product category matters.** Beauty (22% of its orders), Grocery (20%) and Fashion (10%) account for 20 of the 26 low-value orders. Electronics has none.
- **COD has the highest low-value share** (17%, or 9 of 53 orders), compared with 7% to 10% for Wallet, UPI and Card.
- **Chennai (19%) and Kolkata (15%)** have a higher low-value share than Bengaluru (6%), but each has only 26 orders.
- **Age 36-45** has a higher share (17%) than age 18-25 (4%).
- Customer type shows little difference (Returning 11%, New 11%, Premium 6%), and the discount is only slightly higher for low-value orders (19% against 17%).

---

## 7. Recommendations

These are suggestions drawn from this dataset only.

1. **Push single-item orders towards two or more items**, for example with "add one more item" offers or bundles, because 1 to 2 item orders make up most low-value orders.
2. **Focus promotions on Electronics and Premium customers**, who bring the highest order values.
3. **Review small COD orders.** COD has the highest low-value share and the lowest average order value, so a minimum order value or a small COD fee for such orders could be tested.
4. **Do not use big discounts to raise order value.** In this data, higher discounts did not lead to larger orders.
5. **Watch Beauty and Grocery orders**, which are often small, and consider combo offers for them.
6. **Keep an eye on Chennai and Kolkata**, then confirm the pattern with more data before acting.

## 8. Limitations

- The data is synthetic (created with an AI tool), so the results show patterns in this dataset and not in a real store.
- The dataset is small (250 orders after cleaning), and some groups have very few orders.
- The analysis shows relationships between columns, not proof of cause and effect.
- The Rs 1,500 limit for a Low-Value Order and the 10% limit for a small discount are assumptions of this project.

---

## 9. Project Structure

```
online-shopping-order-value-analysis/
├── data/
│   ├── raw/          shopping_data.csv
│   └── processed/    online_shopping_cleaned.csv
├── notebooks/        analysis notebook
├── images/           charts used in this README
├── documents/        SRS and presentation
└── README.md
```

## 10. How to Run

1. Open the notebook in the `notebooks` folder in **Google Colab** or **Jupyter Notebook**.
2. Upload `shopping_data.csv` (from `data/raw`) when the notebook asks for it, or keep it in the same folder.
3. Run all cells from top to bottom (Shift + Enter on each cell).
4. The cleaned file `online_shopping_cleaned.csv` and the charts are saved at the end of the notebook.

## 11. Tools Used

Python, pandas, NumPy, Matplotlib, Google Colab / Jupyter Notebook, GitHub.

## 12. Repository

https://github.com/sakshi02-s/DATA_ANALYTICS_PROJECT
