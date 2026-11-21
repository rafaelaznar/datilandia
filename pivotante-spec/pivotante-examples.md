# Pivotante: Example Queries

This document contains practical Pivotante v1.0 queries. Names shown as `sales_data`, `region`, and `quarter` are illustrative. Each example follows the canonical clause order and uses quoted aliases for output names.

## Conventions

- Use unquoted identifiers for names without spaces or reserved words.
- Use double-quoted identifiers for names containing spaces or reserved words.
- Use single quotes for string literals.
- Escape a quote by doubling it: `'North''s'`.
- Use `--` for a single-line comment and `/* ... */` for a block comment.
- The canonical clause order is `FROM`, `FILTER`, `ROWS`, `COLUMNS`, `VALUES`, `TOTALS`, and `EMPTY AS`.
- Keyword names are case-insensitive. Field names are case-insensitive when unquoted and case-sensitive when quoted.
- `NULL` is not zero and does not participate numerically in aggregates.

## Index

1. [Basic query](#1-basic-query)
2. [Source and subquery](#2-source-and-subquery)
3. [Boolean filters](#3-boolean-filters)
4. [Row hierarchy](#4-row-hierarchy)
5. [Subtotals](#5-subtotals)
6. [Row sorting](#6-row-sorting)
7. [Dynamic columns](#7-dynamic-columns)
8. [Grouping column values](#8-grouping-column-values)
9. [Aggregations](#9-aggregations)
10. [Output aliases](#10-output-aliases)
11. [Percentage of row](#11-percentage-of-row)
12. [Percentage of column](#12-percentage-of-column)
13. [Percentage of total](#13-percentage-of-total)
14. [Date bucketing](#14-date-bucketing)
15. [Calculated fields](#15-calculated-fields)
16. [Period variance](#16-period-variance)
17. [Totals and empty cells](#17-totals-and-empty-cells)

## 1. Basic Query

**Purpose:** Return yearly revenue grouped by region.

```sql
FROM sales_data
ROWS region
VALUES SUM(revenue) AS "Revenue"
```

**Result concept:** One row per region and one `Revenue` value per region.

## 2. Source and Subquery

**Purpose:** Read from a named table or wrap an SQL subquery as the source.

```sql
FROM sales_data
ROWS region
VALUES COUNT(*) AS "Orders"
```

```sql
FROM (
  SELECT region, revenue, order_status
  FROM raw_orders
  WHERE order_status = 'Completed'
)
ROWS region
VALUES SUM(revenue) AS "Completed Revenue"
```

**Result concept:** A subquery source behaves as the filtered tabular dataset.

## 3. Boolean Filters

**Purpose:** Apply comparisons and three-valued boolean logic before aggregation.

```sql
FROM sales_data
FILTER fiscal_year = 2026
  AND region IN ('North', 'East')
  AND customer_id IS NOT NULL
ROWS region
VALUES SUM(revenue) AS "Revenue"
```

**Result concept:** Only matching non-null customer rows contribute to revenue.

## 4. Row Hierarchy

**Purpose:** Create nested left-to-right row levels.

```sql
FROM sales_data
ROWS region, category, product_name
VALUES SUM(revenue) AS "Revenue"
```

**Result concept:** `region` is the outer level, `category` is nested beneath it, and `product_name` is the deepest level.

## 5. Subtotals

**Purpose:** Insert region and category subtotal rows.

```sql
FROM sales_data
ROWS region WITH SUBTOTALS, category WITH SUBTOTALS
VALUES SUM(revenue) AS "Revenue"
TOTALS ROWS
```

**Result concept:** Every nested group level except the deepest level emits a subtotal; `TOTALS ROWS` enables the row grand total.

## 6. Row Sorting

**Purpose:** Sort nested row values in descending category order.

```sql
FROM sales_data
ROWS region, category ORDER BY category DESC
VALUES SUM(revenue) AS "Revenue"
```

**Result concept:** Categories are ordered descending within each region. NULL keys follow the documented NULL sorting rule.

## 7. Dynamic Columns

**Purpose:** Discover quarter values and use them as horizontal headers.

```sql
FROM sales_data
ROWS region
COLUMNS quarter ORDER BY quarter ASC
VALUES SUM(revenue) AS "Revenue"
TOTALS BOTH
EMPTY AS 0
```

**Result concept:** The engine discovers all quarter headers after applying `FILTER`, if present.

## 8. Grouping Column Values

**Purpose:** Prevent a high-cardinality product dimension from creating too many columns.

```sql
FROM sales_data
ROWS region
COLUMNS product_name
  MAX COLUMNS 10
  GROUP REST AS "Other Products"
  ORDER BY product_name ASC
VALUES SUM(revenue) AS "Revenue"
```

**Result concept:** The ten retained products use their own headers; all remaining products share `Other Products`.

## 9. Aggregations

**Purpose:** Combine standard aggregates in one matrix.

```sql
FROM sales_data
ROWS region
COLUMNS quarter
VALUES
  SUM(revenue) AS "Revenue",
  AVG(revenue) AS "Average Revenue",
  COUNT(*) AS "Orders",
  COUNT_DISTINCT(customer_id) AS "Unique Customers",
  MIN(revenue) AS "Minimum",
  MAX(revenue) AS "Maximum"
```

**Result concept:** One table contains six metrics for every region-quarter intersection.

## 10. Output Aliases

**Purpose:** Give every result item a stable, unique display name.

```sql
FROM sales_data
ROWS region
VALUES
  SUM(revenue) AS "Net Sales",
  SUM(profit) AS "Gross Profit",
  SUM(revenue) AS % OF ROW AS "Revenue Share"
```

**Result concept:** The three output names are unique and suitable for display.

## 11. Percentage of Row

**Purpose:** Express each cell as a portion of its horizontal row.

```sql
FROM sales_data
ROWS region
COLUMNS quarter
VALUES SUM(revenue) AS % OF ROW AS "Row Percentage"
EMPTY AS NULL
```

**Result concept:** For a region, all quarter percentages sum to the row total conceptually; a missing or zero denominator yields `NULL`.

## 12. Percentage of Column

**Purpose:** Express each region value as a portion of its quarter column.

```sql
FROM sales_data
ROWS region
COLUMNS quarter
VALUES SUM(revenue) AS % OF COLUMN AS "Column Percentage"
TOTALS COLUMNS
```

**Result concept:** Each cell divides by the corresponding discovered quarter column total.

## 13. Percentage of Total

**Purpose:** Express each intersection against the complete matrix.

```sql
FROM sales_data
ROWS region
COLUMNS quarter
VALUES SUM(revenue) AS % OF TOTAL AS "Grand-Total Percentage"
TOTALS BOTH
```

**Result concept:** The denominator is the grand total across all discovered region and quarter combinations.

## 14. Date Bucketing

**Purpose:** Convert order timestamps into quarterly or monthly headers and row levels.

```sql
FROM enterprise_sales
ROWS region
COLUMNS DATE_BUCKET(order_date, 'QUARTER') ORDER BY 1 ASC
VALUES SUM(amount) AS "Sales"
EMPTY AS 0
```

```sql
FROM enterprise_sales
ROWS DATE_BUCKET(order_date, 'MONTH') ORDER BY 1 ASC
VALUES COUNT(*) AS "Orders"
```

**Result concept:** Raw timestamps become discrete calendar buckets before grouping.

## 15. Calculated Fields

**Purpose:** Calculate revenue margin after aggregation.

```sql
FROM enterprise_sales
ROWS region
COLUMNS quarter
VALUES
  SUM(revenue) AS "Revenue",
  SUM(profit) AS "Profit",
  CALC(SUM(profit) / SUM(revenue)) AS "Margin",
  CALC(SUM(revenue) * 1.21) AS "Revenue incl. 21%"
TOTALS BOTH
EMPTY AS 0
```

**Result concept:** `CALC` runs after aggregation. A zero denominator produces `NULL`, not infinity.

## 16. Period Variance

**Purpose:** Compare a metric with the preceding discovered column.

```sql
FROM sales_data
ROWS region
COLUMNS quarter ORDER BY quarter ASC
VALUES
  SUM(revenue) AS "Revenue",
  DIFF_PREV(SUM(revenue), quarter) AS "Quarterly Change"
TOTALS COLUMNS
```

**Result concept:** The first quarter has `NULL` variance. Later values subtract the preceding quarter revenue.

## 17. Totals and Empty Cells

**Purpose:** Demonstrate total axes and the distinction between no source rows and explicit empty fallback.

```sql
FROM sales_data
FILTER order_status = 'Completed'
ROWS region WITH SUBTOTALS
COLUMNS quarter ORDER BY quarter ASC
VALUES SUM(revenue) AS "Revenue"
TOTALS BOTH
EMPTY AS 0
```

**Result concept:** Both axes and the grand total are included. A materialized row-column intersection without source rows receives `0`; aggregate `SUM` for an underlying empty set remains `NULL` until `EMPTY AS` is applied.

## Additional NULL Example

**Purpose:** NULL can be tested explicitly in filters.

```sql
FROM sales_data
FILTER region IS NULL OR region = 'North'
ROWS region
VALUES COUNT(region) AS "Non-NULL Region Count"
```

**Result concept:** `COUNT(region)` excludes NULL values, while `COUNT(*)` counts source rows regardless of NULL fields.

## Comments Example

**Purpose:** Comments can explain or temporarily document parts of a query.

```sql
-- Exclude refunds from the source
FROM sales_data
/* The report only covers the year in scope. */
FILTER fiscal_year = 2026
ROWS region
VALUES SUM(revenue) AS "Revenue"
```

## Conformance Checklist

Before executing an example, verify:

- Clauses appear in canonical order.
- `FROM` and `VALUES` are present.
- Every explicit output alias is unique.
- `FILTER` expressions evaluate to boolean.
- `MAX COLUMNS` uses a positive integer.
- `DIFF_PREV` references a `ROWS` or `COLUMNS` axis field.
- `EMPTY AS` is compatible with the result value type.
