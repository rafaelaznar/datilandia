# SQL tests · Health and hospitals (20 tables, 246 queries, 35 scripts)

Document generated from `tests-sql-health.json`.

**Contents:** 20 tables · 246 queries · 35 scripts.

> Load `tests-sql-health.json` with the **Data** button in markilandia so that the embeds in this document run each query and script and show their result.

## Queries

### A · Basics and aggregation

#### A01 · All specialties

SELECT * on specialties, ordered by identifier.

Keywords: `health`, `basics`, `specialties`, `select-star`

```sql
SELECT * FROM specialties ORDER BY id_specialty
```

{{query:A01 · All specialties}}

#### A02 · Hospitals: beds in hundreds

Projection with aliases and arithmetic: numeric division and order by beds.

Keywords: `health`, `basics`, `hospitals`

```sql
SELECT name AS hospital, city, num_beds / 100 AS beds_hundreds FROM hospitals ORDER BY num_beds DESC
```

{{query:A02 · Hospitals: beds in hundreds}}

#### A03 · Large hospitals (>= 800 beds)

Numeric filter with >= and a double order.

Keywords: `health`, `basics`, `hospitals`

```sql
SELECT name, num_beds, level FROM hospitals WHERE num_beds >= 800 ORDER BY num_beds DESC, name
```

{{query:A03 · Large hospitals (>= 800 beds)}}

#### A04 · University hospitals with a phone

Boolean filter combined with IS NOT NULL.

Keywords: `health`, `basics`, `hospitals`, `null`

```sql
SELECT id_hospital, name, city FROM hospitals WHERE is_university = true AND phone IS NOT NULL ORDER BY name
```

{{query:A04 · University hospitals with a phone}}

#### A05 · Patients without a phone

Missing values are asked with IS NULL.

Keywords: `health`, `basics`, `patients`, `null`

```sql
SELECT id_patient, name, last_name, city FROM patients WHERE phone IS NULL ORDER BY last_name, name
```

{{query:A05 · Patients without a phone}}

#### A06 · Doctor emails in upper case

UPPER on a field that may be missing: the rows with a null email are filtered out.

Keywords: `health`, `basics`, `doctors`, `null`

```sql
SELECT name, last_name, UPPER(email) AS email FROM doctors WHERE email IS NOT NULL ORDER BY last_name, name
```

{{query:A06 · Doctor emails in upper case}}

#### A07 · Last names that start with G

LIKE with a wildcard at the end.

Keywords: `health`, `basics`, `doctors`, `like`

```sql
SELECT name, last_name FROM doctors WHERE last_name LIKE 'G%' ORDER BY last_name, name
```

{{query:A07 · Last names that start with G}}

#### A08 · Drugs that do not start with A

LIKE combined with NOT.

Keywords: `health`, `basics`, `drugs`, `like`

```sql
SELECT name, active_ingredient, price FROM drugs WHERE name NOT LIKE 'A%' ORDER BY name
```

{{query:A08 · Drugs that do not start with A}}

#### A09 · Surgeries of high or medium risk

Membership of a list with IN.

Keywords: `health`, `basics`, `surgeries`, `in`

```sql
SELECT name, risk, cost FROM surgeries WHERE risk IN ('high', 'medium') ORDER BY cost DESC
```

{{query:A09 · Surgeries of high or medium risk}}

#### A10 · Specialties that are neither Central nor Surgical

NOT IN on the area.

Keywords: `health`, `basics`, `specialties`, `in`

```sql
SELECT name, area, residency_years FROM specialties WHERE area NOT IN ('Central', 'Surgical') ORDER BY name
```

{{query:A10 · Specialties that are neither Central nor Surgical}}

#### A11 · Lab tests with glucose between 100 and 200

Range with BETWEEN (endpoints included).

Keywords: `health`, `basics`, `labs`, `between`

```sql
SELECT id_lab, glucose, cholesterol FROM labs WHERE glucose BETWEEN 100 AND 200 ORDER BY glucose DESC LIMIT 15
```

{{query:A11 · Lab tests with glucose between 100 and 200}}

#### A12 · Emergency admissions with 2 to 5 expected days

BETWEEN combined with a boolean filter.

Keywords: `health`, `basics`, `admissions`, `between`

```sql
SELECT id_admission, reason, expected_days, cost_per_day FROM admissions WHERE is_emergency = true AND expected_days BETWEEN 2 AND 5 ORDER BY expected_days, cost_per_day DESC LIMIT 20
```

{{query:A12 · Emergency admissions with 2 to 5 expected days}}

#### A13 · Doctor categories (DISTINCT)

Distinct values of a column.

Keywords: `health`, `basics`, `doctors`, `distinct`

```sql
SELECT DISTINCT category FROM doctors ORDER BY category
```

{{query:A13 · Doctor categories (DISTINCT)}}

#### A14 · Appointment types and statuses (DISTINCT of two columns)

Distinct combinations of type and status.

Keywords: `health`, `basics`, `appointments`, `distinct`

```sql
SELECT DISTINCT type, status FROM appointments ORDER BY type, status
```

{{query:A14 · Appointment types and statuses (DISTINCT of two columns)}}

#### A15 · Billing totals

COUNT, SUM, MIN, MAX and a rounded AVG.

Keywords: `health`, `basics`, `invoices`, `aggregate`

```sql
SELECT COUNT(*) AS invoices, SUM(amount) AS total, MIN(amount) AS min, MAX(amount) AS max, ROUND(AVG(amount), 2) AS avg FROM invoices
```

{{query:A15 · Billing totals}}

#### A16 · Invoices by payment method

Grouping with COUNT, including the null group.

Keywords: `health`, `basics`, `invoices`, `group-by`, `aggregate`

```sql
SELECT COALESCE(payment_method, 'no method') AS method, COUNT(*) AS invoices, SUM(amount) AS total FROM invoices GROUP BY payment_method ORDER BY total DESC
```

{{query:A16 · Invoices by payment method}}

#### A17 · Appointments by status

GROUP BY and ORDER BY descending.

Keywords: `health`, `basics`, `appointments`, `group-by`, `aggregate`

```sql
SELECT status, COUNT(*) AS appointments FROM appointments GROUP BY status ORDER BY appointments DESC, status
```

{{query:A17 · Appointments by status}}

#### A18 · Appointment statuses with more than 10 appointments

Filtering groups with HAVING.

Keywords: `health`, `basics`, `appointments`, `group-by`, `having`, `aggregate`

```sql
SELECT status, COUNT(*) AS appointments FROM appointments GROUP BY status HAVING COUNT(*) > 10 ORDER BY appointments DESC
```

{{query:A18 · Appointment statuses with more than 10 appointments}}

#### A19 · Patients with three or more admissions

HAVING on a patient-admission join.

Keywords: `health`, `basics`, `patients`, `admissions`, `join`, `group-by`, `having`, `aggregate`

```sql
SELECT p.id_patient, p.name, p.last_name, COUNT(*) AS admissions FROM patients AS p INNER JOIN admissions AS i ON i.id_patient = p.id_patient GROUP BY p.id_patient, p.name, p.last_name HAVING COUNT(*) >= 3 ORDER BY admissions DESC, p.last_name
```

{{query:A19 · Patients with three or more admissions}}

#### A20 · Patients by the initial of the last name

GROUP BY with a text function.

Keywords: `health`, `basics`, `patients`, `group-by`, `aggregate`

```sql
SELECT SUBSTR(last_name, 1, 1) AS initial, COUNT(*) AS patients FROM patients GROUP BY SUBSTR(last_name, 1, 1) ORDER BY initial
```

{{query:A20 · Patients by the initial of the last name}}

#### A21 · Glucose of one patient, from highest to lowest

ORDER BY descending with an explicit NULLS LAST.

Keywords: `health`, `basics`, `labs`

```sql
SELECT id_lab, date, glucose FROM labs WHERE id_patient = 3 ORDER BY glucose DESC NULLS LAST, date
```

{{query:A21 · Glucose of one patient, from highest to lowest}}

#### A22 · Glucose of one patient, from lowest to highest

With ASC the nulls come first by default.

Keywords: `health`, `basics`, `labs`

```sql
SELECT id_lab, glucose FROM labs WHERE id_patient = 7 ORDER BY glucose, id_lab
```

{{query:A22 · Glucose of one patient, from lowest to highest}}

#### A23 · Oldest patients, page 2

LIMIT/OFFSET on an order by date of birth.

Keywords: `health`, `basics`, `patients`

```sql
SELECT name, last_name, birth_date FROM patients ORDER BY birth_date LIMIT 6 OFFSET 6
```

{{query:A23 · Oldest patients, page 2}}

#### A24 · Four patients from the eighth on

MySQL style LIMIT skip, n.

Keywords: `health`, `basics`, `patients`

```sql
SELECT id_patient, name FROM patients ORDER BY id_patient LIMIT 7, 4
```

{{query:A24 · Four patients from the eighth on}}

#### A25 · Record of three patients

Concatenation with || and text functions.

Keywords: `health`, `basics`, `patients`, `in`

```sql
SELECT UPPER(last_name) || ', ' || name AS record, city, blood_group FROM patients WHERE id_patient IN (1, 2, 3) ORDER BY id_patient
```

{{query:A25 · Record of three patients}}

#### A26 · Missing values: COALESCE and ROUND

COALESCE with a default value, plus NULLIF and ROUND.

Keywords: `health`, `basics`, `labs`, `null`

```sql
SELECT id_lab, COALESCE(glucose, -1) AS glucose, IFNULL(cholesterol, 0) AS cholesterol FROM labs WHERE glucose IS NULL OR cholesterol IS NULL ORDER BY id_lab
```

{{query:A26 · Missing values: COALESCE and ROUND}}

#### A27 · Normalised insurer phone

IFNULL + TRIM + NULLIF to leave the empty phones as null.

Keywords: `health`, `basics`, `insurers`

```sql
SELECT name, NULLIF(TRIM(IFNULL(phone, '')), '') AS phone FROM insurers ORDER BY id_insurer
```

{{query:A27 · Normalised insurer phone}}

#### A28 · Oral drugs against the rest

The <> operator and NOT.

Keywords: `health`, `basics`, `drugs`

```sql
SELECT name, route FROM drugs WHERE NOT route = 'oral' ORDER BY route, name
```

{{query:A28 · Oral drugs against the rest}}

#### A29 · No appointment lasts more than two hours

A query without results: rowCount 0.

Keywords: `health`, `basics`, `appointments`

```sql
SELECT id_appointment, duration_min FROM appointments WHERE duration_min > 120
```

{{query:A29 · No appointment lasts more than two hours}}

#### A30 · Average price by presentation

HAVING and order by an alias.

Keywords: `health`, `basics`, `drugs`, `group-by`, `having`, `aggregate`

```sql
SELECT presentation, COUNT(*) AS n, ROUND(AVG(price), 2) AS avg_price FROM drugs GROUP BY presentation HAVING COUNT(*) >= 2 ORDER BY avg_price DESC
```

{{query:A30 · Average price by presentation}}

#### A31 · Patients and emails by city

Implicit join with commas: patients and their insurer.

Keywords: `health`, `basics`, `patients`

```sql
SELECT p.name AS patient, s.name AS insurance FROM patients AS p, insurers AS s WHERE p.id_insurer = s.id_insurer AND p.city = 'Madrid' ORDER BY p.last_name
```

{{query:A31 · Patients and emails by city}}

#### A32 · Patients by blood group

GROUP BY counting the unknown group as well.

Keywords: `health`, `basics`, `patients`, `group-by`, `aggregate`

```sql
SELECT COALESCE(blood_group, 'unknown') AS blood_group, COUNT(*) AS patients FROM patients GROUP BY blood_group ORDER BY patients DESC
```

{{query:A32 · Patients by blood group}}

#### A33 · Nurses by shift

Simple grouping with an implicit DISTINCT.

Keywords: `health`, `basics`, `nurses`, `group-by`, `aggregate`

```sql
SELECT shift, COUNT(*) AS nurses, ROUND(AVG(years_experience), 1) AS avg_experience FROM nurses GROUP BY shift ORDER BY nurses DESC
```

{{query:A33 · Nurses by shift}}

#### A34 · Rooms with a bathroom and more than one bed

Two boolean filters and a numeric one.

Keywords: `health`, `basics`, `rooms`

```sql
SELECT code, type, num_beds FROM rooms WHERE has_bathroom = true AND num_beds > 1 ORDER BY num_beds DESC, code
```

{{query:A34 · Rooms with a bathroom and more than one bed}}

#### A35 · Chronic diagnoses, moderate or severe

Filter combined with AND.

Keywords: `health`, `basics`, `diagnoses`

```sql
SELECT code, name, severity FROM diagnoses WHERE is_chronic = true AND severity <> 'mild' ORDER BY name
```

{{query:A35 · Chronic diagnoses, moderate or severe}}

#### A36 · Successful surgeries by operating room

GROUP BY with SUM and AVG.

Keywords: `health`, `basics`, `surgeries`, `group-by`, `aggregate`

```sql
SELECT operating_room, COUNT(*) AS surgeries, SUM(cost) AS total_cost, ROUND(AVG(duration_min), 0) AS avg_duration FROM surgeries WHERE successful = true GROUP BY operating_room ORDER BY total_cost DESC
```

{{query:A36 · Successful surgeries by operating room}}

#### A37 · Drugs with low stock

Numeric threshold and ascending order.

Keywords: `health`, `basics`, `drugs`

```sql
SELECT name, stock, price FROM drugs WHERE stock <= 150 ORDER BY stock
```

{{query:A37 · Drugs with low stock}}

#### A38 · Veteran doctors who do not do research

Two conditions with NOT.

Keywords: `health`, `basics`, `doctors`

```sql
SELECT name, last_name, years_experience, category FROM doctors WHERE years_experience > 20 AND NOT is_researcher ORDER BY years_experience DESC
```

{{query:A38 · Veteran doctors who do not do research}}

#### A39 · Insurers with high coverage

Numeric and boolean filter.

Keywords: `health`, `basics`, `insurers`

```sql
SELECT name, type, coverage_percent FROM insurers WHERE coverage_percent >= 80 ORDER BY coverage_percent DESC
```

{{query:A39 · Insurers with high coverage}}

#### A40 · Severe allergies

Text and date filter.

Keywords: `health`, `basics`, `allergies`

```sql
SELECT id_allergy, substance, severity, detected_date FROM allergies WHERE severity = 'severe' ORDER BY detected_date DESC
```

{{query:A40 · Severe allergies}}

#### A41 · Longest diagnosis names

ORDER BY of a text function.

Keywords: `health`, `basics`, `diagnoses`

```sql
SELECT code, name, LENGTH(name) AS letters FROM diagnoses ORDER BY letters DESC, code LIMIT 8
```

{{query:A41 · Longest diagnosis names}}

#### A42 · ICD code by prefix

SUBSTR to extract the first letter.

Keywords: `health`, `basics`, `diagnoses`, `distinct`

```sql
SELECT DISTINCT SUBSTR(code, 1, 1) AS letter FROM diagnoses ORDER BY letter
```

{{query:A42 · ICD code by prefix}}

#### A43 · Patients with specific blood groups

IN on text.

Keywords: `health`, `basics`, `patients`, `in`

```sql
SELECT name, last_name, blood_group FROM patients WHERE blood_group IN ('O-', 'AB+', 'A-') ORDER BY blood_group, last_name
```

{{query:A43 · Patients with specific blood groups}}

#### A44 · Appointments in the first half of 2025

BETWEEN on dates stored as text.

Keywords: `health`, `basics`, `appointments`, `between`

```sql
SELECT id_appointment, date, time, type FROM appointments WHERE date BETWEEN '2025-01-01' AND '2025-06-30' ORDER BY date, time LIMIT 20
```

{{query:A44 · Appointments in the first half of 2025}}

#### A45 · Patients without a blood group

IS NULL and ORDER BY.

Keywords: `health`, `basics`, `patients`, `null`

```sql
SELECT id_patient, name, last_name FROM patients WHERE blood_group IS NULL ORDER BY last_name
```

{{query:A45 · Patients without a blood group}}

#### A46 · Patient records with CONCAT

Joins name and surname with CONCAT, and the blood group with the city: the first 7 patients by identifier. The result is null when there is no blood group.

Keywords: `health`, `basics`, `patients`

```sql
SELECT CONCAT(name, ' ', last_name) AS patient, CONCAT(blood_group, ' · ', city) AS blood_city FROM patients ORDER BY id_patient LIMIT 7
```

{{query:A46 · Patient records with CONCAT}}

#### A47 · Global admission totals with every aggregate (no GROUP BY)

Aggregates without GROUP BY over the 50 admissions: 28 patients and 9 different reasons; total 30404, minimum 192, maximum 950 and rounded average 608.08.

Keywords: `health`, `basics`, `admissions`, `aggregate`, `distinct`

```sql
SELECT COUNT(*) AS admissions, COUNT(cost_per_day) AS with_cost, COUNT(DISTINCT id_patient) AS patients, COUNT(DISTINCT reason) AS reasons, SUM(cost_per_day) AS total_cost, MIN(cost_per_day) AS minimum, MAX(cost_per_day) AS maximum, ROUND(AVG(cost_per_day), 2) AS average FROM admissions
```

{{query:A47 · Global admission totals with every aggregate (no GROUP BY)}}

#### A48 · Invoices by payment method with COUNT of a column and HAVING (column with NULL)

GROUP BY on a column with nulls: COALESCE labels the group without a method and COUNT(payment_method) leaves it at zero; HAVING keeps the groups adding up to 65000 or more.

Keywords: `health`, `basics`, `invoices`, `group-by`, `having`, `aggregate`, `distinct`

```sql
SELECT COALESCE(payment_method, 'no method') AS method, COUNT(*) AS invoices, COUNT(payment_method) AS with_method, COUNT(DISTINCT id_patient) AS patients, SUM(amount) AS total, ROUND(AVG(amount), 2) AS average FROM invoices GROUP BY payment_method HAVING SUM(amount) >= 65000 ORDER BY total DESC
```

{{query:A48 · Invoices by payment method with COUNT of a column and HAVING (column with NULL)}}

#### A49 · Appointments by doctor and status (GROUP BY of two columns with HAVING)

Two grouping columns with COUNT(*) and MIN and MAX of the duration: only three doctor-status combinations reach 4 appointments.

Keywords: `health`, `basics`, `appointments`, `group-by`, `having`, `aggregate`

```sql
SELECT id_doctor, status, COUNT(*) AS appointments, MIN(duration_min) AS minimum, MAX(duration_min) AS maximum FROM appointments GROUP BY id_doctor, status HAVING COUNT(*) >= 4 ORDER BY appointments DESC, id_doctor, status
```

{{query:A49 · Appointments by doctor and status (GROUP BY of two columns with HAVING)}}

#### A50 · Patients by blood group (COUNT(*) versus COUNT of a column)

The unknown group shows up in COUNT(*) but at zero in COUNT(blood_group); COUNT(DISTINCT city) counts different cities per group and the HAVING requires 3 patients.

Keywords: `health`, `basics`, `patients`, `group-by`, `having`, `aggregate`, `distinct`

```sql
SELECT COALESCE(blood_group, 'unknown') AS blood_group, COUNT(*) AS patients, COUNT(blood_group) AS with_group, COUNT(DISTINCT city) AS cities FROM patients GROUP BY blood_group HAVING COUNT(*) >= 3 ORDER BY patients DESC, blood_group
```

{{query:A50 · Patients by blood group (COUNT(*) versus COUNT of a column)}}

#### A51 · Admissions per doctor with every numeric aggregate (double HAVING)

