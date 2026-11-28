# SQL tests · Education (8 tables, 190 queries, 32 scripts)

Document generated from `tests-sql-education.json`.

**Contents:** 8 tables · 190 queries · 32 scripts.

> Load `tests-sql-education.json` with the **Data** button in markilandia so that the embeds in this document run each query and script and show their result.

## Queries

### A · Basics and aggregation

#### A01 · All campuses

SELECT * over campuses: the columns in table order and the 4 rows by identifier.

Keywords: `education`, `basics`, `campuses`, `select-star`

```sql
SELECT * FROM campuses ORDER BY id_campus
```

{{query:A01 · All campuses}}

#### A02 · Projection with aliases and arithmetic

Divides the capacity by 1000 and sorts by capacity descending: the 4 campuses from largest to smallest.

Keywords: `education`, `basics`, `campuses`

```sql
SELECT name AS campus, city, max_capacity / 1000 AS capacity_thousands, founded_year FROM campuses ORDER BY max_capacity DESC
```

{{query:A02 · Projection with aliases and arithmetic}}

#### A03 · Large classrooms (>= 55 seats)

Numeric filter with >= and order by capacity descending and code: 5 classrooms.

Keywords: `education`, `basics`, `classrooms`

```sql
SELECT code, capacity, floor FROM classrooms WHERE capacity >= 55 ORDER BY capacity DESC, code
```

{{query:A03 · Large classrooms (>= 55 seats)}}

#### A04 · Classrooms with a projector

Boolean filter (has_projector = true): 10 classrooms sorted by capacity.

Keywords: `education`, `basics`, `classrooms`

```sql
SELECT code, capacity FROM classrooms WHERE has_projector = true ORDER BY capacity DESC, code
```

{{query:A04 · Classrooms with a projector}}

#### A05 · Teachers who do not research and have a heavy load

AND of a boolean (false) and a range (>= 10 h): 3 teachers.

Keywords: `education`, `basics`, `teachers`

```sql
SELECT name, last_name, category, weekly_hours FROM teachers WHERE is_researcher = false AND weekly_hours >= 10 ORDER BY weekly_hours DESC, last_name
```

{{query:A05 · Teachers who do not research and have a heavy load}}

#### A06 · Students with no phone

IS NULL over a field with gaps: 8 students.

Keywords: `education`, `basics`, `students`, `null`

```sql
SELECT name, last_name FROM students WHERE phone IS NULL ORDER BY last_name
```

{{query:A06 · Students with no phone}}

#### A07 · Teachers with an office (IS NOT NULL) in upper case

IS NOT NULL plus UPPER over two columns: 9 teachers with an office, the 3 associates without one are left out.

Keywords: `education`, `basics`, `teachers`, `null`

```sql
SELECT id_teacher, UPPER(name) AS name, UPPER(last_name) AS last_name, office FROM teachers WHERE office IS NOT NULL ORDER BY id_teacher
```

{{query:A07 · Teachers with an office (IS NOT NULL) in upper case}}

#### A08 · Last names that start with G

LIKE with a trailing wildcard: 3 students called García, Gómez and Guerrero.

Keywords: `education`, `basics`, `students`, `like`

```sql
SELECT name, last_name FROM students WHERE last_name LIKE 'G%' ORDER BY id_student
```

{{query:A08 · Last names that start with G}}

#### A09 · Institutional addresses that do not start with a

LIKE and NOT LIKE combined with LIMIT 5 over the sorted address.

Keywords: `education`, `basics`, `students`, `like`

```sql
SELECT name, last_name, email FROM students WHERE email LIKE '%@alu.es' AND email NOT LIKE 'a%' ORDER BY email LIMIT 5
```

{{query:A09 · Institutional addresses that do not start with a}}

#### A10 · Three given classrooms (IN) and the remainder of the division

IN with a list of codes and the remainder operator (%): the remainder of dividing the capacity by 10.

Keywords: `education`, `basics`, `classrooms`, `in`

```sql
SELECT code, capacity, capacity % 10 AS remainder FROM classrooms WHERE code IN ('K-101', 'B-SALA', 'M-001') ORDER BY code
```

{{query:A10 · Three given classrooms (IN) and the remainder of the division}}

#### A11 · Degrees that are neither computing nor education (NOT IN)

NOT IN with three codes: Data Science, Software Engineering and Law are left.

Keywords: `education`, `basics`, `degrees`, `in`

```sql
SELECT code, name, level FROM degrees WHERE code NOT IN ('GINF', 'GMED', 'GEDU') ORDER BY code
```

{{query:A11 · Degrees that are neither computing nor education (NOT IN)}}

#### A12 · Enrollments passed with a grade between 7 and 8 (BETWEEN)

BETWEEN includes the ends: 12 enrollments with a grade of 7, 7.5 or 8, sorted by grade.

Keywords: `education`, `basics`, `enrollments`, `between`

```sql
SELECT id_enrollment, grade FROM enrollments WHERE grade BETWEEN 7 AND 8 ORDER BY grade, id_enrollment
```

{{query:A12 · Enrollments passed with a grade between 7 and 8 (BETWEEN)}}

#### A13 · First and second year subjects with few credits

Parentheses with OR, a boolean and NOT BETWEEN: 9 compulsory subjects of years 1 and 2 with fewer than 7 credits.

Keywords: `education`, `basics`, `subjects`, `between`

```sql
SELECT name, year, credits FROM subjects WHERE (year = 1 OR year = 2) AND is_compulsory = true AND credits NOT BETWEEN 7 AND 10 ORDER BY name
```

{{query:A13 · First and second year subjects with few credits}}

#### A14 · Teacher categories (DISTINCT)

DISTINCT over a text column: 4 professional categories.

Keywords: `education`, `basics`, `teachers`, `distinct`

```sql
SELECT DISTINCT category FROM teachers ORDER BY category
```

{{query:A14 · Teacher categories (DISTINCT)}}

#### A15 · Level/officialness combinations (DISTINCT of two columns)

DISTINCT over two columns, a boolean included: 3 combinations.

Keywords: `education`, `basics`, `degrees`, `distinct`

```sql
SELECT DISTINCT level, is_official FROM degrees ORDER BY level, is_official
```

{{query:A15 · Level/officialness combinations (DISTINCT of two columns)}}

#### A16 · Totals of the records (COUNT, DISTINCT, MIN, MAX, SUM)

Aggregates with no GROUP BY: 48 enrollments, 32 graded, 21 students and 14 subjects with enrollments.

Keywords: `education`, `basics`, `enrollments`, `aggregate`, `distinct`

```sql
SELECT COUNT(*) AS enrollments, COUNT(grade) AS with_grade, COUNT(DISTINCT id_student) AS students, COUNT(DISTINCT id_subject) AS subjects, MIN(grade) AS min_grade, MAX(grade) AS max_grade, SUM(grade) AS grade_sum FROM enrollments
```

{{query:A16 · Totals of the records (COUNT, DISTINCT, MIN, MAX, SUM)}}

#### A17 · Enrollments by status

GROUP BY with two aggregates: the 13 active and the 3 cancelled have no grade, the 32 passed do.

Keywords: `education`, `basics`, `enrollments`, `group-by`, `aggregate`

```sql
SELECT status, COUNT(*) AS total, COUNT(grade) AS graded FROM enrollments GROUP BY status ORDER BY total DESC, status
```

{{query:A17 · Enrollments by status}}

#### A18 · Enrollments by academic year (HAVING)

GROUP BY with HAVING COUNT(*) > 20: both years go over the threshold.

Keywords: `education`, `basics`, `enrollments`, `group-by`, `having`, `aggregate`

```sql
SELECT academic_year, COUNT(*) AS total FROM enrollments GROUP BY academic_year HAVING COUNT(*) > 20 ORDER BY academic_year
```

{{query:A18 · Enrollments by academic year (HAVING)}}

#### A19 · Students with three or more enrollments

GROUP BY over the M:M plus HAVING >= 3: 10 students, one of them with 4 enrollments.

Keywords: `education`, `basics`, `enrollments`, `group-by`, `having`, `aggregate`

```sql
SELECT id_student, COUNT(*) AS enrollments FROM enrollments GROUP BY id_student HAVING COUNT(*) >= 3 ORDER BY enrollments DESC, id_student
```

{{query:A19 · Students with three or more enrollments}}

#### A20 · Classrooms by the first letter of the code (GROUP BY with an expression)

GROUP BY over SUBSTR: 7 different initials with their number of classrooms and added seats.

Keywords: `education`, `basics`, `classrooms`, `group-by`, `aggregate`

```sql
SELECT SUBSTR(code, 1, 1) AS initial, COUNT(*) AS classrooms, SUM(capacity) AS seats FROM classrooms GROUP BY SUBSTR(code, 1, 1) ORDER BY initial
```

{{query:A20 · Classrooms by the first letter of the code (GROUP BY with an expression)}}

#### A21 · Grades of subject 6, from highest to lowest (NULLS LAST)

ORDER BY ... DESC NULLS LAST: the cancelled enrollment with no grade goes last.

Keywords: `education`, `basics`, `enrollments`

```sql
SELECT id_enrollment, grade FROM enrollments WHERE id_subject = 6 ORDER BY grade DESC NULLS LAST, id_enrollment
```

{{query:A21 · Grades of subject 6, from highest to lowest (NULLS LAST)}}

#### A22 · Grades of subject 6, from lowest to highest (nulls first)

In ASC the nulls go first by default: the enrollment with no grade heads the result.

Keywords: `education`, `basics`, `enrollments`

```sql
SELECT id_enrollment, grade FROM enrollments WHERE id_subject = 6 ORDER BY grade ASC, id_enrollment
```

{{query:A22 · Grades of subject 6, from lowest to highest (nulls first)}}

#### A23 · Oldest students, page 2 (LIMIT/OFFSET)

It sorts by date of birth and skips the first two rows: 4 rows from the third one on.

Keywords: `education`, `basics`, `students`

```sql
SELECT name, last_name, birth_date FROM students ORDER BY birth_date LIMIT 4 OFFSET 2
```

{{query:A23 · Oldest students, page 2 (LIMIT/OFFSET)}}

#### A24 · Four students starting at the fourth (MySQL style LIMIT)

LIMIT 3, 4 is the same as OFFSET 3 LIMIT 4: it returns identifiers 4 to 7.

Keywords: `education`, `basics`, `students`

```sql
SELECT id_student, name FROM students ORDER BY id_student LIMIT 3, 4
```

{{query:A24 · Four students starting at the fourth (MySQL style LIMIT)}}

#### A25 · Record of three students (concatenation and text functions)

|| concatenates, LENGTH counts characters and SUBSTR trims: last names in upper case and three initials.

Keywords: `education`, `basics`, `students`, `in`

```sql
SELECT UPPER(last_name) || ', ' || name AS record, LENGTH(name) AS letters, SUBSTR(name, 1, 3) AS initials FROM students WHERE id_student IN (1, 9, 14) ORDER BY id_student
```

{{query:A25 · Record of three students (concatenation and text functions)}}

#### A26 · Missing grades: COALESCE, IFNULL, NULLIF and ROUND

The null values are replaced by -1 and 0; NULLIF turns «cancelled» into null and the distance to 7.5 spreads the null.

Keywords: `education`, `basics`, `enrollments`, `in`

```sql
SELECT id_enrollment, COALESCE(grade, -1) AS grade_coalesce, IFNULL(grade, 0) AS grade_ifnull, NULLIF(status, 'cancelled') AS status_or_null, ROUND(ABS(grade - 7.5), 2) AS distance FROM enrollments WHERE id_enrollment IN (2, 6, 28, 30, 48) ORDER BY id_enrollment
```

{{query:A26 · Missing grades: COALESCE, IFNULL, NULLIF and ROUND}}

#### A27 · Campus phone normalised

IFNULL + TRIM + NULLIF: the campus with no phone returns null instead of an empty string.

Keywords: `education`, `basics`, `campuses`

```sql
SELECT id_campus, NULLIF(TRIM(IFNULL(phone, '')), '') AS phone, name FROM campuses ORDER BY id_campus
```

{{query:A27 · Campus phone normalised}}

#### A28 · Boards that are not digital (comment and brackets)

The -- comment is ignored, [code] is the same as code and <> also discards the nulls.

Keywords: `education`, `basics`, `classrooms`

```sql
-- Classrooms with a chalk board (those with an unknown board do not count)
SELECT [code] AS classroom, [board_type] AS board, capacity FROM classrooms WHERE board_type <> 'digital' ORDER BY code
```

{{query:A28 · Boards that are not digital (comment and brackets)}}

#### A29 · Teachers who are not associates (NOT)

NOT over an equality: 9 of the 12 teachers are left.

Keywords: `education`, `basics`, `teachers`

```sql
SELECT name, category FROM teachers WHERE NOT (category = 'Associate') ORDER BY id_teacher
```

{{query:A29 · Teachers who are not associates (NOT)}}

#### A30 · A query with no results

No classroom goes over 200 seats: the result has the columns and zero rows (rowCount 0).

Keywords: `education`, `basics`, `classrooms`

```sql
SELECT code FROM classrooms WHERE capacity > 200
```

{{query:A30 · A query with no results}}

#### A31 · Average grade per subject (HAVING and ORDER BY by alias)

Only subjects with two or more grades; it sorts by the average and a tie is broken by identifier.

Keywords: `education`, `basics`, `enrollments`, `group-by`, `having`, `aggregate`

```sql
SELECT id_subject, COUNT(*) AS enrollments, COUNT(grade) AS graded, ROUND(AVG(grade), 2) AS average_grade FROM enrollments GROUP BY id_subject HAVING COUNT(grade) >= 2 ORDER BY average_grade DESC, id_subject
```

{{query:A31 · Average grade per subject (HAVING and ORDER BY by alias)}}

#### A32 · Students and addresses per campus

GROUP BY id_campus with COUNT(*) and COUNT(email): 18 of the 24 students have an address.

Keywords: `education`, `basics`, `students`, `group-by`, `aggregate`

```sql
SELECT id_campus, COUNT(*) AS students, COUNT(email) AS with_email FROM students GROUP BY id_campus ORDER BY id_campus
```

{{query:A32 · Students and addresses per campus}}

#### A33 · Records with CONCAT (text and numbers)

Joins name and surname with CONCAT, and the degree code with the identifier: the first 7 students by identifier. The contact is null when there is no email.

Keywords: `education`, `basics`, `students`

```sql
SELECT CONCAT(name, ' ', last_name) AS student, CONCAT(degree_code, '-', id_student) AS enrollment_code, CONCAT(degree_code, ' · ', email) AS contact FROM students ORDER BY id_student LIMIT 7
```

{{query:A33 · Records with CONCAT (text and numbers)}}

#### A34 · Global totals with every aggregate (no GROUP BY)

Aggregates without GROUP BY, one of each kind: 48 enrollments, 32 graded, 21 students and 3 different statuses; total 234, minimum 5, maximum 10 and rounded average 7.31.

Keywords: `education`, `basics`, `enrollments`, `aggregate`, `distinct`

```sql
SELECT COUNT(*) AS enrollments, COUNT(grade) AS graded, COUNT(DISTINCT id_student) AS students, COUNT(DISTINCT status) AS statuses, SUM(grade) AS total_grade, MIN(grade) AS minimum, MAX(grade) AS maximum, ROUND(AVG(grade), 2) AS average FROM enrollments
```

{{query:A34 · Global totals with every aggregate (no GROUP BY)}}

#### A35 · Every aggregate by status (GROUP BY)

GROUP BY status with COUNT(*), COUNT(DISTINCT), SUM, MIN, MAX and AVG: only the 32 passed have grades; active and cancelled give NULL in the grade aggregates.

Keywords: `education`, `basics`, `enrollments`, `group-by`, `aggregate`, `distinct`

