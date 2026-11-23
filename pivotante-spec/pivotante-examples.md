# Pivotante Example Queries

This document exercises every feature of Pivotante 5.0.0 using the 67 rows embedded in section 1.1 and exposed through the logical `sales_data` source. The 3NF schema and reconstruction view are in Appendix A of `pivotante.md`.

## Conventions

- Canonical clause order: `PIVOT`, `FROM`, `ROWS`, `COLUMNS`, `WHERE`, `VALUES`, `TOTALS`.
- `GROUP BY` does not exist in Pivotante; grouping is inferred from `ROWS` and `COLUMNS`.
- Keywords and aggregate names are case-insensitive.
- Every `VALUES` item requires an explicit alias: `AS "Revenue"`.
- Axis field order: `field [ASC|DESC] [NULLS FIRST|LAST] [HAVING ...]`. `ROWS` and `COLUMNS` contain grouping fields only; no aggregation functions.
- Direction defaults to `ASC`; NULL placement defaults to `NULLS LAST`.
- The optional `TOTALS` clause declares total modes with `SUM(field)`, `COUNT(field)`, `AVG(field)`, `MIN(field)`, `MAX(field)`, `FIRST(field)`, or `LAST(field)`. If `TOTALS` is absent, every level uses `SUM`. A present `TOTALS` axis block lists every field of that axis. Only `SUM` is valid on the deepest field.
- A missing row/column intersection is `NULL`.
- A `NULL` axis key is displayed as `(blank)`.

## 1. Source

`sales_data` has 67 rows and the columns `order_id`, `region`, `city`, `category`, `product_name`, `quarter`, `order_date`, `revenue`, `profit`, `orders`, `customer_id`, `fiscal_year`, `order_status`.

### 1.1 Simplified CSV Data

This is the flat CSV used by the examples; load it as the logical source `sales_data`. `fiscal_year` is corrected and `quarter` is derived from `order_date`. The 3NF schema and reconstruction view are in Appendix A of `pivotante.md`.