COUNT(*), SUM, MIN, MAX and AVG of the cost per doctor; HAVING combines COUNT(*) and AVG: three admissions at least and an average of 600 or more.

Keywords: `health`, `basics`, `admissions`, `group-by`, `having`, `aggregate`

```sql
SELECT id_doctor, COUNT(*) AS admissions, SUM(cost_per_day) AS total_cost, MIN(cost_per_day) AS minimum, MAX(cost_per_day) AS maximum, ROUND(AVG(cost_per_day), 2) AS average FROM admissions GROUP BY id_doctor HAVING COUNT(*) >= 3 AND AVG(cost_per_day) >= 600 ORDER BY total_cost DESC, id_doctor
```

{{query:A51 · Admissions per doctor with every numeric aggregate (double HAVING)}}

#### A52 · Rooms by hospital and occupancy (GROUP BY of two columns with SUM)

Grouping by hospital and occupancy with COUNT(*) and SUM(num_beds); HAVING keeps only the groups of 4 rooms or more.

Keywords: `health`, `basics`, `rooms`, `group-by`, `having`, `aggregate`

```sql
SELECT id_hospital, occupied, COUNT(*) AS rooms, SUM(num_beds) AS beds FROM rooms GROUP BY id_hospital, occupied HAVING COUNT(*) >= 4 ORDER BY id_hospital, occupied
```

{{query:A52 · Rooms by hospital and occupancy (GROUP BY of two columns with SUM)}}

#### A53 · Surgeries by risk with every function (HAVING SUM)

COUNT(*), COUNT(DISTINCT operating_room), SUM, MIN, MAX and AVG by risk; the HAVING on the sum leaves out the high risk.

Keywords: `health`, `basics`, `surgeries`, `group-by`, `having`, `aggregate`, `distinct`

```sql
SELECT risk, COUNT(*) AS surgeries, COUNT(DISTINCT operating_room) AS operating_rooms, SUM(cost) AS total_cost, MIN(duration_min) AS min_duration, MAX(duration_min) AS max_duration, ROUND(AVG(cost), 0) AS average_cost FROM surgeries GROUP BY risk HAVING SUM(cost) > 65000 ORDER BY total_cost DESC
```

{{query:A53 · Surgeries by risk with every function (HAVING SUM)}}

#### A54 · Labs by type with COUNT(DISTINCT) and AVG (HAVING COUNT DISTINCT)

Per lab type: COUNT(*) of rows, COUNT(DISTINCT id_patient) of patients and two rounded AVGs; HAVING requires 15 different patients.

Keywords: `health`, `basics`, `labs`, `group-by`, `having`, `aggregate`, `distinct`

```sql
SELECT type, COUNT(*) AS labs, COUNT(DISTINCT id_patient) AS patients, ROUND(AVG(glucose), 1) AS average_glucose, ROUND(AVG(cholesterol), 1) AS average_cholesterol FROM labs GROUP BY type HAVING COUNT(DISTINCT id_patient) >= 15 ORDER BY labs DESC
```

{{query:A54 · Labs by type with COUNT(DISTINCT) and AVG (HAVING COUNT DISTINCT)}}

#### A55 · Prescriptions per drug with SUM, MIN, MAX and AVG (HAVING COUNT)

Per drug, COUNT(*) with SUM of the amounts and MIN, MAX and AVG of the duration; HAVING requires 8 prescriptions or more.

Keywords: `health`, `basics`, `prescriptions`, `group-by`, `having`, `aggregate`

```sql
SELECT id_drug, COUNT(*) AS prescriptions, SUM(amount) AS units, MIN(duration_days) AS min_days, MAX(duration_days) AS max_days, ROUND(AVG(duration_days), 1) AS average_days FROM prescriptions GROUP BY id_drug HAVING COUNT(*) >= 8 ORDER BY units DESC, id_drug
```

{{query:A55 · Prescriptions per drug with SUM, MIN, MAX and AVG (HAVING COUNT)}}

#### A56 · Treatments per year with SUM and AVG (GROUP BY with an expression and HAVING)

GROUP BY on the year of the start date: COUNT(*), COUNT(DISTINCT id_patient), SUM and rounded AVG of the cost; HAVING keeps the years with 10 treatments or more.

Keywords: `health`, `basics`, `treatments`, `group-by`, `having`, `aggregate`, `distinct`

```sql
SELECT SUBSTR(start_date, 1, 4) AS year, COUNT(*) AS treatments, COUNT(DISTINCT id_patient) AS patients, SUM(cost) AS total_cost, ROUND(AVG(cost), 0) AS average_cost FROM treatments GROUP BY SUBSTR(start_date, 1, 4) HAVING COUNT(*) >= 10 ORDER BY year
```

{{query:A56 · Treatments per year with SUM and AVG (GROUP BY with an expression and HAVING)}}

#### A57 · Aggregates over the grouped column itself (COUNT of status)

COUNT(status) matches COUNT(*) and COUNT(DISTINCT status) is 1 in every group; MAX(id_appointment) and COUNT(duration_min) summarise the other columns.

Keywords: `health`, `basics`, `appointments`, `group-by`, `aggregate`, `distinct`

```sql
SELECT status, COUNT(*) AS total, COUNT(status) AS with_status, COUNT(DISTINCT status) AS statuses, MAX(id_appointment) AS last_id, COUNT(duration_min) AS with_duration FROM appointments GROUP BY status ORDER BY total DESC, status
```

{{query:A57 · Aggregates over the grouped column itself (COUNT of status)}}

#### A58 · HAVING with aggregates that are not in the SELECT

HAVING with COUNT(*) and MIN(duration_min) outside the SELECT: only the pending appointments have more than 10 appointments and go below 20 minutes.

Keywords: `health`, `basics`, `appointments`, `group-by`, `having`, `aggregate`

```sql
SELECT status FROM appointments GROUP BY status HAVING COUNT(*) >= 10 AND MIN(duration_min) < 20 ORDER BY status
```

{{query:A58 · HAVING with aggregates that are not in the SELECT}}

#### A59 · Emergency admissions by reason (WHERE, GROUP BY and HAVING)

WHERE keeps only the emergencies and then groups by reason: COUNT(*) of admissions and COUNT(cost_per_day) of those with a cost, with HAVING of 4 or more.

Keywords: `health`, `basics`, `admissions`, `group-by`, `having`, `aggregate`

```sql
SELECT reason, COUNT(*) AS admissions, COUNT(cost_per_day) AS with_cost FROM admissions WHERE is_emergency = true GROUP BY reason HAVING COUNT(*) >= 4 ORDER BY admissions DESC, reason
```

{{query:A59 · Emergency admissions by reason (WHERE, GROUP BY and HAVING)}}

#### A60 · REGEXPR_REPLACE: spaces, groups and global replacement

Normalizes spaces, reorders a comma-separated name with a group and rewrites every code with a global regular expression.

Keywords: `health`, `basics`, `regexpr-replace`, `groups`, `global`

```sql
SELECT TRIM(REGEXPR_REPLACE('  Cardiology   Medical  ', '[ ]+', ' ')) AS normalized,
       REGEXPR_REPLACE('Medical, Cardiology', '([^,]+), (.+)', '$2 $1') AS reordered,
       REGEXPR_REPLACE('A-01 / B-02', '[A-Z]-([0-9]+)', 'code-$1') AS codes
FROM specialties
WHERE id_specialty = 1
```

{{query:A60 · REGEXPR_REPLACE: spaces, groups and global replacement}}

#### A61 · REGEXPR_INSTR: first match position and flags

The three examples: 123 starts at 4, no digit gives 0 and the ^ anchor matches at 1. It also checks the i flag, the alias REGEXP_INSTR and a NULL text.

Keywords: `health`, `regex`, `functions`, `position`

```sql
SELECT REGEXPR_INSTR('abc123def', '[0-9]+') AS digits, REGEXPR_INSTR('hola', '[0-9]+') AS no_match, REGEXPR_INSTR('404 Not Found', '^[0-9]+') AS at_start, REGEXPR_INSTR('xxABCyy', 'abc', 'i') AS with_flag, REGEXPR_INSTR('xxABCyy', 'abc') AS without_flag, REGEXP_INSTR('abc123def', '[0-9]+') AS alias, REGEXPR_INSTR(NULL, '[0-9]+') AS null_text FROM hospitals LIMIT 1
```

{{query:A61 · REGEXPR_INSTR: first match position and flags}}

#### A62 · REGEXPR_INSTR over last names

Finds the first space of each last name: Rubio González at 6, Navarro Molina at 8 and Díaz Domínguez at 5.

Keywords: `health`, `regex`, `functions`, `patients`

```sql
SELECT id_patient, last_name, REGEXPR_INSTR(last_name, ' ') AS first_space FROM patients WHERE id_patient <= 5 ORDER BY id_patient
```

{{query:A62 · REGEXPR_INSTR over last names}}

### B · Joins

#### B01 · Hospitals with their services

INNER JOIN 1:M between hospital and service.

Keywords: `health`, `joins`, `hospitals`, `services`, `join`

```sql
SELECT h.name AS hospital, s.name AS service, s.bed_capacity FROM hospitals AS h INNER JOIN services AS s ON s.id_hospital = h.id_hospital ORDER BY h.name, s.name
```

{{query:B01 · Hospitals with their services}}

#### B02 · Services with their hospital and specialty

Three tables chained.

Keywords: `health`, `joins`, `services`, `specialties`, `hospitals`, `join`

```sql
SELECT s.name AS service, e.name AS specialty, h.name AS hospital FROM services AS s INNER JOIN specialties AS e ON e.id_specialty = s.id_specialty INNER JOIN hospitals AS h ON h.id_hospital = s.id_hospital ORDER BY h.name, s.name
```

{{query:B02 · Services with their hospital and specialty}}

#### B03 · Hospitals without services

LEFT JOIN to find the orphans.

Keywords: `health`, `joins`, `hospitals`, `services`, `join`, `null`

```sql
SELECT h.name AS hospital FROM hospitals AS h LEFT JOIN services AS s ON s.id_hospital = h.id_hospital WHERE s.id_service IS NULL ORDER BY h.name
```

{{query:B03 · Hospitals without services}}

#### B04 · Specialties without doctors

LEFT JOIN and filter by null.

Keywords: `health`, `joins`, `specialties`, `doctors`, `join`, `null`

```sql
SELECT e.name AS specialty FROM specialties AS e LEFT JOIN doctors AS m ON m.id_specialty = e.id_specialty WHERE m.id_doctor IS NULL ORDER BY e.name
```

{{query:B04 · Specialties without doctors}}

#### B05 · Implicit join: doctors and services

Relationship with commas in the FROM.

Keywords: `health`, `joins`, `doctors`

```sql
SELECT m.name, m.last_name, s.name AS service FROM doctors AS m, services AS s WHERE m.id_service = s.id_service AND s.id_hospital = 1 ORDER BY m.last_name
```

{{query:B05 · Implicit join: doctors and services}}

#### B06 · Doctors with their specialty

INNER JOIN by the specialty key.

Keywords: `health`, `joins`, `doctors`, `specialties`, `join`

```sql
SELECT m.last_name, m.name, e.name AS specialty, m.category FROM doctors AS m INNER JOIN specialties AS e ON e.id_specialty = m.id_specialty ORDER BY m.last_name LIMIT 15
```

{{query:B06 · Doctors with their specialty}}

#### B07 · Specialties with at least three doctors

LEFT JOIN + HAVING.

Keywords: `health`, `joins`, `specialties`, `doctors`, `join`, `group-by`, `having`, `aggregate`

```sql
SELECT e.name AS specialty, COUNT(m.id_doctor) AS doctors FROM specialties AS e LEFT JOIN doctors AS m ON m.id_specialty = e.id_specialty GROUP BY e.name HAVING COUNT(m.id_doctor) >= 3 ORDER BY doctors DESC
```

{{query:B07 · Specialties with at least three doctors}}

#### B08 · Patients by insurer

Grouping of a join with the coverage.

Keywords: `health`, `joins`, `insurers`, `patients`, `join`, `group-by`, `aggregate`

```sql
SELECT s.name AS insurance, COUNT(p.id_patient) AS patients, s.coverage_percent FROM insurers AS s LEFT JOIN patients AS p ON p.id_insurer = s.id_insurer GROUP BY s.name, s.coverage_percent ORDER BY patients DESC
```

{{query:B08 · Patients by insurer}}

#### B09 · Rooms by hospital

LEFT JOIN counting rooms, even when they are 0.

Keywords: `health`, `joins`, `hospitals`, `rooms`, `join`, `group-by`, `aggregate`

```sql
SELECT h.name AS hospital, COUNT(rm.id_room) AS rooms, SUM(rm.num_beds) AS beds FROM hospitals AS h LEFT JOIN rooms AS rm ON rm.id_hospital = h.id_hospital GROUP BY h.name ORDER BY rooms DESC
```

{{query:B09 · Rooms by hospital}}

#### B10 · Admissions with patient, room and doctor

Four tables in the same join.

Keywords: `health`, `joins`, `admissions`, `patients`, `rooms`, `join`

```sql
SELECT i.id_admission, p.last_name AS patient, rm.code AS room, m.last_name AS doctor, i.reason FROM admissions AS i INNER JOIN patients AS p ON p.id_patient = i.id_patient LEFT JOIN rooms AS rm ON rm.id_room = i.id_room INNER JOIN doctors AS m ON m.id_doctor = i.id_doctor ORDER BY i.id_admission LIMIT 25
```

{{query:B10 · Admissions with patient, room and doctor}}

#### B11 · Appointments with patient and doctor

Join of the schedule.

Keywords: `health`, `joins`, `appointments`, `patients`, `doctors`, `join`

```sql
SELECT c.id_appointment, c.date, p.last_name AS patient, m.last_name AS doctor, c.status FROM appointments AS c INNER JOIN patients AS p ON p.id_patient = c.id_patient INNER JOIN doctors AS m ON m.id_doctor = c.id_doctor ORDER BY c.date DESC, c.time LIMIT 25
```

{{query:B11 · Appointments with patient and doctor}}

#### B12 · Visits with their doctor

Direct join with order by date.

Keywords: `health`, `joins`, `visits`, `doctors`, `join`

```sql
SELECT v.id_visit, v.date, v.reason, m.last_name AS doctor FROM visits AS v INNER JOIN doctors AS m ON m.id_doctor = v.id_doctor ORDER BY v.date, v.id_visit LIMIT 20
```

{{query:B12 · Visits with their doctor}}

#### B13 · Surgeries with patient and surgeon

Two joins and a filter by risk.

Keywords: `health`, `joins`, `surgeries`, `patients`, `doctors`, `join`

```sql
SELECT iv.name AS surgery, p.last_name AS patient, m.last_name AS surgeon, iv.risk FROM surgeries AS iv INNER JOIN patients AS p ON p.id_patient = iv.id_patient INNER JOIN doctors AS m ON m.id_doctor = iv.id_doctor WHERE iv.risk = 'high' ORDER BY iv.cost DESC
```

{{query:B13 · Surgeries with patient and surgeon}}

#### B14 · Cartesian product specialties × hospitals

Without a join condition: every combination.

Keywords: `health`, `joins`, `hospitals`

```sql
SELECT h.name AS hospital, e.name AS specialty FROM hospitals AS h, specialties AS e ORDER BY h.id_hospital, e.id_specialty
```

{{query:B14 · Cartesian product specialties × hospitals}}

#### B15 · The five most expensive drugs

Join with prescriptions and a limit.

Keywords: `health`, `joins`, `drugs`, `prescriptions`, `join`, `group-by`, `aggregate`

```sql
SELECT med.name, med.price, COUNT(*) AS prescriptions FROM drugs AS med INNER JOIN prescriptions AS pr ON pr.id_drug = med.id_drug GROUP BY med.name, med.price ORDER BY med.price DESC LIMIT 5
```

{{query:B15 · The five most expensive drugs}}

#### B16 · Specialties with distinct doctors

COUNT DISTINCT inside a join.

Keywords: `health`, `joins`, `specialties`, `doctors`, `services`, `join`, `group-by`, `aggregate`, `distinct`

```sql
SELECT e.name AS specialty, COUNT(DISTINCT m.id_doctor) AS doctors, COUNT(DISTINCT s.id_service) AS services FROM specialties AS e LEFT JOIN doctors AS m ON m.id_specialty = e.id_specialty LEFT JOIN services AS s ON s.id_specialty = e.id_specialty GROUP BY e.name ORDER BY doctors DESC
```

{{query:B16 · Specialties with distinct doctors}}

#### B17 · Experience by category

MIN and MAX per group.

Keywords: `health`, `joins`, `doctors`, `group-by`, `aggregate`

```sql
SELECT category, COUNT(*) AS doctors, MIN(years_experience) AS min_experience, MAX(years_experience) AS max_experience FROM doctors GROUP BY category ORDER BY max_experience DESC
```

{{query:B17 · Experience by category}}

#### B18 · Active treatments with patient and diagnosis

Join of three tables and a boolean filter.

Keywords: `health`, `joins`, `treatments`, `patients`, `diagnoses`, `join`

```sql
SELECT t.id_treatment, p.last_name AS patient, d.name AS diagnosis, t.cost FROM treatments AS t INNER JOIN patients AS p ON p.id_patient = t.id_patient INNER JOIN diagnoses AS d ON d.id_diagnosis = t.id_diagnosis WHERE t.active = true ORDER BY t.cost DESC LIMIT 20
```

{{query:B18 · Active treatments with patient and diagnosis}}

#### B19 · Abnormal lab tests with patient and doctor

Boolean filter on a join.

Keywords: `health`, `joins`, `labs`, `patients`, `doctors`, `join`

```sql
SELECT a.id_lab, p.last_name AS patient, m.last_name AS doctor, a.glucose, a.cholesterol FROM labs AS a INNER JOIN patients AS p ON p.id_patient = a.id_patient INNER JOIN doctors AS m ON m.id_doctor = a.id_doctor WHERE a.is_abnormal = true ORDER BY a.date DESC LIMIT 20
```

{{query:B19 · Abnormal lab tests with patient and doctor}}

#### B20 · Unpaid invoices with their patient

LEFT JOIN of the insurer and a boolean filter.

Keywords: `health`, `joins`, `invoices`, `patients`, `insurers`, `join`

```sql
SELECT f.id_invoice, p.last_name AS patient, f.amount, s.name AS insurance FROM invoices AS f INNER JOIN patients AS p ON p.id_patient = f.id_patient LEFT JOIN insurers AS s ON s.id_insurer = p.id_insurer WHERE f.paid = false ORDER BY f.amount DESC LIMIT 20
```

{{query:B20 · Unpaid invoices with their patient}}

#### B21 · Active admissions with their room

IS NULL for the ones still admitted.

Keywords: `health`, `joins`, `admissions`, `patients`, `rooms`, `join`, `null`

```sql
SELECT i.id_admission, p.last_name AS patient, rm.code, i.admission_date FROM admissions AS i INNER JOIN patients AS p ON p.id_patient = i.id_patient LEFT JOIN rooms AS rm ON rm.id_room = i.id_room WHERE i.discharge_date IS NULL ORDER BY i.admission_date
```

{{query:B21 · Active admissions with their room}}

#### B22 · Nurses and their service

Join with data of both.

Keywords: `health`, `joins`, `nurses`, `services`, `hospitals`, `join`

```sql
SELECT nur.last_name AS nurse, nur.shift, s.name AS service, h.name AS hospital FROM nurses AS nur INNER JOIN services AS s ON s.id_service = nur.id_service INNER JOIN hospitals AS h ON h.id_hospital = s.id_hospital ORDER BY nur.last_name LIMIT 20
```

{{query:B22 · Nurses and their service}}

#### B23 · Visits with their main diagnosis

