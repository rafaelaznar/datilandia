Here is the complete technical specification for Pivotante, adapted into English as a formal, SQL-inspired declarative query language for Excel-style pivot tables.

Pivotante Language Specification (v1.0)

**Language status**

This document specifies Pivotante v1.0 as a normative, implementation-oriented language specification.

**Normative keywords**

The keywords **MUST**, **SHOULD**, and **MAY** are to be interpreted as normative requirements, recommendations, and permitted options, respectively.

**Supported dialect scope**

This specification defines the Pivotante v1.0 core dialect. Engine-specific extensions and source-database SQL dialect behavior are outside the core scope.

**Conformance**

A conforming engine **MUST** preserve the canonical clause order, NULL behavior, and alias uniqueness defined or accepted by this specification. Clause order **MUST** remain FROM, FILTER, ROWS, COLUMNS, VALUES, TOTALS, and EMPTY AS. NULL values **MUST** retain their specified query and aggregation semantics rather than being converted to a non-NULL value implicitly. Every output alias **MUST** be unique; duplicate aliases **MUST** be rejected or resolved by a deterministic, documented mechanism.

Pivotante is a declarative, domain-specific query language (DSL) designed to generate dynamic, Excel-style pivot tables directly from tabular datasets or SQL subqueries. It abstracts away complex CASE WHEN pivots, dynamic SQL generation, and GROUPING SETS into a clean, readable syntax modeled after spreadsheet pivot quadrants.

1. Core Syntax & Clause Order

Every Pivotante query consists of a source definition followed by declarative layout clauses. Clauses must follow this canonical order:
sql
FROM <source_table_or_subquery>
[FILTER <boolean_expression>]
[ROWS <row_field_list>]
[COLUMNS <column_field_list>]
VALUES <aggregation_list>
[TOTALS <totals_configuration>]
[EMPTY AS <literal_value>]
Usa el código con precaución.

2. Lexical Elements & Data Types

• Identifiers: Unquoted (region, sales_amount) or double-quoted ("Region Name") for names containing spaces or reserved keywords.
• Literals:
	• Strings: Enclosed in single quotes ('Completed', 'North').
	• Numbers: Integer (2026) or decimal (19.99).
	• Booleans: TRUE, FALSE.
	• Nulls: NULL.
• Comments: Single-line comments start with --; multi-line comments are wrapped in /* ... */.

### 2.1 Formal EBNF Grammar

The following grammar is the authoritative syntax for the Pivotante v1.0 core dialect. Its notation is RFC 5234-style EBNF: `=` introduces a production, `|` denotes alternatives, `[...]` denotes an optional element, `{...}` denotes zero or more repetitions, `(...)` groups alternatives, and quoted text denotes a case-insensitive keyword or punctuation terminal. Literal punctuation such as `"("` and `")"` is enclosed in quotes. Implementations must consume comments and whitespace between terminals.