```csv
order_id,region,city,category,product_name,quarter,order_date,revenue,profit,orders,customer_id,fiscal_year,order_status
1,North,Alles,Hardware,Laptop,Q1,2026-02-10,1200,240,8,C001,2026,Completed
2,North,Bences,Hardware,Laptop,Q2,2026-05-11,1400,280,10,C002,2026,Completed
3,North,Desques,Software,Suite,Q1,2026-03-05,800,320,6,C001,2026,Completed
4,North,Alles,Software,Suite,Q2,2026-06-15,950,380,7,C003,2026,Completed
5,South,Miro,Hardware,Laptop,Q1,2026-01-20,700,140,5,C004,2026,Completed
6,South,Miro,Hardware,Laptop,Q2,2026-04-18,850,170,6,C005,2026,Completed
7,South,Sumi,Software,Suite,Q1,2026-02-22,600,240,4,C004,2026,Completed
8,South,Sumi,Software,Suite,Q2,2026-05-30,700,280,5,C006,2026,Completed
9,East,Laslu,Hardware,Laptop,Q1,2026-03-12,1100,220,9,C007,2026,Completed
10,East,Porca,Hardware,Laptop,Q2,2026-06-02,1250,250,11,C008,2026,UnCompleted
11,East,Porca,Software,Suite,Q1,2026-01-28,900,360,7,C007,2026,Completed
12,East,Laslu,Software,Suite,Q2,2026-04-25,1000,400,8,C009,2026,Completed
13,East,Molas,Hardware,Laptop,Q2,2026-04-21,1100,450,5,C009,2026,UnCompleted
14,North,Alles,Software,Suite,Q2,2025-05-11,650,180,4,C003,2025,Completed
15,South,Miro,Hardware,Laptop,Q2,2025-04-21,400,240,7,C004,2025,UnCompleted
16,South,Miro,Hardware,Laptop,Q1,2025-03-11,150,370,2,C005,2025,Completed
17,East,Laslu,Software,Suite,Q1,2025-03-11,150,370,2,C005,2025,UnCompleted
18,North,Alles,Hardware,Laptop,Q1,2025-01-05,554,110,9,C011,2025,Completed
19,North,Alles,Hardware,Laptop,Q2,2025-05-10,607,121,6,C012,2025,UnCompleted
20,North,Alles,Hardware,Laptop,Q2,2026-06-15,660,132,3,C013,2026,Completed
21,North,Alles,Hardware,Laptop,Q3,2025-07-20,713,142,10,C014,2025,UnCompleted
22,North,Alles,Hardware,Laptop,Q3,2026-08-25,766,153,7,C015,2026,Completed
23,North,Alles,Hardware,Laptop,Q4,2025-12-28,819,163,4,C016,2025,UnCompleted
24,North,Alles,Hardware,Laptop,Q4,2026-10-05,872,174,11,C017,2026,Completed
25,North,Alles,Software,Suite,Q1,2025-02-10,725,290,8,C018,2025,UnCompleted
26,North,Alles,Software,Suite,Q1,2026-03-15,778,311,5,C019,2026,Completed
27,North,Alles,Software,Suite,Q3,2025-07-20,831,332,12,C020,2025,UnCompleted
28,North,Alles,Software,Suite,Q3,2026-08-25,884,353,9,C021,2026,Completed
29,North,Alles,Software,Suite,Q4,2025-12-28,937,374,6,C022,2025,UnCompleted
30,North,Alles,Software,Suite,Q4,2026-10-05,990,396,3,C023,2026,Completed
31,North,Bences,Hardware,Laptop,Q1,2025-02-10,1243,248,10,C024,2025,UnCompleted
32,North,Bences,Hardware,Laptop,Q1,2026-03-15,1296,259,7,C025,2026,Completed
33,North,Bences,Hardware,Laptop,Q2,2025-04-20,1349,269,4,C026,2025,UnCompleted
34,North,Bences,Hardware,Laptop,Q3,2025-08-25,502,100,11,C027,2025,Completed
35,North,Bences,Hardware,Laptop,Q3,2026-09-28,555,111,8,C028,2026,UnCompleted
36,North,Bences,Hardware,Laptop,Q4,2025-10-05,608,121,5,C029,2025,Completed
37,North,Bences,Hardware,Laptop,Q4,2026-11-10,661,132,12,C030,2026,UnCompleted
38,North,Bences,Software,Suite,Q1,2025-03-15,514,205,9,C031,2025,Completed
39,North,Bences,Software,Suite,Q1,2026-01-20,567,226,6,C032,2026,UnCompleted
40,North,Bences,Software,Suite,Q2,2025-05-25,620,248,3,C033,2025,Completed
41,North,Bences,Software,Suite,Q2,2026-06-28,673,269,10,C034,2026,UnCompleted
42,North,Bences,Software,Suite,Q3,2025-07-05,726,290,7,C035,2025,Completed
43,North,Bences,Software,Suite,Q3,2026-08-10,779,311,4,C036,2026,UnCompleted
44,North,Bences,Software,Suite,Q4,2025-12-15,832,332,11,C037,2025,Completed
45,North,Bences,Software,Suite,Q4,2026-10-20,885,354,8,C038,2026,UnCompleted
46,North,Desques,Hardware,Laptop,Q1,2025-02-25,1138,227,5,C039,2025,Completed
47,North,Desques,Hardware,Laptop,Q1,2026-03-28,1191,238,12,C040,2026,UnCompleted
48,North,Desques,Hardware,Laptop,Q2,2025-04-05,1244,248,9,C041,2025,Completed
49,North,Desques,Hardware,Laptop,Q2,2026-05-10,1297,259,6,C042,2026,UnCompleted
50,North,Desques,Hardware,Laptop,Q3,2025-09-15,1350,270,3,C043,2025,Completed
51,North,Desques,Hardware,Laptop,Q3,2026-07-20,503,100,10,C044,2026,UnCompleted
52,North,Desques,Hardware,Laptop,Q4,2025-11-25,556,111,7,C045,2025,Completed
53,North,Desques,Hardware,Laptop,Q4,2026-12-28,609,121,4,C046,2026,UnCompleted
54,North,Desques,Software,Suite,Q1,2025-01-05,462,184,11,C047,2025,Completed
55,North,Desques,Software,Suite,Q2,2025-05-10,515,206,8,C048,2025,UnCompleted
56,North,Desques,Software,Suite,Q2,2026-06-15,568,227,5,C049,2026,Completed
57,North,Desques,Software,Suite,Q3,2025-07-20,621,248,12,C050,2025,UnCompleted
58,North,Desques,Software,Suite,Q3,2026-08-25,674,269,9,C051,2026,Completed
59,North,Desques,Software,Suite,Q4,2025-12-28,727,290,6,C052,2025,Completed
60,North,Desques,Software,Suite,Q4,2026-10-05,780,312,3,C053,2026,UnCompleted
61,South,Miro,Hardware,Laptop,Q3,2025-08-10,1033,206,10,C054,2025,UnCompleted
62,South,Miro,Hardware,Laptop,Q3,2026-09-15,1086,217,7,C055,2026,Completed
63,South,Miro,Hardware,Laptop,Q4,2025-10-20,1139,227,4,C056,2025,Completed
64,South,Miro,Hardware,Laptop,Q4,2025-11-25,1192,238,11,C057,2025,Completed
65,South,Miro,Software,Suite,Q1,2025-03-28,1045,418,8,C058,2025,Completed
66,South,Miro,Software,Suite,Q1,2026-01-05,1098,439,5,C059,2026,Completed
67,South,Miro,Software,Suite,Q2,2025-05-10,1151,460,12,C060,2025,UnCompleted
```

Reference totals for the whole table:

| Metric | Value |
|---|---:|
| Rows (`COUNT(*)`) | 67 |
| Distinct customers | 59 |
| `SUM(revenue)` | 55825 |
| `SUM(profit)` | 16931 |
| `SUM(orders)` | 481 |
| `AVG(revenue)` | 833.21 |
| `MIN(revenue)` | 150 |
| `MAX(revenue)` | 1400 |

| Region | Rows | Revenue | Profit | Orders |
|---|---:|---:|---:|---:|
| East | 6 | 5500 | 2050 | 42 |
| North | 48 | 39181 | 11236 | 353 |
| South | 13 | 11144 | 3645 | 86 |

| Quarter | Rows | Revenue | Profit | Orders |
|---|---:|---:|---:|---:|
| Q1 | 20 | 16211 | 5415 | 138 |
| Q2 | 19 | 16984 | 5069 | 129 |
| Q3 | 14 | 11023 | 3102 | 119 |
| Q4 | 14 | 11607 | 3345 | 95 |

## 2. No Axes

**Purpose:** Run the aggregates over the whole source without `ROWS` or `COLUMNS`.

```sql
PIVOT
FROM sales_data
VALUES
    SUM(revenue) AS "Revenue",
    SUM(profit) AS "Profit",
    SUM(orders) AS "Orders",
    COUNT(*) AS "Rows",
    COUNT(DISTINCT customer_id) AS "Customers"
```

