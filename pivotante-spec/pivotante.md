# Pivotante Language Specification

**Version:** 5.0.0
**Status:** Normative specification (core + Datilandia integration)
**Scope:** Pivotante core dialect, Datilandia integration, and the 3NF data source
**Companion:** `pivotante-examples.md` contains the embedded `sales_data` CSV, worked examples, expected matrices, invalid queries, and conformance fixtures.

## Language status

This document specifies Pivotante v5.0.0. It supersedes v4.1.0 and separates grouping fields from total modes: `ROWS` and `COLUMNS` only describe the header groupings, and an optional `TOTALS` clause declares how each level is consolidated.

Pivotante is a declarative query language for Excel-style pivot tables. A Pivotante query compiles to up to three SQL queries:

1. the detail query, grouped by every `ROWS` and `COLUMNS` field;
2. the row totals query, grouped by every `ROWS` field;
3. the column totals query, grouped by every `COLUMNS` field.

The pivot engine executes those queries and assembles the final matrix, including row totals, column totals, and the grand total.

## Normative keywords

The keywords **MUST**, **SHOULD**, and **MAY** are to be interpreted as normative requirements, recommendations, and permitted options, respectively.

## Conformance

A conforming engine **MUST** preserve the canonical clause order, the inferred grouping rules, the `TOTALS` rules, the `HAVING` rules, the NULL behavior, and the alias uniqueness rules defined in this specification.

## 1. Canonical Syntax

Every Pivotante query starts with `PIVOT` and follows this exact clause order:

```sql
PIVOT
FROM <source>
[ROWS <row_field_list>]
[COLUMNS <column_field_list>]
[WHERE <boolean_expression>]
VALUES <value_list>
[TOTALS
  [ROWS (totals_item_list)]
  [COLUMNS (totals_item_list)]]
```

`PIVOT`, `FROM`, and `VALUES` are required. `ROWS`, `COLUMNS`, `WHERE`, and `TOTALS` are optional. `ROWS` and `COLUMNS` MAY both be omitted; in that case the result is a single aggregate row with no axes. `TOTALS` is optional; when it is absent, every axis level uses `SUM` by default.

`GROUP BY` is **not** part of Pivotante. Grouping is inferred from the `ROWS` and `COLUMNS` fields. An engine **MUST** reject a `GROUP BY` clause with `SYNTAX_ERROR`.

Reference query:

```sql
PIVOT
FROM sales_data
ROWS
    region ASC NULLS LAST,
    city DESC
COLUMNS
    fiscal_year,
    customer_id DESC
WHERE orders > 8
VALUES
    SUM(revenue) AS "Revenue",
    COUNT(category) AS "Cats"
TOTALS
    COLUMNS (COUNT(fiscal_year), SUM(customer_id))
```

## 2. Lexical Elements

- **Identifiers:** unquoted (`region`, `sales_amount`) or double-quoted (`"Region Name"`) for names containing spaces or reserved keywords.
- **Keywords:** case-insensitive. `from`, `FROM`, and `From` have the same meaning.
- **Unquoted identifiers:** case-insensitive for matching. `Region`, `region`, and `REGION` identify the same field.
- **Quoted identifiers:** case-preserving and case-sensitive for matching. Escape a double quote by doubling it: `"Customer ""Tier"""`.
- **Strings:** enclosed in single quotes (`'Completed'`, `'North'`). Escape a single quote by doubling it: `'North''s'`.
- **Numbers:** integer (`2026`) or decimal (`19.99`).
- **Booleans:** `TRUE`, `FALSE`.
- **Nulls:** `NULL`.
- **Comments:** `--` starts a single-line comment; `/* ... */` is a block comment. Block comments do not nest.

## 3. Grammar

The following EBNF is the authoritative syntax for Pivotante v5.0.0. Quoted text denotes a case-insensitive keyword or a punctuation terminal. `[...]` denotes an optional element, `{...}` denotes zero or more repetitions, and `(...)` groups alternatives.

```ebnf
query
  = "PIVOT"
    "FROM" source
    [ "ROWS" row_field_list ]
    [ "COLUMNS" column_field_list ]
    [ "WHERE" boolean_expression ]
    "VALUES" value_list
    [ totals_clause ]
    [ ";" ] ;

totals_clause
  = "TOTALS" totals_axis { totals_axis } ;

totals_axis
  = ( "ROWS" | "COLUMNS" ) "(" totals_item_list ")" ;

totals_item_list
  = totals_item { "," totals_item } ;

totals_item
  = total_mode "(" field_expression ")" ;

source
  = identifier
  | "(" target_sql ")" ;

; Target SQL text beginning with SELECT, with balanced parentheses and
; quotes, and with no statement terminator.
target_sql
  = ? target-SQL text beginning with SELECT ? ;

row_field_list
  = row_field { "," row_field } ;

column_field_list
  = column_field { "," column_field } ;

row_field
  = field_expression
    [ sort_direction ]
    [ nulls_order ]
    [ "HAVING" having_condition ] ;

column_field
  = field_expression
    [ sort_direction ]
    [ nulls_order ]
    [ "HAVING" having_condition ] ;

field_expression
  = identifier ;

sort_direction
  = "ASC" | "DESC" ;

total_mode
  = "SUM" | "COUNT" | "AVG" | "MIN" | "MAX" | "FIRST" | "LAST" ;

nulls_order
  = "NULLS" ( "FIRST" | "LAST" ) ;

having_condition
  = having_or_expression ;

having_or_expression
  = having_and_expression { "OR" having_and_expression } ;

having_and_expression
  = having_not_expression { "AND" having_not_expression } ;

having_not_expression
  = [ "NOT" ] having_primary ;

having_primary
  = "(" having_condition ")"
  | having_comparison ;

having_comparison
  = aggregate_call comparison_operator literal ;

value_list
  = value_item { "," value_item } ;

value_item
  = aggregate_call "AS" display_identifier ;

aggregate_call
  = "COUNT" "(" ( "*" | identifier ) ")"
  | "COUNT_DISTINCT" "(" identifier ")"
  | ( "SUM" | "AVG" | "MIN" | "MAX" ) "(" identifier ")" ;

boolean_expression
  = boolean_or_expression ;

boolean_or_expression
  = boolean_and_expression { "OR" boolean_and_expression } ;

boolean_and_expression
  = boolean_not_expression { "AND" boolean_not_expression } ;

boolean_not_expression
  = [ "NOT" ] boolean_predicate ;

boolean_predicate
  = arithmetic_expression
    [ comparison_operator arithmetic_expression
    | [ "NOT" ] "BETWEEN" arithmetic_expression "AND" arithmetic_expression
    | [ "NOT" ] "IN" "(" literal { "," literal } ")"
    | [ "NOT" ] "LIKE" string_literal
    | "IS" [ "NOT" ] "NULL" ] ;

arithmetic_expression
  = multiplicative_expression
    { ( "+" | "-" ) multiplicative_expression } ;

multiplicative_expression
  = unary_expression
    { ( "*" | "/" ) unary_expression } ;

unary_expression
  = [ "+" | "-" ] primary_expression ;

primary_expression
  = identifier
  | literal
  | "(" boolean_expression ")" ;

comparison_operator
  = "=" | "<>" | "!=" | "<" | "<=" | ">" | ">=" ;

literal
  = string_literal
  | numeric_literal
  | boolean_literal
  | "NULL" ;

display_identifier
  = quoted_identifier ;

identifier
  = unquoted_identifier | quoted_identifier ;

unquoted_identifier
  = identifier_start { identifier_start | "0"-"9" } ;

identifier_start
  = "A"-"Z" | "a"-"z" | "_" ;

quoted_identifier
  = '"' { quoted_identifier_character } '"' ;

string_literal
  = "'" { string_character } "'" ;

quoted_identifier_character
  = ? any character except '"', or the escape pair '""' ? ;

string_character
  = ? any character except "'", or the escape pair "''" ? ;

numeric_literal
  = digit { digit } [ "." digit { digit } ] ;

digit
  = "0"-"9" ;

boolean_literal
  = "TRUE" | "FALSE" ;
```