```ebnf
query
  = "FROM" source
    [ "FILTER" filter_expression ]
    [ "ROWS" row_field_list ]
    [ "COLUMNS" column_field_list ]
    "VALUES" value_list
    [ "TOTALS" totals_option ]
    [ "EMPTY" "AS" fallback_value ] ;

source
  = identifier
  | "(" sql_statement ")" ;

sql_statement
  = "SELECT" { sql_unit } ;

sql_unit
  = identifier
  | string_literal
  | numeric_literal
  | whitespace
  | sql_symbol
  | "(" sql_statement ")" ;

sql_symbol
  = "+" | "-" | "*" | "/" | "=" | "<" | ">"
  | "," | ";" | "." | ":" | "!" | "|" | "[" | "]" ;

whitespace
  = " " | "\t" | "\n" | "\r" ;

row_field_list
  = row_field_item { "," row_field_item } ;

row_field_item
  = field_expression
    [ "WITH" "SUBTOTALS" ]
    [ "ORDER" "BY" sort_key [ sort_direction ] ] ;

column_field_list
  = column_field_item { "," column_field_item } ;

column_field_item
  = field_expression
    [ "MAX" "COLUMNS" integer_literal
      [ "GROUP" "REST" "AS" display_identifier ] ]
    [ "ORDER" "BY" sort_key [ sort_direction ] ] ;

field_expression
  = identifier
  | date_bucket ;

sort_key
  = integer_literal
  | identifier
  | field_expression ;

sort_direction
  = "ASC" | "DESC" ;

date_bucket
  = "DATE_BUCKET" "(" identifier ","
    date_bucket_interval ")" ;

date_bucket_interval
  = string_literal ;

value_list
  = value_item { "," value_item } ;

value_item
  = aggregation [ "AS" share_mode ] [ "AS" display_identifier ]
  | "CALC" "(" calculated_expression ")" "AS" display_identifier ;

aggregation
  = aggregate_call ;

aggregate_call
  = "COUNT" "(" ( "*" | count_expression ) ")"
  | "COUNT_DISTINCT" "(" count_expression ")"
  | ( "SUM" | "AVG" | "MIN" | "MAX" )
    "(" ( identifier | calculated_expression ) ")" ;

count_expression
  = scalar_or_expression ;

scalar_or_expression
  = scalar_and_expression { "OR" scalar_and_expression } ;

scalar_and_expression
  = scalar_not_expression { "AND" scalar_not_expression } ;

scalar_not_expression
  = [ "NOT" ] scalar_predicate ;

scalar_predicate
  = scalar_comparison_expression
    [ ( "BETWEEN" scalar_additive_expression "AND" scalar_additive_expression
      | [ "NOT" ] "IN" "(" value { "," value } ")"
      | [ "NOT" ] "LIKE" string_literal
      | "IS" [ "NOT" ] "NULL" ) ] ;

scalar_comparison_expression
  = scalar_additive_expression
    [ comparison_operator scalar_additive_expression ] ;

scalar_additive_expression
  = scalar_multiplicative_expression
    { ( "+" | "-" ) scalar_multiplicative_expression } ;

scalar_multiplicative_expression
  = scalar_unary_expression
    { ( "*" | "/" ) scalar_unary_expression } ;

scalar_unary_expression
  = [ "+" | "-" ] scalar_primary_expression ;

scalar_primary_expression
  = identifier
  | literal
  | date_bucket
  | "(" scalar_or_expression ")" ;

share_mode
  = "%" "OF" ( "ROW" | "COLUMN" | "TOTAL" ) ;

calculated_expression
  = or_expression ;

filter_expression
  = or_expression ;

or_expression
  = and_expression { "OR" and_expression } ;

and_expression
  = not_expression { "AND" not_expression } ;

not_expression
  = [ "NOT" ] predicate ;

predicate
  = comparison_expression
    [ ( "BETWEEN" additive_expression "AND" additive_expression
      | [ "NOT" ] "IN" "(" value { "," value } ")"
      | [ "NOT" ] "LIKE" string_literal
      | "IS" [ "NOT" ] "NULL" ) ] ;

comparison_expression
  = additive_expression
    [ comparison_operator additive_expression ] ;

comparison_operator
  = "=" | "<>" | "!=" | "<" | "<=" | ">" | ">=" ;

additive_expression
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
  | aggregate_call
  | "(" or_expression ")" ;

value
  = literal ;

literal
  = string_literal
  | numeric_literal
  | boolean_literal
  | "NULL" ;

fallback_value
  = string_literal
  | numeric_literal
  | boolean_literal
  | "NULL" ;

totals_option
  = "ROWS" | "COLUMNS" | "BOTH" | "NONE" ;

display_identifier
  = quoted_identifier ;

identifier
  = unquoted_identifier | quoted_identifier ;

unquoted_identifier
  = identifier_start { identifier_part } ;

identifier_start
  = ("A"-"Z" | "a"-"z" | "_") ;

identifier_part
  = (identifier_start | "0"-"9") ;

quoted_identifier
  = '"' { quoted_identifier_character } '"' ;

string_literal
  = "'" { string_character } "'" ;

numeric_literal
  = [ "-" ] ( integer_literal [ "." digit { digit } ]
            | "." digit { digit } )
    [ ( "E" | "e" ) [ "+" | "-" ] integer_literal ] ;

integer_literal
  = digit { digit } ;

digit
  = "0"-"9" ;

boolean_literal
  = "TRUE" | "FALSE" ;
```

**Clause order.** The canonical order is `FROM`, optional `FILTER`, optional `ROWS`, optional `COLUMNS`, required `VALUES`, optional `TOTALS`, and optional `EMPTY AS`. An engine must reject a clause that appears outside this order or appears more than once. `FROM`, `VALUES`, and `EMPTY AS` are the complete clause names for these positions.