Join visit → relationship → diagnosis.

Keywords: `health`, `joins`, `visits`, `visit_diagnoses`, `diagnoses`, `join`

```sql
SELECT v.id_visit, v.reason, d.name AS diagnosis, d.severity FROM visits AS v INNER JOIN visit_diagnoses AS vd ON vd.id_visit = v.id_visit INNER JOIN diagnoses AS d ON d.id_diagnosis = vd.id_diagnosis WHERE vd.is_main = true ORDER BY v.id_visit LIMIT 25
```

{{query:B23 · Visits with their main diagnosis}}

#### B24 · Prescriptions with drug and treatment

Resolves the M:N showing both ends.

Keywords: `health`, `joins`, `prescriptions`, `drugs`, `treatments`, `join`

```sql
SELECT pr.id_prescription, med.name AS drug, pr.dose, t.id_treatment, p.last_name AS patient FROM prescriptions AS pr INNER JOIN drugs AS med ON med.id_drug = pr.id_drug INNER JOIN treatments AS t ON t.id_treatment = pr.id_treatment INNER JOIN patients AS p ON p.id_patient = t.id_patient ORDER BY pr.id_prescription LIMIT 25
```

{{query:B24 · Prescriptions with drug and treatment}}

#### B25 · Treatments by diagnosis

GROUP BY of a join with HAVING.

Keywords: `health`, `joins`, `diagnoses`, `treatments`, `join`, `group-by`, `having`, `aggregate`

```sql
SELECT d.name AS diagnosis, COUNT(t.id_treatment) AS treatments, ROUND(AVG(t.cost), 0) AS avg_cost FROM diagnoses AS d INNER JOIN treatments AS t ON t.id_diagnosis = d.id_diagnosis GROUP BY d.name HAVING COUNT(t.id_treatment) >= 2 ORDER BY treatments DESC
```

{{query:B25 · Treatments by diagnosis}}

#### B26 · Cost of treatments by doctor

SUM and COUNT grouped.

Keywords: `health`, `joins`, `treatments`, `doctors`, `join`, `group-by`, `aggregate`

```sql
SELECT m.last_name AS doctor, COUNT(*) AS treatments, SUM(t.cost) AS total_cost FROM treatments AS t INNER JOIN doctors AS m ON m.id_doctor = t.id_doctor GROUP BY m.last_name ORDER BY total_cost DESC LIMIT 10
```

{{query:B26 · Cost of treatments by doctor}}

#### B27 · Surgeries by risk level

Grouping with the average cost.

Keywords: `health`, `joins`, `surgeries`, `group-by`, `aggregate`

```sql
SELECT risk, COUNT(*) AS surgeries, ROUND(AVG(cost), 0) AS avg_cost FROM surgeries GROUP BY risk ORDER BY avg_cost DESC
```

{{query:B27 · Surgeries by risk level}}

#### B28 · Patients with insurer and city

Join with COALESCE for the missing insurer.

Keywords: `health`, `joins`, `patients`, `insurers`, `join`

```sql
SELECT p.last_name AS patient, COALESCE(s.name, 'no insurer') AS insurance, p.city FROM patients AS p LEFT JOIN insurers AS s ON s.id_insurer = p.id_insurer ORDER BY p.city, p.last_name LIMIT 25
```

{{query:B28 · Patients with insurer and city}}

#### B29 · Allergies with the drug involved

LEFT JOIN because there are allergies without a drug.

Keywords: `health`, `joins`, `allergies`, `drugs`, `patients`, `join`

```sql
SELECT alg.substance, alg.severity, med.name AS drug, p.last_name AS patient FROM allergies AS alg LEFT JOIN drugs AS med ON med.id_drug = alg.id_drug INNER JOIN patients AS p ON p.id_patient = alg.id_patient ORDER BY alg.severity DESC, alg.substance LIMIT 25
```

{{query:B29 · Allergies with the drug involved}}

#### B30 · Beds by hospital (only those with rooms)

INNER JOIN and sum of beds.

Keywords: `health`, `joins`, `hospitals`, `rooms`, `join`, `group-by`, `aggregate`

```sql
SELECT h.name AS hospital, COUNT(rm.id_room) AS rooms, SUM(rm.num_beds) AS beds FROM hospitals AS h INNER JOIN rooms AS rm ON rm.id_hospital = h.id_hospital GROUP BY h.name ORDER BY beds DESC
```

{{query:B30 · Beds by hospital (only those with rooms)}}

#### B31 · Admissions by month

GROUP BY with SUBSTR on the date.

Keywords: `health`, `joins`, `admissions`, `group-by`, `aggregate`

```sql
SELECT SUBSTR(admission_date, 1, 7) AS month, COUNT(*) AS admissions FROM admissions GROUP BY SUBSTR(admission_date, 1, 7) ORDER BY month
```

{{query:B31 · Admissions by month}}

#### B32 · Billing by year

SUBSTR for the year and sum of amounts.

Keywords: `health`, `joins`, `invoices`, `group-by`, `aggregate`

```sql
SELECT SUBSTR(date, 1, 4) AS year, COUNT(*) AS invoices, SUM(amount) AS total FROM invoices GROUP BY SUBSTR(date, 1, 4) ORDER BY year
```

{{query:B32 · Billing by year}}

#### B33 · Doctors with more visits

Join and GROUP BY with LIMIT.

Keywords: `health`, `joins`, `doctors`, `visits`, `join`, `group-by`, `aggregate`

```sql
SELECT m.last_name AS doctor, COUNT(v.id_visit) AS visits FROM doctors AS m INNER JOIN visits AS v ON v.id_doctor = m.id_doctor GROUP BY m.last_name ORDER BY visits DESC LIMIT 10
```

{{query:B33 · Doctors with more visits}}

#### B34 · Cancelled appointments by doctor

Status filter on a join.

Keywords: `health`, `joins`, `appointments`, `doctors`, `join`, `group-by`, `aggregate`

```sql
SELECT m.last_name AS doctor, COUNT(*) AS cancelled FROM appointments AS c INNER JOIN doctors AS m ON m.id_doctor = c.id_doctor WHERE c.status = 'cancelled' GROUP BY m.last_name ORDER BY cancelled DESC LIMIT 10
```

{{query:B34 · Cancelled appointments by doctor}}

#### B35 · Diagnoses by specialty

COUNT on a join.

Keywords: `health`, `joins`, `specialties`, `diagnoses`, `join`, `group-by`, `aggregate`

```sql
SELECT e.name AS specialty, COUNT(d.id_diagnosis) AS diagnoses FROM specialties AS e LEFT JOIN diagnoses AS d ON d.id_specialty = e.id_specialty GROUP BY e.name ORDER BY diagnoses DESC
```

{{query:B35 · Diagnoses by specialty}}

#### B36 · Drugs by treatment

Counts the M:N with ORDER BY of an aggregate.

Keywords: `health`, `joins`, `treatments`, `prescriptions`, `patients`, `join`, `group-by`, `aggregate`

```sql
SELECT t.id_treatment, p.last_name AS patient, COUNT(pr.id_prescription) AS drugs FROM treatments AS t INNER JOIN prescriptions AS pr ON pr.id_treatment = t.id_treatment INNER JOIN patients AS p ON p.id_patient = t.id_patient GROUP BY t.id_treatment, p.last_name ORDER BY drugs DESC, t.id_treatment LIMIT 15
```

{{query:B36 · Drugs by treatment}}

#### B37 · Surgeries by operating room

Grouping with a count and extreme durations.

Keywords: `health`, `joins`, `surgeries`, `group-by`, `aggregate`

```sql
SELECT operating_room, COUNT(*) AS surgeries, MIN(duration_min) AS min_duration, MAX(duration_min) AS max_duration FROM surgeries GROUP BY operating_room ORDER BY surgeries DESC
```

{{query:B37 · Surgeries by operating room}}

#### B38 · Chronic patients with active treatments

Two filters and a join of three tables.

Keywords: `health`, `joins`, `patients`, `treatments`, `join`, `distinct`

```sql
SELECT DISTINCT p.last_name AS patient, p.city FROM patients AS p INNER JOIN treatments AS t ON t.id_patient = p.id_patient WHERE p.is_chronic = true AND t.active = true ORDER BY p.last_name
```

{{query:B38 · Chronic patients with active treatments}}

#### B39 · Specialties with more visits

Chain service → specialty → visits.

Keywords: `health`, `joins`, `visits`, `doctors`, `specialties`, `join`, `group-by`, `aggregate`

```sql
SELECT e.name AS specialty, COUNT(v.id_visit) AS visits FROM visits AS v INNER JOIN doctors AS m ON m.id_doctor = v.id_doctor INNER JOIN specialties AS e ON e.id_specialty = m.id_specialty GROUP BY e.name ORDER BY visits DESC
```

{{query:B39 · Specialties with more visits}}

#### B40 · Visits by appointment type

LEFT JOIN to include the walk-ins.

Keywords: `health`, `joins`, `visits`, `appointments`, `join`, `group-by`, `aggregate`

```sql
SELECT COALESCE(c.type, 'walk-in') AS type, COUNT(v.id_visit) AS visits FROM visits AS v LEFT JOIN appointments AS c ON c.id_appointment = v.id_appointment GROUP BY c.type ORDER BY visits DESC
```

{{query:B40 · Visits by appointment type}}

#### B41 · Occupied and free rooms by hospital

GROUP BY of two columns, the boolean state included.

Keywords: `health`, `joins`, `hospitals`, `rooms`, `join`, `group-by`, `aggregate`

```sql
SELECT h.name AS hospital, rm.occupied, COUNT(*) AS rooms FROM hospitals AS h INNER JOIN rooms AS rm ON rm.id_hospital = h.id_hospital GROUP BY h.name, rm.occupied ORDER BY h.name, rm.occupied
```

{{query:B41 · Occupied and free rooms by hospital}}

#### B42 · Emergency admissions by hospital

Join patient, admission and hospital.

Keywords: `health`, `joins`, `admissions`, `patients`, `rooms`, `join`, `group-by`, `aggregate`

```sql
SELECT h.name AS hospital, COUNT(*) AS urgent FROM admissions AS i INNER JOIN patients AS p ON p.id_patient = i.id_patient INNER JOIN rooms AS rm ON rm.id_room = i.id_room INNER JOIN hospitals AS h ON h.id_hospital = rm.id_hospital WHERE i.is_emergency = true GROUP BY h.name ORDER BY urgent DESC
```

{{query:B42 · Emergency admissions by hospital}}

#### B43 · Average coverage of invoices by insurer

Join and grouped average.

Keywords: `health`, `joins`, `invoices`, `patients`, `insurers`, `join`, `group-by`, `aggregate`

```sql
SELECT s.name AS insurance, COUNT(f.id_invoice) AS invoices, ROUND(AVG(f.amount), 0) AS avg_amount FROM invoices AS f INNER JOIN patients AS p ON p.id_patient = f.id_patient INNER JOIN insurers AS s ON s.id_insurer = p.id_insurer GROUP BY s.name ORDER BY avg_amount DESC
```

{{query:B43 · Average coverage of invoices by insurer}}

#### B44 · Lab tests by type and result

GROUP BY of two columns.

Keywords: `health`, `joins`, `labs`, `group-by`, `aggregate`

```sql
SELECT type, is_abnormal, COUNT(*) AS labs FROM labs GROUP BY type, is_abnormal ORDER BY type, is_abnormal
```

{{query:B44 · Lab tests by type and result}}

#### B45 · Severe diagnoses with treatments

Join and filter by severity.

Keywords: `health`, `joins`, `diagnoses`, `treatments`, `join`, `group-by`, `aggregate`

```sql
SELECT d.name AS diagnosis, d.severity, COUNT(t.id_treatment) AS treatments FROM diagnoses AS d INNER JOIN treatments AS t ON t.id_diagnosis = d.id_diagnosis WHERE d.severity = 'severe' GROUP BY d.name, d.severity ORDER BY treatments DESC
```

{{query:B45 · Severe diagnoses with treatments}}

### C · Related queries

#### C01 · History of one patient

Gathers admissions, appointments and visits of one patient with distinct counts.

Keywords: `health`, `related-queries`, `patients`, `admissions`, `appointments`, `join`, `group-by`, `aggregate`, `distinct`

```sql
SELECT p.last_name AS patient, COUNT(DISTINCT i.id_admission) AS admissions, COUNT(DISTINCT c.id_appointment) AS appointments, COUNT(DISTINCT v.id_visit) AS visits FROM patients AS p LEFT JOIN admissions AS i ON i.id_patient = p.id_patient LEFT JOIN appointments AS c ON c.id_patient = p.id_patient LEFT JOIN visits AS v ON v.id_patient = p.id_patient WHERE p.id_patient = 1 GROUP BY p.last_name
```

{{query:C01 · History of one patient}}

#### C02 · Visits with two or more diagnoses

GROUP BY on the M:N link with HAVING.

Keywords: `health`, `related-queries`, `visits`, `visit_diagnoses`, `join`, `group-by`, `having`, `aggregate`

```sql
SELECT v.id_visit, v.reason, COUNT(vd.id_diagnosis) AS diagnoses FROM visits AS v INNER JOIN visit_diagnoses AS vd ON vd.id_visit = v.id_visit GROUP BY v.id_visit, v.reason HAVING COUNT(vd.id_diagnosis) >= 2 ORDER BY diagnoses DESC, v.id_visit LIMIT 20
```

{{query:C02 · Visits with two or more diagnoses}}

#### C03 · Most frequent diagnoses

Counts appearances in the bridge table.

Keywords: `health`, `related-queries`, `visit_diagnoses`, `diagnoses`, `join`, `group-by`, `aggregate`

```sql
SELECT d.name AS diagnosis, COUNT(*) AS times FROM visit_diagnoses AS vd INNER JOIN diagnoses AS d ON d.id_diagnosis = vd.id_diagnosis GROUP BY d.name ORDER BY times DESC, diagnosis LIMIT 10
```

{{query:C03 · Most frequent diagnoses}}

#### C04 · Treatments with more than two drugs

HAVING on the M:N of prescriptions.

Keywords: `health`, `related-queries`, `treatments`, `prescriptions`, `join`, `group-by`, `having`, `aggregate`

```sql
SELECT t.id_treatment, COUNT(pr.id_drug) AS drugs FROM treatments AS t INNER JOIN prescriptions AS pr ON pr.id_treatment = t.id_treatment GROUP BY t.id_treatment HAVING COUNT(pr.id_drug) > 2 ORDER BY drugs DESC LIMIT 15
```

{{query:C04 · Treatments with more than two drugs}}

#### C05 · Most prescribed drugs

Ranking from the bridge table.

Keywords: `health`, `related-queries`, `prescriptions`, `drugs`, `join`, `group-by`, `aggregate`

```sql
SELECT med.name AS drug, COUNT(*) AS prescriptions FROM prescriptions AS pr INNER JOIN drugs AS med ON med.id_drug = pr.id_drug GROUP BY med.name ORDER BY prescriptions DESC, drug LIMIT 10
```

{{query:C05 · Most prescribed drugs}}

#### C06 · Patients with more invoices

JOIN and ORDER BY of an aggregate with LIMIT.

Keywords: `health`, `related-queries`, `patients`, `invoices`, `join`, `group-by`, `aggregate`

```sql
SELECT p.last_name AS patient, COUNT(f.id_invoice) AS invoices, SUM(f.amount) AS total FROM patients AS p INNER JOIN invoices AS f ON f.id_patient = p.id_patient GROUP BY p.last_name ORDER BY total DESC LIMIT 10
```

{{query:C06 · Patients with more invoices}}

#### C07 · Patients who never had an appointment

LEFT JOIN and check for null.

Keywords: `health`, `related-queries`, `patients`, `appointments`, `join`, `null`

```sql
SELECT p.last_name AS patient FROM patients AS p LEFT JOIN appointments AS c ON c.id_patient = p.id_patient WHERE c.id_appointment IS NULL ORDER BY p.last_name
```

{{query:C07 · Patients who never had an appointment}}

#### C08 · Doctors without any appointment

Those on the staff who never appear in the schedule.

Keywords: `health`, `related-queries`, `doctors`, `appointments`, `join`, `null`

```sql
SELECT m.last_name AS doctor FROM doctors AS m LEFT JOIN appointments AS c ON c.id_doctor = m.id_doctor WHERE c.id_appointment IS NULL ORDER BY m.last_name
```

{{query:C08 · Doctors without any appointment}}

#### C09 · Rooms without admissions

LEFT JOIN from the rooms.

Keywords: `health`, `related-queries`, `rooms`, `admissions`, `join`, `null`

```sql
SELECT rm.code, rm.type FROM rooms AS rm LEFT JOIN admissions AS i ON i.id_room = rm.id_room WHERE i.id_admission IS NULL ORDER BY rm.code
```

{{query:C09 · Rooms without admissions}}

#### C10 · Nobody has two insurers (rowCount)

Integrity check: the result must be empty.

Keywords: `health`, `related-queries`, `patients`, `group-by`, `having`, `aggregate`, `distinct`

```sql
SELECT id_patient, COUNT(DISTINCT id_insurer) AS insurers FROM patients WHERE id_insurer IS NOT NULL GROUP BY id_patient HAVING COUNT(DISTINCT id_insurer) >= 2
```

{{query:C10 · Nobody has two insurers (rowCount)}}

#### C11 · Patients without lab tests

LEFT JOIN on the laboratory.

Keywords: `health`, `related-queries`, `patients`, `labs`, `join`, `null`

```sql
SELECT p.last_name AS patient FROM patients AS p LEFT JOIN labs AS a ON a.id_patient = p.id_patient WHERE a.id_lab IS NULL ORDER BY p.last_name
```

{{query:C11 · Patients without lab tests}}

#### C12 · Visits of one patient regardless of the order

Assert with ignoreOrder: the set is compared.

Keywords: `health`, `related-queries`, `visits`

```sql
SELECT v.reason FROM visits AS v WHERE v.id_patient = 5
```

{{query:C12 · Visits of one patient regardless of the order}}

#### C13 · Specialty with more admissions

Chain admission → doctor → specialty.

Keywords: `health`, `related-queries`, `admissions`, `doctors`, `specialties`, `join`, `group-by`, `aggregate`

```sql
SELECT e.name AS specialty, COUNT(*) AS admissions FROM admissions AS i INNER JOIN doctors AS m ON m.id_doctor = i.id_doctor INNER JOIN specialties AS e ON e.id_specialty = m.id_specialty GROUP BY e.name ORDER BY admissions DESC LIMIT 5
```

{{query:C13 · Specialty with more admissions}}

#### C14 · Drugs with more allergies

Bridge table allergies-drug.

Keywords: `health`, `related-queries`, `allergies`, `drugs`, `join`, `group-by`, `aggregate`

```sql
SELECT med.name AS drug, COUNT(*) AS allergies FROM allergies AS alg INNER JOIN drugs AS med ON med.id_drug = alg.id_drug GROUP BY med.name ORDER BY allergies DESC, drug LIMIT 10
```

{{query:C14 · Drugs with more allergies}}

#### C15 · Diagnoses without treatments

LEFT JOIN from the catalogue.

Keywords: `health`, `related-queries`, `diagnoses`, `treatments`, `join`, `null`

```sql
SELECT d.name AS diagnosis FROM diagnoses AS d LEFT JOIN treatments AS t ON t.id_diagnosis = d.id_diagnosis WHERE t.id_treatment IS NULL ORDER BY d.name
```

{{query:C15 · Diagnoses without treatments}}

#### C16 · Doctors with more surgeries

Ranking of surgeons.

Keywords: `health`, `related-queries`, `surgeries`, `doctors`, `join`, `group-by`, `aggregate`