## 4. Clause Definitions

### 4.1 PIVOT and FROM

`PIVOT` is the introducer and **MUST** be the first significant token. `FROM` names the source table or a parenthesized SQL query. The source is resolved before any field is validated.

The subquery text inside parentheses is target SQL, not Pivotante. It **MUST** begin with `SELECT`, its parentheses and quotes **MUST** balance, and it **MUST NOT** contain a statement terminator.

### 4.2 ROWS

`ROWS` declares the vertical hierarchy. Fields are separated by commas and are nested from left to right. The first field is the outermost level.

Each field is a grouping field:

```sql
field [ASC | DESC] [NULLS FIRST | NULLS LAST]
      [HAVING <having_condition>]
```

- **Field:** the source field whose distinct values appear in the row headers. Functions are not allowed here.
- **Direction:** `ASC` (default) or `DESC`.
- **`NULLS`:** `FIRST` or `LAST`. The default is `LAST` for both `ASC` and `DESC`.
- **`HAVING`:** filters groups of that level using their totals. See section 6.

Every declared level produces row headers. A `ROWS` clause with one field produces leaf rows; several fields produce a parent level for each prefix. The total modes for those levels are declared in the optional `TOTALS` clause, or default to `SUM` when `TOTALS` is absent.

### 4.3 COLUMNS

`COLUMNS` declares the horizontal hierarchy. It uses the same grouping-field syntax and options as `ROWS`, applied to column headers. Functions are not allowed in `COLUMNS`.

A `COLUMNS` field discovers its distinct values at execution time. The discovered values appear as headers in the order defined by the field options and the column totals query.

A `NULL` axis key is a real header. It is displayed with the label `(blank)` unless the host engine documents another label, and it is never silently dropped. A `NULL` key counts as one discovered value.

### 4.4 WHERE

`WHERE` applies a pre-aggregation filter over source rows. It is evaluated once per source row before grouping, header discovery, and aggregation.

The expression **MUST** be boolean. `TRUE` includes the row; `FALSE` and `NULL` exclude it. Aggregates **MUST NOT** appear in `WHERE`; use axis `HAVING` to filter by totals.

### 4.5 VALUES

`VALUES` declares the metrics shown at every row/column intersection. Each item is an aggregate call followed by a required display alias:

```sql
VALUES
    SUM(revenue) AS "Revenue",
    COUNT(category) AS "Cats"
```

Supported aggregates:

| Aggregate | Meaning |
|---|---|
| `COUNT(*)` | Count source rows, including rows with NULL fields. |
| `COUNT(field)` | Count non-NULL field values. |
| `COUNT_DISTINCT(field)` | Count distinct non-NULL field values. |
| `SUM(field)` | Sum non-NULL numeric values. |
| `AVG(field)` | Average non-NULL numeric values. |
| `MIN(field)` | Minimum ordered non-NULL value. |
| `MAX(field)` | Maximum ordered non-NULL value. |

Aggregates **MUST NOT** be nested. The argument of `SUM`, `AVG`, `MIN`, `MAX`, and `COUNT_DISTINCT` **MUST** be a field identifier. `COUNT` accepts `*` or a field identifier. `FIRST` and `LAST` are not value aggregates; they are total modes and are only valid inside `TOTALS`.

The display alias is mandatory. Every output alias **MUST** be unique within the query result; duplicate aliases **MUST** be rejected with `DUPLICATE_ALIAS`. A missing alias is a `SYNTAX_ERROR`. Requiring an alias removes any implicit-name ambiguity and makes every result column stable.

### 4.6 TOTALS (Optional)

`TOTALS` declares the total mode for the levels of each axis. It appears after `VALUES`:

```sql
TOTALS
    ROWS (SUM(region), SUM(city))
    COLUMNS (COUNT(fiscal_year), SUM(customer_id))
```

- If `TOTALS` is omitted, every axis level uses `SUM`.
- If a `ROWS` block is present, it **MUST** list every field of `ROWS` exactly once. The same rule applies to a `COLUMNS` block.
- Each item is `TOTAL_MODE(field)`. The field is the grouping key; the function is the total mode for that level, not an aggregate over the field values. `COUNT(fiscal_year)` means “consolidate the fiscal_year level with COUNT”.
- Allowed modes are `SUM`, `COUNT`, `AVG`, `MIN`, `MAX`, `FIRST`, and `LAST`.
- Only `SUM(field)` is valid on the deepest field of an axis. Every other mode is parent-only and produces `LAYOUT_ERROR` on a leaf.
- A block for an axis that is not present is a `LAYOUT_ERROR`.
- A duplicate `TOTALS` clause, duplicate axis block, or repeated field is a `SYNTAX_ERROR`. A missing axis field is a `LAYOUT_ERROR`.
- `TOTALS` clauses and blocks have a canonical order: `TOTALS`, then `ROWS`, then `COLUMNS`.


## 5. Query Transformation

A Pivotante query is translated into up to three SQL queries. Let `R` be the ordered `ROWS` fields, `C` the ordered `COLUMNS` fields, and `V` the `VALUES` aggregates. The row totals query is generated only when `ROWS` is present, and the column totals query only when `COLUMNS` is present. `TOTALS` does not change the generated SQL; its modes are applied when the pivot matrix is assembled.

### 5.1 Detail query

```sql
SELECT <R fields>, <C fields>, <V aggregates>
FROM <source>
WHERE <where>
GROUP BY <R fields>, <C fields>
ORDER BY <R fields with direction and NULLS>,
         <C fields with direction and NULLS>
```

This query supplies every base cell. The generated `ORDER BY` uses the declared direction and `NULLS` placement of every field, with explicit `NULLS FIRST` or `NULLS LAST`.

If both `ROWS` and `COLUMNS` are absent, the detail query omits `GROUP BY` and `ORDER BY` and returns one aggregate row. If only one axis is present, the detail query groups by that axis. The `WHERE` clause is omitted when the Pivotante query has no `WHERE`.

### 5.2 Row totals query

Generated when `ROWS` is present:

```sql
SELECT <R fields>, <V aggregates>
FROM <source>
WHERE <where>
GROUP BY <R fields>
ORDER BY <R fields with direction and NULLS>
```

This query supplies the total of every leaf row across all columns. Parent row totals are derived from these leaf totals using the effective total mode of the parent field.

### 5.3 Column totals query

Generated when `COLUMNS` is present:

```sql
SELECT <C fields>, <V aggregates>
FROM <source>
WHERE <where>
GROUP BY <C fields>
ORDER BY <C fields with direction and NULLS>
```

This query supplies the total of every leaf column across all rows. Parent column totals are derived from these leaf totals using the effective total mode of the parent field.

### 5.4 Canonical example

Reference query:

```sql
PIVOT
FROM sales_data
ROWS
    region ASC NULLS LAST,
    city DESC
COLUMNS
    fiscal_year,
    customer_id DESC
WHERE orders > 8
VALUES
    SUM(revenue) AS "Revenue",
    COUNT(category) AS "Cats"
TOTALS
    COLUMNS (COUNT(fiscal_year), SUM(customer_id))
```

Detail query:

```sql
SELECT region, city, fiscal_year, customer_id,
       SUM(revenue) AS Revenue, COUNT(category) AS Cats
FROM sales_data
WHERE orders > 8
GROUP BY region, city, fiscal_year, customer_id
ORDER BY region ASC NULLS LAST,
         city DESC NULLS LAST,
         fiscal_year ASC NULLS LAST,
         customer_id DESC NULLS LAST
```