```sql
SELECT status, COUNT(*) AS total, COUNT(DISTINCT id_student) AS students, SUM(grade) AS total_grade, MIN(grade) AS minimum, MAX(grade) AS maximum, ROUND(AVG(grade), 2) AS average FROM enrollments GROUP BY status ORDER BY total DESC, status
```

{{query:A35 · Every aggregate by status (GROUP BY)}}

#### A36 · Subjects with four enrollments and a high average (double HAVING)

HAVING with two aggregates at once: subjects with at least 4 enrollments and an average of 7 or more; four subjects meet both thresholds.

Keywords: `education`, `basics`, `enrollments`, `group-by`, `having`, `aggregate`

```sql
SELECT id_subject, COUNT(*) AS enrollments, COUNT(grade) AS graded, ROUND(AVG(grade), 2) AS average FROM enrollments GROUP BY id_subject HAVING COUNT(*) >= 4 AND AVG(grade) >= 7 ORDER BY average DESC, id_subject
```

{{query:A36 · Subjects with four enrollments and a high average (double HAVING)}}

#### A37 · Students and contact details per campus (COUNT of columns with NULL)

COUNT(*) counts rows and COUNT(column) only the non-null values: campus 2 has 6 students but fewer e-mails and phones. HAVING keeps the campuses with 5 students or more.

Keywords: `education`, `basics`, `students`, `group-by`, `having`, `aggregate`

```sql
SELECT id_campus, COUNT(*) AS students, COUNT(email) AS with_email, COUNT(phone) AS with_phone FROM students GROUP BY id_campus HAVING COUNT(*) >= 5 ORDER BY students DESC, id_campus
```

{{query:A37 · Students and contact details per campus (COUNT of columns with NULL)}}

#### A38 · Teachers by category with distinct offices (HAVING COUNT DISTINCT)

COUNT(office) ignores nulls and COUNT(DISTINCT office) counts distinct offices: the three assistant teachers have no office and are filtered out by the HAVING.

Keywords: `education`, `basics`, `teachers`, `group-by`, `having`, `aggregate`, `distinct`

```sql
SELECT category, COUNT(*) AS teachers, COUNT(office) AS with_office, COUNT(DISTINCT office) AS offices FROM teachers GROUP BY category HAVING COUNT(DISTINCT office) >= 2 ORDER BY offices DESC, category
```

{{query:A38 · Teachers by category with distinct offices (HAVING COUNT DISTINCT)}}

#### A39 · Enrollments by academic year and status (GROUP BY of two columns)

Grouping by two columns with HAVING: only two combinations reach 10 enrollments; the active one has no grades and its total is NULL.

Keywords: `education`, `basics`, `enrollments`, `group-by`, `having`, `aggregate`

```sql
SELECT academic_year, status, COUNT(*) AS total, SUM(grade) AS total_grade FROM enrollments GROUP BY academic_year, status HAVING COUNT(*) >= 10 ORDER BY academic_year, status
```

{{query:A39 · Enrollments by academic year and status (GROUP BY of two columns)}}

#### A40 · Subjects averaging above 7.5 among graded enrollments (WHERE and HAVING)

WHERE removes enrollments without a grade before grouping and HAVING filters the groups by their average: four subjects remain, one of them with a single grade.

Keywords: `education`, `basics`, `enrollments`, `group-by`, `having`, `aggregate`, `null`

```sql
SELECT id_subject, COUNT(*) AS enrollments, ROUND(AVG(grade), 2) AS average FROM enrollments WHERE grade IS NOT NULL GROUP BY id_subject HAVING AVG(grade) > 7.5 ORDER BY average DESC, id_subject
```

{{query:A40 · Subjects averaging above 7.5 among graded enrollments (WHERE and HAVING)}}

#### A41 · Classrooms per floor with SUM, MIN and MAX (HAVING SUM)

Per floor, COUNT(*), SUM, MIN and MAX of the capacity; HAVING keeps the floors adding up to 100 seats or more.

Keywords: `education`, `basics`, `classrooms`, `group-by`, `having`, `aggregate`

```sql
SELECT floor, COUNT(*) AS classrooms, SUM(capacity) AS seats, MIN(capacity) AS minimum, MAX(capacity) AS maximum FROM classrooms GROUP BY floor HAVING SUM(capacity) >= 100 ORDER BY seats DESC
```

{{query:A41 · Classrooms per floor with SUM, MIN and MAX (HAVING SUM)}}

#### A42 · Weekly teacher hours by category with MIN, MAX and AVG (HAVING AVG)

MIN, MAX and rounded AVG of the weekly hours by category; the HAVING requires an average of 10 hours or more.

Keywords: `education`, `basics`, `teachers`, `group-by`, `having`, `aggregate`

```sql
SELECT category, COUNT(*) AS teachers, MIN(weekly_hours) AS min_hours, MAX(weekly_hours) AS max_hours, ROUND(AVG(weekly_hours), 1) AS average_hours FROM teachers GROUP BY category HAVING AVG(weekly_hours) >= 10 ORDER BY average_hours DESC
```

{{query:A42 · Weekly teacher hours by category with MIN, MAX and AVG (HAVING AVG)}}

#### A43 · Students by birth year (GROUP BY with an expression and HAVING)

GROUP BY on the year of the date (SUBSTR) with COUNT(*) and COUNT(phone); HAVING requires at least two students per year.

Keywords: `education`, `basics`, `students`, `group-by`, `having`, `aggregate`

```sql
SELECT SUBSTR(birth_date, 1, 4) AS year, COUNT(*) AS students, COUNT(phone) AS with_phone FROM students GROUP BY SUBSTR(birth_date, 1, 4) HAVING COUNT(*) >= 2 ORDER BY year
```

{{query:A43 · Students by birth year (GROUP BY with an expression and HAVING)}}

#### A44 · Aggregates over the grouped column itself (COUNT of status)

Aggregates over the grouped column and over others: COUNT(status) matches COUNT(*) and COUNT(DISTINCT status) is 1; graded only shows up in the passed rows.

Keywords: `education`, `basics`, `enrollments`, `group-by`, `aggregate`, `distinct`

```sql
SELECT status, COUNT(*) AS total, COUNT(status) AS with_status, COUNT(DISTINCT status) AS statuses, MAX(id_enrollment) AS last_id, COUNT(grade) AS graded FROM enrollments GROUP BY status ORDER BY total DESC, status
```

{{query:A44 · Aggregates over the grouped column itself (COUNT of status)}}

#### A45 · HAVING with aggregates that are not in the SELECT

The HAVING uses COUNT(*) and SUM(grade), which do not appear in the SELECT: only the passed enrollments meet both thresholds.

Keywords: `education`, `basics`, `enrollments`, `group-by`, `having`, `aggregate`

```sql
SELECT status FROM enrollments GROUP BY status HAVING COUNT(*) >= 13 AND SUM(grade) > 200 ORDER BY status
```

{{query:A45 · HAVING with aggregates that are not in the SELECT}}

#### A46 · Students with an e-mail per campus (WHERE, GROUP BY and HAVING)

WHERE filters before grouping: per campus, COUNT(*) of students with an institutional e-mail and COUNT(phone) of those with a phone; HAVING requires 4 or more.

Keywords: `education`, `basics`, `students`, `group-by`, `having`, `aggregate`, `like`

```sql
SELECT id_campus, COUNT(*) AS students, COUNT(phone) AS with_phone FROM students WHERE email LIKE '%@alu.es' GROUP BY id_campus HAVING COUNT(*) >= 4 ORDER BY students DESC, id_campus
```

{{query:A46 · Students with an e-mail per campus (WHERE, GROUP BY and HAVING)}}

#### A47 · REGEXPR_REPLACE: spaces, groups and global replacement

Normalizes spaces, reorders a comma-separated name with a group and rewrites every code with a global regular expression.

Keywords: `education`, `basics`, `regexpr-replace`, `groups`, `global`

```sql
SELECT TRIM(REGEXPR_REPLACE('  North   Campus  ', '[ ]+', ' ')) AS normalized,
       REGEXPR_REPLACE('Campus, North', '([^,]+), (.+)', '$2 $1') AS reordered,
       REGEXPR_REPLACE('A-01 / B-02', '[A-Z]-([0-9]+)', 'code-$1') AS codes
FROM campuses
WHERE id_campus = 1
```

{{query:A47 · REGEXPR_REPLACE: spaces, groups and global replacement}}

#### A48 · REGEXPR_INSTR: first match position and flags

The three examples: 123 starts at 4, no digit gives 0 and the ^ anchor matches at 1. It also checks the i flag, the alias REGEXP_INSTR and a NULL text.

Keywords: `education`, `regex`, `functions`, `position`

```sql
SELECT REGEXPR_INSTR('abc123def', '[0-9]+') AS digits, REGEXPR_INSTR('hola', '[0-9]+') AS no_match, REGEXPR_INSTR('404 Not Found', '^[0-9]+') AS at_start, REGEXPR_INSTR('xxABCyy', 'abc', 'i') AS with_flag, REGEXPR_INSTR('xxABCyy', 'abc') AS without_flag, REGEXP_INSTR('abc123def', '[0-9]+') AS alias, REGEXPR_INSTR(NULL, '[0-9]+') AS null_text FROM campuses LIMIT 1
```

{{query:A48 · REGEXPR_INSTR: first match position and flags}}

#### A49 · REGEXPR_INSTR over classroom codes

Finds the first digit of each code: B-SALA has none, while C-LAB1 and K-101 start their digits at 6 and 3.

Keywords: `education`, `regex`, `functions`, `classrooms`

```sql
SELECT code, REGEXPR_INSTR(code, '[0-9]+') AS first_digit FROM classrooms WHERE code IN ('K-101', 'B-SALA', 'C-LAB1') ORDER BY code
```

{{query:A49 · REGEXPR_INSTR over classroom codes}}

### B · Joins

#### B01 · Campuses with their buildings (INNER JOIN)

1:M join by id_campus: the 8 buildings with the name of their campus, sorted by campus and building.

Keywords: `education`, `joins`, `campuses`, `buildings`, `join`

```sql
SELECT s.name AS campus, e.name AS building, e.floors, e.has_lift FROM campuses AS s INNER JOIN buildings AS e ON e.id_campus = s.id_campus ORDER BY s.id_campus, e.id_building
```

{{query:B01 · Campuses with their buildings (INNER JOIN)}}

#### B02 · Three tables: large classrooms of campus 1

campuses → buildings → classrooms with a filter by campus and capacity: 3 classrooms, from largest to smallest.

Keywords: `education`, `joins`, `campuses`, `buildings`, `classrooms`, `join`

```sql
SELECT s.name AS campus, e.name AS building, a.code, a.capacity FROM campuses AS s INNER JOIN buildings AS e ON e.id_campus = s.id_campus INNER JOIN classrooms AS a ON a.id_building = e.id_building WHERE s.id_campus = 1 AND a.capacity >= 45 ORDER BY a.capacity DESC, a.code
```

{{query:B02 · Three tables: large classrooms of campus 1}}

#### B03 · Campuses with no buildings (LEFT JOIN with no pair)

LEFT JOIN plus IS NULL: the 4 campuses have buildings, so the result is empty.

Keywords: `education`, `joins`, `campuses`, `buildings`, `join`, `null`

```sql
SELECT s.name AS campus FROM campuses AS s LEFT JOIN buildings AS e ON e.id_campus = s.id_campus WHERE e.id_building IS NULL ORDER BY s.id_campus
```

{{query:B03 · Campuses with no buildings (LEFT JOIN with no pair)}}

#### B03b · Buildings with no classrooms

LEFT JOIN with IS NULL: only the Sports Pavilion is left with no classrooms.

Keywords: `education`, `joins`, `buildings`, `classrooms`, `join`, `null`

```sql
SELECT e.id_building, e.name FROM buildings AS e LEFT JOIN classrooms AS a ON a.id_building = e.id_building WHERE a.id_classroom IS NULL ORDER BY e.id_building
```

{{query:B03b · Buildings with no classrooms}}

#### B04 · Teachers with no subjects

LEFT JOIN teachers → subjects with IS NULL: 3 associates teach no subject.

Keywords: `education`, `joins`, `teachers`, `subjects`, `join`, `null`

```sql
SELECT d.id_teacher, d.name, d.last_name FROM teachers AS d LEFT JOIN subjects AS a ON a.id_teacher = d.id_teacher WHERE a.id_subject IS NULL ORDER BY d.id_teacher
```

{{query:B04 · Teachers with no subjects}}

#### B05 · Implicit join with commas (classrooms of campus 2)

FROM classrooms, buildings with the equality in the WHERE: the engine resolves it as a join.

Keywords: `education`, `joins`, `classrooms`

```sql
SELECT a.code, a.capacity, e.name AS building FROM classrooms AS a, buildings AS e WHERE a.id_building = e.id_building AND e.id_campus = 2 ORDER BY a.id_classroom
```

{{query:B05 · Implicit join with commas (classrooms of campus 2)}}

#### B06 · Subjects and credits per degree (join by a text code)

Join by degrees.code = subjects.degree_code, with COUNT and SUM per degree.

Keywords: `education`, `joins`, `degrees`, `subjects`, `join`, `group-by`, `aggregate`

```sql
SELECT e.code, e.name AS degree, COUNT(a.id_subject) AS subjects, SUM(a.credits) AS credits FROM degrees AS e INNER JOIN subjects AS a ON a.degree_code = e.code GROUP BY e.code, e.name ORDER BY credits DESC, e.code
```

{{query:B06 · Subjects and credits per degree (join by a text code)}}

#### B07 · Degrees with at least three subjects (LEFT JOIN + HAVING)

LEFT JOIN with HAVING COUNT >= 3: Computer Engineering, Primary Education and Medicine.

Keywords: `education`, `joins`, `degrees`, `subjects`, `join`, `group-by`, `having`, `aggregate`

```sql
SELECT e.code, e.name AS degree, COUNT(a.id_subject) AS subjects FROM degrees AS e LEFT JOIN subjects AS a ON a.degree_code = e.code GROUP BY e.code, e.name HAVING COUNT(a.id_subject) >= 3 ORDER BY subjects DESC, e.code
```

{{query:B07 · Degrees with at least three subjects (LEFT JOIN + HAVING)}}

#### B08 · Classrooms and seats per building

Grouping by building with COUNT and SUM: the Pythagoras Lecture Hall heads it with 150 seats.

Keywords: `education`, `joins`, `buildings`, `classrooms`, `join`, `group-by`, `aggregate`

```sql
SELECT e.id_building, e.name AS building, COUNT(a.id_classroom) AS classrooms, SUM(a.capacity) AS seats FROM buildings AS e INNER JOIN classrooms AS a ON a.id_building = e.id_building GROUP BY e.id_building, e.name ORDER BY seats DESC, e.id_building
```

{{query:B08 · Classrooms and seats per building}}

#### B09 · Classrooms per building, including those with none (LEFT JOIN)

LEFT JOIN with COUNT: the Sports Pavilion appears with 0 classrooms.

Keywords: `education`, `joins`, `buildings`, `classrooms`, `join`, `group-by`, `aggregate`

```sql
SELECT e.id_building, e.name AS building, COUNT(a.id_classroom) AS classrooms FROM buildings AS e LEFT JOIN classrooms AS a ON a.id_building = e.id_building GROUP BY e.id_building, e.name ORDER BY classrooms ASC, e.id_building
```

{{query:B09 · Classrooms per building, including those with none (LEFT JOIN)}}

#### B10 · Teaching load: subjects and classroom hours

teachers → subjects with COUNT and SUM: Elena and Carmen go over 145 hours.

Keywords: `education`, `joins`, `teachers`, `subjects`, `join`, `group-by`, `aggregate`