**Identifier case rules.** Keywords, built-in function names, modifiers, and enumeration values are case-insensitive. For example, `from`, `FROM`, and `From` have the same meaning. Unquoted identifiers are case-insensitive for matching: `Region`, `region`, and `REGION` identify the same field. Engines should normalize unquoted identifiers to a documented canonical case before resolving source metadata. Quoted identifiers are case-preserving and case-sensitive in matching. `"Region Name"` and `"region name"` are distinct quoted identifiers unless the source system itself provides a case-insensitive mapping. A reserved keyword or a name containing spaces or other punctuation must be written as a quoted identifier. An unquoted identifier is one or more ASCII letters or underscores followed by ASCII letters, digits, or underscores; it must not be a keyword unless quoted.

**Quoted identifiers and aliases.** A quoted identifier is delimited by double quotes. Its contents may include spaces and keyword text. Escape a double quote by doubling it: `"Customer ""Tier"""`. Display aliases use the same quoted-identifier syntax and must be unique within the query result. Duplicate aliases must be rejected or resolved by a deterministic, engine-documented mechanism.

**Comments.** `--` starts a single-line comment that ends at the next line break or end of input. `/*` starts a block comment that ends at the first `*/`; block comments do not nest. Comments may appear wherever whitespace is legal. An unterminated block comment is an invalid query. Comments do not occur inside string or quoted-identifier literals.

**Numeric, boolean, and null literals.** Integers contain decimal digits; decimals may have digits before or after the point, but not both omitted. The optional leading minus is a unary negative sign. Exponents use `E` or `e` with an optional sign. Decimal and exponent spellings are implementation-representable numeric values, not engine-specific SQL literals. `TRUE` and `FALSE` are booleans and `NULL` is the null value. `NULL` in `FILTER` follows SQL-style three-valued logic.

`DATE_BUCKET` interval values are case-insensitive keywords, but they must be supplied as string literals with exactly one of these values: `YEAR`, `QUARTER`, `MONTH`, or `WEEK`.

`quoted_identifier_character` is one character other than a double quote, or the escape pair `""`. `string_character` is one character other than a single quote, or the escape pair `''`. The grammar's SQL subquery is target-SQL text beginning with `SELECT`; Pivotante does not redefine the complete SQL dialect used inside the source subquery.

**String escaping.** String literals are enclosed in single quotes. Escape a single quote by doubling it: `'North''s'`. A backslash has no special escape meaning in the core dialect; `'\n'` is the literal characters backslash and `n`, not a newline. Literal newlines in a source string are not permitted. An input that requires a newline or backslash character must use a target-database string representation outside this core grammar or an engine-specific escape extension, not an implementation of backslash escape sequences.

### 2.2 Expression Semantics

**Comparison and logical operators.** `=` and `!=`/`<>` test equality and inequality, while `<`, `<=`, `>`, and `>=` test ordering. Comparisons are defined only for values of compatible types; engines MUST reject incompatible operand types rather than silently coerce strings to numbers or booleans. `AND`, `OR`, and `NOT` implement SQL-style three-valued boolean logic: `TRUE AND NULL` is `NULL`, `TRUE OR NULL` is `TRUE`, `FALSE AND NULL` is `FALSE`, `FALSE OR NULL` is `TRUE` only when the other operand is `TRUE` and otherwise `NULL`, and `NOT NULL` is `NULL`. `BETWEEN`, `IN`, `NOT IN`, `LIKE`, and `NOT LIKE` have the same three-valued result rules for `NULL` and for false/empty comparisons. A comparison expression is invalid when it is chained, as in `a < b < c`; it is valid only in the grammar's additive-expression form with one comparison operator.

**Arithmetic types.** `+`, `-`, `*`, and `/` require numeric operands. Unary `+` and unary `-` likewise require a numeric operand. Integer literals and integer source values are integer type; decimal and exponent literals and decimal source values are decimal type. Integer and decimal operands in the same expression promote the result to decimal; integer `+`, `-`, and `*` produce an integer result only when both operands are integer and the result is representable. Unlike integer `+`, `-`, and `*`, integer division `/` MUST always yield a decimal result, and MUST NOT truncate the quotient to an integer. Each conforming engine MUST document the precision and scale of its decimal `/` results, including whether operands are rounded before evaluation and how decimal results are promoted or rounded across engine-specific storage and interchange boundaries. Engines MUST reject type mismatch and numeric overflow rather than implicitly converting a string or boolean to a number. Decimal precision and scale are implementation-representable but MUST preserve enough significant digits for calculated-expression interoperability and MUST be sufficient to preserve the value of evaluated expressions within the engine's documented numeric range.