Row totals query:

```sql
SELECT region, city, SUM(revenue) AS Revenue, COUNT(category) AS Cats
FROM sales_data
WHERE orders > 8
GROUP BY region, city
ORDER BY region ASC NULLS LAST, city DESC NULLS LAST
```

Column totals query:

```sql
SELECT fiscal_year, customer_id, SUM(revenue) AS Revenue, COUNT(category) AS Cats
FROM sales_data
WHERE orders > 8
GROUP BY fiscal_year, customer_id
ORDER BY fiscal_year ASC NULLS LAST, customer_id DESC NULLS LAST
```

`sales_data` is the logical source view defined over the 3NF tables in Appendix A. The generated SQL uses the declared direction and `NULLS` placement for every field and always emits an explicit `NULLS FIRST` or `NULLS LAST` clause. The final header order follows the totals queries; no second ordering pass is required.

## 6. Axis Totals and HAVING

### 6.1 Leaf totals

A leaf group is a full key of its axis. Its total for each metric is the value returned by the corresponding totals query:

- `ROWS` leaf total: the row totals query value.
- `COLUMNS` leaf total: the column totals query value.

The deepest field of each axis **MUST** use `SUM` as its effective total mode. If `TOTALS` is absent, this is the default; if `TOTALS` declares the deepest field, it must use `SUM(field)`. All other modes are parent-only.

### 6.2 Parent totals

The effective total mode comes from `TOTALS`, or is `SUM` when `TOTALS` is absent. It does not change the aggregate calls in the generated totals queries; those always use the aggregates declared in `VALUES`. The total mode controls how leaf totals are consolidated into parent totals.

A parent group is identified by a prefix of the axis fields. Its total is calculated from the totals of its immediate child groups. The bullets use the `TOTALS` item forms:

- `SUM(field)`: for each metric, sum the non-NULL child totals. The result is `NULL` when no child total is non-NULL.
- `COUNT(field)`: for each metric, count the child groups whose total for that metric is non-NULL. The result is `0` when no child has a non-NULL total.
- `AVG(field)`: for each metric, average the non-NULL child totals. The result is `NULL` when no child total is non-NULL.
- `MIN(field)`: for each metric, take the minimum non-NULL child total. The result is `NULL` when no child total is non-NULL.
- `MAX(field)`: for each metric, take the maximum non-NULL child total. The result is `NULL` when no child total is non-NULL.
- `FIRST(field)`: for each metric, take the first non-NULL child total in the final order of the child groups. The result is `NULL` when no child total is non-NULL.
- `LAST(field)`: for each metric, take the last non-NULL child total in the final order of the child groups. The result is `NULL` when no child total is non-NULL.

All total modes except `SUM` are parent-only. `COUNT`, `AVG`, `MIN`, `MAX`, `FIRST`, and `LAST` require a deeper field after them in the same axis. `SUM` and `AVG` require numeric metric totals; `MIN` and `MAX` require ordered comparable totals; `COUNT` always yields an integer. A type mismatch is a `TYPE_ERROR`.

Parent totals consolidate the displayed child totals; they are not a second aggregation over source rows. For `AVG`, `MIN`, `MAX`, `FIRST`, and `LAST`, consolidating child values may not equal re-aggregating the union of source rows. Use the total mode whose consolidation is the intended total.

The grand total uses the effective total mode of the first field of the axis. For rows, the grand total is calculated from the top-level row totals. For columns, the grand total is calculated from the top-level column totals.

### 6.3 Matrix totals

- Each row header has a **row total** across all columns.
- Each column header has a **column total** across all rows.
- A parent row also has a subtotal row, placed immediately after its child rows; a parent column also has a subtotal column, placed immediately after its child columns.
- A parent subtotal label is the group key followed by ` Total`, for example `North Total`. The grand-total label is `Grand Total`.
- The bottom-right cell is the **grand total**.
- If both axes are absent, the result is a single row containing the `VALUES` metrics and no axis totals.

For a parent row and a leaf column, the cell value is obtained by applying the parent field's effective total mode to the values of its immediate child row groups at that column.

For a leaf row and a parent column, the cell value is obtained by applying the parent field's effective total mode to the values of its immediate child column groups at that row.

For a parent row and a parent column, the row-side rule wins: the value is obtained by applying the parent row field's effective total mode to the values of its immediate child row groups at that column. This rule makes every emitted cell deterministic.

The grand-total cell uses the row-side grand total. When the row and column axes use different top-level total modes, the row-side value is authoritative. If `ROWS` is absent, the column-side grand total is used. If `COLUMNS` is absent, there is no separate grand-total column; the row grand total is the only grand total.

### 6.4 HAVING

`HAVING` filters groups by their totals, never by individual source values. It is written after the field options:

```sql
ROWS
    region ASC NULLS LAST HAVING SUM(revenue) >= 3000,
    city DESC
```

Rules:

1. A `HAVING` condition is a boolean expression over comparisons between an aggregate call and a literal, combined with `AND`, `OR`, `NOT`, and parentheses.
2. The aggregate call in a `HAVING` comparison **MUST** match an aggregate call in `VALUES` exactly. It names the metric whose total is tested.
3. The test uses the total of the group at that field's level, calculated with that field's effective total mode (`TOTALS`, or `SUM` by default). The comparison therefore tests the consolidated total, not the raw metric values.
4. A group that fails its `HAVING` condition is removed together with all of its descendants. Its base cells and its contribution to row totals, column totals, subtotals, and the grand total are removed.
5. `HAVING` is applied to the results of the totals queries. The generated SQL queries do not contain a SQL `HAVING` clause.
6. `HAVING` **MUST NOT** reference bare source fields, output aliases, subqueries, or aggregates that are not present in `VALUES`.

After all `HAVING` filters are applied, the engine rebuilds the matrix and recalculates the displayed totals from the retained groups. `WHERE` is always applied before `HAVING`.

### 6.5 Multiple fields and HAVING order

`HAVING` conditions are evaluated level by level, from the first field to the deepest field. If a group is removed at a parent level, its descendants are not evaluated or displayed. If a descendant is removed, the displayed parent total is recalculated from the retained descendant groups.

## 7. NULL and Aggregate Semantics

- `NULL` is not zero and does not participate numerically in aggregates.
- `SUM`, `AVG`, `MIN`, and `MAX` ignore `NULL` values and return `NULL` for an empty or all-`NULL` set.
- `COUNT(*)` counts rows; `COUNT(field)` and `COUNT_DISTINCT(field)` ignore `NULL` values.
- `WHERE` follows SQL three-valued logic: `FALSE` and `NULL` exclude a row.
- A missing row/column intersection has the value `NULL` for every metric. There is no empty-cell fallback.
- A `NULL` axis key is a real key and is displayed as `(blank)`.

## 8. Ordering

- Axis direction defaults to `ASC`.
- Axis NULL placement defaults to `NULLS LAST` for both directions.
- The row totals query determines the final row order.
- The column totals query determines the final column order.
- Generated `ORDER BY` clauses always include explicit `NULLS FIRST` or `NULLS LAST`.
- The detail query uses the declared row order followed by the declared column order.
- Ties are broken by the canonical key value in ascending order and then by source ordinal position.

## 9. Diagnostics

Every parser, validator, or execution error **MUST** include `code`, `message`, `position`, `source_fragment`, and `phase`. `position` contains `line`, `column`, and `offset`; `line` and `column` are 1-based and `offset` is 0-based. `phase` is one of `LEX`, `PARSE`, `SOURCE`, `VALIDATE`, or `EXECUTION`.