```sql
SELECT d.id_teacher, d.name, COUNT(a.id_subject) AS subjects, SUM(a.classroom_hours) AS hours FROM teachers AS d INNER JOIN subjects AS a ON a.id_teacher = d.id_teacher GROUP BY d.id_teacher, d.name ORDER BY hours DESC, d.id_teacher
```

{{query:B10 · Teaching load: subjects and classroom hours}}

#### B11 · Teachers with two subjects and 12 credits or more (double HAVING)

HAVING with COUNT and SUM at once: only Carmen, Elena and Marc make the cut.

Keywords: `education`, `joins`, `teachers`, `subjects`, `join`, `group-by`, `having`, `aggregate`

```sql
SELECT d.id_teacher, d.last_name, COUNT(*) AS subjects, SUM(a.credits) AS credits FROM teachers AS d INNER JOIN subjects AS a ON a.id_teacher = d.id_teacher GROUP BY d.id_teacher, d.last_name HAVING COUNT(*) >= 2 AND SUM(a.credits) >= 12 ORDER BY credits DESC, d.id_teacher
```

{{query:B11 · Teachers with two subjects and 12 credits or more (double HAVING)}}

#### B12 · Subjects with no language set, with their degree and teacher

Three tables (subjects + degrees + teachers) and IS NULL over the language.

Keywords: `education`, `joins`, `subjects`, `degrees`, `teachers`, `join`, `null`

```sql
SELECT a.id_subject, a.name AS subject, e.name AS degree, d.last_name AS teacher, a.credits FROM subjects AS a INNER JOIN degrees AS e ON e.code = a.degree_code INNER JOIN teachers AS d ON d.id_teacher = a.id_teacher WHERE a.language IS NULL ORDER BY a.id_subject
```

{{query:B12 · Subjects with no language set, with their degree and teacher}}

#### B13 · Students, addresses and phones per campus

LEFT JOIN campuses → students with three counts: 18 addresses and 16 phones in all.

Keywords: `education`, `joins`, `campuses`, `students`, `join`, `group-by`, `aggregate`

```sql
SELECT s.id_campus, s.name AS campus, COUNT(e.id_student) AS students, COUNT(e.email) AS with_email, COUNT(e.phone) AS with_phone FROM campuses AS s LEFT JOIN students AS e ON e.id_campus = s.id_campus GROUP BY s.id_campus, s.name ORDER BY students DESC, s.id_campus
```

{{query:B13 · Students, addresses and phones per campus}}

#### B14 · Cartesian product (6 degrees × 4 campuses)

With no join condition you get every combination; LIMIT 6 shows the first ones.

Keywords: `education`, `joins`, `degrees`

```sql
SELECT e.code, s.name AS campus FROM degrees AS e, campuses AS s ORDER BY e.code, s.id_campus LIMIT 6
```

{{query:B14 · Cartesian product (6 degrees × 4 campuses)}}

#### B15 · The five largest classrooms (JOIN + ORDER BY alias + LIMIT)

It sorts by the alias of a column and by the classroom code; the tie at 55 seats is decided by the classroom.

Keywords: `education`, `joins`, `classrooms`, `buildings`, `join`

```sql
SELECT a.code AS classroom, a.capacity AS seats, e.name AS building FROM classrooms AS a INNER JOIN buildings AS e ON e.id_building = a.id_building ORDER BY seats DESC, classroom LIMIT 5
```

{{query:B15 · The five largest classrooms (JOIN + ORDER BY alias + LIMIT)}}

#### B16 · Distinct teachers per degree (COUNT DISTINCT)

COUNT(DISTINCT id_teacher) per degree: Computer Engineering is the one that spreads its teaching over more teachers.

Keywords: `education`, `joins`, `degrees`, `subjects`, `join`, `group-by`, `aggregate`, `distinct`

```sql
SELECT e.code, COUNT(DISTINCT a.id_teacher) AS distinct_teachers, COUNT(*) AS subjects FROM degrees AS e INNER JOIN subjects AS a ON a.degree_code = e.code GROUP BY e.code ORDER BY distinct_teachers DESC, e.code
```

{{query:B16 · Distinct teachers per degree (COUNT DISTINCT)}}

#### B17 · Teaching hours per category (MIN and MAX)

GROUP BY category with a count and the extremes: senior lecturers are the most numerous.

Keywords: `education`, `joins`, `teachers`, `group-by`, `aggregate`

```sql
SELECT category, COUNT(*) AS teachers, MIN(weekly_hours) AS min_hours, MAX(weekly_hours) AS max_hours FROM teachers GROUP BY category ORDER BY teachers DESC, category
```

{{query:B17 · Teaching hours per category (MIN and MAX)}}

#### B18 · Basic subjects of years one and two with their teacher

Join teachers → subjects with BETWEEN, >= and a boolean in the WHERE: 8 subjects.

Keywords: `education`, `joins`, `teachers`, `subjects`, `join`, `between`

```sql
SELECT d.name, d.last_name, a.name AS subject, a.year FROM teachers AS d INNER JOIN subjects AS a ON a.id_teacher = d.id_teacher WHERE a.year BETWEEN 1 AND 2 AND a.credits >= 6 AND a.is_compulsory = true ORDER BY a.id_subject
```

{{query:B18 · Basic subjects of years one and two with their teacher}}

#### B19 · Enrollments and average grade per level (Bachelor / Master)

Four tables and ROUND(AVG()): masters raise the average of the bachelor (43 enrollments against 5).

Keywords: `education`, `joins`, `degrees`, `subjects`, `enrollments`, `join`, `group-by`, `aggregate`, `distinct`

```sql
SELECT e.level, COUNT(DISTINCT m.id_enrollment) AS enrollments, ROUND(AVG(m.grade), 2) AS average_grade FROM degrees AS e INNER JOIN subjects AS a ON a.degree_code = e.code INNER JOIN enrollments AS m ON m.id_subject = a.id_subject GROUP BY e.level ORDER BY average_grade DESC
```

{{query:B19 · Enrollments and average grade per level (Bachelor / Master)}}

#### B20 · Optional subjects and their degree

Boolean filter (is_compulsory = false) over the join degrees ↔ subjects: 5 optional subjects.

Keywords: `education`, `joins`, `subjects`, `degrees`, `join`

```sql
SELECT a.id_subject, a.name, e.name AS degree, a.language FROM subjects AS a INNER JOIN degrees AS e ON e.code = a.degree_code WHERE a.is_compulsory = false ORDER BY a.id_subject
```

{{query:B20 · Optional subjects and their degree}}

#### B21 · Master students with their degree

Join students → degrees by the text code, filtering by level Master: 4 students.

Keywords: `education`, `joins`, `students`, `degrees`, `join`

```sql
SELECT st.id_student, st.name, st.last_name, e.name AS degree, e.level FROM students AS st INNER JOIN degrees AS e ON e.code = st.degree_code WHERE e.level = 'Master' ORDER BY st.id_student
```

{{query:B21 · Master students with their degree}}

#### B22 · Daily schedule of the research staff

Boolean filter and ROUND over a division, sorted by weekly hours: 6 researchers.

Keywords: `education`, `joins`, `teachers`

```sql
SELECT d.name, d.weekly_hours, ROUND(d.weekly_hours / 5, 1) AS hours_per_day FROM teachers AS d WHERE d.is_researcher = true ORDER BY d.weekly_hours DESC, d.id_teacher
```

{{query:B22 · Daily schedule of the research staff}}

### C · Related queries

#### C01 · Full record of one student (M:M resolved)

Three tables over the M:M: the 4 subjects of Ana, with grade and status.

Keywords: `education`, `related-queries`, `enrollments`, `students`, `subjects`, `join`

```sql
SELECT m.id_enrollment, st.last_name AS student, a.name AS subject, m.grade, m.status FROM enrollments AS m INNER JOIN students AS st ON st.id_student = m.id_student INNER JOIN subjects AS a ON a.id_subject = m.id_subject WHERE m.id_student = 1 ORDER BY m.id_enrollment
```

{{query:C01 · Full record of one student (M:M resolved)}}

#### C02 · Subjects with four or more students (large group)

It walks the M:M from subject → enrollments and groups with HAVING >= 4: 8 subjects.

Keywords: `education`, `related-queries`, `subjects`, `enrollments`, `join`, `group-by`, `having`, `aggregate`

```sql
SELECT a.id_subject, a.name AS subject, COUNT(DISTINCT m.id_student) AS students FROM subjects AS a INNER JOIN enrollments AS m ON m.id_subject = a.id_subject GROUP BY a.id_subject, a.name HAVING COUNT(DISTINCT m.id_student) >= 4 ORDER BY students DESC, a.id_subject
```

{{query:C02 · Subjects with four or more students (large group)}}

#### C03 · Load of every student (subjects and credits)

The other side of the M:M: 21 students with enrollments, sorted by credits and breaking ties by identifier.

Keywords: `education`, `related-queries`, `students`, `enrollments`, `subjects`, `join`, `group-by`, `aggregate`

```sql
SELECT st.id_student, st.name, st.last_name, COUNT(*) AS subjects, SUM(a.credits) AS credits FROM students AS st INNER JOIN enrollments AS m ON m.id_student = st.id_student INNER JOIN subjects AS a ON a.id_subject = m.id_subject GROUP BY st.id_student, st.name, st.last_name ORDER BY credits DESC, st.id_student
```

{{query:C03 · Load of every student (subjects and credits)}}

#### C04 · Grades of one student with COALESCE

COALESCE turns the null of the active subject into 0 instead of leaving it empty.

Keywords: `education`, `related-queries`, `enrollments`, `subjects`, `join`

```sql
SELECT a.name AS subject, m.status, COALESCE(m.grade, 0) AS grade FROM enrollments AS m INNER JOIN subjects AS a ON a.id_subject = m.id_subject WHERE m.id_student = 4 ORDER BY a.id_subject
```

{{query:C04 · Grades of one student with COALESCE}}

#### C05 · Enrollments not graded yet (IS NULL)

The first six enrollments with no grade, with student and subject.

Keywords: `education`, `related-queries`, `enrollments`, `students`, `subjects`, `join`, `null`

```sql
SELECT m.id_enrollment, st.last_name AS student, a.name AS subject, m.status FROM enrollments AS m INNER JOIN students AS st ON st.id_student = m.id_student INNER JOIN subjects AS a ON a.id_subject = m.id_subject WHERE m.grade IS NULL ORDER BY m.id_enrollment LIMIT 6
```

{{query:C05 · Enrollments not graded yet (IS NULL)}}

#### C06 · Enrollments with distinction (grade >= 9)

Six enrollments of 9 or more, from the highest grade down and breaking ties by student.

Keywords: `education`, `related-queries`, `enrollments`, `students`, `subjects`, `join`

```sql
SELECT st.last_name AS student, a.name AS subject, m.grade FROM enrollments AS m INNER JOIN students AS st ON st.id_student = m.id_student INNER JOIN subjects AS a ON a.id_subject = m.id_subject WHERE m.grade >= 9 ORDER BY m.grade DESC, st.id_student
```

{{query:C06 · Enrollments with distinction (grade >= 9)}}

#### C07 · Students with no enrollment (LEFT JOIN and null average)

Three students have no enrollments: COUNT is 0 and ROUND(AVG()) is null.

Keywords: `education`, `related-queries`, `students`, `enrollments`, `join`, `group-by`, `having`, `aggregate`

```sql
SELECT st.id_student, st.name, st.last_name, COUNT(m.id_enrollment) AS enrollments, ROUND(AVG(m.grade), 2) AS average_grade FROM students AS st LEFT JOIN enrollments AS m ON m.id_student = st.id_student GROUP BY st.id_student, st.name, st.last_name HAVING COUNT(m.id_enrollment) = 0 ORDER BY st.id_student
```

{{query:C07 · Students with no enrollment (LEFT JOIN and null average)}}

#### C08 · Subjects with no enrollment

LEFT JOIN with IS NULL: Testing and Quality and Business Law are left with no students.

Keywords: `education`, `related-queries`, `subjects`, `enrollments`, `join`, `null`

```sql
SELECT a.id_subject, a.name, a.degree_code FROM subjects AS a LEFT JOIN enrollments AS m ON m.id_subject = a.id_subject WHERE m.id_enrollment IS NULL ORDER BY a.id_subject
```

{{query:C08 · Subjects with no enrollment}}

#### C09 · Coverage: students with enrollments per degree

Four tables (degrees → subjects → enrollments → students) to count the real students of every degree.

Keywords: `education`, `related-queries`, `degrees`, `subjects`, `enrollments`, `join`, `group-by`, `aggregate`, `distinct`

```sql
SELECT e.code, COUNT(DISTINCT st.id_student) AS students FROM degrees AS e INNER JOIN subjects AS a ON a.degree_code = e.code INNER JOIN enrollments AS m ON m.id_subject = a.id_subject INNER JOIN students AS st ON st.id_student = m.id_student GROUP BY e.code ORDER BY students DESC, e.code
```

{{query:C09 · Coverage: students with enrollments per degree}}

#### C10 · Nobody takes subjects of two degrees (only rowCount)

An assert with no expected rows: it is enough to check that the result is empty (rowCount 0).

Keywords: `education`, `related-queries`, `students`, `enrollments`, `subjects`, `join`, `group-by`, `having`, `aggregate`

```sql
SELECT st.id_student, COUNT(DISTINCT a.degree_code) AS degrees FROM students AS st INNER JOIN enrollments AS m ON m.id_student = st.id_student INNER JOIN subjects AS a ON a.id_subject = m.id_subject GROUP BY st.id_student HAVING COUNT(DISTINCT a.degree_code) >= 2 ORDER BY st.id_student
```

{{query:C10 · Nobody takes subjects of two degrees (only rowCount)}}

#### C11 · Recognised enrollments

Boolean is_recognised = true over the M:M: 6 enrollments, one per student.

Keywords: `education`, `related-queries`, `enrollments`, `students`, `subjects`, `join`

```sql
SELECT m.id_enrollment, st.last_name AS student, a.name AS subject, e.code AS degree FROM enrollments AS m INNER JOIN students AS st ON st.id_student = m.id_student INNER JOIN subjects AS a ON a.id_subject = m.id_subject INNER JOIN degrees AS e ON e.code = a.degree_code WHERE m.is_recognised = true ORDER BY m.id_enrollment
```

{{query:C11 · Recognised enrollments}}

#### C12 · Subjects of one student whatever the order (ignoreOrder)

The assert does not sort: it checks that the set of rows matches, with ignoreOrder true.

Keywords: `education`, `related-queries`, `subjects`, `enrollments`, `join`

```sql
SELECT a.name AS subject FROM subjects AS a INNER JOIN enrollments AS m ON m.id_subject = a.id_subject WHERE m.id_student = 4
```

{{query:C12 · Subjects of one student whatever the order (ignoreOrder)}}

#### C13 · Subjects with more enrollments (ORDER BY of an aggregate)

It sorts by COUNT(*) descending and cuts the first five: three subjects tie at 5 enrollments.

Keywords: `education`, `related-queries`, `subjects`, `enrollments`, `join`, `group-by`, `aggregate`

```sql
SELECT a.name AS subject, COUNT(*) AS enrollments FROM subjects AS a INNER JOIN enrollments AS m ON m.id_subject = a.id_subject GROUP BY a.name ORDER BY COUNT(*) DESC, a.name LIMIT 5
```

{{query:C13 · Subjects with more enrollments (ORDER BY of an aggregate)}}

#### C14 · Longest subject names (ORDER BY of a function)

It sorts by LENGTH(name) descending, not by the text: 5 rows.

Keywords: `education`, `related-queries`, `subjects`

```sql
SELECT a.name AS subject, LENGTH(a.name) AS letters FROM subjects AS a ORDER BY LENGTH(a.name) DESC, a.name LIMIT 5
```