**Result:** one row, no totals.

| Revenue | Profit | Orders | Rows | Customers |
|---:|---:|---:|---:|---:|
| 55825 | 16931 | 481 | 67 | 59 |

## 3. ROWS Only

**Purpose:** Group by one row field and produce one total row per region.

```sql
PIVOT
FROM sales_data
ROWS region ASC NULLS LAST
VALUES
    SUM(revenue) AS "Revenue",
    SUM(profit) AS "Profit",
    COUNT(*) AS "Rows"
```

**Generated row totals query:**

```sql
SELECT region, SUM(revenue) AS Revenue, SUM(profit) AS Profit, COUNT(*) AS Rows
FROM sales_data
GROUP BY region
ORDER BY region ASC NULLS LAST
```

`TOTALS` is optional: with no `TOTALS` clause, `ROWS region` uses `SUM` at every level. `NULLS LAST` is also the default, so the explicit clause can be omitted.

**Result:**

| region | Revenue | Profit | Rows |
|---|---:|---:|---:|
| East | 5500 | 2050 | 6 |
| North | 39181 | 11236 | 48 |
| South | 11144 | 3645 | 13 |
| Grand Total | 55825 | 16931 | 67 |

## 4. COLUMNS Only

**Purpose:** Discover quarter headers without a row axis.

```sql
PIVOT
FROM sales_data
COLUMNS quarter ASC NULLS LAST
VALUES
    SUM(revenue) AS "Revenue",
    COUNT(*) AS "Rows"
```

**Generated column totals query:**

```sql
SELECT quarter, SUM(revenue) AS Revenue, COUNT(*) AS Rows
FROM sales_data
GROUP BY quarter
ORDER BY quarter ASC NULLS LAST
```

**Result:** one value row; the `Grand Total` row is the column total.

| Metric | Q1 | Q2 | Q3 | Q4 | Grand Total |
|---|---:|---:|---:|---:|---:|
| Revenue | 16211 | 16984 | 11023 | 11607 | 55825 |
| Rows | 20 | 19 | 14 | 14 | 67 |

## 5. ROWS and COLUMNS

**Purpose:** Build the base matrix; show missing intersections as `NULL`.

```sql
PIVOT
FROM sales_data
ROWS region ASC NULLS LAST
COLUMNS quarter ASC NULLS LAST
VALUES SUM(revenue) AS "Revenue"
```

**Result:**

| region | Q1 | Q2 | Q3 | Q4 | Total |
|---|---:|---:|---:|---:|---:|
| East | 2150 | 3350 | `NULL` | `NULL` | 5500 |
| North | 10468 | 10533 | 8904 | 9276 | 39181 |
| South | 3593 | 3101 | 2119 | 2331 | 11144 |
| Grand Total | 16211 | 16984 | 11023 | 11607 | 55825 |

The same matrix with `VALUES COUNT(*) AS "Rows"` is:

| region | Q1 | Q2 | Q3 | Q4 | Total |
|---|---:|---:|---:|---:|---:|
| East | 3 | 3 | `NULL` | `NULL` | 6 |
| North | 12 | 12 | 12 | 12 | 48 |
| South | 5 | 4 | 2 | 2 | 13 |
| Grand Total | 20 | 19 | 14 | 14 | 67 |

`East/Q3` and `East/Q4` are missing intersections, not zero. They stay `NULL` because Pivotante 5.0.0 has no empty-cell fallback.

## 6. Aggregates and Aliases

### 6.1 Additive and count aggregates

**Purpose:** Exercise the aggregates that can be consolidated by a `TOTALS` mode, plus the mandatory alias syntax.

```sql
PIVOT
FROM sales_data
ROWS region ASC NULLS LAST
VALUES
    SUM(revenue) AS "Revenue",
    SUM(profit) AS "Profit",
    SUM(orders) AS "Orders",
    COUNT(*) AS "Rows",
    COUNT(customer_id) AS "Customers",
    COUNT_DISTINCT(customer_id) AS "UniqueCustomers"
```

**Result:**

| region | Revenue | Profit | Orders | Rows | Customers | UniqueCustomers |
|---|---:|---:|---:|---:|---:|---:|
| East | 5500 | 2050 | 42 | 6 | 6 | 4 |
| North | 39181 | 11236 | 353 | 48 | 48 | 46 |
| South | 11144 | 3645 | 86 | 13 | 13 | 10 |
| Grand Total | 55825 | 16931 | 481 | 67 | 67 | 59 |

`COUNT(customer_id)` equals `COUNT(*)` here because the source has no `NULL` customer identifiers. `COUNT_DISTINCT(customer_id)` removes repeated customers.

### 6.2 Non-additive aggregates

**Purpose:** Use `AVG`, `MIN`, and `MAX` over the whole source, where no parent-total consolidation is involved.

```sql
PIVOT
FROM sales_data
VALUES
    AVG(revenue) AS "AvgRevenue",
    MIN(revenue) AS "MinRevenue",
    MAX(revenue) AS "MaxRevenue"
```

**Result:**

| AvgRevenue | MinRevenue | MaxRevenue |
|---:|---:|---:|
| 833.21 | 150 | 1400 |

Parent totals consolidate child totals; they are not a second aggregation over the union of source rows. For example, the per-region averages are East `916.67`, North `816.27`, and South `857.23`, but a `ROWS region` query would show `2590.17` as the grand total, not the overall `833.21`. Use the `AVG`, `MIN`, or `MAX` value aggregates without axes when the overall value is required; `FIRST` and `LAST` are `TOTALS` modes, not `VALUES` aggregates.