```sql
SELECT m.last_name AS doctor, COUNT(*) AS surgeries, SUM(iv.cost) AS total_cost FROM surgeries AS iv INNER JOIN doctors AS m ON m.id_doctor = iv.id_doctor GROUP BY m.last_name ORDER BY surgeries DESC, total_cost DESC LIMIT 10
```

{{query:C16 · Doctors with more surgeries}}

#### C17 · Treatments without prescriptions

LEFT JOIN on the M:N.

Keywords: `health`, `related-queries`, `treatments`, `prescriptions`, `join`, `null`

```sql
SELECT t.id_treatment FROM treatments AS t LEFT JOIN prescriptions AS pr ON pr.id_treatment = t.id_treatment WHERE pr.id_prescription IS NULL ORDER BY t.id_treatment
```

{{query:C17 · Treatments without prescriptions}}

#### C18 · Prescriptions of one treatment

Concrete filter on the bridge table.

Keywords: `health`, `related-queries`, `prescriptions`, `drugs`, `join`

```sql
SELECT med.name AS drug, pr.dose, pr.duration_days FROM prescriptions AS pr INNER JOIN drugs AS med ON med.id_drug = pr.id_drug WHERE pr.id_treatment = 4 ORDER BY med.name
```

{{query:C18 · Prescriptions of one treatment}}

#### C19 · Insurers with their patients

LEFT JOIN to include insurers without patients.

Keywords: `health`, `related-queries`, `insurers`, `patients`, `join`, `group-by`, `aggregate`

```sql
SELECT s.name AS insurance, COUNT(p.id_patient) AS patients FROM insurers AS s LEFT JOIN patients AS p ON p.id_insurer = s.id_insurer GROUP BY s.name ORDER BY patients DESC
```

{{query:C19 · Insurers with their patients}}

#### C20 · Nursing supervisors by service

Boolean filter and join.

Keywords: `health`, `related-queries`, `nurses`, `services`, `join`, `group-by`, `aggregate`

```sql
SELECT s.name AS service, COUNT(*) AS supervisors FROM nurses AS nur INNER JOIN services AS s ON s.id_service = nur.id_service WHERE nur.is_supervisor = true GROUP BY s.name ORDER BY supervisors DESC
```

{{query:C20 · Nursing supervisors by service}}

#### C21 · Patients with more than five visits

Count on the visits with HAVING.

Keywords: `health`, `related-queries`, `visits`, `patients`, `join`, `group-by`, `having`, `aggregate`

```sql
SELECT p.last_name AS patient, COUNT(*) AS visits FROM visits AS v INNER JOIN patients AS p ON p.id_patient = v.id_patient GROUP BY p.last_name HAVING COUNT(*) >= 5 ORDER BY visits DESC
```

{{query:C21 · Patients with more than five visits}}

#### C22 · Hospitals with more admissions

Chain hospital → room → admission.

Keywords: `health`, `related-queries`, `admissions`, `rooms`, `hospitals`, `join`, `group-by`, `aggregate`

```sql
SELECT h.name AS hospital, COUNT(*) AS admissions FROM admissions AS i INNER JOIN rooms AS rm ON rm.id_room = i.id_room INNER JOIN hospitals AS h ON h.id_hospital = rm.id_hospital GROUP BY h.name ORDER BY admissions DESC
```

{{query:C22 · Hospitals with more admissions}}

#### C23 · Specialties with more doctors

Ranking of staffing.

Keywords: `health`, `related-queries`, `doctors`, `specialties`, `join`, `group-by`, `aggregate`

```sql
SELECT e.name AS specialty, COUNT(*) AS doctors FROM doctors AS m INNER JOIN specialties AS e ON e.id_specialty = m.id_specialty GROUP BY e.name ORDER BY doctors DESC, specialty
```

{{query:C23 · Specialties with more doctors}}

#### C24 · Invoices that go over 75% of the coverage

Compares the amount with a calculation on the insurer.

Keywords: `health`, `related-queries`, `invoices`, `patients`, `insurers`, `join`

```sql
SELECT f.id_invoice, f.amount, s.coverage_percent FROM invoices AS f INNER JOIN patients AS p ON p.id_patient = f.id_patient INNER JOIN insurers AS s ON s.id_insurer = p.id_insurer WHERE f.amount * s.coverage_percent / 100 > 3000 ORDER BY f.amount DESC LIMIT 20
```

{{query:C24 · Invoices that go over 75% of the coverage}}

#### C25 · Appointments of one specific day

Exact date filter with a join (a date that exists in the data).

Keywords: `health`, `related-queries`, `appointments`, `patients`, `doctors`, `join`

```sql
SELECT c.time, p.last_name AS patient, m.last_name AS doctor, c.status FROM appointments AS c INNER JOIN patients AS p ON p.id_patient = c.id_patient INNER JOIN doctors AS m ON m.id_doctor = c.id_doctor WHERE c.date = '2024-02-16' ORDER BY c.time
```

{{query:C25 · Appointments of one specific day}}

#### C26 · Surgeries without a linked admission

IS NULL filter on the foreign key.

Keywords: `health`, `related-queries`, `surgeries`, `null`

```sql
SELECT iv.name, iv.date, iv.cost FROM surgeries AS iv WHERE iv.id_admission IS NULL ORDER BY iv.date DESC
```

{{query:C26 · Surgeries without a linked admission}}

#### C27 · Patients allergic to a drug they take

Crosses allergies and prescriptions of the same patient.

Keywords: `health`, `related-queries`, `allergies`, `patients`, `drugs`, `join`, `distinct`

```sql
SELECT DISTINCT p.last_name AS patient, med.name AS drug FROM allergies AS alg INNER JOIN patients AS p ON p.id_patient = alg.id_patient INNER JOIN drugs AS med ON med.id_drug = alg.id_drug INNER JOIN treatments AS t ON t.id_patient = p.id_patient INNER JOIN prescriptions AS pr ON pr.id_treatment = t.id_treatment AND pr.id_drug = med.id_drug ORDER BY p.last_name, med.name
```

{{query:C27 · Patients allergic to a drug they take}}

#### C28 · Admissions by the specialty of the doctor

Groups by the specialty of the doctor in charge.

Keywords: `health`, `related-queries`, `admissions`, `doctors`, `specialties`, `join`, `group-by`, `aggregate`

```sql
SELECT e.name AS specialty, COUNT(*) AS admissions, ROUND(AVG(i.cost_per_day), 0) AS avg_cost FROM admissions AS i INNER JOIN doctors AS m ON m.id_doctor = i.id_doctor INNER JOIN specialties AS e ON e.id_specialty = m.id_specialty GROUP BY e.name ORDER BY admissions DESC
```

{{query:C28 · Admissions by the specialty of the doctor}}

#### C29 · Billing by insurer type

SUM and COUNT grouped by the type.

Keywords: `health`, `related-queries`, `invoices`, `patients`, `insurers`, `join`, `group-by`, `aggregate`

```sql
SELECT s.type, COUNT(f.id_invoice) AS invoices, SUM(f.amount) AS total FROM invoices AS f INNER JOIN patients AS p ON p.id_patient = f.id_patient INNER JOIN insurers AS s ON s.id_insurer = p.id_insurer GROUP BY s.type ORDER BY total DESC
```

{{query:C29 · Billing by insurer type}}

#### C30 · Most frequent main diagnosis by severity

Filter of main and GROUP BY of severity.

Keywords: `health`, `related-queries`, `visit_diagnoses`, `diagnoses`, `join`, `group-by`, `aggregate`

```sql
SELECT d.severity, d.name AS diagnosis, COUNT(*) AS times FROM visit_diagnoses AS vd INNER JOIN diagnoses AS d ON d.id_diagnosis = vd.id_diagnosis WHERE vd.is_main = true GROUP BY d.severity, d.name ORDER BY times DESC LIMIT 10
```

{{query:C30 · Most frequent main diagnosis by severity}}

#### C31 · Patients with more than one distinct diagnosis

COUNT DISTINCT on the M:N.

Keywords: `health`, `related-queries`, `patients`, `visits`, `visit_diagnoses`, `join`, `group-by`, `having`, `aggregate`

```sql
SELECT p.last_name AS patient, COUNT(DISTINCT d.id_diagnosis) AS diagnoses FROM patients AS p INNER JOIN visits AS v ON v.id_patient = p.id_patient INNER JOIN visit_diagnoses AS vd ON vd.id_visit = v.id_visit INNER JOIN diagnoses AS d ON d.id_diagnosis = vd.id_diagnosis GROUP BY p.last_name HAVING COUNT(DISTINCT d.id_diagnosis) >= 4 ORDER BY diagnoses DESC LIMIT 15
```

{{query:C31 · Patients with more than one distinct diagnosis}}

#### C32 · Hospitals with abnormal lab tests

Chain hospital → service → doctor → lab test.

Keywords: `health`, `related-queries`, `labs`, `doctors`, `services`, `join`, `group-by`, `aggregate`

```sql
SELECT h.name AS hospital, COUNT(*) AS abnormal FROM labs AS a INNER JOIN doctors AS m ON m.id_doctor = a.id_doctor INNER JOIN services AS s ON s.id_service = m.id_service INNER JOIN hospitals AS h ON h.id_hospital = s.id_hospital WHERE a.is_abnormal = true GROUP BY h.name ORDER BY abnormal DESC
```

{{query:C32 · Hospitals with abnormal lab tests}}

#### C33 · Average cost by type of visit

Groups the visits with and without follow-up.

Keywords: `health`, `related-queries`, `visits`, `group-by`, `aggregate`

```sql
SELECT needs_follow_up, COUNT(*) AS visits, ROUND(AVG(cost), 2) AS avg_cost FROM visits GROUP BY needs_follow_up ORDER BY avg_cost DESC
```

{{query:C33 · Average cost by type of visit}}

#### C34 · Total stock by route of administration

SUM and COUNT grouped.

Keywords: `health`, `related-queries`, `drugs`, `group-by`, `aggregate`

```sql
SELECT route, COUNT(*) AS drugs, SUM(stock) AS total_stock FROM drugs GROUP BY route ORDER BY total_stock DESC
```

{{query:C34 · Total stock by route of administration}}

#### C35 · Ranking of patients by spending on treatments

SUM grouped with HAVING and LIMIT.

Keywords: `health`, `related-queries`, `treatments`, `patients`, `join`, `group-by`, `having`, `aggregate`

```sql
SELECT p.last_name AS patient, SUM(t.cost) AS spending FROM treatments AS t INNER JOIN patients AS p ON p.id_patient = t.id_patient GROUP BY p.last_name HAVING SUM(t.cost) > 2000 ORDER BY spending DESC LIMIT 10
```

{{query:C35 · Ranking of patients by spending on treatments}}

### D · Integrity

#### D01 · Admissions without a patient

Broken foreign key: the row in patients is not found.

Keywords: `health`, `integrity`, `admissions`, `patients`, `join`, `null`

```sql
SELECT i.id_admission, i.id_patient FROM admissions AS i LEFT JOIN patients AS p ON p.id_patient = i.id_patient WHERE p.id_patient IS NULL ORDER BY i.id_admission
```

{{query:D01 · Admissions without a patient}}

#### D02 · Admissions without a doctor

The doctor key finds no row.

Keywords: `health`, `integrity`, `admissions`, `doctors`, `join`, `null`

```sql
SELECT i.id_admission, i.id_doctor FROM admissions AS i LEFT JOIN doctors AS m ON m.id_doctor = i.id_doctor WHERE m.id_doctor IS NULL ORDER BY i.id_admission
```

{{query:D02 · Admissions without a doctor}}

#### D03 · Appointments without a patient

Broken reference in the schedule.

Keywords: `health`, `integrity`, `appointments`, `patients`, `join`, `null`

```sql
SELECT c.id_appointment, c.id_patient FROM appointments AS c LEFT JOIN patients AS p ON p.id_patient = c.id_patient WHERE p.id_patient IS NULL ORDER BY c.id_appointment
```

{{query:D03 · Appointments without a patient}}

#### D04 · Appointments without a doctor

Broken reference to the physician.

Keywords: `health`, `integrity`, `appointments`, `doctors`, `join`, `null`

```sql
SELECT c.id_appointment, c.id_doctor FROM appointments AS c LEFT JOIN doctors AS m ON m.id_doctor = c.id_doctor WHERE m.id_doctor IS NULL ORDER BY c.id_appointment
```

{{query:D04 · Appointments without a doctor}}

#### D05 · Patients with an insurer that does not exist

The key points at an insurer that is not there.

Keywords: `health`, `integrity`, `patients`, `insurers`, `join`, `null`

```sql
SELECT p.id_patient, p.id_insurer FROM patients AS p LEFT JOIN insurers AS s ON s.id_insurer = p.id_insurer WHERE p.id_insurer IS NOT NULL AND s.id_insurer IS NULL ORDER BY p.id_patient
```

{{query:D05 · Patients with an insurer that does not exist}}

#### D06 · Prescriptions without a drug

Orphan prescription line.

Keywords: `health`, `integrity`, `prescriptions`, `drugs`, `join`, `null`

```sql
SELECT pr.id_prescription, pr.id_drug FROM prescriptions AS pr LEFT JOIN drugs AS med ON med.id_drug = pr.id_drug WHERE med.id_drug IS NULL ORDER BY pr.id_prescription
```

{{query:D06 · Prescriptions without a drug}}

#### D07 · Treatments without a patient

Broken reference to the patient.

Keywords: `health`, `integrity`, `treatments`, `patients`, `join`, `null`

```sql
SELECT t.id_treatment, t.id_patient FROM treatments AS t LEFT JOIN patients AS p ON p.id_patient = t.id_patient WHERE p.id_patient IS NULL ORDER BY t.id_treatment
```

{{query:D07 · Treatments without a patient}}

#### D08 · Walk-in visits without an appointment

It is not an error: these visits do not come from the schedule.

Keywords: `health`, `integrity`, `visits`, `null`

```sql
SELECT v.id_visit, v.date, v.reason FROM visits AS v WHERE v.id_appointment IS NULL ORDER BY v.id_visit
```

{{query:D08 · Walk-in visits without an appointment}}

#### D09 · Patients without an insurer

Optional data that is missing.

Keywords: `health`, `integrity`, `patients`, `null`

```sql
SELECT id_patient, name, last_name FROM patients WHERE id_insurer IS NULL ORDER BY id_patient
```

{{query:D09 · Patients without an insurer}}

#### D10 · Diagnoses without a specialty

Incomplete catalogue.

Keywords: `health`, `integrity`, `diagnoses`, `specialties`, `join`, `null`

```sql
SELECT d.id_diagnosis, d.name FROM diagnoses AS d LEFT JOIN specialties AS e ON e.id_specialty = d.id_specialty WHERE e.id_specialty IS NULL ORDER BY d.id_diagnosis
```

{{query:D10 · Diagnoses without a specialty}}

#### D11 · Paid invoices without a payment method

Inconsistency: charged but with no way of paying.

Keywords: `health`, `integrity`, `invoices`, `null`

```sql
SELECT id_invoice, amount, paid, payment_method FROM invoices WHERE paid = true AND payment_method IS NULL ORDER BY id_invoice
```

{{query:D11 · Paid invoices without a payment method}}

#### D12 · Unpaid invoices with a payment method

The inconsistency symmetrical to the previous one.

Keywords: `health`, `integrity`, `invoices`, `null`

```sql
SELECT id_invoice, amount, paid, payment_method FROM invoices WHERE paid = false AND payment_method IS NOT NULL ORDER BY id_invoice
```

{{query:D12 · Unpaid invoices with a payment method}}

#### D13 · Orphan visit-diagnosis relationships

The bridge table mentions a visit that does not exist.

Keywords: `health`, `integrity`, `visit_diagnoses`, `visits`, `join`, `null`

```sql
SELECT vd.id_vd, vd.id_visit FROM visit_diagnoses AS vd LEFT JOIN visits AS v ON v.id_visit = vd.id_visit WHERE v.id_visit IS NULL ORDER BY vd.id_vd
```

{{query:D13 · Orphan visit-diagnosis relationships}}

#### D14 · Rooms without beds

A room registered with a capacity of zero.

Keywords: `health`, `integrity`, `rooms`

```sql
SELECT id_room, code, type, num_beds FROM rooms WHERE num_beds <= 0 ORDER BY code
```

{{query:D14 · Rooms without beds}}

#### D15 · Attended appointments without a registered visit

The status says attended but there is no visit.

Keywords: `health`, `integrity`, `appointments`, `visits`, `join`, `null`

```sql
SELECT c.id_appointment, c.date FROM appointments AS c LEFT JOIN visits AS v ON v.id_appointment = c.id_appointment WHERE c.status = 'attended' AND v.id_visit IS NULL ORDER BY c.id_appointment
```

{{query:D15 · Attended appointments without a registered visit}}

#### D16 · Secondary diagnoses marked as main

There can only be one main diagnosis per visit.

Keywords: `health`, `integrity`, `visit_diagnoses`, `group-by`, `having`, `aggregate`

```sql
SELECT vd.id_visit, COUNT(*) AS main FROM visit_diagnoses AS vd WHERE vd.is_main = true GROUP BY vd.id_visit HAVING COUNT(*) > 1 ORDER BY vd.id_visit
```

{{query:D16 · Secondary diagnoses marked as main}}

#### D17 · Surgeries without a previous admission

Operated on without any recorded stay.

Keywords: `health`, `integrity`, `surgeries`, `null`

```sql
SELECT id_surgery, name, date FROM surgeries WHERE id_admission IS NULL ORDER BY id_surgery
```

{{query:D17 · Surgeries without a previous admission}}

#### D18 · Treatment costs that are null or negative

Values out of range.

Keywords: `health`, `integrity`, `treatments`, `null`

```sql
SELECT id_treatment, cost FROM treatments WHERE cost IS NULL OR cost < 0 ORDER BY id_treatment
```

{{query:D18 · Treatment costs that are null or negative}}

#### D19 · Surgery duration out of range

More than five hours is suspicious.

Keywords: `health`, `integrity`, `surgeries`

```sql
SELECT id_surgery, name, duration_min FROM surgeries WHERE duration_min > 300 ORDER BY duration_min DESC
```

{{query:D19 · Surgery duration out of range}}

#### D20 · Prescriptions with a suspicious dose

Frequency and duration outside the reasonable ranges.

Keywords: `health`, `integrity`, `prescriptions`

```sql
SELECT id_prescription, dose, frequency_hours, duration_days FROM prescriptions WHERE frequency_hours <= 0 OR duration_days > 90 ORDER BY id_prescription
```

{{query:D20 · Prescriptions with a suspicious dose}}

### E · Views

#### E01 · Simple view: SELECT * of another query

The result of A03 is read as a table: the 3 large hospitals with their three columns (name, num_beds, level).

Keywords: `health`, `views`, `select-star`

```sql
SELECT * FROM [A03 · Large hospitals (>= 800 beds)] ORDER BY num_beds DESC, name
```

{{query:E01 · Simple view: SELECT * of another query}}

#### E02 · Arithmetic on the columns of a view

num_beds is still a number after going through the view A03: it can be divided by 100 and its remainder calculated.

Keywords: `health`, `views`

```sql
SELECT name AS hospital, num_beds, num_beds / 100 AS hundreds, num_beds % 100 AS remainder FROM [A03 · Large hospitals (>= 800 beds)] ORDER BY num_beds DESC, name
```

{{query:E02 · Arithmetic on the columns of a view}}

#### E03 · WHERE and DISTINCT on a view

Filters the view A03 by num_beds >= 900 and keeps the distinct levels: the three large hospitals are level 4.

Keywords: `health`, `views`, `distinct`