| Code | Meaning |
|---|---|
| `SYNTAX_ERROR` | The text is outside the grammar, a clause is out of order or duplicated, a display alias is missing, a `TOTALS` block is duplicated or repeats a field, or `GROUP BY` is used. |
| `UNKNOWN_FIELD` | An identifier cannot be resolved against the source metadata, or a `TOTALS` item names a field that is not in that axis. |
| `TYPE_ERROR` | An aggregate argument, total mode, `WHERE` expression, or `HAVING` condition has an incompatible type. |
| `DUPLICATE_ALIAS` | Two output aliases are identical. |
| `UNSUPPORTED_FUNCTION` | A function is not part of the supported aggregate list. |
| `LAYOUT_ERROR` | A `TOTALS` block targets an absent axis, omits a field of that axis, or uses a non-`SUM` mode on the deepest field. |

## 10. Logical Result Shape

The result is a matrix with grouped headers. The following JSON shape is a logical contract, not a required wire format:

```json
{
  "rowHeaders": [
    {"key": ["North"], "label": "North Total", "level": 0, "type": "subtotal"},
    {"key": ["North", "Alles"], "label": "Alles", "level": 1, "type": "base"},
    {"key": [], "label": "Grand Total", "level": 0, "type": "grandTotal"}
  ],
  "columnHeaders": [
    {"key": ["Q1"], "label": "Q1", "level": 0, "type": "base"},
    {"key": ["Q2"], "label": "Q2", "level": 0, "type": "base"},
    {"key": [], "label": "Grand Total", "level": 0, "type": "grandTotal"}
  ],
  "cells": [
    {
      "rowKey": ["North", "Alles"],
      "columnKey": ["Q1"],
      "rowType": "base",
      "columnType": "base",
      "values": {"Revenue": 3257, "Cats": 4}
    }
  ],
  "grandTotal": {"Revenue": 55825, "Cats": 67},
  "types": {"Revenue": "number", "Cats": "number"}
}
```

- `rowHeaders[].key` and `columnHeaders[].key` are arrays that preserve hierarchy depth.
- `type` is `"base"`, `"subtotal"`, or `"grandTotal"`.
- `cells` contains one record for every displayed intersection, including subtotal and total intersections. `rowType` and `columnType` identify the referenced header types.
- `values` maps each output alias to its value. A missing intersection has `null` for every metric.
- `grandTotal` maps each output alias to the grand-total value.

## 11. Execution Pipeline

A conforming engine **MUST** run these stages:

1. **Lexing and parsing.** Consume comments, parse the clauses in canonical order, and build the AST. Reject `GROUP BY`, duplicate clauses, duplicate `TOTALS` blocks, and clauses outside the canonical order.
2. **Source resolution and validation.** Resolve the source, fields, aggregate arguments, `WHERE` types, axis options, `TOTALS` coverage, and aliases. Reject unknown fields, nested aggregates, unsupported functions, duplicate aliases, incomplete `TOTALS` blocks, and non-`SUM` modes on a deepest field.
3. **Query generation.** Generate the detail, row totals, and column totals queries described in section 5.
4. **Execution.** Run the generated queries. `WHERE` is applied by every generated query.
5. **HAVING filtering.** Apply the axis `HAVING` conditions to the totals query results, level by level, and discard excluded groups.
6. **Matrix assembly.** Build row and column headers, base cells, subtotals, row totals, column totals, and the grand total using the effective `TOTALS` modes. Recalculate totals from the retained groups after `HAVING`.
7. **Formatting.** Assign labels, apply the `(blank)` label to NULL keys, and emit the logical result shape.

## 12. Conformance Checklist

An engine claiming Pivotante v5.0.0 conformance **MUST**:

- Require `PIVOT` as the first significant token.
- Enforce the clause order `PIVOT`, `FROM`, `ROWS`, `COLUMNS`, `WHERE`, `VALUES`, `TOTALS`.
- Reject `GROUP BY`.
- Infer grouping from the `ROWS` and `COLUMNS` fields only; functions are not allowed in either axis.
- Generate the detail, row totals, and column totals queries.
- Apply `WHERE` before grouping and aggregation.
- Accept the optional `TOTALS` clause with `SUM`, `COUNT`, `AVG`, `MIN`, `MAX`, `FIRST`, and `LAST` modes.
- Default every axis level to `SUM` when `TOTALS` is absent.
- Require a present `TOTALS` axis block to list every field of that axis exactly once.
- Reject every non-`SUM` mode on the deepest field of an axis.
- Apply `HAVING` only to totals, after the totals queries.
- Remove excluded groups from cells, subtotals, row totals, column totals, and the grand total.
- Treat `NULL` axis keys as real headers labeled `(blank)`.
- Preserve the declared axis order and `NULLS` placement, emitting explicit `NULLS FIRST|LAST` in generated SQL.
- Require an explicit, unique display alias for every `VALUES` item.
- Return the logical result shape of section 10.

## 13. Change Summary (v5.0.0)

This revision separates grouping fields from total modes:

- Canonical order is `PIVOT`, `FROM`, `ROWS`, `COLUMNS`, `WHERE`, `VALUES`, `TOTALS`.
- `ROWS` and `COLUMNS` contain only grouping fields, ordering, `NULLS`, and `HAVING`; aggregation functions are not allowed there.
- The optional `TOTALS` clause declares total modes per axis level using `SUM`, `COUNT`, `AVG`, `MIN`, `MAX`, `FIRST`, or `LAST`.
- If `TOTALS` is absent, every level uses `SUM`.
- If a `TOTALS` axis block is present, it lists every field of that axis exactly once.
- Only `SUM` is valid on the deepest field of an axis; the other modes are parent-only.
- `HAVING` is declared per axis field and filters only totals.
- The execution model is fixed to up to three SQL queries: detail, row totals, and column totals.
- Generated SQL uses explicit `NULLS FIRST|LAST` and the declared axis directions.
- Display aliases are mandatory.
- The result is the assembled pivot matrix with row totals, column totals, subtotals, and the grand total.

# Part II — Datilandia Integration

This part adapts Pivotante 5.0.0 to Datilandia. It is normative for the Datilandia dialect and complements, but does not replace, the core language in Part I.

## 14.1 Compatibility and Dispatch

- Existing Datilandia `SELECT` statements keep their grammar, execution, result shape, saved-query behavior, and diagnostics.
- Pivotante statements are optional and read-only. They introduce no `INSERT`, `UPDATE`, `DELETE`, or DDL.
- Datilandia accepts exactly one statement. An optional final `;` is allowed; text after the semicolon is an error.
- Dispatch is based on the first significant token, after comments:
  - `SELECT` routes to the existing Datilandia parser.
  - `PIVOT` routes to the Pivotante parser.
- `PIVOT` remains a valid table name in existing `SELECT` statements. Inside a Pivotante statement, a table named `PIVOT` **MUST** be written as `[PIVOT]`.
- No existing `SELECT` syntax is removed or reinterpreted.

## 14.2 Integrated Syntax

### 14.2.1 Canonical order

```sql
PIVOT
FROM <source>
[ROWS <row_field_list>]
[COLUMNS <column_field_list>]
[WHERE <boolean_expression>]
VALUES <value_list>
[TOTALS
  [ROWS (totals_item_list)]
  [COLUMNS (totals_item_list)]]
[;]
```

`PIVOT`, `FROM`, and `VALUES` are required. `ROWS`, `COLUMNS`, `WHERE`, and `TOTALS` are optional. `GROUP BY` is forbidden; grouping is inferred from the axis fields. Duplicate or out-of-order clauses are `SYNTAX_ERROR`. If `TOTALS` is absent, every axis level uses `SUM`.

### 14.2.2 Datilandia dialect differences