## 7. Directions and NULLS

**Purpose:** Apply `DESC` and explicit NULL placement to both axes.

```sql
PIVOT
FROM sales_data
ROWS region DESC NULLS LAST
COLUMNS quarter DESC NULLS FIRST
VALUES SUM(revenue) AS "Revenue"
```

**Generated detail query order:**

```sql
ORDER BY region DESC NULLS LAST, quarter DESC NULLS FIRST
```

**Result:**

| region | Q4 | Q3 | Q2 | Q1 | Total |
|---|---:|---:|---:|---:|---:|
| South | 2331 | 2119 | 3101 | 3593 | 11144 |
| North | 9276 | 8904 | 10533 | 10468 | 39181 |
| East | `NULL` | `NULL` | 3350 | 2150 | 5500 |
| Grand Total | 11607 | 11023 | 16984 | 16211 | 55825 |

The dataset has no `NULL` quarter keys, so `NULLS FIRST` does not change this matrix. Section 16 exercises an actual `NULL` key.

## 8. Nested ROWS and Subtotals

**Purpose:** Build a two-level row hierarchy with a subtotal row for every parent group.

```sql
PIVOT
FROM sales_data
ROWS
    region ASC NULLS LAST,
    city ASC NULLS LAST
VALUES
    SUM(revenue) AS "Revenue",
    COUNT(*) AS "Rows"
```

**Result:**

| region | city | Revenue | Rows |
|---|---|---:|---:|
| East | Laslu | 2250 | 3 |
| East | Molas | 1100 | 1 |
| East | Porca | 2150 | 2 |
| East Total | | 5500 | 6 |
| North | Alles | 12936 | 16 |
| North | Bences | 13210 | 16 |
| North | Desques | 13035 | 16 |
| North Total | | 39181 | 48 |
| South | Miro | 9844 | 11 |
| South | Sumi | 1300 | 2 |
| South Total | | 11144 | 13 |
| Grand Total | | 55825 | 67 |

Parent subtotal rows are placed after their child rows. The grand total is the sum of the top-level region totals.

## 9. Nested COLUMNS and Subtotals

**Purpose:** Build a two-level column hierarchy with a subtotal column per year.

```sql
PIVOT
FROM sales_data
ROWS region ASC NULLS LAST
COLUMNS
    fiscal_year ASC NULLS LAST,
    quarter ASC NULLS LAST
VALUES SUM(revenue) AS "Revenue"
```

**Result:**

| region | 2025 Q1 | 2025 Q2 | 2025 Q3 | 2025 Q4 | 2025 Total | 2026 Q1 | 2026 Q2 | 2026 Q3 | 2026 Q4 | 2026 Total | Total |
|---|---:|---:|---:|---:|---:|---:|---:|---:|---:|---:|---:|
| East | 150 | `NULL` | `NULL` | `NULL` | 150 | 2000 | 3350 | `NULL` | `NULL` | 5350 | 5500 |
| North | 4636 | 4985 | 4743 | 4479 | 18843 | 5832 | 5548 | 4161 | 4797 | 20338 | 39181 |
| South | 1195 | 1551 | 1033 | 2331 | 6110 | 2398 | 1550 | 1086 | `NULL` | 5034 | 11144 |
| Grand Total | 5981 | 6536 | 5776 | 6810 | 25103 | 10230 | 10448 | 5247 | 4797 | 30722 | 55825 |

The year subtotal columns use `SUM` because section 9 has no `TOTALS` clause and every level defaults to `SUM`. Missing year/quarter combinations stay `NULL`.

## 10. Total Modes in TOTALS

### 10.1 COUNT on rows

**Purpose:** Count city groups instead of adding revenue at the region level.

```sql
PIVOT
FROM sales_data
ROWS
    region ASC NULLS LAST,
    city DESC
WHERE fiscal_year = 2026
VALUES SUM(revenue) AS "Revenue"
TOTALS
    ROWS (COUNT(region), SUM(city))
```

**Result:** leaf city values are revenue; parent region values are the number of city groups with a non-NULL Revenue total.

| region | city | Revenue |
|---|---|---:|
| East | Porca | 2150 |
| East | Molas | 1100 |
| East | Laslu | 2100 |
| East Total | | 3 |
| North | Desques | 6422 |
| North | Bences | 6816 |
| North | Alles | 7100 |
| North Total | | 3 |
| South | Sumi | 1300 |
| South | Miro | 3734 |
| South Total | | 2 |
| Grand Total | | 3 |

### 10.2 COUNT on columns

**Purpose:** Count quarter groups at the year level. The table shows only the year subtotal columns and the row total; the base quarter columns are the same as in section 9.

```sql
PIVOT
FROM sales_data
ROWS region ASC NULLS LAST
COLUMNS
    fiscal_year ASC NULLS LAST,
    quarter ASC NULLS LAST
VALUES SUM(revenue) AS "Revenue"
TOTALS
    COLUMNS (COUNT(fiscal_year), SUM(quarter))
```

**Result, compact view:**

| region | 2025 Total (count) | 2026 Total (count) | Revenue Total |
|---|---:|---:|---:|
| East | 1 | 2 | 5500 |
| North | 4 | 4 | 39181 |
| South | 4 | 3 | 11144 |
| Grand Total | 4 | 4 | 55825 |