```sql
SELECT DISTINCT level FROM [A03 · Large hospitals (>= 800 beds)] WHERE num_beds >= 900 ORDER BY level
```

{{query:E03 · WHERE and DISTINCT on a view}}

#### E04 · Aggregates on a view

Five aggregates in a single row on the result of A03: 3 hospitals, 3450 beds, from 900 to 1350 and an average of 1150.

Keywords: `health`, `views`, `aggregate`

```sql
SELECT COUNT(*) AS hospitals, SUM(num_beds) AS beds, MIN(num_beds) AS min, MAX(num_beds) AS max, ROUND(AVG(num_beds), 2) AS avg FROM [A03 · Large hospitals (>= 800 beds)]
```

{{query:E04 · Aggregates on a view}}

#### E05 · Levels with two or more large hospitals

Groups the view A03 by level and keeps the levels with two or more hospitals: only level 4, with three of them.

Keywords: `health`, `views`, `group-by`, `having`, `aggregate`

```sql
SELECT level, COUNT(*) AS hospitals, SUM(num_beds) AS beds FROM [A03 · Large hospitals (>= 800 beds)] GROUP BY level HAVING COUNT(*) >= 2 ORDER BY level
```

{{query:E05 · Levels with two or more large hospitals}}

#### E06 · A view joined to a base table (INNER JOIN)

The view B07 joined to the table specialties by name: the 5 specialties with three or more doctors, with their area and whether they need an ICU.

Keywords: `health`, `views`, `specialties`, `join`

```sql
SELECT b.specialty, b.doctors, e.area, e.needs_icu FROM [B07 · Specialties with at least three doctors] AS b INNER JOIN specialties AS e ON e.name = b.specialty ORDER BY b.doctors DESC, b.specialty
```

{{query:E06 · A view joined to a base table (INNER JOIN)}}

#### E07 · Two views joined by specialty

The views C13 and C23 joined by specialty: the 5 specialties of the admissions top that also appear in the ranking of doctors.

Keywords: `health`, `views`, `join`

```sql
SELECT c.specialty, c.admissions, m.doctors FROM [C13 · Specialty with more admissions] AS c INNER JOIN [C23 · Specialties with more doctors] AS m ON m.specialty = c.specialty ORDER BY c.admissions DESC, c.specialty
```

{{query:E07 · Two views joined by specialty}}

#### E08 · Table LEFT JOIN view with IS NULL

LEFT JOIN of the table hospitals against the view B30 plus IS NULL: the only hospital without rooms, San José Hospital.

Keywords: `health`, `views`, `hospitals`, `join`, `null`

```sql
SELECT h.name AS hospital FROM hospitals AS h LEFT JOIN [B30 · Beds by hospital (only those with rooms)] AS b ON b.hospital = h.name WHERE b.beds IS NULL ORDER BY h.name
```

{{query:E08 · Table LEFT JOIN view with IS NULL}}

#### E09 · View of a view (two levels of query)

Reads the view E05, which in turn reads the view A03: three levels of query chained and a single row (level 4, 3450 beds).

Keywords: `health`, `views`

```sql
SELECT level, hospitals, beds FROM [E05 · Levels with two or more large hospitals] WHERE beds >= 2000 ORDER BY beds DESC
```

{{query:E09 · View of a view (two levels of query)}}

#### E10 · The same view twice (self join)

The view C05 appears twice with different aliases and is joined with itself by the number of prescriptions: the 7 pairs of drugs that share it.

Keywords: `health`, `views`, `join`

```sql
SELECT a.drug AS one, b.drug AS other, a.prescriptions FROM [C05 · Most prescribed drugs] AS a INNER JOIN [C05 · Most prescribed drugs] AS b ON b.prescriptions = a.prescriptions AND b.drug > a.drug ORDER BY a.prescriptions DESC, a.drug, b.drug
```

{{query:E10 · The same view twice (self join)}}

#### E11 · Implicit join with commas and a view

Cartesian product of the view C19 with the table insurers, filtered by coverage >= 80: the 2 insurers with the highest coverage.

Keywords: `health`, `views`

```sql
SELECT s.name AS insurance, s.coverage_percent, c.patients FROM [C19 · Insurers with their patients] AS c, insurers AS s WHERE s.name = c.insurance AND s.coverage_percent >= 80 ORDER BY c.patients DESC, s.name
```

{{query:E11 · Implicit join with commas and a view}}

#### E12 · ORDER BY an alias and LIMIT on a view

Orders by the alias coverage and cuts the view C19 to 3 rows: the three insurers with more patients.

Keywords: `health`, `views`

```sql
SELECT insurance AS coverage, patients FROM [C19 · Insurers with their patients] ORDER BY patients DESC, insurance LIMIT 3
```

{{query:E12 · ORDER BY an alias and LIMIT on a view}}

#### E13 · A boolean through a view

The boolean is_abnormal travels untouched from the table labs to the view B44: only the types urine and blood switch it on.

Keywords: `health`, `views`

```sql
SELECT type, is_abnormal, labs FROM [B44 · Lab tests by type and result] WHERE is_abnormal = true ORDER BY type
```

{{query:E13 · A boolean through a view}}

#### E14 · Nulls through a view (COALESCE)

The missing phone of «Andes Health» is still null after reading the view A27, and COALESCE replaces it with «no phone».

Keywords: `health`, `views`

```sql
SELECT name AS insurance, COALESCE(phone, 'no phone') AS contact FROM [A27 · Normalised insurer phone] ORDER BY name
```

{{query:E14 · Nulls through a view (COALESCE)}}

#### E15 · Aggregates on a view that is already aggregated

Summarises the already aggregated view B08 (one row per insurer): 6 insurers, 35 patients and between 2 and 9 per insurer.

Keywords: `health`, `views`, `aggregate`

```sql
SELECT COUNT(*) AS insurers, SUM(patients) AS patients, MIN(patients) AS min, MAX(patients) AS max FROM [B08 · Patients by insurer]
```

{{query:E15 · Aggregates on a view that is already aggregated}}

#### E16 · A view that reads a query with LIMIT

The view C05 has LIMIT 10: reading it as a table brings only those 10 rows (72 prescriptions in total).

Keywords: `health`, `views`, `aggregate`

```sql
SELECT COUNT(*) AS drugs, SUM(prescriptions) AS prescriptions FROM [C05 · Most prescribed drugs]
```

{{query:E16 · A view that reads a query with LIMIT}}

#### E17 · View of an empty result

The view C10 returns no rows: reading it as a table gives an empty table, and COUNT(*) returns 0 instead of an error.

Keywords: `health`, `views`, `aggregate`

```sql
SELECT COUNT(*) AS rows FROM [C10 · Nobody has two insurers (rowCount)]
```

{{query:E17 · View of an empty result}}

#### E18 · LIMIT and OFFSET on a view

LIMIT 3 OFFSET 2 on the 8 rows of the view A41: the fourth, fifth and sixth diagnoses by length of the name.

Keywords: `health`, `views`

```sql
SELECT code, name, letters FROM [A41 · Longest diagnosis names] ORDER BY letters DESC, code LIMIT 3 OFFSET 2
```

{{query:E18 · LIMIT and OFFSET on a view}}

#### E19 · LIKE filter on a view

LIKE on a column of the view A37: the 2 drugs with low stock whose name starts with A.

Keywords: `health`, `views`, `like`

```sql
SELECT name, stock, price FROM [A37 · Drugs with low stock] WHERE name LIKE 'A%' ORDER BY name
```

{{query:E19 · LIKE filter on a view}}

#### E20 · A view of a view that joins two views

Reads the view E07 (which joins C13 and C23) and keeps the specialties with three doctors or more: 4 rows.

Keywords: `health`, `views`

```sql
SELECT specialty, admissions, doctors FROM [E07 · Two views joined by specialty] WHERE doctors >= 3 ORDER BY doctors DESC, specialty
```

{{query:E20 · A view of a view that joins two views}}

#### E21 · LEFT JOIN between two views

C13 LEFT JOIN C23: the 5 specialties of the admissions top with the number of doctors of each one, from the lowest to the highest.

Keywords: `health`, `views`, `join`

```sql
SELECT c.specialty, c.admissions, m.doctors FROM [C13 · Specialty with more admissions] AS c LEFT JOIN [C23 · Specialties with more doctors] AS m ON m.specialty = c.specialty ORDER BY m.doctors ASC NULLS FIRST, c.specialty
```

{{query:E21 · LEFT JOIN between two views}}

#### E22 · Projection with an alias and a star on a view

The star of the alias (v.*) on the view A37: the 6 drugs with low stock, from the lowest to the highest stock.

Keywords: `health`, `views`

```sql
SELECT v.* FROM [A37 · Drugs with low stock] AS v ORDER BY v.stock
```

{{query:E22 · Projection with an alias and a star on a view}}

#### E23 · A view that reads a query with several tables

The view B10 joins four tables inside (admissions, patients, rooms and doctors): reading it as a table brings its 25 rows.

Keywords: `health`, `views`, `aggregate`

```sql
SELECT COUNT(*) AS rows FROM [B10 · Admissions with patient, room and doctor]
```

{{query:E23 · A view that reads a query with several tables}}

#### E24 · Group by a boolean of a view

GROUP BY on the boolean needs_follow_up that comes from the view C33: the visits with and without follow-up.

Keywords: `health`, `views`, `group-by`, `aggregate`

```sql
SELECT needs_follow_up, COUNT(*) AS brackets, SUM(visits) AS visits FROM [C33 · Average cost by type of visit] GROUP BY needs_follow_up ORDER BY needs_follow_up
```

{{query:E24 · Group by a boolean of a view}}

### F · Subqueries

#### F01 · Insurers of the patients of Madrid (IN with a subquery)

IN with a subquery: the insurers whose identifier is used by a patient of Madrid.

Keywords: `health`, `subqueries`, `insurers`, `patients`, `subquery`, `in`

```sql
SELECT id_insurer, name FROM insurers WHERE id_insurer IN (SELECT id_insurer FROM patients WHERE city = 'Madrid') ORDER BY id_insurer
```

{{query:F01 · Insurers of the patients of Madrid (IN with a subquery)}}

#### F02 · Drugs with no prescription (NOT IN with a subquery)

NOT IN with a subquery: the drugs that no prescription uses.

Keywords: `health`, `subqueries`, `drugs`, `prescriptions`, `subquery`, `in`

```sql
SELECT id_drug, name FROM drugs WHERE id_drug NOT IN (SELECT id_drug FROM prescriptions) ORDER BY id_drug
```

{{query:F02 · Drugs with no prescription (NOT IN with a subquery)}}

#### F03 · Specialties with no diagnosis (NOT IN with a subquery)

The specialties that no diagnosis is linked to.

Keywords: `health`, `subqueries`, `specialties`, `diagnoses`, `subquery`, `in`

```sql
SELECT id_specialty, name FROM specialties WHERE id_specialty NOT IN (SELECT id_specialty FROM diagnoses) ORDER BY id_specialty
```

{{query:F03 · Specialties with no diagnosis (NOT IN with a subquery)}}

#### F04 · Hospitals with an ICU room (EXISTS)

EXISTS with a correlated subquery: it is checked again for every hospital.

Keywords: `health`, `subqueries`, `hospitals`, `rooms`, `subquery`, `exists`

```sql
SELECT h.id_hospital, h.name FROM hospitals AS h WHERE EXISTS (SELECT 1 FROM rooms AS r WHERE r.id_hospital = h.id_hospital AND r.type = 'ICU') ORDER BY h.id_hospital
```

{{query:F04 · Hospitals with an ICU room (EXISTS)}}

#### F05 · Hospitals with no ICU room (NOT EXISTS)

The other side of the previous example: NOT EXISTS keeps the hospitals with no match.

Keywords: `health`, `subqueries`, `hospitals`, `rooms`, `subquery`, `exists`

```sql
SELECT h.id_hospital, h.name FROM hospitals AS h WHERE NOT EXISTS (SELECT 1 FROM rooms AS r WHERE r.id_hospital = h.id_hospital AND r.type = 'ICU') ORDER BY h.id_hospital
```

{{query:F05 · Hospitals with no ICU room (NOT EXISTS)}}

#### F06 · Patients with a high-risk surgery (EXISTS, correlated)

The subquery points at the outer patient (p.id_patient): a correlated EXISTS.

Keywords: `health`, `subqueries`, `patients`, `surgeries`, `subquery`, `exists`

```sql
SELECT p.id_patient, p.last_name FROM patients AS p WHERE EXISTS (SELECT 1 FROM surgeries AS s WHERE s.id_patient = p.id_patient AND s.risk = 'high') ORDER BY p.id_patient
```

{{query:F06 · Patients with a high-risk surgery (EXISTS, correlated)}}

#### F07 · Dermatology doctors with more experience than some Cardiology doctor (> ANY)

x > ANY (…) is true when the years beat at least one of the inner values.

Keywords: `health`, `subqueries`, `doctors`, `subquery`

```sql
SELECT id_doctor, last_name, years_experience FROM doctors WHERE id_specialty = 6 AND years_experience > ANY (SELECT years_experience FROM doctors WHERE id_specialty = 1) ORDER BY id_doctor
```

{{query:F07 · Dermatology doctors with more experience than some Cardiology doctor (> ANY)}}

#### F08 · The most experienced doctor of each specialty (>= ALL, correlated)

x >= ALL (…) needs the comparison to hold for every inner value: only the top of each specialty passes.

Keywords: `health`, `subqueries`, `doctors`, `subquery`

```sql
SELECT d.id_doctor, d.last_name, d.years_experience FROM doctors AS d WHERE d.years_experience >= ALL (SELECT d2.years_experience FROM doctors AS d2 WHERE d2.id_specialty = d.id_specialty) ORDER BY d.id_specialty, d.id_doctor
```

{{query:F08 · The most experienced doctor of each specialty (>= ALL, correlated)}}

#### F09 · Chronic patients of a city with a private insurer (<> ALL)

x <> ALL (…) is the same as x NOT IN (…): it keeps the values that differ from every one of them.

Keywords: `health`, `subqueries`, `patients`, `insurers`, `subquery`

```sql
SELECT id_patient, last_name FROM patients WHERE is_chronic = TRUE AND id_insurer <> ALL (SELECT id_insurer FROM insurers WHERE type = 'public') ORDER BY id_patient
```

{{query:F09 · Chronic patients of a city with a private insurer (<> ALL)}}

#### F10 · University hospitals

This query is not a test by itself: it is the view the next examples read inside a subquery.

Keywords: `health`, `subqueries`, `hospitals`

```sql
SELECT id_hospital, name, city FROM hospitals WHERE is_university = TRUE
```

{{query:F10 · University hospitals}}

#### F11 · Rooms of the university hospitals read from the view (subquery over a view)

The subquery reads the view F10 as if it were a table: [F10 · …] inside the IN.

Keywords: `health`, `subqueries`, `rooms`, `subquery`, `in`

```sql
SELECT r.id_room, r.code FROM rooms AS r WHERE r.id_hospital IN (SELECT id_hospital FROM [F10 · University hospitals]) ORDER BY r.id_room
```

{{query:F11 · Rooms of the university hospitals read from the view (subquery over a view)}}

#### F12 · Average cost per day of admissions of 700 or more (derived table)

A subquery in the FROM with an alias (t) is a derived table: it is queried like any other table.

Keywords: `health`, `subqueries`, `admissions`, `rooms`, `subquery`, `join`, `group-by`, `aggregate`

```sql
SELECT t.id_hospital, t.avg_cost FROM (SELECT r.id_hospital, AVG(a.cost_per_day) AS avg_cost FROM admissions AS a INNER JOIN rooms AS r ON r.id_room = a.id_room GROUP BY r.id_hospital) AS t WHERE t.avg_cost >= 700 ORDER BY t.id_hospital
```

{{query:F12 · Average cost per day of admissions of 700 or more (derived table)}}

#### F13 · Hospitals with ten or more admissions (derived table and JOIN)

The derived table is joined to the hospitals table and its column total is filtered.

Keywords: `health`, `subqueries`, `admissions`, `rooms`, `hospitals`, `subquery`, `join`, `group-by`, `aggregate`

```sql
SELECT t.id_hospital, h.name, t.total FROM (SELECT r.id_hospital, COUNT(*) AS total FROM admissions AS a INNER JOIN rooms AS r ON r.id_room = a.id_room GROUP BY r.id_hospital) AS t INNER JOIN hospitals AS h ON h.id_hospital = t.id_hospital WHERE t.total >= 10 ORDER BY t.total DESC, t.id_hospital
```

{{query:F13 · Hospitals with ten or more admissions (derived table and JOIN)}}

#### F14 · Visits of every patient of a city (scalar subquery in SELECT)

The subquery in the column list returns one value per row: the visits of that patient.

Keywords: `health`, `subqueries`, `visits`, `patients`, `subquery`, `aggregate`

```sql
SELECT p.id_patient, p.last_name, (SELECT COUNT(*) FROM visits AS v WHERE v.id_patient = p.id_patient) AS visits FROM patients AS p WHERE p.city = 'Bilbao' ORDER BY p.id_patient
```

{{query:F14 · Visits of every patient of a city (scalar subquery in SELECT)}}

#### F15 · Admissions more expensive than the average (scalar subquery in WHERE)

The subquery returns a single value (the average) that the WHERE compares with.

Keywords: `health`, `subqueries`, `admissions`, `subquery`, `aggregate`

```sql
SELECT id_admission, cost_per_day FROM admissions WHERE cost_per_day > (SELECT AVG(cost_per_day) FROM admissions) ORDER BY id_admission
```

{{query:F15 · Admissions more expensive than the average (scalar subquery in WHERE)}}

#### F16 · Specialties with three or more diagnoses

A query whose SQL carries a subquery also works as a view: this one uses a correlated EXISTS with HAVING.

Keywords: `health`, `subqueries`, `specialties`, `diagnoses`, `subquery`, `having`, `aggregate`, `exists`

```sql
SELECT s.id_specialty, s.name FROM specialties AS s WHERE EXISTS (SELECT 1 FROM diagnoses AS d WHERE d.id_specialty = s.id_specialty HAVING COUNT(*) >= 3) ORDER BY s.id_specialty
```

{{query:F16 · Specialties with three or more diagnoses}}

#### F17 · The previous view read as a table

F16 is read from the FROM like a table, even though inside it there is a subquery.

Keywords: `health`, `subqueries`

```sql
SELECT id_specialty, name FROM [F16 · Specialties with three or more diagnoses] ORDER BY id_specialty
```

{{query:F17 · The previous view read as a table}}

#### F18 · Doctors of the specialties with a severe diagnosis (nested subqueries)

One subquery inside another: the inner one picks the specialties and the outer one the doctors.

Keywords: `health`, `subqueries`, `doctors`, `diagnoses`, `subquery`, `distinct`, `in`

```sql
SELECT DISTINCT d.id_doctor, d.last_name FROM doctors AS d WHERE d.id_specialty IN (SELECT id_specialty FROM diagnoses WHERE severity = 'severe') ORDER BY d.id_doctor
```

{{query:F18 · Doctors of the specialties with a severe diagnosis (nested subqueries)}}

#### F19 · Oncology doctors but the most experienced (NOT with ALL)

NOT (… >= ALL (…)) is the negation of the ALL predicate: the whole specialty but its top doctor.

Keywords: `health`, `subqueries`, `doctors`, `subquery`

```sql
SELECT d.id_doctor, d.last_name, d.years_experience FROM doctors AS d WHERE d.id_specialty = 5 AND NOT (d.years_experience >= ALL (SELECT d2.years_experience FROM doctors AS d2 WHERE d2.id_specialty = 5)) ORDER BY d.id_doctor
```