{{query:C14 · Longest subject names (ORDER BY of a function)}}

#### C15 · Students with more credits enrolled (ORDER BY alias + LIMIT)

It sorts by the alias credits: Ana heads it with 24 credits and the rest tie at 19.

Keywords: `education`, `related-queries`, `students`, `enrollments`, `subjects`, `join`, `group-by`, `aggregate`

```sql
SELECT st.id_student, st.last_name, COUNT(*) AS subjects, SUM(a.credits) AS credits FROM students AS st INNER JOIN enrollments AS m ON m.id_student = st.id_student INNER JOIN subjects AS a ON a.id_subject = m.id_subject GROUP BY st.id_student, st.last_name ORDER BY credits DESC, st.id_student LIMIT 4
```

{{query:C15 · Students with more credits enrolled (ORDER BY alias + LIMIT)}}

#### C16 · Average grade of the Computing subjects

Filter by degree code, aggregates and NULLS LAST: the subject with no grade goes last.

Keywords: `education`, `related-queries`, `subjects`, `enrollments`, `join`, `group-by`, `aggregate`

```sql
SELECT a.name AS subject, COUNT(m.id_enrollment) AS enrollments, COUNT(m.grade) AS graded, ROUND(AVG(m.grade), 2) AS average_grade FROM subjects AS a INNER JOIN enrollments AS m ON m.id_subject = a.id_subject WHERE a.degree_code = 'GINF' GROUP BY a.id_subject, a.name ORDER BY average_grade DESC NULLS LAST, a.id_subject
```

{{query:C16 · Average grade of the Computing subjects}}

#### C17 · Scholarship holders in Human Anatomy

A student boolean and a subject filter over the M:M: 2 of the 4 enrolled have a scholarship.

Keywords: `education`, `related-queries`, `students`, `enrollments`, `subjects`, `join`

```sql
SELECT st.last_name AS student, st.has_scholarship, a.name AS subject FROM students AS st INNER JOIN enrollments AS m ON m.id_student = st.id_student INNER JOIN subjects AS a ON a.id_subject = m.id_subject WHERE st.has_scholarship = true AND m.id_subject = 5 ORDER BY st.id_student
```

{{query:C17 · Scholarship holders in Human Anatomy}}

#### C18 · Enrollments per degree and academic year

GROUP BY with two text keys: 12 combinations, in degree + year order.

Keywords: `education`, `related-queries`, `enrollments`, `subjects`, `join`, `group-by`, `aggregate`

```sql
SELECT a.degree_code, m.academic_year, COUNT(*) AS enrollments FROM enrollments AS m INNER JOIN subjects AS a ON a.id_subject = m.id_subject GROUP BY a.degree_code, m.academic_year ORDER BY a.degree_code, m.academic_year
```

{{query:C18 · Enrollments per degree and academic year}}

#### C19 · Subjects with distinctions

Grade filter and GROUP BY: Data Structures is the only one with two distinctions.

Keywords: `education`, `related-queries`, `enrollments`, `subjects`, `join`, `group-by`, `aggregate`

```sql
SELECT a.name AS subject, COUNT(*) AS outstanding FROM enrollments AS m INNER JOIN subjects AS a ON a.id_subject = m.id_subject WHERE m.grade >= 9 GROUP BY a.name ORDER BY outstanding DESC, a.name
```

{{query:C19 · Subjects with distinctions}}

#### C20 · Average per Computing student with two or more grades

Four tables, IS NOT NULL and HAVING COUNT >= 2: Ana and Sara go over 7.

Keywords: `education`, `related-queries`, `students`, `enrollments`, `subjects`, `join`, `group-by`, `having`, `aggregate`

```sql
SELECT st.id_student, st.last_name, ROUND(AVG(m.grade), 2) AS average_grade FROM students AS st INNER JOIN enrollments AS m ON m.id_student = st.id_student INNER JOIN subjects AS a ON a.id_subject = m.id_subject INNER JOIN degrees AS e ON e.code = a.degree_code WHERE e.code = 'GINF' AND m.grade IS NOT NULL GROUP BY st.id_student, st.last_name HAVING COUNT(*) >= 2 ORDER BY average_grade DESC, st.id_student
```

{{query:C20 · Average per Computing student with two or more grades}}

### D · Integrity

#### D01 · Enrollments with no student (integrity of the 1:M)

Anti-join enrollments → students: no enrollment points to a student that does not exist.

Keywords: `education`, `integrity`, `enrollments`, `students`, `join`, `null`

```sql
SELECT m.id_enrollment, m.id_student FROM enrollments AS m LEFT JOIN students AS st ON st.id_student = m.id_student WHERE st.id_student IS NULL ORDER BY m.id_enrollment
```

{{query:D01 · Enrollments with no student (integrity of the 1:M)}}

#### D02 · Enrollments with no subject (integrity of the other 1:M)

Anti-join enrollments → subjects: the M:M has no dangling references.

Keywords: `education`, `integrity`, `enrollments`, `subjects`, `join`, `null`

```sql
SELECT m.id_enrollment, m.id_subject FROM enrollments AS m LEFT JOIN subjects AS a ON a.id_subject = m.id_subject WHERE a.id_subject IS NULL ORDER BY m.id_enrollment
```

{{query:D02 · Enrollments with no subject (integrity of the other 1:M)}}

#### D03 · Subjects with no degree (integrity of the text code)

Anti-join subjects → degrees by degree_code = code: the 16 subjects have a degree.

Keywords: `education`, `integrity`, `subjects`, `degrees`, `join`, `null`

```sql
SELECT a.id_subject, a.degree_code FROM subjects AS a LEFT JOIN degrees AS e ON e.code = a.degree_code WHERE e.id_degree IS NULL ORDER BY a.id_subject
```

{{query:D03 · Subjects with no degree (integrity of the text code)}}

#### D04 · Subjects with no teacher

Anti-join subjects → teachers: every subject has a teacher.

Keywords: `education`, `integrity`, `subjects`, `teachers`, `join`, `null`

```sql
SELECT a.id_subject, a.id_teacher FROM subjects AS a LEFT JOIN teachers AS d ON d.id_teacher = a.id_teacher WHERE d.id_teacher IS NULL ORDER BY a.id_subject
```

{{query:D04 · Subjects with no teacher}}

#### D05 · Classrooms with no building

Anti-join classrooms → buildings: no classroom is left outside a building.

Keywords: `education`, `integrity`, `classrooms`, `buildings`, `join`, `null`

```sql
SELECT a.id_classroom, a.id_building FROM classrooms AS a LEFT JOIN buildings AS e ON e.id_building = a.id_building WHERE e.id_building IS NULL ORDER BY a.id_classroom
```

{{query:D05 · Classrooms with no building}}

#### D06 · Buildings with no campus

Anti-join buildings → campuses: the 8 buildings hang from an existing campus.

Keywords: `education`, `integrity`, `buildings`, `campuses`, `join`, `null`

```sql
SELECT e.id_building, e.id_campus FROM buildings AS e LEFT JOIN campuses AS s ON s.id_campus = e.id_campus WHERE s.id_campus IS NULL ORDER BY e.id_building
```

{{query:D06 · Buildings with no campus}}

#### D07 · Students with no campus or with no degree

Two anti-joins at once with OR: all the students have a valid campus and degree.

Keywords: `education`, `integrity`, `students`, `campuses`, `degrees`, `join`, `null`

```sql
SELECT st.id_student FROM students AS st LEFT JOIN campuses AS s ON s.id_campus = st.id_campus LEFT JOIN degrees AS e ON e.code = st.degree_code WHERE s.id_campus IS NULL OR e.id_degree IS NULL ORDER BY st.id_student
```

{{query:D07 · Students with no campus or with no degree}}

#### D08 · Repeated enrollments of the same student/subject pair

GROUP BY + HAVING over the M:M: there are no duplicated pairs, the natural key is unique.

Keywords: `education`, `integrity`, `enrollments`, `group-by`, `having`, `aggregate`

```sql
SELECT m.id_student, m.id_subject, COUNT(*) AS times FROM enrollments AS m GROUP BY m.id_student, m.id_subject HAVING COUNT(*) > 1
```

{{query:D08 · Repeated enrollments of the same student/subject pair}}

#### D09 · Grades outside the 0–10 range

Business rule over the grade range: none outside 0–10.

Keywords: `education`, `integrity`, `enrollments`, `null`

```sql
SELECT id_enrollment, grade FROM enrollments WHERE grade IS NOT NULL AND (grade < 0 OR grade > 10)
```

{{query:D09 · Grades outside the 0–10 range}}

#### D10 · Invented enrollment statuses

NOT IN against the list of allowed statuses: only active, passed and cancelled exist.

Keywords: `education`, `integrity`, `enrollments`, `in`

```sql
SELECT id_enrollment, status FROM enrollments WHERE status NOT IN ('active', 'passed', 'cancelled') ORDER BY id_enrollment
```

{{query:D10 · Invented enrollment statuses}}

#### D11 · Passed enrollments with no grade

The rule «passed ⇒ grade reported»: no passed enrollment is left without a grade.

Keywords: `education`, `integrity`, `enrollments`, `null`

```sql
SELECT id_enrollment, status, grade FROM enrollments WHERE status = 'passed' AND grade IS NULL ORDER BY id_enrollment
```

{{query:D11 · Passed enrollments with no grade}}

#### D12 · Graded enrollments that are not passed

The other way round: if there is a grade, the enrollment is passed (active and cancelled are not graded).

Keywords: `education`, `integrity`, `enrollments`, `null`

```sql
SELECT id_enrollment, status, grade FROM enrollments WHERE grade IS NOT NULL AND status <> 'passed' ORDER BY id_enrollment
```

{{query:D12 · Graded enrollments that are not passed}}

#### D13 · Classrooms with no seats

Business rule: every classroom offers at least one seat.

Keywords: `education`, `integrity`, `classrooms`

```sql
SELECT id_classroom, code, capacity FROM classrooms WHERE capacity <= 0 ORDER BY id_classroom
```

{{query:D13 · Classrooms with no seats}}

### E · Views

#### E01 · Simple view: SELECT * of another query

The result of A03 is read as a table: the 5 large classrooms with their three columns (code, capacity, floor), sorted by capacity.

Keywords: `education`, `views`, `select-star`

```sql
SELECT * FROM [A03 · Large classrooms (>= 55 seats)] ORDER BY capacity DESC, code
```

{{query:E01 · Simple view: SELECT * of another query}}

#### E02 · Arithmetic over the columns of a view

The capacity is still a number after going through view A03: it can be divided by 10 and the remainder computed.

Keywords: `education`, `views`

```sql
SELECT code AS classroom, capacity, capacity / 10 AS tens, capacity % 10 AS remainder FROM [A03 · Large classrooms (>= 55 seats)] ORDER BY capacity DESC, code
```

{{query:E02 · Arithmetic over the columns of a view}}

#### E03 · WHERE and DISTINCT over a view

It filters view A03 by capacity >= 60 and leaves the distinct floors: floors 1 and 3 are left.

Keywords: `education`, `views`, `distinct`

```sql
SELECT DISTINCT floor FROM [A03 · Large classrooms (>= 55 seats)] WHERE capacity >= 60 ORDER BY floor
```

{{query:E03 · WHERE and DISTINCT over a view}}

#### E04 · Aggregates over a view

Five aggregates in one row over the result of A03: 5 classrooms, 320 seats, from 55 to 80 and an average of 64.

Keywords: `education`, `views`, `aggregate`

```sql
SELECT COUNT(*) AS classrooms, SUM(capacity) AS seats, MIN(capacity) AS min_grade, MAX(capacity) AS max_grade, ROUND(AVG(capacity), 2) AS average_grade FROM [A03 · Large classrooms (>= 55 seats)]
```

{{query:E04 · Aggregates over a view}}

#### E05 · Floors with two or more large classrooms

It groups view A03 by floor and keeps the floors with two or more classrooms: only floor 1 (4 classrooms, 240 seats).

Keywords: `education`, `views`, `group-by`, `having`, `aggregate`

```sql
SELECT floor, COUNT(*) AS classrooms, SUM(capacity) AS seats FROM [A03 · Large classrooms (>= 55 seats)] GROUP BY floor HAVING COUNT(*) >= 2 ORDER BY floor
```

{{query:E05 · Floors with two or more large classrooms}}

#### E06 · View joined to a base table (INNER JOIN)

View C02 joined to the subjects table by id_subject: the 8 subjects with four or more students, with their year and credits.

Keywords: `education`, `views`, `subjects`, `join`

```sql
SELECT c.subject, c.students, a.year, a.credits FROM [C02 · Subjects with four or more students (large group)] AS c INNER JOIN subjects AS a ON a.id_subject = c.id_subject ORDER BY c.students DESC, c.id_subject
```

{{query:E06 · View joined to a base table (INNER JOIN)}}

#### E07 · Two views joined by subject

Views C02 and A31 joined by id_subject: the 6 large subjects that also have an average grade.

Keywords: `education`, `views`, `join`, `having`

```sql
SELECT c.subject, c.students, n.average_grade FROM [C02 · Subjects with four or more students (large group)] AS c INNER JOIN [A31 · Average grade per subject (HAVING and ORDER BY by alias)] AS n ON n.id_subject = c.id_subject ORDER BY c.students DESC, c.id_subject
```

{{query:E07 · Two views joined by subject}}

#### E08 · Table LEFT JOIN view with IS NULL

LEFT JOIN of the buildings table against view B08 plus IS NULL: the only building with no classrooms, the Sports Pavilion.

Keywords: `education`, `views`, `buildings`, `join`, `null`

```sql
SELECT e.id_building, e.name AS building FROM buildings AS e LEFT JOIN [B08 · Classrooms and seats per building] AS v ON v.id_building = e.id_building WHERE v.classrooms IS NULL ORDER BY e.id_building
```

{{query:E08 · Table LEFT JOIN view with IS NULL}}

#### E09 · View of a view (two levels of query)

It reads view E05, which in turn reads view A03: three chained levels and a single row (floor 1, 240 seats).

Keywords: `education`, `views`

```sql
SELECT floor, classrooms, seats FROM [E05 · Floors with two or more large classrooms] WHERE seats >= 100 ORDER BY seats DESC
```

{{query:E09 · View of a view (two levels of query)}}

#### E10 · The same view twice (self-join)

View A03 appears twice with different aliases and joins itself by capacity: only P-101 and P-102 tie at 55 seats.

Keywords: `education`, `views`, `join`

```sql
SELECT a.code AS classroom_1, b.code AS classroom_2, a.capacity FROM [A03 · Large classrooms (>= 55 seats)] AS a INNER JOIN [A03 · Large classrooms (>= 55 seats)] AS b ON b.capacity = a.capacity AND b.code > a.code ORDER BY a.capacity DESC, a.code, b.code
```

{{query:E10 · The same view twice (self-join)}}

#### E11 · Implicit join with commas including a view

Cartesian product of view B06 with the campuses table, filtered by campus 1: the 6 degrees with their credits, from most to least.

Keywords: `education`, `views`, `join`

```sql
SELECT c.code, c.degree, c.credits, s.name AS campus FROM [B06 · Subjects and credits per degree (join by a text code)] AS c, campuses AS s WHERE s.id_campus = 1 ORDER BY c.credits DESC, c.code
```

{{query:E11 · Implicit join with commas including a view}}

#### E12 · ORDER BY by alias and LIMIT over a view

It sorts by the alias subject and cuts view C02 to 3 rows: the three subjects with 5 students.

Keywords: `education`, `views`

```sql
SELECT subject AS materia, students FROM [C02 · Subjects with four or more students (large group)] ORDER BY students DESC, subject LIMIT 3
```

{{query:E12 · ORDER BY by alias and LIMIT over a view}}