The year totals count the quarter groups with a non-NULL Revenue total. Under `2025 Total` and `2026 Total`, the bottom row shows `4` because the parent column total also uses the `COUNT` rule over its four quarter columns. The final column is the normal row total, and its bottom-right cell is the row-side grand total (`55825`). `COUNT(field)` in `TOTALS` is not a monetary total; use `SUM(field)` when the total must add money.

### 10.3 AVG, MIN, MAX, FIRST and LAST

**Purpose:** Consolidate child totals with a total mode other than SUM or COUNT. The mode is declared in `TOTALS` on the parent level, so `region` needs a deeper `city` field.

```sql
PIVOT
FROM sales_data
ROWS
    region ASC NULLS LAST,
    city ASC NULLS LAST
VALUES SUM(revenue) AS "Revenue"
TOTALS
    ROWS (AVG(region), SUM(city))
```

The region total is the average of its city totals. Replacing `AVG(region)` with the other parent-only functions gives:

| Region-level `TOTALS` item | East | North | South | Grand Total |
|---|---:|---:|---:|---:|
| `AVG(region)` | 1833.33 | 13060.33 | 5572.00 | 6821.89 |
| `MIN(region)` | 1100 | 12936 | 1300 | 1100 |
| `MAX(region)` | 2250 | 13210 | 9844 | 13210 |
| `FIRST(region)` | 2250 | 12936 | 9844 | 2250 |
| `LAST(region)` | 2150 | 13035 | 1300 | 1300 |

`FIRST` and `LAST` use the final order of the child groups and skip NULL child totals. With `city ASC`, `East` uses `Laslu`, `Molas`, `Porca`, so its first city total is `2250` and its last is `2150`. The grand total applies the same function to the top-level region totals. The same modes are valid on a parent `COLUMNS` field, for example `FIRST(fiscal_year)` with `SUM(quarter)` below it.


## 11. HAVING on ROWS

**Purpose:** Keep only row groups whose total satisfies a condition.

```sql
PIVOT
FROM sales_data
ROWS
    region ASC NULLS LAST HAVING SUM(revenue) >= 20000
COLUMNS quarter ASC NULLS LAST
VALUES SUM(revenue) AS "Revenue"
```

Only `North` has a region total of at least `20000`.

**Result:**

| region | Q1 | Q2 | Q3 | Q4 | Total |
|---|---:|---:|---:|---:|---:|
| North | 10468 | 10533 | 8904 | 9276 | 39181 |
| Grand Total | 10468 | 10533 | 8904 | 9276 | 39181 |

`East` and `South` disappear from the matrix, the column totals and the grand total.

## 12. HAVING on COLUMNS

**Purpose:** Keep only column groups whose total satisfies a condition.

```sql
PIVOT
FROM sales_data
ROWS region ASC NULLS LAST
COLUMNS
    quarter ASC NULLS LAST HAVING SUM(revenue) >= 12000
VALUES SUM(revenue) AS "Revenue"
```

Only `Q1` (`16211`) and `Q2` (`16984`) pass the condition.

**Result:**

| region | Q1 | Q2 | Total |
|---|---:|---:|---:|
| East | 2150 | 3350 | 5500 |
| North | 10468 | 10533 | 21001 |
| South | 3593 | 3101 | 6694 |
| Grand Total | 16211 | 16984 | 33195 |

Row totals are recalculated without `Q3` and `Q4`. The grand total drops from `55825` to `33195`.

## 13. HAVING on a Nested Field

**Purpose:** Filter the deepest row level and recalculate its parents.

```sql
PIVOT
FROM sales_data
ROWS
    region ASC NULLS LAST,
    city ASC NULLS LAST HAVING SUM(revenue) >= 3000
WHERE fiscal_year = 2026
VALUES SUM(revenue) AS "Revenue"
```

2026 city totals below `3000` are removed: `Laslu` (`2100`), `Molas` (`1100`), `Porca` (`2150`) and `Sumi` (`1300`). `East` disappears because all of its cities are removed.

**Result:**

| region | city | Revenue |
|---|---|---:|
| North | Alles | 7100 |
| North | Bences | 6816 |
| North | Desques | 6422 |
| North Total | | 20338 |
| South | Miro | 3734 |
| South Total | | 3734 |
| Grand Total | | 24072 |

## 14. HAVING with AND, OR and NOT

**Purpose:** Combine total predicates.

```sql
PIVOT
FROM sales_data
ROWS
    region ASC NULLS LAST
    HAVING SUM(revenue) >= 20000 OR COUNT(*) < 10
COLUMNS quarter ASC NULLS LAST
VALUES SUM(revenue) AS "Revenue"
```

`North` passes the revenue condition. `East` passes the row-count condition (`6 < 10`). `South` fails both.

**Result:**

| region | Q1 | Q2 | Q3 | Q4 | Total |
|---|---:|---:|---:|---:|---:|
| East | 2150 | 3350 | `NULL` | `NULL` | 5500 |
| North | 10468 | 10533 | 8904 | 9276 | 39181 |
| Grand Total | 12618 | 13883 | 8904 | 9276 | 44681 |

Other forms, with the same data:

| HAVING | Regions retained |
|---|---|
| `SUM(revenue) >= 10000 AND COUNT(*) >= 10` | North, South |
| `NOT (SUM(revenue) < 20000)` | North |
| `SUM(revenue) >= 20000 OR NOT (COUNT(*) < 10)` | North, South |

`HAVING` can only compare aggregate calls that appear in `VALUES`; it never sees raw source rows.

## 15. WHERE Predicates

**Purpose:** Exercise pre-aggregation filters. `WHERE` runs before grouping and before `HAVING`.

