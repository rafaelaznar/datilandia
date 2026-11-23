# Sample report · Health

> A **markilandia** sample using the `pruebas-sql-salud.json` project.
> Load that file with the **Data** button before opening or previewing this report.

This document goes through the tables, queries and scripts of the sample project
and shows every embed option: rows, columns, titles, format masks and footer
totals.

## 1. Tables

### Full table

{{table:hospitales}}

### Selected columns, masks and titled totals

{{table:hospitales|columns:nombre,ciudad,pais,nivel,num_camas,anio_fundacion|format:nivel=0;num_camas=#,##0;anio_fundacion=0|title:Hospitals and capacity|totals:sum(num_camas)=Total beds;avg(num_camas)=Average beds;count(*)=Hospitals|countFormat:#,##0}}

### No header, row limit and the average of one column

{{table:especialidades|columns:nombre,area,anios_residencia|rows:6|format:anios_residencia=0|title:none|totals:avg(anios_residencia)=Average residency}}

### All rows and per-column totals

{{table:seguros|columns:nombre,tipo,cobertura_porcentaje,cubre_cirugia|rows:0|format:cobertura_porcentaje=0.0 %|title:Insurance plans|totals:sum(cobertura_porcentaje)=Total coverage;avg(cobertura_porcentaje)=Average coverage;count(*)=Plans}}

### Large table, dates, currency and totals

{{table:facturas|columns:id_factura,fecha,importe,pagada,metodo_pago,descuento|rows:10|format:id_factura=#,##0;fecha=dd/MM/yyyy;importe=#.##0,00 €;descuento=0.0 %|headers:id_factura=No.;fecha=Date;importe=Amount;pagada=Paid;metodo_pago=Payment method;descuento=Discount|highlight:rows=1,3;cols=3|title:Invoices (10 of 60)|totals:sum(importe)=Total billed;avg(importe)=Average invoice;count(*)=Invoices|countFormat:#,##0}}

## 2. Queries

### Simple query, limit, format and count

{{query:A01 · Todas las especialidades|rows:4|format:id_especialidad=#,##0;anios_residencia=0|title:Specialties (4 rows)|totals:count(*)=Rows|countFormat:#,##0}}

### Aggregates with sum, count and average

{{query:A16 · Facturas por método de pago|format:facturas=#,##0;total=#,##0.00 €|title:Billing by payment method|totals:sum(total)=Billed;avg(total)=Average ticket;count(*)=Methods|countFormat:0}}

### Selected columns and sum

{{query:A17 · Citas por estado|columns:estado,citas|format:citas=#,##0|title:Appointments by status|totals:sum(citas)=Appointments}}

### No header and count

{{query:A01 · Todas las especialidades|title:none|totals:count(*)=Rows}}

### All rows and the three operations

{{query:A01 · Todas las especialidades|rows:0|format:id_especialidad=#,##0;anios_residencia=0|title:All specialties|totals}}

## 3. Scripts

### Script with a limit

{{script:Pacientes crónicos|rows:5|title:Chronic patients (5 rows)|totals:count(*)=Rows}}

### Aggregated script with sum, count and average

{{script:Pacientes por ciudad|format:pacientes=#,##0;porcentaje=0.0 %|title:Patients by city|totals:sum(pacientes)=Patients;avg(porcentaje)=Average percentage;count(*)=Cities}}

### Several sums, columns and currency

{{script:Ingresos y coste por hospital|columns:hospital,ingresos,urgentes,coste|format:ingresos=#,##0;urgentes=#,##0;coste=#,##0.00 €|title:Admissions and cost|totals:sum(ingresos)=Admissions;sum(coste)=Cost;count(*)=Hospitals}}

### No header and all rows

{{script:Médicos por especialidad|rows:0|format:medicos=#,##0;experiencia_media=0.0;investigadores=#,##0|title:none|totals:count(*)=Rows}}

## 4. The options

Each embed is written on its own line:

```markdown
{{table:hospitales}}
{{query:A01 · Todas las especialidades}}
{{script:Pacientes crónicos}}
```

Options are added after the name, separated by vertical bars:

- `rows:N`: show at most N rows. The default is 100.
- `rows:0`: show every row in the result.
- `columns:a,b`: show only those columns, in that order.
- `title:Text`: change the grid caption.
- `title:none`: hide the grid header.
- `headers:column=Title`: changes the visible title of that column. `columns`,
  `format` and `totals` keep using the original name.
- `highlight:rows=1,3;cols=2,4`: highlights the indicated rows and columns with a
  background color (1-based, over the rows and columns shown). `highlightRows`
  and `highlightCols` are also accepted.
- `format:column=mask`: apply a mask to that column. Several masks are separated
  with `;`. `format:*=mask` sets the default.
- Numeric masks: `#,##0` (comma thousands), `#,##0.00` (comma thousands and two
  decimal places with a dot), `#.##0,00` (dot thousands and comma decimals),
  `0.0` (one decimal), `#,##0.00 €` (suffix), `0.0 %` (literal percentage) and
  `0.00%` (multiply by 100 before adding the percentage sign).
- `number:...`, `date:...` and `text` force the mask type.
- Dates: `dd/MM/yyyy`, `yyyy-MM-dd`, `dd-MM-yyyy HH:mm`, and so on.
- `countFormat:mask`: format of the counts in the totals.
- `totals:sum(column)=Title;avg(column)=Title;count(*)=Title`: adds one row per
  operation, shows only the specified columns and writes the title and the
  function used. `count(*)` counts every column.
- `totals:sum`, `totals:count`, `totals:avg`, `totals:sum,count,avg` and `totals`
  keep working and apply to every column.

```markdown
{{table:facturas|columns:fecha,importe|format:fecha=dd/MM/yyyy;importe=#.##0,00 €|totals:sum(importe)=Total billed;avg(importe)=Average invoice}}
```

Totals are calculated over the rows shown. Use `rows:0` if you want them to
cover every row in the result.

## 5. Parameters

Queries and scripts can be **parameterised** with the `{{ … }}` notation: it is
written in the SQL (in the `WHERE`, the `HAVING` or any other expression) or in
the code of a script. When they run, **datilandia** and **planilandia** show a
drop-down with the possible values at the top and, when the value changes, they
recalculate the result. The first value is the default one: it is the value used
by the project tests, the views, the scripts that read the query and the
markilandia reports (that is why the tables of this page show it).

The content of `{{ … }}` may be:

- a **range of whole numbers**: `{{ 0..5 }}` offers 0, 1, … 5;
- a **subquery**: `{{ SELECT id_medico FROM medicos WHERE es_investigador = true }}`
  offers one row of the result per option (the first field is the value and the
  rest, its label). With `IN {{ … }}` an extra «All» option uses every value at
  once;
- the **name of a table, a query or a script** of the project, for example
  `{{ P01 · Ciudades de los hospitales (origen del parámetro) }}`.

### Integer range in the WHERE

The floor is chosen between 0 and 5; the report shows the default floor (0):

{{query:P03 · Habitaciones de una planta (rango)|format:num_camas=0;planta=0|title:Floor 0|totals:count(*)=Rooms}}

### Subquery in the WHERE

The researcher doctors come from the doctors table; the default value, «All»,
uses their identifiers at once:

{{query:P04 · Citas por estado de los médicos investigadores (subconsulta)|format:citas=#,##0|title:Appointments by status (all researchers)|totals:sum(citas)=Appointments}}

### Query reference in the IN

The drop-down offers the seven cities of query `P01` with the number of
hospitals of each one:

{{query:P05 · Pacientes de las ciudades de los hospitales (referencia a consulta)|rows:6|title:Patients of the cities with a hospital (6 of 29)|totals:count(*)=Rows}}

### Reference in a NOT IN

The query of the most expensive invoice returns one row only, so it is chosen
with no further options:

{{query:P06 · Facturas salvo la más cara (NOT IN con referencia)|format:id_factura=#,##0;importe=#,##0.00 €|title:Invoices except the most expensive one|totals:sum(importe)=Amount}}

### Range in the HAVING

The minimum number of admissions of every hospital is chosen in a drop-down:

{{query:P07 · Ingresos por hospital (HAVING con rango)|format:ingresos=#,##0|title:Admissions by hospital|totals:sum(ingresos)=Admissions}}

### A script with a parameter

The code of a script accepts the notation too; the minimum daily cost is chosen
between 200 and 900:

{{script:P09 · Ingresos con coste mínimo (script con parámetro)|rows:8|format:id_ingreso=#,##0;coste_dia=#,##0|title:Admissions with cost 200 or more (8 of 49)|totals:avg(coste_dia)=Average cost}}