#### E13 · Boolean going through a view

The boolean is_main travels untouched from the campuses table to view A01: only North Campus is the main one.

Keywords: `education`, `views`

```sql
SELECT name AS campus, is_main FROM [A01 · All campuses] WHERE is_main = true ORDER BY id_campus
```

{{query:E13 · Boolean going through a view}}

#### E14 · Nulls going through a view (COALESCE)

The missing phone of campus 3 is still null after reading view A01, and COALESCE replaces it with «no phone».

Keywords: `education`, `views`

```sql
SELECT name AS campus, COALESCE(phone, 'no phone') AS contact FROM [A01 · All campuses] ORDER BY id_campus
```

{{query:E14 · Nulls going through a view (COALESCE)}}

#### E15 · Aggregates over a view that is already aggregated

It summarises the already aggregated view A32 (one row per campus): 4 campuses, 24 students and between 3 and 9 per campus.

Keywords: `education`, `views`, `aggregate`

```sql
SELECT COUNT(*) AS campuses, SUM(students) AS students, MIN(students) AS minimum, MAX(students) AS maximum FROM [A32 · Students and addresses per campus]
```

{{query:E15 · Aggregates over a view that is already aggregated}}

#### E16 · A view that reads a query with LIMIT

View C13 carries LIMIT 5: reading it as a table brings only those 5 rows (23 enrollments in all).

Keywords: `education`, `views`, `aggregate`

```sql
SELECT COUNT(*) AS rows, SUM(enrollments) AS enrollments FROM [C13 · Subjects with more enrollments (ORDER BY of an aggregate)]
```

{{query:E16 · A view that reads a query with LIMIT}}

#### E17 · A view of an empty result

View C10 returns no rows: reading it as a table gives an empty table, and COUNT(*) returns 0 instead of an error.

Keywords: `education`, `views`, `aggregate`

```sql
SELECT COUNT(*) AS rows FROM [C10 · Nobody takes subjects of two degrees (only rowCount)]
```

{{query:E17 · A view of an empty result}}

#### E18 · LIMIT and OFFSET over a view

LIMIT 3 OFFSET 2 over the 5 rows of view A03: the third, fourth and fifth classrooms by capacity.

Keywords: `education`, `views`

```sql
SELECT code, capacity FROM [A03 · Large classrooms (>= 55 seats)] ORDER BY capacity DESC, code LIMIT 3 OFFSET 2
```

{{query:E18 · LIMIT and OFFSET over a view}}

#### E19 · LIKE filter over a view

LIKE over a column of view A03: the only large classroom whose code starts with K.

Keywords: `education`, `views`, `like`

```sql
SELECT code, capacity FROM [A03 · Large classrooms (>= 55 seats)] WHERE code LIKE 'K%' ORDER BY code
```

{{query:E19 · LIKE filter over a view}}

#### E20 · A view of a view that joins two views

It reads view E07 (which joins C02 and A31) and keeps the average grades of 7 or more: 4 subjects.

Keywords: `education`, `views`

```sql
SELECT subject, students, average_grade FROM [E07 · Two views joined by subject] WHERE average_grade >= 7 ORDER BY average_grade DESC, subject
```

{{query:E20 · A view of a view that joins two views}}

#### E21 · LEFT JOIN between two views

C02 LEFT JOIN A31: the large subjects that do not reach the HAVING of A31 (Computer Networks and Public Health) come with a null average_grade, the first ones.

Keywords: `education`, `views`, `join`, `having`

```sql
SELECT c.subject, c.students, n.average_grade FROM [C02 · Subjects with four or more students (large group)] AS c LEFT JOIN [A31 · Average grade per subject (HAVING and ORDER BY by alias)] AS n ON n.id_subject = c.id_subject ORDER BY n.average_grade ASC NULLS FIRST, c.subject
```

{{query:E21 · LEFT JOIN between two views}}

#### E22 · Projection with an alias and an asterisk over a view

The asterisk of the alias (v.*) over view A04: the 10 classrooms with a projector, with their two columns.

Keywords: `education`, `views`

```sql
SELECT v.* FROM [A04 · Classrooms with a projector] AS v ORDER BY v.capacity DESC, v.code
```

{{query:E22 · Projection with an alias and an asterisk over a view}}

#### E23 · A view that reads a query with three tables

View B02 joins three tables inside (campuses, buildings and classrooms): reading it as a table brings its 3 rows.

Keywords: `education`, `views`, `aggregate`

```sql
SELECT COUNT(*) AS rows FROM [B02 · Three tables: large classrooms of campus 1]
```

{{query:E23 · A view that reads a query with three tables}}

#### E24 · Group by a boolean of a view

GROUP BY over a boolean that comes from view A15: how many level/officialness combinations there are of each kind.

Keywords: `education`, `views`, `group-by`, `aggregate`, `distinct`

```sql
SELECT is_official, COUNT(*) AS combinations FROM [A15 · Level/officialness combinations (DISTINCT of two columns)] GROUP BY is_official ORDER BY is_official
```

{{query:E24 · Group by a boolean of a view}}

### F · Subqueries

#### F01 · Students with an active enrollment (IN with a subquery)

IN with a subquery: the students whose identifier shows up in the inner SELECT.

Keywords: `education`, `subqueries`, `students`, `enrollments`, `subquery`, `in`

```sql
SELECT id_student, last_name FROM students WHERE id_student IN (SELECT id_student FROM enrollments WHERE status = 'active') ORDER BY id_student
```

{{query:F01 · Students with an active enrollment (IN with a subquery)}}

#### F02 · Students with no enrollment (NOT IN with a subquery)

NOT IN with a subquery: only the students nobody enrolled anywhere.

Keywords: `education`, `subqueries`, `students`, `enrollments`, `subquery`, `in`

```sql
SELECT id_student, last_name FROM students WHERE id_student NOT IN (SELECT id_student FROM enrollments) ORDER BY id_student
```

{{query:F02 · Students with no enrollment (NOT IN with a subquery)}}

#### F03 · Subjects with no enrollment (NOT IN with a subquery)

The subjects that do not appear in any enrollment.

Keywords: `education`, `subqueries`, `subjects`, `enrollments`, `subquery`, `in`

```sql
SELECT id_subject, name FROM subjects WHERE id_subject NOT IN (SELECT id_subject FROM enrollments) ORDER BY id_subject
```

{{query:F03 · Subjects with no enrollment (NOT IN with a subquery)}}

#### F04 · Campuses with a building of more than four floors (EXISTS)

EXISTS with a correlated subquery: it is checked again for every campus.

Keywords: `education`, `subqueries`, `campuses`, `buildings`, `subquery`, `exists`

```sql
SELECT c.id_campus, c.name FROM campuses AS c WHERE EXISTS (SELECT 1 FROM buildings AS b WHERE b.id_campus = c.id_campus AND b.floors > 4) ORDER BY c.id_campus
```

{{query:F04 · Campuses with a building of more than four floors (EXISTS)}}

#### F05 · Campuses with no building of more than four floors (NOT EXISTS)

The other side of the previous example: NOT EXISTS keeps the campuses with no match.

Keywords: `education`, `subqueries`, `campuses`, `buildings`, `subquery`, `exists`

```sql
SELECT c.id_campus, c.name FROM campuses AS c WHERE NOT EXISTS (SELECT 1 FROM buildings AS b WHERE b.id_campus = c.id_campus AND b.floors > 4) ORDER BY c.id_campus
```

{{query:F05 · Campuses with no building of more than four floors (NOT EXISTS)}}

#### F06 · Degrees with a scholarship student (EXISTS, correlated)

The subquery points at the outer degree (d.code): a correlated EXISTS.

Keywords: `education`, `subqueries`, `degrees`, `students`, `subquery`, `exists`

```sql
SELECT d.code, d.name FROM degrees AS d WHERE EXISTS (SELECT 1 FROM students AS s WHERE s.degree_code = d.code AND s.has_scholarship = TRUE) ORDER BY d.code
```

{{query:F06 · Degrees with a scholarship student (EXISTS, correlated)}}

#### F07 · Databases enrollments better than some Data Structures grade (> ANY)

x > ANY (…) is true when the grade beats at least one of the inner values.

Keywords: `education`, `subqueries`, `enrollments`, `subquery`

```sql
SELECT id_enrollment, grade FROM enrollments WHERE id_subject = 2 AND grade > ANY (SELECT grade FROM enrollments WHERE id_subject = 1) ORDER BY id_enrollment
```

{{query:F07 · Databases enrollments better than some Data Structures grade (> ANY)}}

#### F08 · The highest credits of the first year (>= ALL)

x >= ALL (…) needs the comparison to hold for every inner value: only the top credits pass.

Keywords: `education`, `subqueries`, `subjects`, `subquery`

```sql
SELECT id_subject, name, credits FROM subjects WHERE credits >= ALL (SELECT credits FROM subjects WHERE year = 1) ORDER BY id_subject
```

{{query:F08 · The highest credits of the first year (>= ALL)}}

#### F09 · Subjects with no researcher teacher (<> ALL)

x <> ALL (…) is the same as x NOT IN (…): it keeps the values that differ from every one of them.

Keywords: `education`, `subqueries`, `subjects`, `teachers`, `subquery`

```sql
SELECT id_subject, name FROM subjects WHERE id_teacher <> ALL (SELECT id_teacher FROM teachers WHERE is_researcher = TRUE) ORDER BY id_subject
```

{{query:F09 · Subjects with no researcher teacher (<> ALL)}}

#### F10 · Students with a scholarship

This query is not a test by itself: it is the view the next examples read inside a subquery.

Keywords: `education`, `subqueries`, `students`

```sql
SELECT id_student, last_name, degree_code FROM students WHERE has_scholarship = TRUE
```

{{query:F10 · Students with a scholarship}}

#### F11 · Scholarship students read from the view (subquery over a view)

The subquery reads the view F10 as if it were a table: [F10 · …] inside the IN.

Keywords: `education`, `subqueries`, `students`, `subquery`, `in`

```sql
SELECT s.id_student, s.last_name FROM students AS s WHERE s.id_student IN (SELECT id_student FROM [F10 · Students with a scholarship]) ORDER BY s.id_student
```

{{query:F11 · Scholarship students read from the view (subquery over a view)}}

#### F12 · Average grade per subject of 8 or more (derived table)

A subquery in the FROM with an alias (t) is a derived table: it is queried like any other table.

Keywords: `education`, `subqueries`, `enrollments`, `subquery`, `group-by`, `aggregate`, `null`

```sql
SELECT t.id_subject, t.avg_grade FROM (SELECT id_subject, AVG(grade) AS avg_grade FROM enrollments WHERE grade IS NOT NULL GROUP BY id_subject) AS t WHERE t.avg_grade >= 8 ORDER BY t.id_subject
```

{{query:F12 · Average grade per subject of 8 or more (derived table)}}

#### F13 · Subjects with four or more enrollments (derived table and JOIN)

The derived table is joined to the subjects table and its column total is filtered.

Keywords: `education`, `subqueries`, `enrollments`, `subjects`, `subquery`, `join`, `group-by`, `aggregate`

```sql
SELECT t.id_subject, a.name, t.total FROM (SELECT id_subject, COUNT(*) AS total FROM enrollments GROUP BY id_subject) AS t INNER JOIN subjects AS a ON a.id_subject = t.id_subject WHERE t.total >= 4 ORDER BY t.total DESC, t.id_subject
```

{{query:F13 · Subjects with four or more enrollments (derived table and JOIN)}}

#### F14 · Enrollments of every student of a campus (scalar subquery in SELECT)

The subquery in the column list returns one value per row: the enrollments of that student.

Keywords: `education`, `subqueries`, `enrollments`, `students`, `subquery`, `aggregate`

```sql
SELECT s.id_student, s.last_name, (SELECT COUNT(*) FROM enrollments AS m WHERE m.id_student = s.id_student) AS enrollments FROM students AS s WHERE s.id_campus = 4 ORDER BY s.id_student
```

{{query:F14 · Enrollments of every student of a campus (scalar subquery in SELECT)}}

#### F15 · Subjects above the average credits (scalar subquery in WHERE)

The subquery returns a single value (the average) that the WHERE compares with.

Keywords: `education`, `subqueries`, `subjects`, `subquery`, `aggregate`

```sql
SELECT id_subject, name, credits FROM subjects WHERE credits > (SELECT AVG(credits) FROM subjects) ORDER BY id_subject
```

{{query:F15 · Subjects above the average credits (scalar subquery in WHERE)}}

#### F16 · Degrees with five or more students

A query whose SQL carries a subquery also works as a view: this one uses a correlated EXISTS with HAVING.

Keywords: `education`, `subqueries`, `degrees`, `students`, `subquery`, `having`, `aggregate`, `exists`

```sql
SELECT d.code, d.name FROM degrees AS d WHERE EXISTS (SELECT 1 FROM students AS s WHERE s.degree_code = d.code HAVING COUNT(*) >= 5) ORDER BY d.code
```

{{query:F16 · Degrees with five or more students}}

#### F17 · The previous view read as a table

F16 is read from the FROM like a table, even though inside it there is a subquery.

Keywords: `education`, `subqueries`

```sql
SELECT code, name FROM [F16 · Degrees with five or more students] ORDER BY code
```

{{query:F17 · The previous view read as a table}}

#### F18 · Subjects of degrees with a scholarship student (nested subqueries)

One subquery inside another: the inner one picks the degrees and the outer one the subjects.

Keywords: `education`, `subqueries`, `subjects`, `students`, `subquery`, `distinct`, `in`

```sql
SELECT DISTINCT a.id_subject, a.name FROM subjects AS a WHERE a.degree_code IN (SELECT s.degree_code FROM students AS s WHERE s.has_scholarship = TRUE) ORDER BY a.id_subject
```

{{query:F18 · Subjects of degrees with a scholarship student (nested subqueries)}}

#### F19 · Subjects that do not reach the top credits (NOT with ALL)

NOT (… >= ALL (…)) is the negation of the ALL predicate: everything but the top ones.

Keywords: `education`, `subqueries`, `subjects`, `subquery`

```sql
SELECT id_subject, name FROM subjects WHERE NOT (credits >= ALL (SELECT credits FROM subjects WHERE year = 1)) ORDER BY id_subject
```

{{query:F19 · Subjects that do not reach the top credits (NOT with ALL)}}

#### F20 · Buildings of the main campus (= ANY)

x = ANY (…) is the same as x IN (…): it is kept for completeness of the quantifiers.

Keywords: `education`, `subqueries`, `buildings`, `campuses`, `subquery`, `distinct`

```sql
SELECT DISTINCT b.id_campus FROM buildings AS b WHERE b.id_campus = ANY (SELECT id_campus FROM campuses WHERE is_main = TRUE) ORDER BY b.id_campus
```

{{query:F20 · Buildings of the main campus (= ANY)}}

#### F21 · Subjects with more credits than some Primary Education one (> SOME)

SOME is a synonym of ANY: the comparison has to hold for at least one inner value.

Keywords: `education`, `subqueries`, `subjects`, `subquery`

```sql
SELECT id_subject, name FROM subjects WHERE credits > SOME (SELECT credits FROM subjects WHERE degree_code = 'GEDU') ORDER BY id_subject
```

{{query:F21 · Subjects with more credits than some Primary Education one (> SOME)}}

### G · Unions

#### G01 · Campus and degree names, without repeats

UNION of two tables with one column each: the campus names and the degree names, every one once and sorted by name.

Keywords: `education`, `unions`, `campuses`, `degrees`, `union`

```sql
SELECT name FROM campuses UNION SELECT name FROM degrees ORDER BY name
```

{{query:G01 · Campus and degree names, without repeats}}

#### G02 · The country of every campus, twice (UNION ALL)

The same SELECT on both sides of UNION ALL: the rows are stacked without removing repeats, so there are 8 of them.