```sql
PIVOT
FROM sales_data
ROWS region ASC NULLS LAST
COLUMNS quarter ASC NULLS LAST
WHERE fiscal_year = 2026
  AND region IN ('North', 'East')
  AND revenue BETWEEN 500 AND 1200
VALUES SUM(revenue) AS "Revenue"
```

**Result:**

| region | Q1 | Q2 | Q3 | Q4 | Total |
|---|---:|---:|---:|---:|---:|
| East | 2000 | 2100 | `NULL` | `NULL` | 4100 |
| North | 4536 | 2851 | 4161 | 4797 | 16345 |
| Grand Total | 6536 | 4951 | 4161 | 4797 | 20445 |

Other predicate forms over the same source:

| Filter | Rows | Revenue | Result by region |
|---|---:|---:|---|
| `city LIKE 'B%'` | 16 | 13210 | North only |
| `order_date BETWEEN '2026-01-01' AND '2026-03-31'` | 11 | 10230 | East 2000, North 5832, South 2398 |
| `NOT (region = 'North')` | 19 | 16644 | East 5500, South 11144 |
| `customer_id IS NOT NULL` | 67 | 55825 | All regions |
| `customer_id IS NULL` | 0 | `NULL` | Empty matrix |

`FALSE` and `NULL` predicates exclude rows. An empty result is valid and produces no headers.

## 16. NULL Axis Keys

**Purpose:** Show that a `NULL` key is retained and labeled `(blank)`. The normalized dataset has no `NULL` keys, so this example uses a derived source built from four real orders; it is marked as a fixture, not part of the 3NF data.

```sql
PIVOT
FROM (
    SELECT order_id, region, city, quarter, revenue
    FROM sales_data
    WHERE order_id IN (1, 2, 3, 4)
    UNION
    SELECT order_id, region, NULL AS city, quarter, revenue
    FROM sales_data
    WHERE order_id IN (1, 2)
) AS sales_null_demo
ROWS region ASC NULLS LAST
COLUMNS city ASC NULLS LAST
VALUES SUM(revenue) AS "Revenue"
```

**Result:**

| region | Alles | Bences | Desques | (blank) | Total |
|---|---:|---:|---:|---:|---:|
| North | 2150 | 1400 | 800 | 2600 | 6950 |
| Grand Total | 2150 | 1400 | 800 | 2600 | 6950 |

The `(blank)` header is a real key. `NULLS LAST` places it after `Desques`; `NULLS FIRST` places it before `Alles`. The derived rows duplicate orders `1` and `2` to create the `NULL` city key, so this table is not a financial total of the original dataset.

## 17. Datilandia Dialect

**Purpose:** Use bracketed identifiers, string aliases and the `COUNT(DISTINCT ...)` spelling.

```sql
PIVOT
FROM [sales_data] AS s
ROWS s.[region] ASC NULLS LAST
VALUES
    COUNT(DISTINCT s.[customer_id]) AS 'Customers',
    COUNT_DISTINCT(s.[customer_id]) AS 'Customers2'
```

**Result:** both spellings are identical.

| region | Customers | Customers2 |
|---|---:|---:|
| East | 4 | 4 |
| North | 46 | 46 |
| South | 10 | 10 |
| Grand Total | 59 | 59 |

A derived table must have an alias:

```sql
PIVOT
FROM (
    SELECT region, quarter, revenue, fiscal_year
    FROM sales_data
    WHERE fiscal_year = 2026
) AS y2026
ROWS y2026.region ASC NULLS LAST
COLUMNS y2026.quarter ASC NULLS LAST
VALUES SUM(y2026.revenue) AS "Revenue"
```

This is equivalent to section 5 restricted to 2026. In Datilandia, `[brackets]` delimit identifiers and both quote characters delimit display strings.

## 18. Double ROWS and Double COLUMNS (Default SUM Totals)

**Purpose:** Combine two `ROWS` levels and two `COLUMNS` levels in one matrix. `TOTALS` is omitted, so every level uses `SUM`.

```sql
PIVOT
FROM sales_data
ROWS
    region ASC NULLS LAST,
    city ASC NULLS LAST
COLUMNS
    fiscal_year ASC NULLS LAST,
    quarter ASC NULLS LAST
VALUES SUM(revenue) AS "Revenue"
```

**Result:**

| region | city | 2025 Q1 | 2025 Q2 | 2025 Q3 | 2025 Q4 | 2025 Total | 2026 Q1 | 2026 Q2 | 2026 Q3 | 2026 Q4 | 2026 Total | Total |
|---|---|---:|---:|---:|---:|---:|---:|---:|---:|---:|---:|---:|
| East | Laslu | 150 | `NULL` | `NULL` | `NULL` | 150 | 1100 | 1000 | `NULL` | `NULL` | 2100 | 2250 |
| East | Molas | `NULL` | `NULL` | `NULL` | `NULL` | `NULL` | `NULL` | 1100 | `NULL` | `NULL` | 1100 | 1100 |
| East | Porca | `NULL` | `NULL` | `NULL` | `NULL` | `NULL` | 900 | 1250 | `NULL` | `NULL` | 2150 | 2150 |
| East Total | | 150 | `NULL` | `NULL` | `NULL` | 150 | 2000 | 3350 | `NULL` | `NULL` | 5350 | 5500 |
| North | Alles | 1279 | 1257 | 1544 | 1756 | 5836 | 1978 | 1610 | 1650 | 1862 | 7100 | 12936 |
| North | Bences | 1757 | 1969 | 1228 | 1440 | 6394 | 1863 | 2073 | 1334 | 1546 | 6816 | 13210 |
| North | Desques | 1600 | 1759 | 1971 | 1283 | 6613 | 1991 | 1865 | 1177 | 1389 | 6422 | 13035 |
| North Total | | 4636 | 4985 | 4743 | 4479 | 18843 | 5832 | 5548 | 4161 | 4797 | 20338 | 39181 |
| South | Miro | 1195 | 1551 | 1033 | 2331 | 6110 | 1798 | 850 | 1086 | `NULL` | 3734 | 9844 |
| South | Sumi | `NULL` | `NULL` | `NULL` | `NULL` | `NULL` | 600 | 700 | `NULL` | `NULL` | 1300 | 1300 |
| South Total | | 1195 | 1551 | 1033 | 2331 | 6110 | 2398 | 1550 | 1086 | `NULL` | 5034 | 11144 |
| Grand Total | | 5981 | 6536 | 5776 | 6810 | 25103 | 10230 | 10448 | 5247 | 4797 | 30722 | 55825 |