| Core Pivotante | Datilandia Pivotante | Consequence |
|---|---|---|
| `"Region Name"` identifier | `[Region Name]` identifier | Double quotes are strings in Datilandia, not identifiers. |
| `'North'` string | `'North'` or `"North"` string | Both quote characters delimit text. |
| `AS "Revenue"` quoted identifier | `AS "Revenue"` or `AS 'Revenue'` display string | The alias is a string used as a display name. |
| `COUNT_DISTINCT(x)` | `COUNT(DISTINCT x)` | Both spellings **MUST** be accepted and produce the same result. |
| `NULLS LAST` default | explicit `NULLS FIRST` / `NULLS LAST` | Pivotante axes default to `NULLS LAST`; Datilandia `SELECT` keeps its own default. The compiler **MUST** emit an explicit `NULLS` clause for every ordered field, regardless of the target default. |
| Exponents or `.5` numeric literals | digits with an optional decimal part | Datilandia rejects exponents and leading-dot decimals. |

Datilandia supports `%` modulo, `==`, `||` and `CONCAT`, `[NOT] LIKE ... ESCAPE`, `NULLS FIRST|LAST`, and `[NOT] IN (SELECT ...)`. Pivotante inherits those expression features for `WHERE` and axis `HAVING` comparisons. Datilandia does not support `CASE`, window functions, or `GROUPING SETS`; Pivotante does not require them.

### 14.2.3 Integration grammar

```ebnf
statement
  = datilandia_select
  | pivotante_statement ;

datilandia_select
  = ? a complete Datilandia SELECT statement ? ;

pivotante_statement
  = "PIVOT"
    "FROM" source
    [ "ROWS" row_field_list ]
    [ "COLUMNS" column_field_list ]
    [ "WHERE" datilandia_boolean_expression ]
    "VALUES" value_list
    [ totals_clause ]
    [ ";" ] ;

totals_clause
  = "TOTALS" totals_axis { totals_axis } ;

totals_axis
  = ( "ROWS" | "COLUMNS" ) "(" totals_item_list ")" ;

totals_item_list
  = totals_item { "," totals_item } ;

totals_item
  = ( "SUM" | "COUNT" | "AVG" | "MIN" | "MAX" | "FIRST" | "LAST" )
    "(" datilandia_field ")" ;

source
  = datilandia_table_or_saved_query
  | datilandia_derived_table ;

datilandia_table_or_saved_query
  = datilandia_identifier [ "AS" datilandia_identifier ] ;

datilandia_derived_table
  = "(" datilandia_select ")" "AS" datilandia_identifier ;

row_field_list
  = row_field { "," row_field } ;

column_field_list
  = column_field { "," column_field } ;

row_field
  = datilandia_field
    [ "ASC" | "DESC" ]
    [ "NULLS" ( "FIRST" | "LAST" ) ]
    [ "HAVING" having_condition ] ;

column_field
  = datilandia_field
    [ "ASC" | "DESC" ]
    [ "NULLS" ( "FIRST" | "LAST" ) ]
    [ "HAVING" having_condition ] ;

having_condition
  = having_or_expression ;

having_or_expression
  = having_and_expression { "OR" having_and_expression } ;

having_and_expression
  = having_not_expression { "AND" having_not_expression } ;

having_not_expression
  = [ "NOT" ] having_primary ;

having_primary
  = "(" having_condition ")"
  | aggregation comparison_operator literal ;

value_list
  = value_item { "," value_item } ;

value_item
  = aggregation "AS" display_alias ;

aggregation
  = "COUNT" "(" ( "*" | [ "DISTINCT" ] datilandia_field ) ")"
  | "COUNT_DISTINCT" "(" datilandia_field ")"
  | ( "SUM" | "AVG" | "MIN" | "MAX" )
    "(" [ "DISTINCT" ] datilandia_field ")" ;

display_alias
  = double_quoted_string | single_quoted_string ;

datilandia_field
  = datilandia_identifier [ "." datilandia_identifier ] ;

datilandia_identifier
  = ? Datilandia unquoted identifier or bracketed identifier ? ;

comparison_operator
  = "=" | "==" | "<>" | "!=" | "<" | "<=" | ">" | ">=" ;

literal
  = ? Datilandia numeric, string, boolean, or NULL literal ? ;

datilandia_boolean_expression
  = ? a Datilandia expression of boolean type ? ;
```

`datilandia_select` and `datilandia_boolean_expression` refer to the existing Datilandia grammar and are not redefined here. `ROWS` and `COLUMNS` contain grouping fields only. The optional `TOTALS` clause maps those fields to total modes; the functions in `TOTALS` are not aggregates over the field values.

## 14.3 Sources

A Pivotante source is a project table, a saved query, or a derived table with an alias. The examples in this folder use `sales_data`, the logical view defined over the 3NF tables in `Appendix A`:

```sql
PIVOT
FROM [sales_data] AS s
ROWS s.[region] ASC NULLS LAST
VALUES SUM(s.[revenue]) AS "Net Sales"
```

- Table-first resolution applies: if a name matches both a table and a saved query, the table wins.
- Saved queries are materialized as views before Pivotante filtering and aggregation. Their columns keep their output aliases and Datilandia types.
- A derived table is a parenthesized existing Datilandia `SELECT` with a required alias. It may contain joins, `WHERE`, `GROUP BY`, `HAVING`, `DISTINCT`, `UNION`, `ORDER BY`, `LIMIT`, and `OFFSET`.
- Circular saved-query or derived-table references use the existing Datilandia cycle diagnostic.
- Joins are not part of Pivotante syntax in 5.0.0. Write a join inside a derived table or saved query and use its result as the source.
- Table and derived-table aliases are not display aliases. They qualify field references only.

### 14.3.1 Top-level WHERE

The top-level `WHERE` is a pre-aggregation row filter, not a SQL `HAVING`.

- It **MUST** be a Datilandia boolean expression over source fields, aliases, literals, and the subquery predicates Datilandia permits.
- It **MUST NOT** contain aggregate calls. Use axis `HAVING` for aggregate filters.
- `TRUE` includes a row; `FALSE` and `NULL` exclude it.
- It is applied by every generated query before grouping.
- Existing `WHERE` or `HAVING` inside a saved query or derived table remains part of that source and is not changed.

## 14.4 Generated Queries

For a source `S`, row fields `R`, column fields `C`, metrics `V`, and filter `W`, the compiler generates:

**Detail query**

```sql
SELECT <R>, <C>, <V>
FROM <S>
WHERE <W>
GROUP BY <R>, <C>
ORDER BY <R fields with direction and NULLS>,
         <C fields with direction and NULLS>
```

**Row totals query** (when `ROWS` is present)

```sql
SELECT <R>, <V>
FROM <S>
WHERE <W>
GROUP BY <R>
ORDER BY <R fields with directions>
```

**Column totals query** (when `COLUMNS` is present)

```sql
SELECT <C>, <V>
FROM <S>
WHERE <W>
GROUP BY <C>
ORDER BY <C fields with direction and NULLS>
```

Generated queries use the declared direction and `NULLS` placement for every field and always emit explicit `NULLS FIRST` or `NULLS LAST`.

If both axes are absent, the detail query omits `GROUP BY` and `ORDER BY` and returns one aggregate row. If only one axis is present, the detail query groups by that axis. The `WHERE` clause is omitted when the Pivotante query has no `WHERE`.

Example:

```sql
PIVOT
FROM sales_data
ROWS
    region ASC NULLS LAST,
    city DESC
COLUMNS
    fiscal_year,
    customer_id DESC
WHERE orders > 8
VALUES
    SUM(revenue) AS "Revenue",
    COUNT(category) AS "Cats"
TOTALS
    COLUMNS (COUNT(fiscal_year), SUM(customer_id))
```