**NULL propagation.** `NULL` propagates through arithmetic, unary numeric operators, and comparisons: an arithmetic or comparison operation with one or more `NULL` operands produces `NULL`. Logical operators use the three-valued logic in the preceding paragraph rather than ordinary boolean truth rules. `NULL` IS NOT an implicit zero and does not participate as a numerical value in an aggregate.

**IS NULL and IS NOT NULL.** `IS NULL` returns `TRUE` if and only if the operand is `NULL`, and `FALSE` otherwise. `IS NOT NULL` returns `TRUE` if and only if the operand is not `NULL`, and `FALSE` otherwise. These predicates do not propagate NULL; they always yield `TRUE` or `FALSE` (including for an operand of unknown or SQL-NULL origin).

**Boolean FILTER expressions.** Every `FILTER` expression MUST evaluate to boolean type. `TRUE` includes a row, `FALSE` excludes a row, and `NULL` excludes a row just like `FALSE`; therefore FILTER is a SQL row-level predicate rather than a general truthy/falsy conversion. A typed expression producing a numeric or string value is invalid as a FILTER unless it is enclosed in an explicit comparison or boolean predicate that yields boolean type.

**Function type signatures.** Built-in functions MUST be checked against these signatures:

| Function | Accepted arguments | Result type |
|---|---|---|
| `COUNT(*)` | zero field expression terms represented by `*` | integer |
| `COUNT(expression)` | one expression or field; it may evaluate to NULL | integer |
| `COUNT_DISTINCT(expression)` | one expression or field; it may evaluate to NULL | integer |
| `SUM(identifier | calculated_expression)` | numeric expression | decimal or integer, according to the operand type and engine numeric rules |
| `AVG(identifier | calculated_expression)` | numeric expression | decimal |
| `MIN(identifier | calculated_expression)` | ordered compatible expression | operand type |
| `MAX(identifier | calculated_expression)` | ordered compatible expression | operand type |
| `DATE_BUCKET(identifier, interval_string)` | date/timestamp field and exactly `YEAR`, `QUARTER`, `MONTH`, or `WEEK` | date/timestamp bucket type |
| `CALC(calculated_expression)` | expression composed from aggregates and/or numeric fields | result type of the calculated expression |
| `DIFF_PREV(SUM(...), axis_field)` | a value expression plus a row/column axis field | numeric result of the value expression |

`COUNT(*)` counts source rows, including rows whose fields are `NULL`. `COUNT(expression)` or `COUNT(field)` counts only rows for which the expression or field evaluates to a non-NULL value; it does not count `NULL` results. COUNT expressions may be nullable or non-nullable and are evaluated before counting. `COUNT_DISTINCT(field-or-expression)` ignores `NULL` values and counts distinct non-NULL evaluated values. `SUM`, `AVG`, `MIN`, and `MAX` accept only operands permitted by their signature; an engine MUST report a type error for unsupported operand types. Function names are case-insensitive.

**Division by zero.** `/` is defined for a divisor that is numeric and non-NULL. Integer operands divided with `/` produce a decimal result under the engine-documented precision and scale rules, never an integer-truncated quotient. If the divisor evaluates to zero, the result MUST be `NULL`; it MUST NOT produce infinity or raise an execution failure. If the dividend is `NULL`, the result is `NULL` regardless of divisor; for any non-NULL dividend and zero divisor, division is `NULL`. This rule applies equally to calculated expressions and aggregate-derived divisions.

**Aliases and duplicate output names.** The optional display alias following an aggregation or share modifier and the required alias following `CALC(...)` MUST be a quoted identifier. If no explicit alias is supplied for a standard aggregation or share item, the engine MUST generate a deterministic implementation-defined display name, document that name, and ensure it is unique in the result. Every output alias/name MUST be unique within a query result. Duplicate explicit aliases and collisions with implicit names MUST be rejected or resolved by a deterministic, engine-documented mechanism. Replacing one alias with another or silently numbering duplicate names is permitted only if that mechanism is implemented consistently and disclosed by the engine.