Keywords: `education`, `unions`, `campuses`, `union`

```sql
SELECT country FROM campuses UNION ALL SELECT country FROM campuses ORDER BY country
```

{{query:G02 · The country of every campus, twice (UNION ALL)}}

#### G03 · Every country once (UNION)

The same union without ALL: UNION removes the repeated rows, so each country comes out once.

Keywords: `education`, `unions`, `campuses`, `union`

```sql
SELECT country FROM campuses UNION SELECT country FROM campuses ORDER BY country
```

{{query:G03 · Every country once (UNION)}}

#### G04 · A union read as a table

The union goes in the FROM with an alias (derived table): the subject identifiers of the catalogue and of the enrollments, without repeats, are counted.

Keywords: `education`, `unions`, `subjects`, `enrollments`, `subquery`, `union`, `aggregate`

```sql
SELECT COUNT(*) AS n FROM (SELECT id_subject FROM subjects UNION SELECT id_subject FROM enrollments) AS t
```

{{query:G04 · A union read as a table}}

#### G05 · Union with output alias, ORDER BY and LIMIT

Both sides name their column item: the ORDER BY of the union sorts the combined result by that name and the LIMIT keeps the first 5.

Keywords: `education`, `unions`, `buildings`, `classrooms`, `union`

```sql
SELECT name AS item FROM buildings UNION SELECT code AS item FROM classrooms ORDER BY item LIMIT 5
```

{{query:G05 · Union with output alias, ORDER BY and LIMIT}}

### H · Scripts as tables

#### H01 · Students per campus, read from the script

Reads the result of the script “Students per campus” as if it were a table.

Keywords: `education`, `scripts-as-tables`

```sql
SELECT campus, students FROM [Students per campus] ORDER BY students DESC, campus
```

{{query:H01 · Students per campus, read from the script}}

#### H02 · How many campuses the script returns

Reads the result of the script “Students per campus” as if it were a table.

Keywords: `education`, `scripts-as-tables`, `aggregate`

```sql
SELECT COUNT(*) AS total FROM [Students per campus]
```

{{query:H02 · How many campuses the script returns}}

#### H03 · Campuses with 6 or more students

Reads the result of the script “Students per campus” as if it were a table.

Keywords: `education`, `scripts-as-tables`

```sql
SELECT campus, students FROM [Students per campus] WHERE students >= 6 ORDER BY students DESC, campus
```

{{query:H03 · Campuses with 6 or more students}}

### I · Parameters

#### P01 · Campuses that give the parameter its values

Source of a parameter: every row offers a value (id_campus) and its label (name).

Keywords: `education`, `parameters`, `source`, `reference`

```sql
SELECT id_campus, name FROM campuses ORDER BY name
```

{{query:P01 · Campuses that give the parameter its values}}

#### P02 · Main campus (parameter source)

Source of a parameter that returns one row only: the main campus.

Keywords: `education`, `parameters`, `source`, `reference`

```sql
SELECT id_campus, name FROM campuses WHERE is_main = true ORDER BY name
```

{{query:P02 · Main campus (parameter source)}}

#### P03 · Classrooms by minimum capacity (range)

Range parameter: the minimum capacity is chosen in a drop-down from 50 to 120.

Keywords: `education`, `parameters`, `range`, `classrooms`

```sql
SELECT code, capacity FROM classrooms WHERE capacity >= {{ 50..120 | Minimum capacity }} ORDER BY capacity DESC, code
```

{{query:P03 · Classrooms by minimum capacity (range)}}

#### P04 · Enrolments of the chosen year (subquery)

Parameter with a subquery: the years come from the enrolments themselves.

Keywords: `education`, `parameters`, `subquery`, `enrolments`

```sql
SELECT academic_year, COUNT(*) AS total FROM enrollments WHERE academic_year IN {{ SELECT academic_year FROM enrollments GROUP BY academic_year ORDER BY academic_year | Academic year }} GROUP BY academic_year ORDER BY academic_year
```

{{query:P04 · Enrolments of the chosen year (subquery)}}

#### P05 · Students of the chosen campuses (query reference)

Parameter that references a saved query: its id/name pairs fill the drop-down.

Keywords: `education`, `parameters`, `reference`, `students`

```sql
SELECT name, last_name, id_campus FROM students WHERE id_campus IN {{ P01 · Campuses that give the parameter its values | Campus }} ORDER BY last_name, name
```

{{query:P05 · Students of the chosen campuses (query reference)}}

#### P06 · Campuses except the main one (NOT IN with a reference)

Parameter in a NOT IN that references the query of the main campus.

Keywords: `education`, `parameters`, `reference`, `not-in`

```sql
SELECT name, city, country FROM campuses WHERE id_campus NOT IN {{ P02 · Main campus (parameter source) | Main campus }} ORDER BY name
```

{{query:P06 · Campuses except the main one (NOT IN with a reference)}}

#### P07 · Subjects by year (HAVING with a range)

Range parameter inside a HAVING: it filters the groups by their number of rows.

Keywords: `education`, `parameters`, `range`, `having`

```sql
SELECT year, COUNT(*) AS total, ROUND(AVG(credits), 1) AS average_credits FROM subjects GROUP BY year HAVING COUNT(*) >= {{ 1..10 | Minimum subjects }} ORDER BY year
```

{{query:P07 · Subjects by year (HAVING with a range)}}

#### P08 · Classrooms with the largest capacity (scalar subquery)

Parameter with a scalar subquery: MAX returns the single value that is chosen.

Keywords: `education`, `parameters`, `subquery`, `classrooms`

```sql
SELECT code, capacity FROM classrooms WHERE capacity = {{ SELECT MAX(capacity) FROM classrooms | Maximum capacity }} ORDER BY code
```

{{query:P08 · Classrooms with the largest capacity (scalar subquery)}}

#### P09 · Scholarship students of the script campuses (script reference)

Parameter that references a script: its rows give the values and labels of the drop-down.

Keywords: `education`, `parameters`, `reference`, `script`

```sql
SELECT name, last_name FROM students WHERE has_scholarship = true AND id_campus IN {{ P10 · Campuses for the parameter | Campus }} ORDER BY last_name, name
```

{{query:P09 · Scholarship students of the script campuses (script reference)}}

#### P12 · Classrooms between two capacities (two ranges)

Two range parameters in a BETWEEN: minimum and maximum capacity.

Keywords: `education`, `parameters`, `several`, `range`

```sql
SELECT code, capacity, floor FROM classrooms WHERE capacity BETWEEN {{ 20..60 | Minimum capacity }} AND {{ 50..120 | Maximum capacity }} ORDER BY capacity, code
```

{{query:P12 · Classrooms between two capacities (two ranges)}}

#### P13 · Enrolments by year and status (two subqueries)

Two subquery parameters: the year (a list) and the status (one value).

Keywords: `education`, `parameters`, `several`, `subquery`

```sql
SELECT academic_year, status, COUNT(*) AS total FROM enrollments WHERE academic_year IN {{ SELECT academic_year FROM enrollments GROUP BY academic_year ORDER BY academic_year | Year }} AND status = {{ SELECT status FROM enrollments GROUP BY status ORDER BY status | Status }} GROUP BY academic_year, status ORDER BY academic_year, status
```

{{query:P13 · Enrolments by year and status (two subqueries)}}

#### P14 · Teachers of a category with minimum hours (subquery and range)

Two parameters: the category from a subquery and the hours from a range.

Keywords: `education`, `parameters`, `several`, `teachers`

```sql
SELECT name, last_name, category, weekly_hours FROM teachers WHERE category = {{ SELECT DISTINCT category FROM teachers ORDER BY category | Category }} AND weekly_hours >= {{ 5..20 | Minimum hours }} ORDER BY weekly_hours DESC, last_name
```

{{query:P14 · Teachers of a category with minimum hours (subquery and range)}}

#### P15 · Buildings of a campus with minimum floors (reference and range)

Two parameters: the campus of a saved query and the minimum number of floors.

Keywords: `education`, `parameters`, `several`, `reference`

```sql
SELECT b.name, b.floors, b.construction_year FROM buildings AS b WHERE b.id_campus IN {{ P01 · Campuses that give the parameter its values | Campus }} AND b.floors >= {{ 1..5 | Minimum floors }} ORDER BY b.floors DESC, b.name
```

{{query:P15 · Buildings of a campus with minimum floors (reference and range)}}

#### P16 · Scholarship students of a degree and a campus (two references)

Two subquery parameters that filter by degree and by campus at the same time.

Keywords: `education`, `parameters`, `several`, `students`

```sql
SELECT name, last_name, degree_code, id_campus FROM students WHERE has_scholarship = true AND degree_code IN {{ SELECT code FROM degrees ORDER BY code | Degree }} AND id_campus IN {{ P01 · Campuses that give the parameter its values | Campus }} ORDER BY last_name, name
```

{{query:P16 · Scholarship students of a degree and a campus (two references)}}

### J · PIVOT

#### P01 · Enrollments by status and year

PIVOT with status rows, academic-year columns and COUNT(*), plus row and column totals.

Keywords: `pivot`, `dynamic table`, `count`, `average`, `subtotal`

```sql
PIVOT (SELECT status) BY (SELECT academic_year) FROM enrollments VALUES COUNT(*) AS enrollments TOTALS ROWS GRAND COLUMNS GRAND;
```

{{query:P01 · Enrollments by status and year}}

#### P02 · Seats and rooms by floor and projector

Two measures on columns (seats and rooms) by floor and projector, with totals.

Keywords: `pivot`, `dynamic table`, `count`, `average`, `subtotal`

```sql
PIVOT (SELECT floor) BY (SELECT has_projector) FROM classrooms VALUES SUM(capacity) AS seats, COUNT(*) AS rooms TOTALS ROWS GRAND COLUMNS GRAND MEASURES ON COLUMNS;
```

{{query:P02 · Seats and rooms by floor and projector}}

#### P03 · Average grade by status and year

AVG(grade) by status and year; an empty cell is filled with “–” and a null aggregate with “n/d”.

Keywords: `pivot`, `dynamic table`, `count`, `average`, `subtotal`

```sql
PIVOT (SELECT status) BY (SELECT academic_year) FROM enrollments VALUES AVG(grade) AS average_grade TOTALS ROWS GRAND COLUMNS GRAND FILL EMPTY '–', NULLVALUE 'n/d';
```

{{query:P03 · Average grade by status and year}}

#### P04 · Teacher load by category and research

AVG(weekly_hours) and COUNT(*) of teachers by category and research profile.

Keywords: `pivot`, `dynamic table`, `count`, `average`, `subtotal`

```sql
PIVOT (SELECT category) BY (SELECT is_researcher) FROM teachers VALUES AVG(weekly_hours) AS average_hours, COUNT(*) AS teachers TOTALS ROWS GRAND COLUMNS GRAND;
```

{{query:P04 · Teacher load by category and research}}

#### P05 · Classrooms by floor, projector and board

Hierarchical columns (projector and board type) with a subtotal by the first column level.

Keywords: `pivot`, `dynamic table`, `count`, `average`, `subtotal`

```sql
PIVOT (SELECT floor) BY (SELECT has_projector, board_type) FROM classrooms VALUES COUNT(*) AS rooms TOTALS ROWS GRAND COLUMNS SUBTOTAL BY has_projector GRAND;
```

{{query:P05 · Classrooms by floor, projector and board}}

### K · CASE and IF

#### K01 · Classrooms by size (searched CASE)

Searched CASE with overlapping conditions: below 25 is small, 25–55 medium and the rest large. The boundary 55 is medium and 60 is large.

Keywords: `education`, `case`, `functions`, `classrooms`

```sql
SELECT code, capacity, CASE WHEN capacity < 25 THEN 'Small' WHEN capacity BETWEEN 25 AND 55 THEN 'Medium' ELSE 'Large' END AS size FROM classrooms WHERE capacity >= 55 ORDER BY capacity DESC, code
```

{{query:K01 · Classrooms by size (searched CASE)}}

#### K02 · Degrees by cycle (simple CASE)

Simple CASE: compares the level with each WHEN using = and maps Bachelor and Master to a cycle.

Keywords: `education`, `case`, `functions`, `degrees`

```sql
SELECT code, CASE level WHEN 'Bachelor' THEN 'Undergraduate' WHEN 'Master' THEN 'Postgraduate' ELSE 'Other' END AS cycle FROM degrees ORDER BY code
```

{{query:K02 · Degrees by cycle (simple CASE)}}

#### K03 · Teachers without an office (CASE IS NULL)

The searched CASE catches the missing office with IS NULL, so it does not fall into ELSE silently.

Keywords: `education`, `case`, `null`, `teachers`

```sql
SELECT name, last_name, CASE WHEN office IS NULL THEN 'No office' ELSE office END AS room FROM teachers WHERE office IS NULL ORDER BY last_name
```

{{query:K03 · Teachers without an office (CASE IS NULL)}}

#### K04 · Large classrooms with IF

IF(condition, value, substitute) is the shorthand of CASE WHEN condition THEN value ELSE substitute END.

Keywords: `education`, `if`, `functions`, `classrooms`

```sql
SELECT code, IF(capacity >= 55, 'Large', 'Small') AS size FROM classrooms WHERE code IN ('B-SALA', 'K-102', 'C-LAB1') ORDER BY capacity DESC
```

{{query:K04 · Large classrooms with IF}}

#### K05 · Teachers by load (CASE in GROUP BY)

The same CASE expression classifies the rows and is used again in GROUP BY: six teachers in each group.

Keywords: `education`, `case`, `group-by`, `teachers`

```sql
SELECT CASE WHEN weekly_hours >= 15 THEN 'High load' ELSE 'Low load' END AS load, COUNT(*) AS n FROM teachers GROUP BY CASE WHEN weekly_hours >= 15 THEN 'High load' ELSE 'Low load' END ORDER BY load
```

{{query:K05 · Teachers by load (CASE in GROUP BY)}}

### L · Functions in SELECT and PIVOT

#### L01 · Numeric functions: ABS and ROUND

Applies ABS and ROUND to classroom capacity: distance from 50 and one decimal of capacity / 3.

Keywords: `education`, `functions`, `numeric`, `abs`, `round`

```sql
SELECT code, capacity, ABS(capacity - 50) AS distance, ROUND(capacity / 3.0, 1) AS third FROM classrooms WHERE capacity >= 55 ORDER BY capacity DESC, code
```

{{query:L01 · Numeric functions: ABS and ROUND}}

#### L02 · Text functions: UPPER, LOWER, LENGTH, TRIM, SUBSTR and CONCAT

Applies the six text functions to the first three teachers.

Keywords: `education`, `functions`, `text`, `upper`, `lower`, `length`, `trim`, `substr`, `concat`

```sql
SELECT id_teacher, UPPER(name) AS upper_name, LOWER(last_name) AS lower_last_name, LENGTH(last_name) AS letters, TRIM(name) AS trimmed, SUBSTR(last_name, 1, 5) AS prefix, CONCAT(name, ' ', last_name) AS full_name FROM teachers WHERE id_teacher <= 3 ORDER BY id_teacher
```

{{query:L02 · Text functions: UPPER, LOWER, LENGTH, TRIM, SUBSTR and CONCAT}}

#### L03 · Regular expressions: REPLACE and INSTR

Uses REGEXPR_REPLACE to mask digits and REGEXPR_INSTR to locate the first digit in three classroom codes.

Keywords: `education`, `functions`, `regex`, `replace`, `instr`

```sql
SELECT code, REGEXPR_REPLACE(code, '[0-9]', '#') AS masked, REGEXPR_INSTR(code, '[0-9]+') AS first_digit FROM classrooms WHERE code IN ('K-101', 'B-SALA', 'C-LAB1') ORDER BY code
```