```sql
SELECT region, city, fiscal_year, customer_id,
       SUM(revenue) AS Revenue, COUNT(category) AS Cats
FROM sales_data
WHERE orders > 8
GROUP BY region, city, fiscal_year, customer_id
ORDER BY region ASC NULLS LAST,
         city DESC NULLS LAST,
         fiscal_year ASC NULLS LAST,
         customer_id DESC NULLS LAST;

SELECT region, city, SUM(revenue) AS Revenue, COUNT(category) AS Cats
FROM sales_data
WHERE orders > 8
GROUP BY region, city
ORDER BY region ASC NULLS LAST, city DESC NULLS LAST;

SELECT fiscal_year, customer_id, SUM(revenue) AS Revenue, COUNT(category) AS Cats
FROM sales_data
WHERE orders > 8
GROUP BY fiscal_year, customer_id
ORDER BY fiscal_year ASC NULLS LAST, customer_id DESC NULLS LAST;
```

`sales_data` is the logical view defined over the 3NF tables in `Appendix A`. The compiler **MUST** emit the declared direction and an explicit `NULLS FIRST|LAST` clause for every ordered field.

## 14.5 Totals

`ROWS` and `COLUMNS` contain only grouping fields. The optional `TOTALS` clause maps those fields to total modes:

```sql
TOTALS
    ROWS (SUM(region), SUM(city))
    COLUMNS (COUNT(fiscal_year), SUM(customer_id))
```

- If `TOTALS` is absent, every axis level uses `SUM`.
- If a `ROWS` block is present, it **MUST** list every `ROWS` field exactly once; the same rule applies to a `COLUMNS` block. A missing field is `LAYOUT_ERROR`, a duplicate field is `SYNTAX_ERROR`, and an extra field is `UNKNOWN_FIELD`.
- Each item is `TOTAL_MODE(field)`. The field is the grouping key; the mode controls how leaf totals are consolidated into parent totals. It does not change the aggregates in `VALUES`.
- Allowed modes are `SUM`, `COUNT`, `AVG`, `MIN`, `MAX`, `FIRST`, and `LAST`.
- Only `SUM(field)` is valid on the deepest field; every other mode is parent-only. A non-`SUM` mode on the deepest field fails with `LAYOUT_ERROR`.
- `SUM` and `AVG` require numeric metric totals; `MIN` and `MAX` require ordered comparable totals; `COUNT` always yields an integer. A mismatch is a `TYPE_ERROR`.
- `FIRST(field)` and `LAST(field)` take the first or last non-NULL child total in the final child order.
- Leaf totals come directly from the row or column totals query.
- Parent totals consolidate displayed child totals; they are not a second aggregation over source rows. For `AVG`, `MIN`, `MAX`, `FIRST`, and `LAST`, consolidating child values may not equal re-aggregating the union of source rows.
- The grand total uses the effective total mode of the first field of the axis. If both axes are present, the row-side grand total is authoritative for the bottom-right cell.

Parent row totals, parent column totals, and subtotal cells are calculated in the Pivotante layer from the generated query results. The engine **MUST NOT** require Datilandia `CASE`, `GROUPING SETS`, or `ROLLUP`.

## 14.6 HAVING

`HAVING` filters axis groups by their totals. It never filters individual source values.

```sql
PIVOT
FROM sales_data
ROWS
    region ASC NULLS LAST HAVING SUM(revenue) >= 3000,
    city DESC
COLUMNS quarter ASC NULLS LAST
WHERE fiscal_year = 2026
VALUES SUM(revenue) AS "Revenue"
```

Rules:

- A `HAVING` aggregate call **MUST** match an aggregate call in `VALUES` exactly. It identifies the metric whose total is tested.
- The comparison tests the total of the group at that field's level, using that field's effective total mode from `TOTALS` (or `SUM` by default).
- A failing group and all of its descendants are removed from headers, cells, subtotals, row totals, column totals, and the grand total.
- `HAVING` is applied to the results of the totals queries. The generated SQL statements do **not** contain a `HAVING` clause.
- `HAVING` **MUST NOT** reference source fields, display aliases, subqueries, or aggregates absent from `VALUES`.
- `WHERE` is applied before grouping and before `HAVING`.
- After filtering, displayed totals are recalculated from the retained groups.

## 14.7 Datilandia NULL and Type Behavior

Datilandia field types are `string`, `number`, and `boolean`; there is no dedicated date type.

- `COUNT(*)` counts source rows, including rows with NULL fields.
- `COUNT(field)` and `COUNT(DISTINCT field)` ignore NULL values.
- `SUM`, `AVG`, `MIN`, and `MAX` ignore NULL values. Over an empty or all-NULL set they return NULL.
- `COUNT` over an empty set returns zero.
- Arithmetic and comparisons propagate NULL according to Datilandia/SQL semantics.
- `IS NULL` and `IS NOT NULL` are explicit predicates.
- Top-level `WHERE` excludes rows whose predicate is `FALSE` or `NULL`.
- Division by zero produces NULL in Datilandia. Pivotante 5.0.0 has no calculated metrics, so this only applies to expressions inside `WHERE` or `HAVING`.
- A NULL axis key is a real group key, displayed as `(blank)`, and is never silently dropped.
- A missing row/column intersection is NULL. There is no `EMPTY AS` fallback.

## 14.8 Diagnostics

Every lexer, parser, validator, or execution error **MUST** include `code`, `message`, `position`, `source_fragment`, and `phase`. `position` contains `line`, `column`, and `offset`; `line` and `column` are 1-based and `offset` is 0-based. Phases are `LEX`, `PARSE`, `SOURCE`, `VALIDATE`, and `EXECUTION`.

| Code | Meaning |
|---|---|
| `SYNTAX_ERROR` | Text is outside the grammar, a clause is out of order, a display alias is missing, a `TOTALS` block is duplicated or repeats a field, `GROUP BY` is present, or a removed clause is used. |
| `UNKNOWN_FIELD` | An identifier cannot be resolved against the source metadata, a `TOTALS` item names a field outside its axis, or a `HAVING` aggregate is absent from `VALUES`. |
| `TYPE_ERROR` | An aggregate argument, total mode, `WHERE` expression, or `HAVING` comparison has incompatible types. |
| `DUPLICATE_ALIAS` | A display alias is duplicated. |
| `UNSUPPORTED_FUNCTION` | A function is not available in Datilandia or is not part of the Pivotante aggregate list. |
| `SOURCE_CYCLE` | A saved query or derived table is circular. |
| `LAYOUT_ERROR` | A `TOTALS` block targets an absent axis, omits a field of that axis, or uses a non-`SUM` mode on the deepest field. |

## 14.9 Examples

### 14.9.1 Basic matrix

```sql
PIVOT
FROM sales_data
ROWS region ASC NULLS LAST
COLUMNS quarter ASC NULLS LAST
WHERE fiscal_year = 2026
VALUES SUM(revenue) AS "Revenue"
```

Result:

| region | Q1 | Q2 | Q3 | Q4 | Total |
|---|---:|---:|---:|---:|---:|
| East | 2000 | 3350 | `NULL` | `NULL` | 5500 |
| North | 5832 | 5548 | 4161 | 4797 | 20338 |
| South | 2398 | 1550 | 1086 | `NULL` | 5034 |
| Grand Total | 10230 | 10448 | 5247 | 4797 | 30722 |

### 14.9.2 Nested rows and HAVING

```sql
PIVOT
FROM sales_data
ROWS
    region ASC NULLS LAST,
    city DESC NULLS LAST HAVING SUM(revenue) > 3000
COLUMNS quarter ASC NULLS LAST
WHERE fiscal_year = 2026
VALUES SUM(revenue) AS "Revenue"
```

`Laslu` (`2100`), `Molas` (`1100`), `Porca` (`2150`), and `Sumi` (`1300`) are removed. The whole `East` region disappears, `North` keeps `20338`, `South` keeps `3734`, and the grand total becomes `24072`.

### 14.9.3 COUNT totals

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