**Aggregate behavior for empty sets.** An aggregate group containing no source rows has `COUNT(*) = 0`, `COUNT(field) = 0`, and `COUNT_DISTINCT(field) = 0`. `SUM`, `AVG`, `MIN`, and `MAX` return `NULL` for an empty set. A matrix intersection or pivot header with no source rows is separately subject to `EMPTY AS`: the default `EMPTY AS NULL` may keep the intersection `NULL`, while `EMPTY AS <literal>` substitutes the configured literal only in the final matrix representation. `EMPTY AS` does not alter the underlying aggregate's empty-set result or turn aggregate `NULL` values into zeros.

**Operator precedence.** Operators are listed from lowest to highest precedence:

| Precedence | Operators |
|---:|---|
| 1 (lowest) | `OR` |
| 2 | `AND` |
| 3 | `NOT` |
| 4 | `=`, `<>`, `!=`, `<`, `<=`, `>`, `>=`, `BETWEEN`, `IN`, `NOT IN`, `LIKE`, `NOT LIKE`, `IS NULL`, `IS NOT NULL` |
| 5 | `+`, `-` (binary) |
| 6 | `*`, `/` |
| 7 (highest) | unary `+`, unary `-`, literals, identifiers, parenthesized expressions |

Comparisons are non-chaining: `a < b < c` is invalid. `NOT IN` and `NOT LIKE` where supported must be written with explicit `NOT`, as in `x NOT IN (...)` and `x NOT LIKE 'pattern'`; `NOT IN` binds at the comparison level, not as a negated `IN` expression. Parentheses may override precedence. `AND` has higher precedence than `OR`, and `NOT` has higher precedence than `AND` but lower precedence than comparisons. Arithmetic expressions follow conventional left-associative evaluation.

3. Clause Definitions


3.1 FROM (Required)

Defines the underlying dataset. Accepts a table name, a view, or an inline SQL subquery wrapped in parentheses.
sql
FROM sales_data
-- or
FROM (SELECT * FROM raw_orders WHERE status = 'SHIPPED')
Usa el código con precaución.

3.2 FILTER (Optional)