{{query:L03 · Regular expressions: REPLACE and INSTR}}

#### L04 · NULL functions: COALESCE, IFNULL and NULLIF

Uses COALESCE, IFNULL and NULLIF over office, email and category: missing values and the value equal to Associate are transformed.

Keywords: `education`, `functions`, `null`, `coalesce`, `ifnull`, `nullif`

```sql
SELECT id_teacher, COALESCE(office, 'No office') AS office, IFNULL(email, 'No email') AS email, NULLIF(category, 'Associate') AS not_associate FROM teachers WHERE id_teacher <= 4 ORDER BY id_teacher
```

{{query:L04 · NULL functions: COALESCE, IFNULL and NULLIF}}

#### L05 · Conditional functions: IF and CASE

Uses IF and both CASE forms to classify three classrooms by capacity.

Keywords: `education`, `functions`, `conditional`, `if`, `case`

```sql
SELECT code, IF(capacity >= 55, 'Large', 'Small') AS size, CASE WHEN capacity < 25 THEN 'Tiny' WHEN capacity BETWEEN 25 AND 55 THEN 'Medium' ELSE 'Large' END AS category FROM classrooms WHERE code IN ('B-SALA', 'K-102', 'C-LAB1') ORDER BY capacity DESC
```

{{query:L05 · Conditional functions: IF and CASE}}

#### L06 · Aggregate functions: COUNT, SUM, AVG, MIN and MAX

Runs COUNT(*), COUNT(column), COUNT(DISTINCT), SUM, AVG, MIN and MAX over teachers.

Keywords: `education`, `functions`, `aggregate`, `count`, `sum`, `avg`, `min`, `max`

```sql
SELECT COUNT(*) AS total, COUNT(office) AS with_office, COUNT(DISTINCT category) AS categories, SUM(weekly_hours) AS hours, AVG(weekly_hours) AS average_hours, MIN(weekly_hours) AS min_hours, MAX(weekly_hours) AS max_hours FROM teachers
```

{{query:L06 · Aggregate functions: COUNT, SUM, AVG, MIN and MAX}}

#### L07 · PIVOT with all aggregate functions

PIVOT with COUNT, SUM, AVG, MIN and MAX as measures, one measure per row, by floor and projector.

Keywords: `education`, `functions`, `pivot`, `aggregate`

```sql
PIVOT (SELECT floor) BY (SELECT has_projector) FROM classrooms VALUES COUNT(*) AS rooms, SUM(capacity) AS seats, AVG(capacity) AS average_seats, MIN(capacity) AS min_seats, MAX(capacity) AS max_seats TOTALS ROWS GRAND COLUMNS GRAND MEASURES ON ROWS;
```

{{query:L07 · PIVOT with all aggregate functions}}

#### L08 · PIVOT with scalar functions in the row axis

PIVOT with scalar functions in the row axis: UPPER and CASE create the load, REGEXPR_INSTR filters teachers with an e-mail, and COUNT/AVG aggregate.

Keywords: `education`, `functions`, `pivot`, `scalar`, `case`, `regexpr_instr`

```sql
PIVOT (SELECT UPPER(CASE WHEN weekly_hours >= 15 THEN 'high' ELSE 'low' END) AS load WHERE REGEXPR_INSTR(email, '@') > 0) BY (SELECT is_researcher) FROM teachers VALUES COUNT(*) AS teachers, AVG(weekly_hours) AS average_hours TOTALS ROWS GRAND COLUMNS GRAND;
```

{{query:L08 · PIVOT with scalar functions in the row axis}}

## Scripts

### A · Basics and aggregation

#### Scholarship students and their contact

Filters scholarship students and shows their full name, e-mail and campus.

Keywords: `education`, `filters`, `students`, `filter`, `map`

```js
const datos = tables["students"];
return datos
  .filter((fila) => fila.has_scholarship)
  .map((fila) => ({
    "full_name": `${fila.name} ${fila.last_name}`,
    "email": fila.email,
    "campus": fila.id_campus,
  }));
```

{{script:Scholarship students and their contact}}

#### Student ages

Calculates the age on 1 September 2025 from the birth date and sorts from oldest to youngest.

Keywords: `education`, `dates`, `students`, `map`, `sort`

```js
const datos = tables["students"];
const reference = new Date("2025-09-01");
return datos
  .map((fila) => {
    const birth = new Date(fila.birth_date);
    let age = reference.getFullYear() - birth.getFullYear();
    const m = reference.getMonth() - birth.getMonth();
    if (m < 0 || (m === 0 && reference.getDate() < birth.getDate())) {
      age -= 1;
    }
    return {
      "full_name": `${fila.name} ${fila.last_name}`,
      "birth_date": fila.birth_date,
      "age": age,
    };
  })
  .sort((a, b) => b["age"] - a["age"]);
```

{{script:Student ages}}

#### Ten oldest students

Sorts by birth date and returns the first ten rows with their rank.

Keywords: `education`, `filters`, `students`, `map`, `sort`, `slice`

```js
const datos = tables["students"];
return datos
  .slice()
  .sort((a, b) => String(a.birth_date).localeCompare(String(b.birth_date)))
  .slice(0, 10)
  .map((fila, index) => ({
    "rank": index + 1,
    "full_name": `${fila.name} ${fila.last_name}`,
    "birth_date": fila.birth_date,
  }));
```

{{script:Ten oldest students}}

#### Students per campus

Crosses students with the campuses table to count how many there are in each one.

Keywords: `education`, `aggregates`, `students`, `campuses`, `map`, `reduce`, `sort`, `objects`

```js
const datos = tables["students"];
const campuses = new Map(tables["campuses"].map((c) => [c.id_campus, c.name]));
const counts = datos.reduce((acc, fila) => {
  const name = campuses.get(fila.id_campus) || "—";
  acc[name] = (acc[name] || 0) + 1;
  return acc;
}, {});
return Object.entries(counts)
  .map(([name, count]) => ({ "campus": name, "students": count }))
  .sort((a, b) => b["students"] - a["students"]);
```

{{script:Students per campus}}

#### Incomplete contacts

Lists the students who are missing their e-mail, their phone or both.

Keywords: `education`, `filters`, `students`, `filter`, `map`

```js
const datos = tables["students"];
return datos
  .filter((fila) => !fila.email || !fila.phone)
  .map((fila) => {
    const missing = [];
    if (!fila.email) missing.push("email");
    if (!fila.phone) missing.push("phone");
    return {
      "full_name": `${fila.name} ${fila.last_name}`,
      "email": fila.email,
      "phone": fila.phone,
      "missing": missing.join(", "),
    };
  });
```

{{script:Incomplete contacts}}

#### Scholarships by degree

Groups by degree code and calculates the total students, scholarship holders and percentage.

Keywords: `education`, `aggregates`, `students`, `map`, `reduce`, `sort`, `math`

```js
const datos = tables["students"];
const groups = datos.reduce((acc, fila) => {
  const key = fila.degree_code;
  acc[key] = acc[key] || { total: 0, scholarship: 0 };
  acc[key].total += 1;
  if (fila.has_scholarship) acc[key].scholarship += 1;
  return acc;
}, {});
return Object.entries(groups)
  .map(([degree, g]) => ({
    "degree": degree,
    "students": g.total,
    "scholarships": g.scholarship,
    "percentage": Math.round((g.scholarship / g.total) * 1000) / 10,
  }))
  .sort((a, b) => a["degree"].localeCompare(b["degree"]));
```

{{script:Scholarships by degree}}

#### Grades by status

Ignores null grades and calculates how many there are, their average, minimum and maximum per status.

Keywords: `education`, `aggregates`, `enrollments`, `filter`, `map`, `reduce`, `math`

```js
const datos = tables["enrollments"];
const groups = datos
  .filter((fila) => fila.grade !== null && fila.grade !== undefined)
  .reduce((acc, fila) => {
    const key = fila.status;
    acc[key] = acc[key] || { count: 0, sum: 0, min: Infinity, max: -Infinity };
    const g = acc[key];
    g.count += 1;
    g.sum += fila.grade;
    g.min = Math.min(g.min, fila.grade);
    g.max = Math.max(g.max, fila.grade);
    return acc;
  }, {});
return Object.entries(groups).map(([status, g]) => ({
  "status": status,
  "count": g.count,
  "average": Math.round((g.sum / g.count) * 100) / 100,
  "min": g.min,
  "max": g.max,
}));
```

{{script:Grades by status}}

#### Pass rate by academic year

Counts enrollments, passed ones and the average grade of the graded ones in each academic year.

Keywords: `education`, `aggregates`, `enrollments`, `map`, `reduce`, `math`, `objects`

```js
const datos = tables["enrollments"];
const groups = datos.reduce((acc, fila) => {
  const key = fila.academic_year;
  acc[key] = acc[key] || { total: 0, passed: 0, graded: 0, sum: 0 };
  const g = acc[key];
  g.total += 1;
  if (fila.status === "passed") g.passed += 1;
  if (fila.grade !== null && fila.grade !== undefined) {
    g.graded += 1;
    g.sum += fila.grade;
  }
  return acc;
}, {});
return Object.entries(groups).map(([year, g]) => ({
  "year": year,
  "count": g.total,
  "passed": g.passed,
  "percentage": Math.round((g.passed / g.total) * 1000) / 10,
  "average": g.graded ? Math.round((g.sum / g.graded) * 100) / 100 : null,
}));
```

{{script:Pass rate by academic year}}

#### Statuses by year (pivot)

Turns the statuses into columns: active, passed and cancelled per academic year.

Keywords: `education`, `aggregates`, `enrollments`, `map`, `reduce`, `sort`, `objects`

```js
const datos = tables["enrollments"];
const groups = datos.reduce((acc, fila) => {
  const year = fila.academic_year;
  const status = fila.status;
  acc[year] = acc[year] || { year, active: 0, passed: 0, cancelled: 0 };
  if (status === "active") acc[year].active += 1;
  else if (status === "passed") acc[year].passed += 1;
  else if (status === "cancelled") acc[year].cancelled += 1;
  return acc;
}, {});
return Object.values(groups)
  .map((g) => ({
    "year": g.year,
    "active": g.active,
    "passed": g.passed,
    "cancelled": g.cancelled,
  }))
  .sort((a, b) => String(a["year"]).localeCompare(String(b["year"])));
```

{{script:Statuses by year (pivot)}}

#### Grade distribution

Sorts the grades into four ranges and calculates how many there are and their percentage.

Keywords: `education`, `aggregates`, `enrollments`, `map`, `reduce`, `foreach`, `math`

```js
const datos = tables["enrollments"];
const buckets = { "0-4.9": 0, "5-6.9": 0, "7-8.9": 0, "9-10": 0 };
datos.forEach((fila) => {
  const g = fila.grade;
  if (g === null || g === undefined) return;
  if (g < 5) buckets["0-4.9"] += 1;
  else if (g < 7) buckets["5-6.9"] += 1;
  else if (g < 9) buckets["7-8.9"] += 1;
  else buckets["9-10"] += 1;
});
const total = Object.values(buckets).reduce((a, b) => a + b, 0);
return Object.entries(buckets).map(([range, count]) => ({
  "range": range,
  "count": count,
  "percentage": total ? Math.round((count / total) * 1000) / 10 : 0,
}));
```

{{script:Grade distribution}}

#### Subjects by year

Counts subjects, credits and average classroom hours per year.

Keywords: `education`, `aggregates`, `subjects`, `map`, `reduce`, `sort`, `math`

```js
const datos = tables["subjects"];
const groups = datos.reduce((acc, fila) => {
  const key = fila.year;
  acc[key] = acc[key] || { count: 0, credits: 0, hours: 0 };
  acc[key].count += 1;
  acc[key].credits += fila.credits;
  acc[key].hours += fila.classroom_hours;
  return acc;
}, {});
return Object.entries(groups)
  .map(([year, g]) => ({
    "year": Number(year),
    "count": g.count,
    "credits": g.credits,
    "average_hours": Math.round((g.hours / g.count) * 10) / 10,
  }))
  .sort((a, b) => a["year"] - b["year"]);
```

{{script:Subjects by year}}

#### Teachers by category

Groups by category and calculates how many there are, the average hours and how many research.

Keywords: `education`, `aggregates`, `teachers`, `map`, `reduce`, `sort`, `math`

```js
const datos = tables["teachers"];
const groups = datos.reduce((acc, fila) => {
  const key = fila.category;
  acc[key] = acc[key] || { count: 0, hours: 0, researchers: 0 };
  acc[key].count += 1;
  acc[key].hours += fila.weekly_hours;
  if (fila.is_researcher) acc[key].researchers += 1;
  return acc;
}, {});
return Object.entries(groups)
  .map(([category, g]) => ({
    "category": category,
    "count": g.count,
    "average_hours": Math.round((g.hours / g.count) * 10) / 10,
    "researchers": g.researchers,
  }))
  .sort((a, b) => b["count"] - a["count"]);
```

{{script:Teachers by category}}

#### Teachers without an office or e-mail

Lists the teachers who are missing their office, their e-mail or both.

Keywords: `education`, `filters`, `teachers`, `filter`, `map`

```js
const datos = tables["teachers"];
return datos
  .filter((fila) => !fila.email || !fila.office)
  .map((fila) => {
    const missing = [];
    if (!fila.email) missing.push("email");
    if (!fila.office) missing.push("office");
    return {
      "teacher": `${fila.name} ${fila.last_name}`,
      "category": fila.category,
      "email": fila.email,
      "office": fila.office,
      "missing": missing.join(", "),
    };
  });
```

{{script:Teachers without an office or e-mail}}

#### Campus summary

Calculates the share of each campus capacity over the total and sorts by capacity.

Keywords: `education`, `aggregates`, `campuses`, `map`, `reduce`, `sort`, `math`

```js
const datos = tables["campuses"];
const total = datos.reduce((sum, fila) => sum + fila.max_capacity, 0);
return datos
  .map((fila) => ({
    "campus": fila.name,
    "city": fila.city,
    "capacity": fila.max_capacity,
    "percentage": Math.round((fila.max_capacity / total) * 1000) / 10,
  }))
  .sort((a, b) => b["capacity"] - a["capacity"]);
```

{{script:Campus summary}}

#### Credit-weighted average

Weights the grade of each enrollment by the credits of its subject and returns one row.

Keywords: `education`, `aggregates`, `enrollments`, `subjects`, `map`, `foreach`, `math`

```js
const datos = tables["enrollments"];
const subjects = new Map(tables["subjects"].map((s) => [s.id_subject, s.credits]));
let sum = 0;
let credits = 0;
datos.forEach((fila) => {
  if (fila.grade === null || fila.grade === undefined) return;
  const c = subjects.get(fila.id_subject) || 0;
  sum += fila.grade * c;
  credits += c;
});
return [{
  "average": credits ? Math.round((sum / credits) * 100) / 100 : null,
  "credits": credits,
}];
```

{{script:Credit-weighted average}}

#### JavaScript · Enrollment timeline

Enrollments per month with Date, status columns and a running total.

Keywords: `education`, `dates`, `enrollments`, `map`, `sort`, `foreach`

```js
const groups = new Map();
tables["enrollments"].forEach((row) => {
  const date = new Date(row.enrollment_date);
  const month = `${date.getFullYear()}-${String(date.getMonth() + 1).padStart(2, "0")}`;
  const group = groups.get(month) || { month, total: 0, active: 0, passed: 0, cancelled: 0 };
  group.total += 1;
  if (group[row.status] !== undefined) group[row.status] += 1;
  groups.set(month, group);
});
let cumulative = 0;
return [...groups.values()]
  .sort((a, b) => a.month.localeCompare(b.month))
  .map((group) => {
    cumulative += group.total;
    return {
      "month": group.month,
      "enrollments": group.total,
      "active": group.active,
      "passed": group.passed,
      "cancelled": group.cancelled,
      "cumulative": cumulative,
    };
  });
```