The region subtotal is the number of city groups with a non-NULL Revenue total: East `3`, North `3`, South `2`. The grand total is the number of regions: `3`.

### 14.9.4 Datilandia identifiers and strings

```sql
PIVOT
FROM [sales_data] AS s
ROWS s.[region] ASC NULLS LAST
COLUMNS s.[quarter] ASC NULLS LAST
WHERE s.[fiscal_year] = 2026
VALUES COUNT(DISTINCT s.[customer_id]) AS "Customers"
```

Double quotes delimit the display string `"Customers"`. Brackets delimit identifiers. A derived table must have an alias:

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

### 14.9.5 Other Total Modes

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

The region total is the average of its city totals: East `1833.33`, North `13060.33`, South `5572.00`, grand total `6821.89`. Replacing `AVG(region)` with `MIN(region)`, `MAX(region)`, `FIRST(region)`, or `LAST(region)` applies that function to the child city totals; `FIRST` and `LAST` use the final city order.

## 14.10 Conformance Checklist

A Datilandia implementation claiming Pivotante 5.0.0-datilandia conformance **MUST**:

- Route `SELECT` statements unchanged to the existing SQL engine.
- Route `PIVOT` statements to the Pivotante parser.
- Enforce `PIVOT`, `FROM`, `ROWS`, `COLUMNS`, `WHERE`, `VALUES`, `TOTALS` clause order.
- Reject `GROUP BY` and all removed v2 clauses.
- Infer grouping from the `ROWS` and `COLUMNS` fields only.
- Generate and execute the detail, row totals, and column totals queries.
- Apply `WHERE` before grouping and aggregation.
- Apply axis `HAVING` only to totals, after the totals queries.
- Apply the optional `TOTALS` clause with `SUM`, `COUNT`, `AVG`, `MIN`, `MAX`, `FIRST`, and `LAST` modes using the rules of section 14.5.
- Default every axis level to `SUM` when `TOTALS` is absent.
- Require a present `TOTALS` axis block to list every field of that axis exactly once.
- Reject every non-`SUM` mode on the deepest axis field.
- Remove `HAVING`-excluded groups from all cells and totals.
- Preserve Datilandia table-first source resolution, saved queries, derived tables, comments, quote rules, and NULL behavior.
- Keep `NULL` axis keys as `(blank)` headers.
- Always emit explicit `NULLS FIRST|LAST` clauses in generated axis ordering.
- Require an explicit, unique display alias for every `VALUES` item.
- Return the assembled matrix with row totals, column totals, subtotals, and the grand total.
- Keep all Pivotante statements read-only.

## 14.11 Out of Scope

The following are outside Pivotante 5.0.0-datilandia:

- SQL mutations and DDL.
- A `CASE` requirement.
- External DuckDB, PostgreSQL, SQLite, or database-server execution.
- Server-side SQL or database credentials.
- Window functions and `GROUPING SETS` as user syntax.
- The removed v2 features: `EMPTY AS`, `WITH SUBTOTALS`, `MAX COLUMNS`, shares, `CALC`, `DIFF_PREV`, and `DATE_BUCKET`.
- Joins written directly in a Pivotante `FROM`; use a derived table or saved query.
- Chart generation, conditional formatting, exports, and pivot drill-down UI.
- Locale-specific number, date, or subtotal-label policy beyond Datilandia defaults.

## 14.12 Change Summary (5.0.0-datilandia)

- Replaces the v3 integration with the simplified Pivotante 5.0.0 language: `ROWS`/`COLUMNS` group fields and `TOTALS` declares total modes.
- Canonical order is `PIVOT`, `FROM`, `ROWS`, `COLUMNS`, `WHERE`, `VALUES`, `TOTALS`.
- Removes `GROUP BY`, `EMPTY AS`, `WITH SUBTOTALS`, `MAX COLUMNS`, shares, `CALC`, `DIFF_PREV`, and `DATE_BUCKET`.
- Adds the optional `TOTALS` clause with `SUM`, `COUNT`, `AVG`, `MIN`, `MAX`, `FIRST`, and `LAST` modes and per-field `HAVING` over totals.
- Fixes the execution model to up to three generated SQL queries.
- Requires explicit display aliases and explicit `NULLS FIRST|LAST` clauses.
- Uses the `sales_data` view over the 3NF tables as the reference source.
- Removes the v3 `TOTALS AS` syntax and the v4 axis total functions.
- Adds `TOTALS` as the only place where total modes are declared.
- Aligns the result with the assembled matrix and recalculated totals.


# Appendix A — Data Source and 3NF Schema

The examples use a flat source called `sales_data`. The 67 rows are embedded as CSV in `pivotante-examples.md`, section 1.1. That flat CSV is the materialized source for every example; the separate normalized CSV files are not distributed. This appendix records the 3NF decomposition and the reconstruction view.

## A.1 Source profile

| Column | Distinct values | Note |
|---|---:|---|
| `order_id` | 67 | Primary key; no duplicates. |
| `region` | 3 | East, North, South. |
| `city` | 8 | Each city belongs to one region. |
| `category` | 2 | Hardware, Software. |
| `product_name` | 2 | Laptop belongs to Hardware; Suite to Software. |
| `quarter` | 4 | Q1-Q4; derived from the month of `order_date`. |
| `order_date` | 49 | ISO `YYYY-MM-DD`. |
| `revenue` | 64 | Numeric measure. |
| `profit` | 44 | Numeric measure. |
| `orders` | 11 | Numeric measure (number of orders/units). |
| `customer_id` | 59 | External identifier; the source has no customer attributes. |
| `fiscal_year` | 2 | 2025, 2026; corrected from the date. |
| `order_status` | 2 | Completed, UnCompleted. |

## A.2 Functional dependencies

- `order_id → region, city, category, product_name, quarter, order_date, revenue, profit, orders, customer_id, fiscal_year, order_status` (candidate key).
- `city → region`: Alles, Bences and Desques are in North; Miro and Sumi in South; Laslu, Molas and Porca in East.
- `product_name → category`: Laptop → Hardware; Suite → Software. The inverse is not assumed.
- `order_date → quarter`, and `order_date → fiscal_year` under the calendar-year rule.
- `customer_id` does not determine region, city, status or fiscal year. For example, C001 appears in Alles and Desques; C004 in Miro and Sumi; C005 in South and East.

## A.3 Normalization diagnosis

The flat source is in 1NF (atomic values, no repeating groups). Because `order_id` is a single-column key, it is in 2NF. It violates 3NF through the transitive dependencies `order_id → city → region`, `order_id → product_name → category`, and `order_id → order_date → fiscal_year, quarter`.

## A.4 3NF schema

| Relation | Primary key | Attributes | Foreign keys |
|---|---|---|---|
| `regions` | `region_id` | `region_name` (unique) | — |
| `cities` | `city_id` | `city_name` (unique), `region_id` | `region_id → regions` |
| `categories` | `category_id` | `category_name` (unique) | — |
| `products` | `product_id` | `product_name` (unique), `category_id` | `category_id → categories` |
| `order_statuses` | `status_id` | `status_name` (unique) | — |
| `calendar` | `order_date` | `fiscal_year`, `quarter` | — |
| `sales` | `order_id` | `order_date`, `city_id`, `product_id`, `customer_id`, `status_id`, `revenue`, `profit`, `orders` | `order_date → calendar`, `city_id → cities`, `product_id → products`, `status_id → order_statuses` |

`customer_id` is not materialized as a table because the source has no customer attributes; adding a one-column table would not remove any dependency. `orders` is a measure, not a dimension.

### A.4.1 DDL

This DDL is illustrative standard SQL. Datilandia is a read-only query engine; it loads the flat CSV as `sales_data` or uses the reconstruction view as a saved query.