{{query:F19 · Oncology doctors but the most experienced (NOT with ALL)}}

#### F20 · Rooms of the university hospitals (= ANY)

x = ANY (…) is the same as x IN (…): it is kept for completeness of the quantifiers.

Keywords: `health`, `subqueries`, `rooms`, `hospitals`, `subquery`, `distinct`

```sql
SELECT DISTINCT r.id_hospital FROM rooms AS r WHERE r.id_hospital = ANY (SELECT id_hospital FROM hospitals WHERE is_university = TRUE) ORDER BY r.id_hospital
```

{{query:F20 · Rooms of the university hospitals (= ANY)}}

#### F21 · Doctors with more experience than some Cardiology doctor (> SOME)

SOME is a synonym of ANY: the comparison has to hold for at least one inner value.

Keywords: `health`, `subqueries`, `doctors`, `subquery`

```sql
SELECT id_doctor, last_name FROM doctors WHERE years_experience > SOME (SELECT years_experience FROM doctors WHERE id_specialty = 1) ORDER BY id_doctor
```

{{query:F21 · Doctors with more experience than some Cardiology doctor (> SOME)}}

### G · Unions

#### G01 · Hospital and insurer names, without repeats

UNION of two tables with one column each: the hospital names and the insurer names, every one once and sorted by name.

Keywords: `health`, `unions`, `hospitals`, `insurers`, `union`

```sql
SELECT name FROM hospitals UNION SELECT name FROM insurers ORDER BY name
```

{{query:G01 · Hospital and insurer names, without repeats}}

#### G02 · The country of every hospital, twice (UNION ALL)

The same SELECT on both sides of UNION ALL: the rows are stacked without removing repeats, so there are 14 of them.

Keywords: `health`, `unions`, `hospitals`, `union`

```sql
SELECT country FROM hospitals UNION ALL SELECT country FROM hospitals ORDER BY country
```

{{query:G02 · The country of every hospital, twice (UNION ALL)}}

#### G03 · Every country once (UNION)

The same union without ALL: UNION removes the repeated rows, so each country comes out once.

Keywords: `health`, `unions`, `hospitals`, `union`

```sql
SELECT country FROM hospitals UNION SELECT country FROM hospitals ORDER BY country
```

{{query:G03 · Every country once (UNION)}}

#### G04 · A union read as a table

The union goes in the FROM with an alias (derived table): the doctor identifiers of the staff and of the appointments, without repeats, are counted.

Keywords: `health`, `unions`, `doctors`, `appointments`, `subquery`, `union`, `aggregate`

```sql
SELECT COUNT(*) AS n FROM (SELECT id_doctor FROM doctors UNION SELECT id_doctor FROM appointments) AS t
```

{{query:G04 · A union read as a table}}

#### G05 · Union with output alias, ORDER BY and LIMIT

Both sides name their column item: the ORDER BY of the union sorts the combined result by that name and the LIMIT keeps the first 5.

Keywords: `health`, `unions`, `hospitals`, `insurers`, `union`

```sql
SELECT name AS item FROM hospitals UNION SELECT name AS item FROM insurers ORDER BY item LIMIT 5
```

{{query:G05 · Union with output alias, ORDER BY and LIMIT}}

### H · Scripts as tables

#### H01 · Patients by city, read from the script

Reads the result of the script “Patients by city” as if it were a table.

Keywords: `health`, `scripts-as-tables`

```sql
SELECT city, patients FROM [Patients by city] ORDER BY patients DESC, city
```

{{query:H01 · Patients by city, read from the script}}

#### H02 · How many cities the script returns

Reads the result of the script “Patients by city” as if it were a table.

Keywords: `health`, `scripts-as-tables`, `aggregate`

```sql
SELECT COUNT(*) AS total FROM [Patients by city]
```

{{query:H02 · How many cities the script returns}}

#### H03 · Cities with 5 or more patients

Reads the result of the script “Patients by city” as if it were a table.

Keywords: `health`, `scripts-as-tables`

```sql
SELECT city, patients FROM [Patients by city] WHERE patients >= 5 ORDER BY patients DESC, city
```

{{query:H03 · Cities with 5 or more patients}}

### I · Parameters

#### P01 · Cities of the hospitals (parameter source)

Source of a parameter: every row offers a city and how many hospitals it has.

Keywords: `health`, `parameters`, `source`, `reference`

```sql
SELECT city, COUNT(*) AS hospitals FROM hospitals GROUP BY city ORDER BY city
```

{{query:P01 · Cities of the hospitals (parameter source)}}

#### P02 · Most expensive invoice (parameter source)

Source of a parameter that returns one row only: the invoice with the highest amount.

Keywords: `health`, `parameters`, `source`, `reference`

```sql
SELECT id_invoice, amount FROM invoices ORDER BY amount DESC, id_invoice LIMIT 1
```

{{query:P02 · Most expensive invoice (parameter source)}}

#### P03 · Rooms of a floor (range)

Range parameter: the floor is chosen in a drop-down from 0 to 5.

Keywords: `health`, `parameters`, `range`, `rooms`

```sql
SELECT code, type, num_beds, floor FROM rooms WHERE floor = {{ 0..5 | Floor }} ORDER BY code
```

{{query:P03 · Rooms of a floor (range)}}

#### P04 · Appointments by status of the researcher doctors (subquery)

Parameter with a subquery: the researcher doctors come from the doctors table.

Keywords: `health`, `parameters`, `subquery`, `appointments`

```sql
SELECT status, COUNT(*) AS appointments FROM appointments WHERE id_doctor IN {{ SELECT id_doctor FROM doctors WHERE is_researcher = true ORDER BY id_doctor | Researcher doctor }} GROUP BY status ORDER BY status
```

{{query:P04 · Appointments by status of the researcher doctors (subquery)}}

#### P05 · Patients of the cities of the hospitals (query reference)

Parameter that references a saved query: its city/count pairs fill the drop-down.

Keywords: `health`, `parameters`, `reference`, `patients`

```sql
SELECT name, last_name, city FROM patients WHERE city IN {{ P01 · Cities of the hospitals (parameter source) | City }} ORDER BY last_name, name
```

{{query:P05 · Patients of the cities of the hospitals (query reference)}}

#### P06 · Invoices except the most expensive one (NOT IN with a reference)

Parameter in a NOT IN that references the query of the most expensive invoice.

Keywords: `health`, `parameters`, `reference`, `not-in`

```sql
SELECT id_invoice, amount, payment_method FROM invoices WHERE id_invoice NOT IN {{ P02 · Most expensive invoice (parameter source) | Most expensive invoice }} ORDER BY amount DESC, id_invoice LIMIT 10
```

{{query:P06 · Invoices except the most expensive one (NOT IN with a reference)}}

#### P07 · Admissions by hospital (HAVING with a range)

Range parameter inside a HAVING: it filters the hospitals by their number of admissions.

Keywords: `health`, `parameters`, `range`, `having`

```sql
SELECT h.name, COUNT(*) AS admissions FROM hospitals AS h INNER JOIN rooms AS r ON r.id_hospital = h.id_hospital INNER JOIN admissions AS i ON i.id_room = r.id_room GROUP BY h.name HAVING COUNT(*) >= {{ 1..20 | Minimum admissions }} ORDER BY admissions DESC, h.name
```

{{query:P07 · Admissions by hospital (HAVING with a range)}}

#### P08 · Hospitals of the script (script reference)

Parameter that references a script: its rows give the values and labels of the drop-down.

Keywords: `health`, `parameters`, `reference`, `script`

```sql
SELECT name, city, num_beds FROM hospitals WHERE id_hospital IN {{ P10 · Hospitals for the parameter | Hospital }} ORDER BY name
```

{{query:P08 · Hospitals of the script (script reference)}}

#### P11 · Admissions between two costs (two ranges)

Two range parameters in a BETWEEN: minimum and maximum daily cost.

Keywords: `health`, `parameters`, `several`, `range`

```sql
SELECT id_admission, cost_per_day, expected_days FROM admissions WHERE cost_per_day BETWEEN {{ 200..500 | Minimum cost }} AND {{ 500..900 | Maximum cost }} ORDER BY cost_per_day, id_admission
```

{{query:P11 · Admissions between two costs (two ranges)}}

#### P12 · Appointments by status and type (two subqueries)

Two subquery parameters: the status and the type of the appointment, at once.

Keywords: `health`, `parameters`, `several`, `subquery`

```sql
SELECT status, type, COUNT(*) AS appointments FROM appointments WHERE status = {{ SELECT status FROM appointments GROUP BY status ORDER BY status | Status }} AND type = {{ SELECT type FROM appointments GROUP BY type ORDER BY type | Type }} GROUP BY status, type ORDER BY status, type
```

{{query:P12 · Appointments by status and type (two subqueries)}}

#### P13 · Patients of a city and an insurer (reference and subquery)

Two parameters: the cities of a saved query and an insurer from a subquery.

Keywords: `health`, `parameters`, `several`, `patients`

```sql
SELECT name, last_name, city FROM patients WHERE city IN {{ P01 · Cities of the hospitals (parameter source) | City }} AND id_insurer = {{ SELECT id_insurer FROM insurers ORDER BY id_insurer | Insurer }} ORDER BY last_name, name
```

{{query:P13 · Patients of a city and an insurer (reference and subquery)}}

#### P14 · Rooms of a floor with minimum beds (two ranges)

Two range parameters: the floor and the minimum number of beds.

Keywords: `health`, `parameters`, `several`, `rooms`

```sql
SELECT code, type, num_beds, floor FROM rooms WHERE floor = {{ 0..5 | Floor }} AND num_beds >= {{ 0..2 | Minimum beds }} ORDER BY code
```

{{query:P14 · Rooms of a floor with minimum beds (two ranges)}}

#### P15 · Admissions of a hospital with minimum cost (reference and range)

Two parameters: the hospital (a script list) and the minimum daily cost (a range).

Keywords: `health`, `parameters`, `several`, `reference`

```sql
SELECT h.name AS hospital, i.id_admission, i.cost_per_day FROM hospitals AS h INNER JOIN rooms AS r ON r.id_hospital = h.id_hospital INNER JOIN admissions AS i ON i.id_room = r.id_room WHERE h.id_hospital IN {{ P10 · Hospitals for the parameter | Hospital }} AND i.cost_per_day >= {{ 200..900 | Minimum cost }} ORDER BY i.cost_per_day DESC, i.id_admission LIMIT 10
```

{{query:P15 · Admissions of a hospital with minimum cost (reference and range)}}

### J · PIVOT

#### P01 · Appointments by status and type

PIVOT with status rows, appointment-type columns and COUNT(*), plus totals.

Keywords: `pivot`, `dynamic table`, `count`, `average`, `percentage`

```sql
PIVOT (SELECT status) BY (SELECT type) FROM appointments VALUES COUNT(*) AS appointments TOTALS ROWS GRAND COLUMNS GRAND;
```

{{query:P01 · Appointments by status and type}}

#### P02 · Billing by payment method and paid

Billing amount and average discount by payment method and paid flag.

Keywords: `pivot`, `dynamic table`, `count`, `average`, `percentage`

```sql
PIVOT (SELECT payment_method NULLS AS '(no method)') BY (SELECT paid) FROM invoices VALUES SUM(amount) AS billed, AVG(discount) AS average_discount TOTALS ROWS GRAND COLUMNS GRAND;
```

{{query:P02 · Billing by payment method and paid}}

#### P03 · Surgeries by risk and success

Surgeries and average cost by risk and success.

Keywords: `pivot`, `dynamic table`, `count`, `average`, `percentage`

```sql
PIVOT (SELECT risk) BY (SELECT successful) FROM surgeries VALUES COUNT(*) AS surgeries, AVG(cost) AS average_cost TOTALS ROWS GRAND COLUMNS GRAND;
```

{{query:P03 · Surgeries by risk and success}}

#### P04 · Nurses by shift and supervisor

Nurses and distinct experience levels by shift and supervisor flag.

Keywords: `pivot`, `dynamic table`, `count`, `average`, `percentage`

```sql
PIVOT (SELECT shift) BY (SELECT is_supervisor) FROM nurses VALUES COUNT(*) AS nurses, COUNT(DISTINCT years_experience) AS experience_levels TOTALS ROWS GRAND COLUMNS GRAND;
```

{{query:P04 · Nurses by shift and supervisor}}

#### P05 · Diagnoses by severity and chronic

Diagnoses and PCT_OF ROW by severity and chronic flag, with measures on rows.

Keywords: `pivot`, `dynamic table`, `count`, `average`, `percentage`

```sql
PIVOT (SELECT severity) BY (SELECT is_chronic) FROM diagnoses VALUES COUNT(*) AS diagnoses, COUNT(*) SHOW AS PCT_OF ROW AS pct TOTALS ROWS GRAND COLUMNS GRAND ONLY diagnoses MEASURES ON ROWS;
```

{{query:P05 · Diagnoses by severity and chronic}}

### K · CASE and IF

#### K01 · Insurers by coverage (searched CASE)

Searched CASE with the boundary at 60: below 60 is low, 60–85 medium and the rest high.

Keywords: `health`, `case`, `functions`, `insurers`

```sql
SELECT name, coverage_percent, CASE WHEN coverage_percent < 60 THEN 'Low' WHEN coverage_percent BETWEEN 60 AND 85 THEN 'Medium' ELSE 'High' END AS coverage FROM insurers ORDER BY coverage_percent
```

{{query:K01 · Insurers by coverage (searched CASE)}}

#### K02 · Surgeries by risk (simple CASE)

Simple CASE: compares the risk with each WHEN using = and returns one label per distinct value.

Keywords: `health`, `case`, `functions`, `surgeries`

```sql
SELECT DISTINCT risk, CASE risk WHEN 'low' THEN 'Low' WHEN 'medium' THEN 'Medium' WHEN 'high' THEN 'High' ELSE 'Unknown' END AS risk_label FROM surgeries ORDER BY risk
```

{{query:K02 · Surgeries by risk (simple CASE)}}

#### K03 · Patients without a phone (CASE IS NULL)

The searched CASE catches the missing phone with IS NULL and keeps the real phone otherwise.

Keywords: `health`, `case`, `null`, `patients`

```sql
SELECT name, last_name, CASE WHEN phone IS NULL THEN 'Unknown' ELSE phone END AS contact FROM patients WHERE phone IS NULL ORDER BY last_name LIMIT 5
```

{{query:K03 · Patients without a phone (CASE IS NULL)}}

#### K04 · Patients with IF

IF(condition, value, substitute) is the shorthand of CASE WHEN condition THEN value ELSE substitute END.

Keywords: `health`, `if`, `functions`, `patients`

```sql
SELECT id_patient, IF(is_chronic, 'Chronic', 'Acute') AS kind FROM patients WHERE id_patient <= 6 ORDER BY id_patient
```

{{query:K04 · Patients with IF}}

#### K05 · Doctors by experience (CASE in GROUP BY)

The same CASE expression classifies the doctors and is used again in GROUP BY: 14 veterans and 16 juniors.

Keywords: `health`, `case`, `group-by`, `doctors`

```sql
SELECT CASE WHEN years_experience >= 20 THEN 'Veteran' ELSE 'Junior' END AS band, COUNT(*) AS n FROM doctors GROUP BY CASE WHEN years_experience >= 20 THEN 'Veteran' ELSE 'Junior' END ORDER BY band
```

{{query:K05 · Doctors by experience (CASE in GROUP BY)}}

### L · Functions in SELECT and PIVOT

#### L01 · Numeric functions: ABS and ROUND

Applies ABS and ROUND to drug prices: one decimal and distance from 5.

Keywords: `health`, `functions`, `numeric`, `abs`, `round`

```sql
SELECT id_drug, price, ROUND(price, 1) AS rounded, ROUND(ABS(price - 5), 2) AS distance FROM drugs WHERE id_drug <= 4 ORDER BY id_drug
```

{{query:L01 · Numeric functions: ABS and ROUND}}

#### L02 · Text functions: UPPER, LOWER, LENGTH, TRIM, SUBSTR and CONCAT

Applies the six text functions to the first three drugs.

Keywords: `health`, `functions`, `text`, `upper`, `lower`, `length`, `trim`, `substr`, `concat`

```sql
SELECT id_drug, UPPER(name) AS upper_name, LOWER(presentation) AS lower_presentation, LENGTH(active_ingredient) AS letters, TRIM(name) AS trimmed, SUBSTR(name, 1, 4) AS prefix, CONCAT(name, ' (', presentation, ')') AS label FROM drugs WHERE id_drug <= 3 ORDER BY id_drug
```

{{query:L02 · Text functions: UPPER, LOWER, LENGTH, TRIM, SUBSTR and CONCAT}}

#### L03 · Regular expressions: INSTR and REPLACE

Uses REGEXPR_INSTR to find the @ in doctor e-mails and REGEXPR_REPLACE to mask the digits of the licence number.

Keywords: `health`, `functions`, `regex`, `instr`, `replace`

```sql
SELECT id_doctor, email, REGEXPR_INSTR(email, '@') AS at_position, REGEXPR_REPLACE(licence_number, '[0-9]', '#') AS masked FROM doctors WHERE id_doctor <= 3 ORDER BY id_doctor
```

{{query:L03 · Regular expressions: INSTR and REPLACE}}

#### L04 · NULL functions: COALESCE, IFNULL and NULLIF

Uses COALESCE, IFNULL and NULLIF over insurer phone and type.

Keywords: `health`, `functions`, `null`, `coalesce`, `ifnull`, `nullif`

```sql
SELECT id_insurer, COALESCE(phone, 'No phone') AS phone, IFNULL(type, 'Unknown') AS type, NULLIF(type, 'public') AS not_public FROM insurers ORDER BY id_insurer
```

{{query:L04 · NULL functions: COALESCE, IFNULL and NULLIF}}

#### L05 · Conditional functions: IF and CASE

Uses IF and CASE to classify patients by chronic status and phone length.

Keywords: `health`, `functions`, `conditional`, `if`, `case`

```sql
SELECT id_patient, IF(is_chronic, 'Chronic', 'Acute') AS kind, CASE WHEN phone IS NULL THEN 'No phone' WHEN LENGTH(phone) = 9 THEN 'Valid' ELSE 'Other' END AS phone_status FROM patients WHERE id_patient <= 6 ORDER BY id_patient
```

{{query:L05 · Conditional functions: IF and CASE}}

#### L06 · Aggregate functions: COUNT, SUM, AVG, MIN and MAX

Runs COUNT(*), COUNT(column), COUNT(DISTINCT), SUM, AVG, MIN and MAX over visits.

Keywords: `health`, `functions`, `aggregate`, `count`, `sum`, `avg`, `min`, `max`

```sql
SELECT COUNT(*) AS total, COUNT(reason) AS with_reason, COUNT(DISTINCT id_doctor) AS doctors, SUM(cost) AS cost, AVG(cost) AS average_cost, MIN(cost) AS min_cost, MAX(cost) AS max_cost FROM visits
```

{{query:L06 · Aggregate functions: COUNT, SUM, AVG, MIN and MAX}}

#### L07 · PIVOT with all aggregate functions

PIVOT with COUNT, SUM, AVG, MIN and MAX as measures, one measure per row, by room type and occupancy.

Keywords: `health`, `functions`, `pivot`, `aggregate`

```sql
PIVOT (SELECT type) BY (SELECT occupied) FROM rooms VALUES COUNT(*) AS rooms, SUM(num_beds) AS beds, AVG(num_beds) AS average_beds, MIN(num_beds) AS min_beds, MAX(num_beds) AS max_beds TOTALS ROWS GRAND COLUMNS GRAND MEASURES ON ROWS;
```