{{script:JavaScript · Enrollment timeline}}

#### JavaScript · Subject popularity pivot

One row per subject with status counts, a pass rate and a top-five mark from query C13.

Keywords: `education`, `aggregates`, `subjects`, `enrollments`, `map`, `sort`, `foreach`, `math`

```js
const subjects = new Map(tables["subjects"].map((s) => [s.id_subject, s]));
const top = new Set(queries["C13 · Subjects with more enrollments (ORDER BY of an aggregate)"]().map((row) => row.subject));
const groups = new Map();
tables["enrollments"].forEach((row) => {
  const subject = subjects.get(row.id_subject);
  if (!subject) return;
  const group = groups.get(row.id_subject) || { subject, total: 0, passed: 0, active: 0, cancelled: 0 };
  group.total += 1;
  if (group[row.status] !== undefined) group[row.status] += 1;
  groups.set(row.id_subject, group);
});
return [...groups.values()]
  .map((group) => ({
    "subject": group.subject.name,
    "degree": group.subject.degree_code,
    "enrollments": group.total,
    "passed": group.passed,
    "active": group.active,
    "cancelled": group.cancelled,
    "pass_percent": group.total ? Math.round((group.passed / group.total) * 1000) / 10 : 0,
    "top_five": top.has(group.subject.name) ? "yes" : "no",
  }))
  .sort((a, b) => b["enrollments"] - a["enrollments"] || a["subject"].localeCompare(b["subject"]));
```

{{script:JavaScript · Subject popularity pivot}}

### B · Joins

#### Subject ranking by average grade

Groups graded enrollments by subject, crosses their name and sorts by average.

Keywords: `education`, `joins`, `enrollments`, `subjects`, `map`, `reduce`, `sort`, `math`

```js
const datos = tables["enrollments"];
const subjects = new Map(tables["subjects"].map((s) => [s.id_subject, s.name]));
const groups = datos.reduce((acc, fila) => {
  if (fila.grade === null || fila.grade === undefined) return acc;
  const key = fila.id_subject;
  acc[key] = acc[key] || { sum: 0, count: 0 };
  acc[key].sum += fila.grade;
  acc[key].count += 1;
  return acc;
}, {});
return Object.entries(groups)
  .map(([id, g]) => ({
    "subject": subjects.get(Number(id)) || `#${id}`,
    "count": g.count,
    "average": Math.round((g.sum / g.count) * 100) / 100,
  }))
  .sort((a, b) => b["average"] - a["average"])
  .map((row, index) => ({ "rank": index + 1, ...row }));
```

{{script:Subject ranking by average grade}}

#### Enrollment summary per student

Groups by student, lists their subjects and calculates their average grade.

Keywords: `education`, `joins`, `enrollments`, `students`, `subjects`, `map`, `reduce`, `sort`, `math`

```js
const datos = tables["enrollments"];
const students = new Map(tables["students"].map((s) => [s.id_student, `${s.name} ${s.last_name}`]));
const subjects = new Map(tables["subjects"].map((s) => [s.id_subject, s.name]));
const groups = datos.reduce((acc, fila) => {
  const id = fila.id_student;
  acc[id] = acc[id] || { subjects: new Set(), grades: [] };
  acc[id].subjects.add(subjects.get(fila.id_subject) || `#${fila.id_subject}`);
  if (fila.grade !== null && fila.grade !== undefined) acc[id].grades.push(fila.grade);
  return acc;
}, {});
return Object.entries(groups)
  .map(([id, g]) => ({
    "student": students.get(Number(id)) || `#${id}`,
    "subjects": g.subjects.size,
    "average": g.grades.length ? Math.round((g.grades.reduce((a, b) => a + b, 0) / g.grades.length) * 100) / 100 : null,
    "list": Array.from(g.subjects).join(" · "),
  }))
  .sort((a, b) => b["subjects"] - a["subjects"]);
```

{{script:Enrollment summary per student}}

#### Classroom capacity by building

Adds up the capacity of the classrooms of each building and crosses its name.

Keywords: `education`, `joins`, `classrooms`, `buildings`, `map`, `reduce`, `sort`, `math`

```js
const datos = tables["classrooms"];
const buildings = new Map(tables["buildings"].map((b) => [b.id_building, b.name]));
const groups = datos.reduce((acc, fila) => {
  const key = fila.id_building;
  acc[key] = acc[key] || { count: 0, capacity: 0 };
  acc[key].count += 1;
  acc[key].capacity += fila.capacity;
  return acc;
}, {});
return Object.entries(groups)
  .map(([id, g]) => ({
    "building": buildings.get(Number(id)) || `#${id}`,
    "classrooms": g.count,
    "capacity": g.capacity,
    "average": Math.round((g.capacity / g.count) * 10) / 10,
  }))
  .sort((a, b) => b["capacity"] - a["capacity"]);
```

{{script:Classroom capacity by building}}

#### Orphan enrollments

Finds enrollments whose student or subject no longer exists in the tables.

Keywords: `education`, `joins`, `enrollments`, `students`, `subjects`, `filter`, `map`

```js
const datos = tables["enrollments"];
const studentIds = new Set(tables["students"].map((s) => s.id_student));
const subjectIds = new Set(tables["subjects"].map((s) => s.id_subject));
return datos
  .filter((fila) => !studentIds.has(fila.id_student) || !subjectIds.has(fila.id_subject))
  .map((fila) => ({
    "enrollment": fila.id_enrollment,
    "student": fila.id_student,
    "subject": fila.id_subject,
    "missing": !studentIds.has(fila.id_student) ? "student" : "subject",
  }));
```

{{script:Orphan enrollments}}

#### JavaScript · Campus capacity ratio

The students-per-campus query crossed with the campus capacity table.

Keywords: `education`, `joins`, `campuses`, `map`, `reduce`, `math`

```js
const counts = queries["H01 · Students per campus, read from the script"]();
const campuses = new Map(tables["campuses"].map((c) => [c.name, c]));
let cumulative = 0;
const total = counts.reduce((sum, row) => sum + row.students, 0);
return counts.map((row) => {
  const campus = campuses.get(row.campus);
  const capacity = campus ? campus.max_capacity : 0;
  const ratio = capacity ? Math.round((row.students / capacity) * 1000) / 10 : null;
  cumulative += row.students;
  return {
    "campus": row.campus,
    "students": row.students,
    "capacity": capacity,
    "fill_percent": ratio,
    "share_percent": total ? Math.round((row.students / total) * 1000) / 10 : 0,
    "cumulative": cumulative,
    "status": ratio === null ? "no capacity" : ratio >= 75 ? "full" : ratio >= 50 ? "busy" : "room left",
  };
});
```

{{script:JavaScript · Campus capacity ratio}}

#### JavaScript · Data quality checklist

Runs five integrity queries and turns their row counts into a checklist.

Keywords: `education`, `joins`, `map`

```js
const checks = [
  ["Enrollments without a student", "D01 · Enrollments with no student (integrity of the 1:M)"],
  ["Enrollments without a subject", "D02 · Enrollments with no subject (integrity of the other 1:M)"],
  ["Subjects without a degree", "D03 · Subjects with no degree (integrity of the text code)"],
  ["Subjects without a teacher", "D04 · Subjects with no teacher"],
  ["Repeated student/subject pairs", "D08 · Repeated enrollments of the same student/subject pair"],
];
return checks.map(([label, query]) => {
  const rows = queries[query]();
  return {
    "check": label,
    "issues": rows.length,
    "status": rows.length === 0 ? "ok" : "review",
    "query": query,
  };
});
```

{{script:JavaScript · Data quality checklist}}

### C · Related queries

#### Use a saved query

Calls query A19 (students with three or more enrollments) and shows its result.

Keywords: `education`, `reports`, `map`

```js
const rows = queries["A19 · Students with three or more enrollments"]();
return rows.map((row) => ({
  "student": row.id_student,
  "enrollments": row.enrollments,
}));
```

{{script:Use a saved query}}

#### Report with forEach and resultado

Builds a report per status with forEach, adds a TOTAL row and stores it in resultado.

Keywords: `education`, `reports`, `enrollments`, `map`, `foreach`, `math`, `objects`

```js
const datos = tables["enrollments"];
const groups = {};
datos.forEach((fila) => {
  const key = fila.status;
  groups[key] = groups[key] || { count: 0, sum: 0 };
  groups[key].count += 1;
  if (fila.grade !== null && fila.grade !== undefined) groups[key].sum += fila.grade;
});
const rows = Object.entries(groups).map(([status, g]) => ({
  "status": status,
  "count": g.count,
  "average": g.count ? Math.round((g.sum / g.count) * 100) / 100 : null,
}));
rows.push({
  "status": "TOTAL",
  "count": datos.length,
  "average": null,
});
resultado = rows;
```

{{script:Report with forEach and resultado}}

#### JavaScript · Student directory with Intl

Directory sorted with Intl.Collator, with initials, e-mail parts and campus.

Keywords: `education`, `javascript`, `students`, `campuses`, `map`, `sort`, `slice`, `intl`

```js
const students = tables["students"];
const campuses = new Map(tables["campuses"].map((c) => [c.id_campus, c.name]));
const collator = new Intl.Collator("en", { sensitivity: "base" });
return students
  .map((fila) => {
    const email = fila.email ? String(fila.email) : "";
    const at = email.indexOf("@");
    return {
      "student": `${fila.last_name}, ${fila.name}`,
      "initials": `${fila.name.charAt(0)}${fila.last_name.charAt(0)}`.toUpperCase(),
      "campus": campuses.get(fila.id_campus) || "—",
      "email_user": at > 0 ? email.slice(0, at) : null,
      "email_domain": at > 0 ? email.slice(at + 1) : null,
      "scholarship": fila.has_scholarship ? "yes" : "no",
    };
  })
  .sort((a, b) => collator.compare(a["student"], b["student"]));
```

{{script:JavaScript · Student directory with Intl}}

#### JavaScript · Degree scorecard

Degree totals from the tables joined to the per-subject averages of query A31.

Keywords: `education`, `javascript`, `degrees`, `subjects`, `map`, `sort`, `foreach`, `math`

```js
const degrees = tables["degrees"];
const subjectsById = new Map(tables["subjects"].map((s) => [s.id_subject, s]));
const averages = queries["A31 · Average grade per subject (HAVING and ORDER BY by alias)"]();
const groups = new Map(degrees.map((d) => [d.code, { degree: d, subjects: 0, graded: 0, weighted: 0 }]));
tables["subjects"].forEach((subject) => {
  const group = groups.get(subject.degree_code);
  if (group) group.subjects += 1;
});
averages.forEach((row) => {
  const subject = subjectsById.get(row.id_subject);
  const group = subject ? groups.get(subject.degree_code) : null;
  if (!group) return;
  group.graded += row.graded;
  group.weighted += row.average_grade * row.graded;
});
return [...groups.values()]
  .map((group) => ({
    "degree": group.degree.name,
    "level": group.degree.level,
    "subjects": group.subjects,
    "graded_enrollments": group.graded,
    "average_grade": group.graded ? Math.round((group.weighted / group.graded) * 100) / 100 : null,
    "official": group.degree.is_official ? "yes" : "no",
  }))
  .sort((a, b) => (b["average_grade"] || 0) - (a["average_grade"] || 0));
```

{{script:JavaScript · Degree scorecard}}

#### JavaScript · Student leaderboard

Query C03 ranked with competition ranking and enriched with grades and scholarships.

Keywords: `education`, `reports`, `students`, `enrollments`, `map`, `foreach`, `math`

```js
const load = queries["C03 · Load of every student (subjects and credits)"]();
const students = new Map(tables["students"].map((s) => [s.id_student, s]));
const grades = new Map();
tables["enrollments"].forEach((row) => {
  if (row.grade === null || row.grade === undefined) return;
  const group = grades.get(row.id_student) || { sum: 0, count: 0 };
  group.sum += row.grade;
  group.count += 1;
  grades.set(row.id_student, group);
});
let previous = null;
let rank = 0;
return load.map((row, index) => {
  if (row.credits !== previous) {
    rank = index + 1;
    previous = row.credits;
  }
  const grade = grades.get(row.id_student);
  const student = students.get(row.id_student);
  return {
    "rank": rank,
    "student": `${row.last_name}, ${row.name}`,
    "subjects": row.subjects,
    "credits": row.credits,
    "average_grade": grade ? Math.round((grade.sum / grade.count) * 100) / 100 : null,
    "scholarship": student && student.has_scholarship ? "yes" : "no",
  };
});
```

{{script:JavaScript · Student leaderboard}}

#### JavaScript · Teacher workload balance

Weekly hours against the project average, with the subjects each teacher gives.

Keywords: `education`, `reports`, `teachers`, `subjects`, `map`, `reduce`, `sort`, `foreach`

```js
const teachers = tables["teachers"];
const subjectsByTeacher = new Map();
tables["subjects"].forEach((subject) => {
  const list = subjectsByTeacher.get(subject.id_teacher) || [];
  list.push(subject.name);
  subjectsByTeacher.set(subject.id_teacher, list);
});
const average = teachers.length
  ? teachers.reduce((sum, teacher) => sum + teacher.weekly_hours, 0) / teachers.length
  : 0;
return teachers
  .map((teacher) => {
    const subjects = subjectsByTeacher.get(teacher.id_teacher) || [];
    const difference = Math.round((teacher.weekly_hours - average) * 10) / 10;
    return {
      "teacher": `${teacher.last_name}, ${teacher.name}`,
      "category": teacher.category,
      "subjects": subjects.length,
      "weekly_hours": teacher.weekly_hours,
      "average_hours": Math.round(average * 10) / 10,
      "difference": difference,
      "load": difference >= 6 ? "overloaded" : difference <= -6 ? "light" : "balanced",
      "subject_list": subjects.join(" · "),
    };
  })
  .sort((a, b) => b["difference"] - a["difference"] || a["teacher"].localeCompare(b["teacher"]));
```

{{script:JavaScript · Teacher workload balance}}

### I · Parameters

#### P10 · Campuses for the parameter

Script used as the source of a parameter: it returns the id and the name of every campus.

Keywords: `education`, `parameters`, `source`, `script`

```js
return tables["campuses"].map((campus) => ({
  id_campus: campus.id_campus,
  name: campus.name,
}));
```

{{script:P10 · Campuses for the parameter}}

#### P11 · Enrolments with a minimum grade (script with a parameter)

Parameterised script: the minimum grade is chosen in a drop-down from 5 to 10.

Keywords: `education`, `parameters`, `range`, `script`

```js
const minimumGrade = {{ 5..10 | Minimum grade }};
return tables["enrollments"]
  .filter((enrolment) => enrolment.grade !== null && enrolment.grade >= minimumGrade)
  .map((enrolment) => ({
    id_enrollment: enrolment.id_enrollment,
    grade: enrolment.grade,
    status: enrolment.status,
  }));
```

{{script:P11 · Enrolments with a minimum grade (script with a parameter)}}

#### P17 · Enrolments between two grades (script with two parameters)

Script parameterised twice: minimum and maximum grade, each with its own drop-down.

Keywords: `education`, `parameters`, `several`, `script`

```js
const minimumGrade = {{ 5..10 | Minimum grade }};
const maximumGrade = {{ 6..10 | Maximum grade }};
return tables["enrollments"]
  .filter((enrolment) => enrolment.grade !== null && enrolment.grade >= minimumGrade && enrolment.grade <= maximumGrade)
  .map((enrolment) => ({
    id_enrollment: enrolment.id_enrollment,
    grade: enrolment.grade,
    status: enrolment.status,
  }));
```

{{script:P17 · Enrolments between two grades (script with two parameters)}}