The `region` and `city` levels both consolidate with `SUM`; the `fiscal_year` and `quarter` levels do the same. Missing city/year combinations remain `NULL`. The grand total is `55825`.

## 19. Invalid Queries

| Invalid statement | Expected code | Phase | Reason |
|---|---|---|---|
| `PIVOT FROM sales_data VALUES SUM(revenue)` | `SYNTAX_ERROR` | `PARSE` | Every `VALUES` item needs an alias. |
| `PIVOT FROM sales_data ROWS region VALUES SUM(revenue) AS "R", SUM(profit) AS "R"` | `DUPLICATE_ALIAS` | `VALIDATE` | Aliases must be unique. |
| `PIVOT FROM sales_data ROWS region GROUP BY region VALUES SUM(revenue) AS "R"` | `SYNTAX_ERROR` | `PARSE` | `GROUP BY` is not part of Pivotante. |
| `PIVOT FROM sales_data ROWS SUM(region) VALUES SUM(revenue) AS "R"` | `SYNTAX_ERROR` | `PARSE` | Functions are not allowed in `ROWS`; use `TOTALS`. |
| `PIVOT FROM sales_data ROWS region, city TOTALS ROWS (SUM(region)) VALUES SUM(revenue) AS "R"` | `LAYOUT_ERROR` | `VALIDATE` | A `TOTALS` axis block must list every field of that axis. |
| `PIVOT FROM sales_data ROWS region TOTALS COLUMNS (SUM(region)) VALUES SUM(revenue) AS "R"` | `LAYOUT_ERROR` | `VALIDATE` | `TOTALS COLUMNS` requires a `COLUMNS` clause. |
| `PIVOT FROM sales_data ROWS region, city TOTALS ROWS (SUM(region), SUM(region)) VALUES SUM(revenue) AS "R"` | `SYNTAX_ERROR` | `PARSE` | A `TOTALS` field may appear only once. |
| `PIVOT FROM sales_data ROWS region TOTALS ROWS (SUM(region), SUM(city)) VALUES SUM(revenue) AS "R"` | `UNKNOWN_FIELD` | `VALIDATE` | `city` is not a field of `ROWS`. |
| `PIVOT FROM sales_data ROWS region TOTALS AS SUM VALUES SUM(revenue) AS "R"` | `SYNTAX_ERROR` | `PARSE` | The old `TOTALS AS` syntax was removed; use `TOTALS ROWS (...)` or `TOTALS COLUMNS (...)`. |
| `PIVOT FROM sales_data ROWS region COLUMNS quarter VALUES SUM(revenue) AS "R" EMPTY AS 0` | `SYNTAX_ERROR` | `PARSE` | `EMPTY AS` was removed. |
| `PIVOT FROM sales_data ROWS region COLUMNS quarter VALUES SUM(revenue) AS "R" MAX COLUMNS 3` | `SYNTAX_ERROR` | `PARSE` | `MAX COLUMNS` was removed. |
| `PIVOT FROM sales_data ROWS region TOTALS ROWS (COUNT(region)) VALUES SUM(revenue) AS "R"` | `LAYOUT_ERROR` | `VALIDATE` | Only `SUM(field)` is valid on the deepest field. |
| `PIVOT FROM sales_data ROWS region TOTALS ROWS (AVG(region)) VALUES SUM(revenue) AS "R"` | `LAYOUT_ERROR` | `VALIDATE` | Only `SUM(field)` is valid on the deepest field. |
| `PIVOT FROM sales_data VALUES SUM(AVG(revenue)) AS "X"` | `SYNTAX_ERROR` | `PARSE` | An aggregate argument must be a field identifier. |
| `PIVOT FROM sales_data WHERE SUM(revenue) > 0 VALUES COUNT(*) AS "Rows"` | `TYPE_ERROR` | `VALIDATE` | Aggregates are not allowed in `WHERE`. |
| `PIVOT FROM sales_data ROWS region HAVING SUM(profit) > 0 VALUES SUM(revenue) AS "R"` | `UNKNOWN_FIELD` | `VALIDATE` | The `HAVING` aggregate must exist in `VALUES`. |
| `PIVOT FROM sales_data ROWS region COLUMNS quarter VALUES SUM(revenue) AS "R" HAVING SUM(revenue) > 0` | `SYNTAX_ERROR` | `PARSE` | `HAVING` belongs to a `ROWS` or `COLUMNS` field. |
| `PIVOT FROM sales_data ROWS DATE_BUCKET(order_date, 'MONTH') VALUES COUNT(*) AS "Rows"` | `SYNTAX_ERROR` | `PARSE` | `DATE_BUCKET` was removed; use the source field directly. |
| `PIVOT FROM sales_data VALUES CALC(SUM(revenue)) AS "X"` | `SYNTAX_ERROR` | `PARSE` | `CALC` was removed. |
| `PIVOT FROM sales_data ROWS region VALUES DIFF_PREV(SUM(revenue), region) AS "X"` | `SYNTAX_ERROR` | `PARSE` | `DIFF_PREV` was removed. |
| `PIVOT FROM sales_data VALUES SUM(revenue) AS "R" ROWS region` | `SYNTAX_ERROR` | `PARSE` | Clause order is `PIVOT`, `FROM`, `ROWS`, `COLUMNS`, `WHERE`, `VALUES`, `TOTALS`. |
| `PIVOT FROM sales_data ROWS unknown_field VALUES SUM(revenue) AS "R"` | `UNKNOWN_FIELD` | `VALIDATE` | The field does not exist in the source. |
| `PIVOT FROM sales_data ROWS region HAVING SUM(revenue) > 'x' VALUES SUM(revenue) AS "R"` | `TYPE_ERROR` | `VALIDATE` | The `HAVING` literal is not numeric. |