{{query:L07 · PIVOT with all aggregate functions}}

#### L08 · PIVOT with scalar functions in the row axis

PIVOT with scalar functions in the row axis: UPPER and CASE create the experience band, REGEXPR_INSTR filters doctors with an e-mail, and COUNT/AVG aggregate.

Keywords: `health`, `functions`, `pivot`, `scalar`, `case`, `regexpr_instr`

```sql
PIVOT (SELECT UPPER(CASE WHEN years_experience >= 20 THEN 'veteran' ELSE 'junior' END) AS band WHERE REGEXPR_INSTR(email, '@') > 0) BY (SELECT is_researcher) FROM doctors VALUES COUNT(*) AS doctors, AVG(years_experience) AS average_experience TOTALS ROWS GRAND COLUMNS GRAND;
```

{{query:L08 · PIVOT with scalar functions in the row axis}}

## Scripts

### A · Basics and aggregation

#### Chronic patients

Filters chronic patients and shows their name, city, blood group and phone.

Keywords: `health`, `filters`, `patients`, `filter`, `map`

```js
const datos = tables["patients"];
return datos
  .filter((fila) => fila.is_chronic)
  .map((fila) => ({
    "full_name": `${fila.name} ${fila.last_name}`,
    "city": fila.city,
    "blood_group": fila.blood_group,
    "phone": fila.phone,
  }));
```

{{script:Chronic patients}}

#### Patient ages

Calculates the age on 1 September 2025 and sorts from oldest to youngest.

Keywords: `health`, `dates`, `patients`, `map`, `sort`

```js
const datos = tables["patients"];
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

{{script:Patient ages}}

#### Patients by city

Counts the patients in each city and calculates their percentage over the total.

Keywords: `health`, `aggregates`, `patients`, `map`, `reduce`, `sort`, `math`

```js
const datos = tables["patients"];
const groups = datos.reduce((acc, fila) => {
  const key = fila.city || "—";
  acc[key] = (acc[key] || 0) + 1;
  return acc;
}, {});
const total = datos.length;
return Object.entries(groups)
  .map(([city, count]) => ({
    "city": city,
    "patients": count,
    "percentage": total ? Math.round((count / total) * 1000) / 10 : 0,
  }))
  .sort((a, b) => b["patients"] - a["patients"]);
```

{{script:Patients by city}}

#### Incomplete contacts

Lists the patients who are missing their phone, their e-mail or both.

Keywords: `health`, `filters`, `patients`, `filter`, `map`

```js
const datos = tables["patients"];
return datos
  .filter((fila) => !fila.phone || !fila.email)
  .map((fila) => {
    const missing = [];
    if (!fila.phone) missing.push("phone");
    if (!fila.email) missing.push("email");
    return {
      "full_name": `${fila.name} ${fila.last_name}`,
      "phone": fila.phone,
      "email": fila.email,
      "missing": missing.join(", "),
    };
  });
```

{{script:Incomplete contacts}}

#### Blood groups

Counts the patients by blood group (without the null ones) and calculates the percentage.

Keywords: `health`, `aggregates`, `patients`, `map`, `reduce`, `sort`, `math`

```js
const datos = tables["patients"];
const groups = datos.reduce((acc, fila) => {
  const key = fila.blood_group;
  if (!key) return acc;
  acc[key] = (acc[key] || 0) + 1;
  return acc;
}, {});
const total = Object.values(groups).reduce((a, b) => a + b, 0);
return Object.entries(groups)
  .map(([group, count]) => ({
    "blood_group": group,
    "patients": count,
    "percentage": total ? Math.round((count / total) * 1000) / 10 : 0,
  }))
  .sort((a, b) => b["patients"] - a["patients"]);
```

{{script:Blood groups}}

#### Admissions by reason

Groups admissions by reason, counts how many are emergency and calculates the average daily cost.

Keywords: `health`, `aggregates`, `admissions`, `map`, `reduce`, `sort`, `math`

```js
const datos = tables["admissions"];
const groups = datos.reduce((acc, fila) => {
  const key = fila.reason || "—";
  acc[key] = acc[key] || { count: 0, cost: 0, emergency: 0 };
  acc[key].count += 1;
  acc[key].cost += fila.cost_per_day || 0;
  if (fila.is_emergency) acc[key].emergency += 1;
  return acc;
}, {});
return Object.entries(groups)
  .map(([reason, g]) => ({
    "reason": reason,
    "admissions": g.count,
    "emergency": g.emergency,
    "average_cost": Math.round(g.cost / g.count),
  }))
  .sort((a, b) => b["admissions"] - a["admissions"]);
```

{{script:Admissions by reason}}

#### Billing by payment method

Groups invoices by payment method (including null ones) and adds up amounts and discounts.

Keywords: `health`, `aggregates`, `invoices`, `map`, `reduce`, `sort`, `math`

```js
const datos = tables["invoices"];
const groups = datos.reduce((acc, fila) => {
  const key = fila.payment_method || "—";
  acc[key] = acc[key] || { count: 0, amount: 0, discount: 0 };
  acc[key].count += 1;
  acc[key].amount += fila.amount || 0;
  acc[key].discount += fila.discount || 0;
  return acc;
}, {});
const total = Object.values(groups).reduce((a, b) => a + b.amount, 0);
return Object.entries(groups)
  .map(([method, g]) => ({
    "payment_method": method,
    "invoices": g.count,
    "amount": Math.round(g.amount),
    "average": Math.round(g.amount / g.count),
    "discount": Math.round((g.discount / g.count) * 10) / 10,
    "percentage": total ? Math.round((g.amount / total) * 1000) / 10 : 0,
  }))
  .sort((a, b) => b["amount"] - a["amount"]);
```

{{script:Billing by payment method}}

#### Appointments by status and type (pivot)

Turns appointment types into dynamic columns and counts how many there are per status.

Keywords: `health`, `aggregates`, `appointments`, `map`, `reduce`, `sort`, `foreach`

```js
const datos = tables["appointments"];
const types = Array.from(new Set(datos.map((fila) => fila.type))).sort();
const groups = datos.reduce((acc, fila) => {
  const status = fila.status;
  const type = fila.type;
  acc[status] = acc[status] || { status };
  acc[status][type] = (acc[status][type] || 0) + 1;
  return acc;
}, {});
return Object.values(groups)
  .map((row) => {
    const out = { "status": row.status };
    types.forEach((type) => { out[type] = row[type] || 0; });
    return out;
  })
  .sort((a, b) => String(a["status"]).localeCompare(String(b["status"])));
```

{{script:Appointments by status and type (pivot)}}

#### Abnormal labs by type

Groups labs by type and calculates the percentage of abnormal ones and the average glucose and cholesterol.

Keywords: `health`, `aggregates`, `labs`, `map`, `reduce`, `sort`, `math`

```js
const datos = tables["labs"];
const groups = datos.reduce((acc, fila) => {
  const key = fila.type || "—";
  acc[key] = acc[key] || { count: 0, abnormal: 0, glucose: 0, glucoseCount: 0, cholesterol: 0, cholesterolCount: 0 };
  const g = acc[key];
  g.count += 1;
  if (fila.is_abnormal) g.abnormal += 1;
  if (fila.glucose !== null && fila.glucose !== undefined) { g.glucose += fila.glucose; g.glucoseCount += 1; }
  if (fila.cholesterol !== null && fila.cholesterol !== undefined) { g.cholesterol += fila.cholesterol; g.cholesterolCount += 1; }
  return acc;
}, {});
return Object.entries(groups)
  .map(([type, g]) => ({
    "type": type,
    "count": g.count,
    "abnormal": g.abnormal,
    "percentage": Math.round((g.abnormal / g.count) * 1000) / 10,
    "average_glucose": g.glucoseCount ? Math.round((g.glucose / g.glucoseCount) * 10) / 10 : null,
    "average_cholesterol": g.cholesterolCount ? Math.round((g.cholesterol / g.cholesterolCount) * 10) / 10 : null,
  }))
  .sort((a, b) => b["count"] - a["count"]);
```

{{script:Abnormal labs by type}}

#### Low-stock drugs

Lists the drugs with fewer than 50 units and calculates how much to reorder.

Keywords: `health`, `filters`, `drugs`, `filter`, `map`, `sort`, `math`

```js
const datos = tables["drugs"];
return datos
  .filter((fila) => (fila.stock || 0) < 50)
  .map((fila) => ({
    "drug": fila.name,
    "stock": fila.stock,
    "reorder": Math.max(0, 100 - (fila.stock || 0)),
  }))
  .sort((a, b) => a["stock"] - b["stock"]);
```

{{script:Low-stock drugs}}

#### Patients by age group

Sorts patients into minors, young people, adults and seniors, and calculates their average age.

Keywords: `health`, `dates`, `patients`, `map`, `reduce`, `sort`, `math`

```js
const datos = tables["patients"];
const reference = new Date("2025-09-01");
const groups = datos.reduce((acc, fila) => {
  const birth = new Date(fila.birth_date);
  let age = reference.getFullYear() - birth.getFullYear();
  const m = reference.getMonth() - birth.getMonth();
  if (m < 0 || (m === 0 && reference.getDate() < birth.getDate())) age -= 1;
  const key = age < 18 ? "<18" : age < 40 ? "18-39" : age < 65 ? "40-64" : "65+";
  acc[key] = acc[key] || { count: 0, sum: 0 };
  acc[key].count += 1;
  acc[key].sum += age;
  return acc;
}, {});
return Object.entries(groups)
  .map(([group, g]) => ({
    "age_group": group,
    "patients": g.count,
    "average_age": Math.round((g.sum / g.count) * 10) / 10,
  }))
  .sort((a, b) => String(a["age_group"]).localeCompare(String(b["age_group"])));
```

{{script:Patients by age group}}

#### Appointments per month

Groups appointments by year and month, counts the cancelled ones and sorts chronologically.

Keywords: `health`, `dates`, `appointments`, `map`, `reduce`, `sort`, `slice`

```js
const datos = tables["appointments"];
const groups = datos.reduce((acc, fila) => {
  const key = String(fila.date).slice(0, 7);
  acc[key] = acc[key] || { count: 0, cancelled: 0 };
  acc[key].count += 1;
  if (fila.status === "cancelled") acc[key].cancelled += 1;
  return acc;
}, {});
return Object.entries(groups)
  .map(([month, g]) => ({
    "month": month,
    "appointments": g.count,
    "cancelled": g.cancelled,
  }))
  .sort((a, b) => String(a["month"]).localeCompare(String(b["month"])));
```

{{script:Appointments per month}}

#### JavaScript · Clinical timeline

Merges admissions, appointments, labs and surgeries of the busiest patient into one sorted timeline.

Keywords: `health`, `dates`, `admissions`, `appointments`, `labs`, `filter`, `map`, `sort`, `foreach`

```js
const counts = new Map();
const add = (id) => counts.set(id, (counts.get(id) || 0) + 1);
tables["admissions"].forEach((row) => add(row.id_patient));
tables["appointments"].forEach((row) => add(row.id_patient));
tables["labs"].forEach((row) => add(row.id_patient));
tables["surgeries"].forEach((row) => add(row.id_patient));
const busiest = [...counts.entries()].sort((a, b) => b[1] - a[1] || a[0] - b[0])[0];
const patientId = busiest[0];
const patient = tables["patients"].find((row) => row.id_patient === patientId);
const events = [];
tables["admissions"].filter((row) => row.id_patient === patientId).forEach((row) => {
  events.push({
    "date": row.admission_date,
    "source": "admission",
    "detail": row.reason,
    "status": row.discharge_date ? "discharged" : "active",
  });
});
tables["appointments"].filter((row) => row.id_patient === patientId).forEach((row) => {
  events.push({
    "date": row.date,
    "source": "appointment",
    "detail": `${row.type} · ${row.duration_min} min`,
    "status": row.status,
  });
});
tables["labs"].filter((row) => row.id_patient === patientId).forEach((row) => {
  events.push({
    "date": row.date,
    "source": "lab",
    "detail": `${row.type}: glucose ${row.glucose}`,
    "status": row.is_abnormal ? "abnormal" : "normal",
  });
});
tables["surgeries"].filter((row) => row.id_patient === patientId).forEach((row) => {
  events.push({
    "date": row.date,
    "source": "surgery",
    "detail": row.name,
    "status": row.successful ? "successful" : "unsuccessful",
  });
});
return events
  .sort((a, b) => String(a.date).localeCompare(String(b.date)) || a.source.localeCompare(b.source))
  .map((event, index) => ({
    "order": index + 1,
    "patient": `${patient.last_name}, ${patient.name}`,
    "date": event.date,
    "source": event.source,
    "detail": event.detail,
    "status": event.status,
  }));
```

{{script:JavaScript · Clinical timeline}}

#### JavaScript · Pharmacy cost forecast

Drug price × prescribed units, with stock and the prescriptions behind each figure.

Keywords: `health`, `aggregates`, `drugs`, `prescriptions`, `map`, `sort`, `foreach`, `math`

```js
const drugs = new Map(tables["drugs"].map((row) => [row.id_drug, row]));
const groups = new Map();
tables["prescriptions"].forEach((row) => {
  const drug = drugs.get(row.id_drug);
  if (!drug) return;
  const group = groups.get(row.id_drug) || { drug, prescriptions: 0, units: 0 };
  group.prescriptions += 1;
  group.units += (row.amount || 0) * (row.duration_days || 0);
  groups.set(row.id_drug, group);
});
return [...groups.values()]
  .map((group) => {
    const cost = group.units * (group.drug.price || 0);
    return {
      "drug": group.drug.name,
      "route": group.drug.route,
      "prescriptions": group.prescriptions,
      "units": group.units,
      "forecast_cost": Math.round(cost),
      "stock": group.drug.stock,
      "stock_alert": (group.drug.stock || 0) < 50 ? "low" : "ok",
      "cost_per_unit": group.drug.price,
    };
  })
  .sort((a, b) => b["forecast_cost"] - a["forecast_cost"] || a["drug"].localeCompare(b["drug"]));
```

{{script:JavaScript · Pharmacy cost forecast}}

#### JavaScript · Billing monthly report

Invoices per month with paid and unpaid totals, collection rate and a running total.

Keywords: `health`, `dates`, `invoices`, `map`, `sort`, `foreach`, `math`

```js
const groups = new Map();
tables["invoices"].forEach((row) => {
  const date = new Date(row.date);
  const month = `${date.getFullYear()}-${String(date.getMonth() + 1).padStart(2, "0")}`;
  const group = groups.get(month) || { month, invoices: 0, total: 0, paid: 0, unpaid: 0 };
  group.invoices += 1;
  group.total += row.amount || 0;
  if (row.paid) group.paid += row.amount || 0;
  else group.unpaid += row.amount || 0;
  groups.set(month, group);
});
let cumulative = 0;
return [...groups.values()]
  .sort((a, b) => a.month.localeCompare(b.month))
  .map((group) => {
    cumulative += group.total;
    return {
      "month": group.month,
      "invoices": group.invoices,
      "amount": Math.round(group.total),
      "paid": Math.round(group.paid),
      "unpaid": Math.round(group.unpaid),
      "collection_percent": group.total ? Math.round((group.paid / group.total) * 1000) / 10 : 0,
      "cumulative": Math.round(cumulative),
    };
  });
```

{{script:JavaScript · Billing monthly report}}

### B · Joins

#### Patients by insurer

Crosses patients with insurers, counts how many there are per insurer and shows their coverage.

Keywords: `health`, `joins`, `patients`, `insurers`, `map`, `reduce`, `sort`, `math`

```js
const datos = tables["patients"];
const insurers = new Map(tables["insurers"].map((i) => [i.id_insurer, i]));
const groups = datos.reduce((acc, fila) => {
  const id = fila.id_insurer;
  const insurer = insurers.get(id);
  const name = insurer ? insurer.name : "—";
  acc[name] = acc[name] || { count: 0, coverage: insurer ? insurer.coverage_percent : null };
  acc[name].count += 1;
  return acc;
}, {});
const total = datos.length;
return Object.entries(groups)
  .map(([name, g]) => ({
    "insurer": name,
    "patients": g.count,
    "coverage": g.coverage,
    "percentage": total ? Math.round((g.count / total) * 1000) / 10 : 0,
  }))
  .sort((a, b) => b["patients"] - a["patients"]);
```

{{script:Patients by insurer}}

#### Admissions and cost per hospital

Crosses admissions, rooms and hospitals; counts admissions, emergencies and the estimated cost per stay.

Keywords: `health`, `joins`, `admissions`, `rooms`, `hospitals`, `map`, `reduce`, `sort`, `math`

```js
const datos = tables["admissions"];
const daysBetween = (from, to) => {
  if (!from || !to) return 0;
  return Math.max(0, Math.round((new Date(to) - new Date(from)) / 86400000));
};
const rooms = new Map(tables["rooms"].map((r) => [r.id_room, r]));
const hospitals = new Map(tables["hospitals"].map((h) => [h.id_hospital, h.name]));
const groups = datos.reduce((acc, fila) => {
  const room = rooms.get(fila.id_room);
  const hospital = room ? hospitals.get(room.id_hospital) : null;
  const key = hospital || "—";
  acc[key] = acc[key] || { count: 0, cost: 0, emergency: 0 };
  const days = daysBetween(fila.admission_date, fila.discharge_date);
  acc[key].count += 1;
  acc[key].cost += days * (fila.cost_per_day || 0);
  if (fila.is_emergency) acc[key].emergency += 1;
  return acc;
}, {});
return Object.entries(groups)
  .map(([hospital, g]) => ({
    "hospital": hospital,
    "admissions": g.count,
    "emergency": g.emergency,
    "cost": Math.round(g.cost),
    "average_cost": Math.round(g.cost / g.count),
  }))
  .sort((a, b) => b["cost"] - a["cost"]);
```

{{script:Admissions and cost per hospital}}

#### Cost and days of stay

Calculates the days admitted and the cost of discharged admissions; sorts by cost.

Keywords: `health`, `joins`, `admissions`, `patients`, `filter`, `map`, `sort`, `slice`

```js
const datos = tables["admissions"];
const daysBetween = (from, to) => {
  if (!from || !to) return 0;
  return Math.max(0, Math.round((new Date(to) - new Date(from)) / 86400000));
};
const patients = new Map(tables["patients"].map((p) => [p.id_patient, `${p.name} ${p.last_name}`]));
return datos
  .filter((fila) => fila.discharge_date)
  .map((fila) => {
    const days = daysBetween(fila.admission_date, fila.discharge_date);
    return {
      "patient": patients.get(fila.id_patient) || `#${fila.id_patient}`,
      "reason": fila.reason,
      "days": days,
      "cost": Math.round(days * (fila.cost_per_day || 0)),
    };
  })
  .sort((a, b) => b["cost"] - a["cost"])
  .slice(0, 20);
```

{{script:Cost and days of stay}}

#### Unpaid invoices by patient

Filters unpaid invoices, groups them by patient and adds up what they owe.

Keywords: `health`, `joins`, `invoices`, `patients`, `filter`, `map`, `reduce`, `sort`

```js
const datos = tables["invoices"];
const patients = new Map(tables["patients"].map((p) => [p.id_patient, `${p.name} ${p.last_name}`]));
const groups = datos
  .filter((fila) => fila.paid === false)
  .reduce((acc, fila) => {
    const key = fila.id_patient;
    acc[key] = acc[key] || { count: 0, amount: 0 };
    acc[key].count += 1;
    acc[key].amount += fila.amount || 0;
    return acc;
  }, {});
return Object.entries(groups)
  .map(([id, g]) => ({
    "patient": patients.get(Number(id)) || `#${id}`,
    "invoices": g.count,
    "amount": Math.round(g.amount),
  }))
  .sort((a, b) => b["amount"] - a["amount"]);