```sql
CREATE TABLE regions (
  region_id   INTEGER PRIMARY KEY,
  region_name TEXT NOT NULL UNIQUE
);

CREATE TABLE cities (
  city_id    INTEGER PRIMARY KEY,
  city_name  TEXT NOT NULL UNIQUE,
  region_id  INTEGER NOT NULL REFERENCES regions(region_id)
);

CREATE TABLE categories (
  category_id   INTEGER PRIMARY KEY,
  category_name TEXT NOT NULL UNIQUE
);

CREATE TABLE products (
  product_id  INTEGER PRIMARY KEY,
  product_name TEXT NOT NULL UNIQUE,
  category_id INTEGER NOT NULL REFERENCES categories(category_id)
);

CREATE TABLE order_statuses (
  status_id   INTEGER PRIMARY KEY,
  status_name TEXT NOT NULL UNIQUE
);

CREATE TABLE calendar (
  order_date  TEXT PRIMARY KEY,
  fiscal_year INTEGER NOT NULL,
  quarter     TEXT NOT NULL CHECK (quarter IN ('Q1', 'Q2', 'Q3', 'Q4'))
);

CREATE TABLE sales (
  order_id    INTEGER PRIMARY KEY,
  order_date  TEXT NOT NULL REFERENCES calendar(order_date),
  city_id     INTEGER NOT NULL REFERENCES cities(city_id),
  product_id  INTEGER NOT NULL REFERENCES products(product_id),
  customer_id TEXT NOT NULL,
  status_id   INTEGER NOT NULL REFERENCES order_statuses(status_id),
  revenue     INTEGER NOT NULL,
  profit      INTEGER NOT NULL,
  orders      INTEGER NOT NULL
);
```

## A.5 Data-quality corrections

`quarter` matches `order_date` in all 67 source rows. `fiscal_year` is corrected in these 9 rows to the calendar year of `order_date`:

| `order_id` | `order_date` | Source `fiscal_year` | Normalized `fiscal_year` |
|---:|---|---:|---:|
| 37 | 2026-11-10 | 2025 | 2026 |
| 41 | 2026-06-28 | 2025 | 2026 |
| 43 | 2026-08-10 | 2025 | 2026 |
| 45 | 2026-10-20 | 2025 | 2026 |
| 47 | 2026-03-28 | 2025 | 2026 |
| 62 | 2026-09-15 | 2025 | 2026 |
| 63 | 2025-10-20 | 2026 | 2025 |
| 64 | 2025-11-25 | 2026 | 2025 |
| 66 | 2026-01-05 | 2025 | 2026 |

There are no duplicate `order_id` values, no duplicate complete rows, and no contradictions in `city → region` or `product_name → category`.

## A.6 Reconstruction view `sales_data`

Pivotante 5.0.0 has no `JOIN` syntax. The flat CSV in the examples file is the materialized `sales_data` source. The same source can be reconstructed from the 3NF schema with this saved query:

```sql
SELECT s.order_id,
       r.region_name AS region,
       c.city_name AS city,
       cat.category_name AS category,
       p.product_name,
       cal.quarter,
       cal.order_date,
       s.revenue,
       s.profit,
       s.orders,
       s.customer_id,
       cal.fiscal_year,
       st.status_name AS order_status
FROM sales AS s
INNER JOIN calendar AS cal ON s.order_date = cal.order_date
INNER JOIN cities AS c ON s.city_id = c.city_id
INNER JOIN regions AS r ON c.region_id = r.region_id
INNER JOIN products AS p ON s.product_id = p.product_id
INNER JOIN categories AS cat ON p.category_id = cat.category_id
INNER JOIN order_statuses AS st ON s.status_id = st.status_id
```

## A.7 3NF verification

- `regions`: `region_id` is the key; `region_name` depends on it.
- `cities`: `city_id` is the key; `city_name` and `region_id` depend on it. The region name is not stored, so there is no transitive dependency.
- `categories`: `category_id` is the key; `category_name` depends on it.
- `products`: `product_id` is the key; `product_name` and `category_id` depend on it. The category name is not stored.
- `order_statuses`: `status_id` is the key; `status_name` depends on it.
- `calendar`: `order_date` is the key; `fiscal_year` and `quarter` depend on the date.
- `sales`: `order_id` is the key; measures and foreign keys depend on `order_id`. No dimension names or date-derived attributes are stored.

No relation has partial dependencies (2NF) or transitive dependencies of non-prime attributes on the key (3NF).

## A.8 Reference totals

| Metric | Value |
|---|---:|
| `COUNT(*)` | 67 |
| Distinct customers | 59 |
| `SUM(revenue)` | 55825 |
| `SUM(profit)` | 16931 |
| `SUM(orders)` | 481 |
| `AVG(revenue)` | 833.21 |
| `MIN(revenue)` | 150 |
| `MAX(revenue)` | 1400 |


# Appendix B — Resolved Design Decisions

The following decisions are normative for Pivotante 5.0.0:

| # | Decision | Resolution |
|---:|---|---|
| 1 | Non-`SUM` total mode on the deepest axis field | Rejected with `LAYOUT_ERROR`. The deepest field has no child groups; its total comes from the totals query. |
| 2 | Grand total with different top-level modes | The row-side grand total is authoritative for the bottom-right cell. Without `ROWS`, the column-side grand total is used. |
| 3 | `HAVING` on parent levels | Evaluated top-down against the pre-filter totals. A removed group removes its descendants; displayed totals are recalculated from retained groups. |
| 4 | `COUNT(field)` semantics | Counts child groups whose total for that metric is non-NULL. The result is `0` when no child has a non-NULL total. |
| 5 | `NULLS` in generated SQL | Every generated `ORDER BY` emits an explicit `NULLS FIRST` or `NULLS LAST`, regardless of the target default. |
| 6 | Source name | Examples use the flat `sales_data` source. Appendix A defines its 3NF decomposition and reconstruction view. |
| 7 | Metric aliases | Mandatory and unique. `VALUES SUM(revenue)` without `AS` is a `SYNTAX_ERROR`. |
| 8 | Column order | Declared axis directions and `NULLS` placement are emitted directly in the detail and totals queries; there is no second ordering pass. |
| 9 | `TOTALS` modes vs `VALUES` | `SUM(field)`, `COUNT(field)`, `AVG(field)`, `MIN(field)`, `MAX(field)`, `FIRST(field)`, and `LAST(field)` consolidate leaf totals into parent totals; they do not change the aggregates in `VALUES`. The field remains the grouping key. |
| 10 | `NULL` axis keys | Retained as real headers labeled `(blank)`. The normalized dataset has no `NULL` keys; the examples file includes a marked derived fixture for that case. |
| 11 | Non-additive metrics | Parent totals consolidate child totals; they are not a second aggregation over source rows. For `AVG`, `MIN`, `MAX`, `FIRST`, and `LAST`, the result may differ from re-aggregating the union of source rows. |
| 12 | Axis and `TOTALS` syntax | `ROWS` and `COLUMNS` contain grouping fields only. The optional `TOTALS` clause maps each axis field to a total mode; when absent, every level uses `SUM`. If a `TOTALS` block is present, it must list every field of that axis exactly once. Only `SUM` is valid on a leaf level. |
| 13 | `FIRST` and `LAST` | Take the first or last non-NULL child total in the final order of the child groups. |
| 14 | Type checking of total modes | `SUM` and `AVG` require numeric metric totals; `MIN` and `MAX` require ordered comparable totals; `COUNT` yields an integer. A mismatch is a `TYPE_ERROR`. |
| 15 | `TOTALS` default and coverage | If `TOTALS` is absent, every level uses `SUM`. A present axis block must list every field of that axis. A missing field is `LAYOUT_ERROR`, a duplicate field is `SYNTAX_ERROR`, and an extra field is `UNKNOWN_FIELD`. |