## 20. Conformance Test Cases

| Case | Query | Expected |
|---|---|---|
| No axes | Section 2 query | 55825 revenue, 67 rows, 59 customers |
| ROWS only | Section 3 query | East 5500, North 39181, South 11144, grand 55825 |
| COLUMNS only | Section 4 query | Q1 16211, Q2 16984, Q3 11023, Q4 11607, grand 55825 |
| Matrix | Section 5 query | East Q1 2150, East Q2 3350, East Q3 `NULL`, grand 55825 |
| Additive aggregates | Section 6.1 query | North unique customers 46, grand unique customers 59 |
| Non-additive aggregates | Section 6.2 query | Avg 833.21, min 150, max 1400 |
| Directions | Section 7 query | South first, Q4 first, grand 55825 |
| Nested rows | Section 8 query | North Total 39181, East Total 5500, grand 55825 |
| Nested columns | Section 9 query | 2025 Total 25103, 2026 Total 30722, grand 55825 |
| COUNT rows | Section 10.1 query | East Total 3, North Total 3, South Total 2, grand 3 |
| COUNT columns | Section 10.2 query | East 1/2, North 4/4, South 4/3, grand 55825 |
| HAVING rows | Section 11 query | Only North, grand 39181 |
| HAVING columns | Section 12 query | Only Q1/Q2, grand 33195 |
| HAVING nested | Section 13 query | East removed, North 20338, South 3734, grand 24072 |
| HAVING OR | Section 14 query | East and North, grand 44681 |
| WHERE compound | Section 15 query | East 4100, North 16345, grand 20445 |
| NULL key | Section 16 query | `(blank)` column 2600, grand 6950 |
| COUNT_DISTINCT | Section 17 query | East 4, North 46, South 10, grand 59 |
| Other total modes | Section 10.3 | AVG 1833.33/13060.33/5572.00/6821.89; MIN/MAX/FIRST/LAST as shown |
| Double rows and columns | Section 18 query | East Total 5500, North Total 39181, 2025 Total 25103, 2026 Total 30722, grand 55825 |
| Missing alias | `VALUES SUM(revenue)` | `SYNTAX_ERROR` |
| Duplicate alias | Two items named `"R"` | `DUPLICATE_ALIAS` |
| COUNT on deepest | `ROWS region TOTALS ROWS (COUNT(region))` | `LAYOUT_ERROR` |
| AVG on deepest | `ROWS region TOTALS ROWS (AVG(region))` | `LAYOUT_ERROR` |
| Incomplete TOTALS block | `ROWS region, city TOTALS ROWS (SUM(region))` | `LAYOUT_ERROR` |
| TOTALS for absent axis | `ROWS region TOTALS COLUMNS (SUM(region))` | `LAYOUT_ERROR` |
| Duplicate TOTALS field | `TOTALS ROWS (SUM(region), SUM(region))` | `SYNTAX_ERROR` |
| Unknown TOTALS field | `ROWS region TOTALS ROWS (SUM(region), SUM(city))` | `UNKNOWN_FIELD` |
| GROUP BY | Any query with `GROUP BY` | `SYNTAX_ERROR` |
| Aggregate in WHERE | `WHERE SUM(revenue) > 0` | `TYPE_ERROR` |

## 21. Checklist

- The statement starts with `PIVOT` and follows `PIVOT`, `FROM`, `ROWS`, `COLUMNS`, `WHERE`, `VALUES`, `TOTALS`.
- `ROWS` and `COLUMNS` contain only grouping fields, direction, `NULLS`, and `HAVING`; they contain no aggregation functions.
- There is no `GROUP BY`, `EMPTY AS`, `MAX COLUMNS`, `WITH SUBTOTALS`, `CALC`, `DIFF_PREV`, or `DATE_BUCKET`.
- Every `VALUES` item has a unique explicit alias.
- If `TOTALS` is omitted, every axis level uses `SUM`.
- If a `TOTALS` axis block is present, it lists every field of that axis exactly once.
- Only `SUM(field)` is used on the deepest field; `COUNT`, `AVG`, `MIN`, `MAX`, `FIRST`, and `LAST` are used only on parent levels.
- Every `HAVING` aggregate call appears in `VALUES`.
- `WHERE` contains no aggregates.
- In Datilandia, identifiers use `[brackets]` and display aliases use strings.