```

{{script:Unpaid invoices by patient}}

#### Average appointment duration per doctor

Crosses appointments with doctors, calculates the average duration and returns the top ten.

Keywords: `health`, `joins`, `appointments`, `doctors`, `map`, `reduce`, `sort`, `slice`

```js
const datos = tables["appointments"];
const doctors = new Map(tables["doctors"].map((d) => [d.id_doctor, `${d.name} ${d.last_name}`]));
const groups = datos.reduce((acc, fila) => {
  const id = fila.id_doctor;
  acc[id] = acc[id] || { count: 0, total: 0 };
  acc[id].count += 1;
  acc[id].total += fila.duration_min || 0;
  return acc;
}, {});
return Object.entries(groups)
  .map(([id, g]) => ({
    "doctor": doctors.get(Number(id)) || `#${id}`,
    "appointments": g.count,
    "average_duration": Math.round((g.total / g.count) * 10) / 10,
  }))
  .sort((a, b) => b["average_duration"] - a["average_duration"])
  .slice(0, 10);
```

{{script:Average appointment duration per doctor}}

#### Doctors per specialty

Groups doctors by specialty and calculates how many there are, their average experience and how many research.

Keywords: `health`, `joins`, `doctors`, `specialties`, `map`, `reduce`, `sort`, `math`

```js
const datos = tables["doctors"];
const specialties = new Map(tables["specialties"].map((s) => [s.id_specialty, s.name]));
const groups = datos.reduce((acc, fila) => {
  const key = fila.id_specialty;
  acc[key] = acc[key] || { count: 0, experience: 0, researchers: 0 };
  acc[key].count += 1;
  acc[key].experience += fila.years_experience || 0;
  if (fila.is_researcher) acc[key].researchers += 1;
  return acc;
}, {});
return Object.entries(groups)
  .map(([id, g]) => ({
    "specialty": specialties.get(Number(id)) || `#${id}`,
    "doctors": g.count,
    "average_experience": Math.round((g.experience / g.count) * 10) / 10,
    "researchers": g.researchers,
  }))
  .sort((a, b) => b["doctors"] - a["doctors"]);
```

{{script:Doctors per specialty}}

#### Patients with the most admissions

Groups admissions by patient, adds up their estimated cost and returns the top ten.

Keywords: `health`, `joins`, `admissions`, `patients`, `map`, `reduce`, `sort`, `slice`

```js
const datos = tables["admissions"];
const daysBetween = (from, to) => {
  if (!from || !to) return 0;
  return Math.max(0, Math.round((new Date(to) - new Date(from)) / 86400000));
};
const patients = new Map(tables["patients"].map((p) => [p.id_patient, `${p.name} ${p.last_name}`]));
const groups = datos.reduce((acc, fila) => {
  const id = fila.id_patient;
  acc[id] = acc[id] || { count: 0, cost: 0 };
  const days = daysBetween(fila.admission_date, fila.discharge_date);
  acc[id].count += 1;
  acc[id].cost += days * (fila.cost_per_day || 0);
  return acc;
}, {});
return Object.entries(groups)
  .map(([id, g]) => ({
    "patient": patients.get(Number(id)) || `#${id}`,
    "admissions": g.count,
    "cost": Math.round(g.cost),
  }))
  .sort((a, b) => b["admissions"] - a["admissions"])
  .slice(0, 10);
```

{{script:Patients with the most admissions}}

#### Room occupancy by hospital

Counts rooms, occupied ones and beds per hospital and calculates the occupancy rate.

Keywords: `health`, `joins`, `rooms`, `hospitals`, `map`, `reduce`, `sort`, `math`

```js
const datos = tables["rooms"];
const hospitals = new Map(tables["hospitals"].map((h) => [h.id_hospital, h.name]));
const groups = datos.reduce((acc, fila) => {
  const id = fila.id_hospital;
  acc[id] = acc[id] || { rooms: 0, occupied: 0, beds: 0 };
  acc[id].rooms += 1;
  if (fila.occupied) acc[id].occupied += 1;
  acc[id].beds += fila.num_beds || 0;
  return acc;
}, {});
return Object.entries(groups)
  .map(([id, g]) => ({
    "hospital": hospitals.get(Number(id)) || `#${id}`,
    "rooms": g.rooms,
    "occupied": g.occupied,
    "beds": g.beds,
    "occupancy": Math.round((g.occupied / g.rooms) * 1000) / 10,
  }))
  .sort((a, b) => b["occupancy"] - a["occupancy"]);
```

{{script:Room occupancy by hospital}}

#### Orphan admissions

Finds admissions whose patient, doctor or room no longer exists in the tables.

Keywords: `health`, `joins`, `admissions`, `patients`, `doctors`, `filter`, `map`

```js
const datos = tables["admissions"];
const patientIds = new Set(tables["patients"].map((p) => p.id_patient));
const doctorIds = new Set(tables["doctors"].map((d) => d.id_doctor));
const roomIds = new Set(tables["rooms"].map((r) => r.id_room));
return datos
  .filter((fila) => !patientIds.has(fila.id_patient) || !doctorIds.has(fila.id_doctor) || !roomIds.has(fila.id_room))
  .map((fila) => {
    const missing = [];
    if (!patientIds.has(fila.id_patient)) missing.push("patient");
    if (!doctorIds.has(fila.id_doctor)) missing.push("doctor");
    if (!roomIds.has(fila.id_room)) missing.push("room");
    return {
      "admission": fila.id_admission,
      "patient": fila.id_patient,
      "doctor": fila.id_doctor,
      "room": fila.id_room,
      "missing": missing.join(", "),
    };
  });
```

{{script:Orphan admissions}}

#### Cost by specialty

Crosses admissions with doctors and specialties and calculates the estimated and average cost per specialty.

Keywords: `health`, `joins`, `admissions`, `doctors`, `specialties`, `map`, `reduce`, `sort`, `math`

```js
const datos = tables["admissions"];
const daysBetween = (from, to) => {
  if (!from || !to) return 0;
  return Math.max(0, Math.round((new Date(to) - new Date(from)) / 86400000));
};
const doctors = new Map(tables["doctors"].map((d) => [d.id_doctor, d]));
const specialties = new Map(tables["specialties"].map((s) => [s.id_specialty, s.name]));
const groups = datos.reduce((acc, fila) => {
  const doctor = doctors.get(fila.id_doctor);
  const specialty = doctor ? specialties.get(doctor.id_specialty) : null;
  const key = specialty || "—";
  const days = daysBetween(fila.admission_date, fila.discharge_date);
  acc[key] = acc[key] || { count: 0, cost: 0 };
  acc[key].count += 1;
  acc[key].cost += days * (fila.cost_per_day || 0);
  return acc;
}, {});
return Object.entries(groups)
  .map(([specialty, g]) => ({
    "specialty": specialty,
    "admissions": g.count,
    "average_cost": Math.round(g.cost / g.count),
    "cost": Math.round(g.cost),
  }))
  .sort((a, b) => b["cost"] - a["cost"]);
```

{{script:Cost by specialty}}

#### JavaScript · Patient risk profile

Risk points from chronic conditions, severe allergies, abnormal labs and query C31.

Keywords: `health`, `joins`, `allergies`, `labs`, `patients`, `map`, `sort`, `slice`, `foreach`

```js
const diagnoses = new Map(queries["C31 · Patients with more than one distinct diagnosis"]().map((row) => [row.patient, row.diagnoses]));
const allergies = new Map();
tables["allergies"].forEach((row) => {
  const group = allergies.get(row.id_patient) || { total: 0, severe: 0 };
  group.total += 1;
  if (row.severity === "severe") group.severe += 1;
  allergies.set(row.id_patient, group);
});
const abnormal = new Map();
tables["labs"].forEach((row) => {
  if (!row.is_abnormal) return;
  abnormal.set(row.id_patient, (abnormal.get(row.id_patient) || 0) + 1);
});
return tables["patients"]
  .map((patient) => {
    const allergy = allergies.get(patient.id_patient) || { total: 0, severe: 0 };
    const abnormalLabs = abnormal.get(patient.id_patient) || 0;
    const distinctDiagnoses = diagnoses.get(patient.last_name) || 0;
    const points = (patient.is_chronic ? 2 : 0) + allergy.severe * 2 + allergy.total + abnormalLabs + (distinctDiagnoses >= 4 ? 2 : 0);
    return {
      "patient": `${patient.last_name}, ${patient.name}`,
      "age": Math.floor((new Date("2025-09-01") - new Date(patient.birth_date)) / (365.25 * 86400000)),
      "chronic": patient.is_chronic ? "yes" : "no",
      "allergies": allergy.total,
      "severe_allergies": allergy.severe,
      "abnormal_labs": abnormalLabs,
      "risk_points": points,
      "risk": points >= 6 ? "high" : points >= 3 ? "medium" : "low",
    };
  })
  .sort((a, b) => b["risk_points"] - a["risk_points"] || a["patient"].localeCompare(b["patient"]))
  .slice(0, 20);
```

{{script:JavaScript · Patient risk profile}}

#### JavaScript · Insurance coverage audit

Billed, covered and patient share per insurer, using query B08 for the patient counts.

Keywords: `health`, `joins`, `insurers`, `patients`, `invoices`, `map`, `sort`, `foreach`, `math`

```js
const insurers = new Map(tables["insurers"].map((row) => [row.id_insurer, row]));
const patients = new Map(tables["patients"].map((row) => [row.id_patient, row]));
const counts = new Map(queries["B08 · Patients by insurer"]().map((row) => [row.insurance, row.patients]));
const billing = new Map();
tables["invoices"].forEach((row) => {
  const patient = patients.get(row.id_patient);
  if (!patient) return;
  const key = patient.id_insurer;
  const group = billing.get(key) || { total: 0, invoices: 0, paid: 0 };
  group.total += row.amount || 0;
  group.invoices += 1;
  if (row.paid) group.paid += row.amount || 0;
  billing.set(key, group);
});
return [...insurers.values()]
  .map((insurer) => {
    const bill = billing.get(insurer.id_insurer) || { total: 0, invoices: 0, paid: 0 };
    const covered = bill.total * ((insurer.coverage_percent || 0) / 100);
    return {
      "insurer": insurer.name,
      "type": insurer.type,
      "coverage_percent": insurer.coverage_percent,
      "patients": counts.get(insurer.name) || 0,
      "invoices": bill.invoices,
      "billed": Math.round(bill.total),
      "covered": Math.round(covered),
      "patient_share": Math.round(bill.total - covered),
      "paid": Math.round(bill.paid),
      "unpaid": Math.round(bill.total - bill.paid),
    };
  })
  .sort((a, b) => b["billed"] - a["billed"] || a["insurer"].localeCompare(b["insurer"]));
```

{{script:JavaScript · Insurance coverage audit}}

### C · Related queries

#### Use a saved query

Calls query A19 (patients with three or more admissions) and shows its result.

Keywords: `health`, `reports`, `map`

```js
const rows = queries["A19 · Patients with three or more admissions"]();
return rows.map((row) => ({
  "patient": `${row.name} ${row.last_name}`,
  "admissions": row.admissions,
}));
```

{{script:Use a saved query}}

#### Report with forEach and resultado

Builds a report of paid and unpaid invoices with forEach, adds a TOTAL row and stores it in resultado.

Keywords: `health`, `reports`, `invoices`, `map`, `reduce`, `foreach`, `math`

```js
const datos = tables["invoices"];
const groups = {};
datos.forEach((fila) => {
  const key = fila.paid ? "paid" : "unpaid";
  groups[key] = groups[key] || { count: 0, amount: 0 };
  groups[key].count += 1;
  groups[key].amount += fila.amount || 0;
});
const rows = Object.entries(groups).map(([status, g]) => ({
  "payment_status": status,
  "invoices": g.count,
  "amount": Math.round(g.amount),
  "average": Math.round(g.amount / g.count),
}));
rows.push({
  "payment_status": "TOTAL",
  "invoices": datos.length,
  "amount": Math.round(datos.reduce((sum, fila) => sum + (fila.amount || 0), 0)),
  "average": null,
});
resultado = rows;
```

{{script:Report with forEach and resultado}}

#### JavaScript · Patient directory with Intl

Directory sorted with Intl.Collator, with age, initials, insurer and coverage.

Keywords: `health`, `javascript`, `patients`, `insurers`, `map`, `sort`, `slice`, `intl`

```js
const patients = tables["patients"];
const insurers = new Map(tables["insurers"].map((i) => [i.id_insurer, i]));
const reference = new Date("2025-09-01");
const collator = new Intl.Collator("en", { sensitivity: "base" });
return patients
  .map((fila) => {
    const birth = new Date(fila.birth_date);
    let age = reference.getFullYear() - birth.getFullYear();
    const month = reference.getMonth() - birth.getMonth();
    if (month < 0 || (month === 0 && reference.getDate() < birth.getDate())) age -= 1;
    const email = fila.email ? String(fila.email) : "";
    const at = email.indexOf("@");
    const insurer = insurers.get(fila.id_insurer);
    return {
      "patient": `${fila.last_name}, ${fila.name}`,
      "initials": `${fila.name.charAt(0)}${fila.last_name.charAt(0)}`.toUpperCase(),
      "age": age,
      "city": fila.city,
      "insurer": insurer ? insurer.name : "—",
      "coverage": insurer ? insurer.coverage_percent : null,
      "email_domain": at > 0 ? email.slice(at + 1) : null,
      "chronic": fila.is_chronic ? "yes" : "no",
    };
  })
  .sort((a, b) => collator.compare(a["patient"], b["patient"]));
```

{{script:JavaScript · Patient directory with Intl}}

#### JavaScript · Hospital scorecard

Hospital capacity, services, occupancy and revenue joined with the admissions query C22.

Keywords: `health`, `javascript`, `hospitals`, `services`, `rooms`, `map`, `sort`, `foreach`, `math`

```js
const hospitals = tables["hospitals"];
const services = new Map();
tables["services"].forEach((row) => {
  const list = services.get(row.id_hospital) || [];
  list.push(row.name);
  services.set(row.id_hospital, list);
});
const rooms = new Map(tables["rooms"].map((row) => [row.id_room, row]));
const totals = new Map(hospitals.map((h) => [h.name, { rooms: 0, beds: 0, occupied: 0, cost: 0 }]));
tables["rooms"].forEach((room) => {
  const hospital = hospitals.find((h) => h.id_hospital === room.id_hospital);
  if (!hospital) return;
  const total = totals.get(hospital.name);
  total.rooms += 1;
  total.beds += room.num_beds || 0;
  if (room.occupied) total.occupied += 1;
});
const daysBetween = (from, to) => {
  if (!from || !to) return 0;
  return Math.max(0, Math.round((new Date(to) - new Date(from)) / 86400000));
};
tables["admissions"].forEach((row) => {
  const room = rooms.get(row.id_room);
  const hospital = room ? hospitals.find((h) => h.id_hospital === room.id_hospital) : null;
  if (!hospital) return;
  totals.get(hospital.name).cost += daysBetween(row.admission_date, row.discharge_date) * (row.cost_per_day || 0);
});
const admissions = new Map(queries["C22 · Hospitals with more admissions"]().map((row) => [row.hospital, row.admissions]));
return hospitals
  .map((hospital) => {
    const total = totals.get(hospital.name);
    const count = admissions.get(hospital.name) || 0;
    return {
      "hospital": hospital.name,
      "level": hospital.level,
      "university": hospital.is_university ? "yes" : "no",
      "services": (services.get(hospital.id_hospital) || []).length,
      "rooms": total.rooms,
      "beds": total.beds,
      "occupied_rooms": total.occupied,
      "occupancy_percent": total.rooms ? Math.round((total.occupied / total.rooms) * 1000) / 10 : 0,
      "admissions": count,
      "revenue": Math.round(total.cost),
      "cost_per_admission": count ? Math.round(total.cost / count) : null,
    };
  })
  .sort((a, b) => b["admissions"] - a["admissions"] || a["hospital"].localeCompare(b["hospital"]));
```

{{script:JavaScript · Hospital scorecard}}

#### JavaScript · Doctor leaderboard

Doctors ranked by a score built from appointments, surgeries and the surgery query C16.

Keywords: `health`, `reports`, `doctors`, `appointments`, `map`, `sort`, `slice`, `foreach`

```js
const doctors = new Map(tables["doctors"].map((row) => [row.id_doctor, row]));
const surgeries = new Map(queries["C16 · Doctors with more surgeries"]().map((row) => [row.doctor, row]));
const appointments = new Map();
tables["appointments"].forEach((row) => {
  const group = appointments.get(row.id_doctor) || { count: 0, duration: 0 };
  group.count += 1;
  group.duration += row.duration_min || 0;
  appointments.set(row.id_doctor, group);
});
const rows = [...doctors.values()].map((doctor) => {
  const agenda = appointments.get(doctor.id_doctor) || { count: 0, duration: 0 };
  const surgery = surgeries.get(doctor.last_name) || { surgeries: 0, total_cost: 0 };
  return {
    "doctor": `${doctor.last_name}, ${doctor.name}`,
    "specialty_id": doctor.id_specialty,
    "experience": doctor.years_experience,
    "appointments": agenda.count,
    "average_duration": agenda.count ? Math.round((agenda.duration / agenda.count) * 10) / 10 : 0,
    "surgeries": surgery.surgeries || 0,
    "surgery_cost": Math.round(surgery.total_cost || 0),
    "score": agenda.count * 2 + (surgery.surgeries || 0) * 5 + Math.round((doctor.years_experience || 0) / 2),
  };
});
let previous = null;
let rank = 0;
return rows
  .sort((a, b) => b["score"] - a["score"] || a["doctor"].localeCompare(b["doctor"]))
  .slice(0, 20)
  .map((row, index) => {
    if (row["score"] !== previous) {
      rank = index + 1;
      previous = row["score"];
    }
    return { "rank": rank, ...row };
  });
```

{{script:JavaScript · Doctor leaderboard}}

### I · Parameters

#### P10 · Hospitals for the parameter

Script used as the source of a parameter: it returns the id and the name of every hospital.

Keywords: `health`, `parameters`, `source`, `script`

```js
return tables["hospitals"].map((hospital) => ({
  id_hospital: hospital.id_hospital,
  name: hospital.name,
}));
```

{{script:P10 · Hospitals for the parameter}}

#### P09 · Admissions with a minimum cost (script with a parameter)

Parameterised script: the minimum daily cost is chosen in a drop-down from 200 to 900.

Keywords: `health`, `parameters`, `range`, `script`

```js
const minimumCost = {{ 200..900 | Minimum cost }};
return tables["admissions"]
  .filter((admission) => admission.cost_per_day >= minimumCost)
  .map((admission) => ({
    id_admission: admission.id_admission,
    cost_per_day: admission.cost_per_day,
    is_emergency: admission.is_emergency,
  }));
```

{{script:P09 · Admissions with a minimum cost (script with a parameter)}}

#### P16 · Invoices between two amounts (script with two parameters)

Script parameterised twice: minimum and maximum amount, each with its own drop-down.

Keywords: `health`, `parameters`, `several`, `script`

```js
const minimumAmount = {{ 100..500 | Minimum amount }};
const maximumAmount = {{ 200..1000 | Maximum amount }};
return tables["invoices"]
  .filter((invoice) => invoice.amount >= minimumAmount && invoice.amount <= maximumAmount)
  .map((invoice) => ({
    id_invoice: invoice.id_invoice,
    amount: invoice.amount,
    paid: invoice.paid,
  }));
```

{{script:P16 · Invoices between two amounts (script with two parameters)}}