Applies pre-aggregation row-level filtering (equivalent to SQL WHERE or Excel's Report Filter quadrant).
sql
FILTER fiscal_year = 2026 AND region IN ('North', 'East')
Usa el código con precaución.

3.3 ROWS (Optional)

Defines vertical grouping hierarchies. Multiple fields create nested levels from left to right.
Fields in a ROWS list are separated by commas. The first field is the outermost level and each subsequent field is nested beneath it.

**Modifiers**
- `WITH SUBTOTALS` applies to the ROWS clause. It adds one subtotal row for every nested group level except the deepest level; a grand total is emitted only when `TOTALS ROWS`, `TOTALS BOTH`, or the row side of a `BOTH` configuration requires it.
- `ORDER BY <field> [ASC | DESC]` accepts one field in the ROWS list. Omitting ASC or DESC means ASC. Missing and NULL keys sort as one key and appear last for ASC and first for DESC. If sorting produces ties, engines MUST break ties by the canonical field value (ascending), then by ordinal source position if the source has no stable canonical value. Engines SHOULD document stable tie behavior.

sql
ROWS region WITH SUBTOTALS, category ORDER BY category ASC
Usa el código con precaución.

3.4 COLUMNS (Optional)

Defines dynamic horizontal pivot headers. Unique values discovered at runtime automatically expand into columns.
- `MAX COLUMNS <int>` MUST be a positive integer. It limits distinct discovered column keys after discovery. When the limit is exceeded, keys ranked outside the limit MUST be combined into a synthetic `GROUP REST` key.
- `GROUP REST AS "<label>"` defines the rest-key display label. If omitted, engines MAY use `"Other"` and MUST document that default.
- `ORDER BY <field> [ASC | DESC]` controls header discovery order. For a value field, engines MUST reject the modifier as ambiguous unless the field is unambiguously a COLUMNS field or an indexed header expression. NULL headers sort according to the row/column NULL ordering rules and are included as a distinct header.
- Column discovery MUST apply FILTER and discard keys whose expressions evaluate to NULL unless NULL is explicitly resolved as a key. Final columns are sorted by the declared order, then by canonical key ascending, to break ties.
- When rest grouping is active, the rest bucket is placed after retained natural columns unless a header sorting rule explicitly places it elsewhere. The retained set uses canonical key order before applying any post-discovery limit.

sql
COLUMNS quarter ORDER BY quarter ASC
-- With cardinality guard:
COLUMNS product_name MAX COLUMNS 15 GROUP REST AS "Other Products"
Usa el código con precaución.

3.5 VALUES (Required)

Defines the metrics calculated at each row/column intersection.
• Standard Aggregations: SUM(), AVG(), COUNT(), COUNT_DISTINCT(), MIN(), MAX().
• Aliasing: Defined using AS "<Display Name>".
sql
VALUES 
    SUM(revenue) AS "Total Revenue",
    AVG(revenue) AS "Average Ticket",
    COUNT_DISTINCT(customer_id) AS "Unique Buyers"
Usa el código con precaución.

3.6 TOTALS (Optional)

Controls grand totals across axes.
• Options: ROWS, COLUMNS, BOTH, NONE (default: NONE).
sql
TOTALS BOTH
Usa el código con precaución.

3.7 EMPTY AS (Optional)

Specifies the fallback value when a row/column intersection contains no records (default: NULL).
sql
EMPTY AS 0
Usa el código con precaución.

4. Built-in Analytical Functions

Pivotante includes native functions to replicate Excel's "Show Values As" and date-grouping features without window functions:

Feature	Syntax	Behavior
Date Bucketing	DATE_BUCKET(date_col, 'YEAR' | 'QUARTER' | 'MONTH' | 'WEEK')	Groups raw timestamps into calendar intervals inside ROWS or COLUMNS.
Share of Row	`SUM(val) AS % OF ROW AS "<Alias>"`	Calculates each cell as a percentage of its horizontal row total.
Share of Column	`SUM(val) AS % OF COLUMN AS "<Alias>"`	Calculates each cell as a percentage of its vertical column total.
Share of Grand Total	`SUM(val) AS % OF TOTAL AS "<Alias>"`	Calculates each cell as a percentage of the entire matrix total.
Period Variance	`DIFF_PREV(SUM(val), <axis_field>) AS "<Alias>"`	Computes the signed difference from the preceding value on the selected axis.
Calculated Fields	`CALC(<expression>) AS "<Alias>"`	Evaluates post-aggregation arithmetic (e.g., `CALC(SUM(profit) / SUM(revenue)) AS "Margin %"`)

**Totals semantics**
- `TOTALS ROWS` emits only row-axis subtotals and the configured row grand totals.
- `TOTALS COLUMNS` emits only column totals.
- `TOTALS BOTH` emits both row and column totals and their shared grand-total cell.
- `TOTALS NONE` emits no totals. It is the default.
- Subtotal labels MUST use the group field's display name, followed by `" Total"`; engines MUST document locale-specific label changes.

**Share denominators**
- `% OF ROW` divides a cell by the corresponding row total across discovered columns, including retained/rest buckets.
- `% OF COLUMN` divides a cell by the corresponding column total across all nested row groups.
- `% OF TOTAL` divides a cell by the matrix grand total.
- A missing or zero denominator yields `NULL`. An empty numerator yields `NULL`. A non-NULL numerator and non-NULL denominator produce a decimal ratio.

**Period variance**
- `DIFF_PREV(SUM(val), <axis_field>)` uses `<axis_field>` to determine the axis and must reference a field from `ROWS` or `COLUMNS`.
- If the field is a COLUMNS field, the preceding discovered header is used. If it is a ROWS field, the preceding nested row is used.
- The result is `SUM(val) at current - SUM(val) at preceding`.
- The first position has no preceding value and MUST return `NULL`. Missing intersections yield `NULL` and do not use `EMPTY AS 0` unless the engine first materializes a real value of zero.

**Calculated fields**
- `CALC` expressions are evaluated after source aggregation and may reference aggregate calls, numeric row/column fields, and constants.
- A calculated field MUST declare dependencies on each aggregate it contains. Engines MUST reject references to fields not present in the row/column axes or aggregation set.
- Division in `CALC` follows the core division-by-zero rule.


5. Complete Reference Example

sql
FROM enterprise_sales
FILTER order_status = 'Completed' AND order_date >= '2026-01-01'
ROWS 
    region WITH SUBTOTALS, 
    sales_rep
COLUMNS 
    DATE_BUCKET(order_date, 'QUARTER') ORDER BY 1 ASC
VALUES 
    SUM(amount) AS "Sales ($)",
    SUM(amount) AS % OF ROW AS "Quarterly Share",
    CALC(SUM(profit) / SUM(amount)) AS "Margin"
TOTALS BOTH
EMPTY AS 0
Usa el código con precaución.

6. Execution Pipeline Specification

Any compliant Pivotante engine must execute queries in four deterministic stages:
1. AST Parsing: Tokenize and parse the query into an Abstract Syntax Tree, validating clause ordering and type compatibility.
2. Header Discovery (Introspection): Execute a distinct metadata scan on COLUMNS expressions using the FILTERed source. Apply MAX COLUMNS only after discovery, sort equal headers by canonical key ascending, and resolve the exact output schema.
3. SQL Transpilation: Compile the AST and discovered headers into target SQL using GROUPING SETS / ROLLUP for subtotals and conditional aggregation (FILTER (WHERE ...) or CASE WHEN) for pivoted metrics.
4. Matrix Formatting: Resolve all SQL aggregates and calculated fields, then post-process the flat result set into a multi-header grid. Apply EMPTY AS only to missing matrix intersections after aggregate evaluation and relative percentage calculations.

**Diagnostic Contract**

Every parser or transpiler error MUST include `code`, `message`, `position`, `source_fragment`, and `phase`. `position` MUST contain `line`, `column`, and `offset`. Diagnostics MUST use one of these stable codes:

| Code | Meaning |
|---|---|
| `SYNTAX_ERROR` | Query text is outside the grammar or clause order is invalid. |
| `UNKNOWN_FIELD` | An identifier cannot be resolved against FROM metadata. |
| `TYPE_ERROR` | An expression, aggregation, or modifier has incompatible types. |
| `DUPLICATE_ALIAS` | A display alias is duplicated or cannot be made uniquely disambiguated. |
| `UNSUPPORTED_FUNCTION` | A function or dialect-specific overload is not available. |
| `CARDINALITY_LIMIT` | MAX COLUMNS is zero, negative, or otherwise exceeds engine limits. |

Engines MUST fail before header discovery or SQL execution when the AST or types are invalid. Header discovery failures MUST be reported with phase `DISCOVERY`. Runtime SQL failures MUST preserve a target-database diagnostic in `details` without exposing SQL credentials or source secrets.

**Canonical Result Shape**

The logical result shape is engine-independent:

```json
{
  "rowHeaders": [
    {"level": 0, "key": "North", "label": "North", "subtotal": false}
  ],
  "columnHeaders": [
    {"level": 0, "key": "2026-Q1", "label": "Q1 2026", "rest": false}
  ],
  "cells": [
    {"rowKey": ["North"], "columnKey": ["2026-Q1"], "value": 12500.50}
  ],
  "totals": {
    "rows": false,
    "columns": false,
    "grand": null
  },
  "missing": "empty-as"
}
```

- Row keys and column keys MUST preserve hierarchy depth and declared header order.
- `cells` MUST contain a record for each materialized row/column intersection or reference every missing intersection through `missing`.
- Missing intersections MUST have `value: null` when EMPTY AS is NULL; when EMPTY AS is literal, `value` MUST equal that literal.
- Totals MUST be distinguishable from ordinary cells through `subtotal` or `total` metadata.
- This JSON shape is a logical contract, not the required engine implementation or wire format.

**Conformance Checklist**

An engine claiming Pivotante v1.0 conformance MUST:
- Reject queries outside canonical clause order.
- Apply FILTER before header discovery and aggregation.
- Resolve identifiers and aliases deterministically.
- Emit stable diagnostics and preserve NULL semantics.
- Discover headers before compiling SQL.
- Apply EMPTY AS only during final matrix formatting.
- Produce row and column nesting, totals, shares, and rest groups exactly as documented.

**Change Summary**

This v1.0.1 specification revision adds normative preambles, formal EBNF, deterministic expression and layout semantics, analytical edge cases, stable diagnostics, a logical result contract, and a clarified execution pipeline.
