# Sample report · Education

> A **markilandia** sample using the `pruebas-sql-educacion.json` project.
> Load that file with the **Data** button before opening or previewing this report.

This document goes through the tables, queries and scripts of the sample project
and shows every embed option: rows, columns, titles, format masks and footer
totals.

## 1. Tables

### Full table

{{table:sedes}}

### Selected columns, masks and titled totals

{{table:sedes|columns:nombre,ciudad,pais,aforo_maximo,anio_fundacion|format:aforo_maximo=#,##0;anio_fundacion=0|headers:nombre=Name;ciudad=City;pais=Country;aforo_maximo=Capacity;anio_fundacion=Founded|highlight:rows=1;cols=4|title:Sedes and capacity|totals:sum(aforo_maximo)=Total capacity;avg(aforo_maximo)=Average capacity;count(*)=Sedes|countFormat:#,##0}}

### No header, row limit and the average of one column

{{table:aulas|columns:codigo,capacidad,tipo_pizarra,planta|rows:6|format:capacidad=#,##0;planta=0|title:none|totals:avg(capacidad)=Average capacity}}

### All rows and the three operations

{{table:estudios|columns:codigo,nombre,nivel,creditos,duracion_anios|rows:0|format:creditos=#,##0;duracion_anios=0|title:Studies|totals}}

### Large table, masks and per-column totals

{{table:matriculas|columns:id_matricula,curso_academico,nota,estado|rows:12|format:id_matricula=#,##0;nota=0.0|title:Enrolments (12 of 48)|totals:sum(nota)=Sum of grades;avg(nota)=Average grade;count(nota)=Graded}}

## 2. Queries

### Simple query, limit, format and count

{{query:A01 · Todas las sedes|rows:2|format:id_sede=#,##0;aforo_maximo=#,##0|title:Sedes (2 rows)|totals:count(*)=Rows|countFormat:#,##0}}

### Aggregates with sum, count and average

{{query:A17 · Matrículas por estado|format:total=#,##0;calificadas=#,##0|title:Enrolments by status|totals:sum(total)=Total;avg(calificadas)=Average graded;count(*)=Statuses|countFormat:0}}

### Selected columns and sum

{{query:A18 · Matrículas por curso académico (HAVING)|columns:curso_academico,total|format:total=#,##0|title:Enrolments by year|totals:sum(total)=Total}}

### No header and count

{{query:A23 · Estudiantes más mayores, página 2 (LIMIT/OFFSET)|title:none|totals:count(*)=Rows}}

### All rows and the three operations

{{query:A01 · Todas las sedes|rows:0|format:id_sede=#,##0;aforo_maximo=#,##0;anio_fundacion=0|title:All sedes|totals}}

## 3. Scripts

### Script with a limit

{{script:Becados con su contacto|rows:5|title:Scholarship holders (5 rows)|totals:count(*)=Rows}}

### Aggregated script with sum, count and average

{{script:Asignaturas por curso|format:curso=0;total=#,##0;creditos=#,##0;media_horas=0.0|title:Subjects by year|totals:sum(creditos)=Credits;avg(media_horas)=Average hours;count(*)=Years}}

### Selected columns, percentage and Spanish aliases

{{script:Distribución de notas|columns:rango,total,porcentaje|format:total=#,##0;porcentaje=0.0 %|title:Grade distribution|totals:sum(total)=Grades;avg(porcentaje)=Average percentage;count(*)=Ranges}}

### No header and all rows

{{script:Becados con su contacto|rows:0|title:none|totals:count(*)=Rows}}

## 4. The options

Each embed is written on its own line:

```markdown
{{table:sedes}}
{{query:A01 · Todas las sedes}}
{{script:Becados con su contacto}}
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
{{table:sedes|columns:nombre,aforo_maximo,anio_fundacion|format:aforo_maximo=#,##0;anio_fundacion=0|totals:sum(aforo_maximo)=Total capacity;avg(aforo_maximo)=Average capacity}}
```

Totals are calculated over the rows shown. Use `rows:0` if you want them to
cover every row in the result.

## 5. Parameters

Queries and scripts can be **parameterised** with the `{{ … }}` notation: it is
written in the SQL (in the `WHERE`, the `HAVING` or any other expression) or in
the code of a script. When they run, **datilandia** and **planilandia** show a
bar of drop-downs **above the results**, right after the pagination bar; each
drop-down carries a **header with the name of the field** it filters or with the
**label** written after a vertical bar (and the
**«All»** option when the source has several rows) and, when the value changes,
they recalculate the result. The bar is not shown until the query or the script
runs. The first value is the default one: it is the value used
by the project tests, the views, the scripts that read the query and the
markilandia reports (that is why the tables of this page show it).

The content of `{{ … }}` may be:

- a **range of whole numbers**: `{{ 50..120 }}` offers 50, 51, … 120;
- a **subquery**: `{{ SELECT curso_academico FROM matriculas GROUP BY curso_academico }}`
  offers one row of the result per option (the first field is the value and the
  rest, its label). With `IN {{ … }}` an extra «All» option uses every value at
  once;
- the **name of a table, a query or a script** of the project, for example
  `{{ P01 · Sedes que dan valores al parámetro }}`.

A **label** may be added to the source after a vertical bar; it is shown as the
header of the drop-down and replaces the name of the field:

```markdown
{{ 50..120 | Minimum capacity }}
{{ SELECT id_sede, nombre FROM sedes | Choose a campus }}
{{ sedes | Campus }}
```

The `||` concatenation operator of SQL is not confused with the separator.

### Integer range in the WHERE

The minimum capacity is chosen between 50 and 120 seats; the report shows the
default value (50):

{{query:P03 · Aulas por capacidad mínima (rango)|format:capacidad=#,##0|title:Minimum capacity 50|totals:count(*)=Classrooms}}

### Subquery in the WHERE

The academic years come from the enrolments themselves; the default value,
«All», uses both years at once:

{{query:P04 · Matrículas del curso elegido (subconsulta)|format:total=#,##0|title:One year per option (all by default)|totals:sum(total)=Enrolments}}

### Query reference in the IN

The drop-down offers the four campuses of query `P01` with their id/name pairs:

{{query:P05 · Estudiantes de las sedes elegidas (referencia a consulta)|rows:6|title:Students of every campus (6 of 24)|totals:count(*)=Rows}}

### Reference in a NOT IN

The query of the main campus returns one row only, so it is chosen with no
further options:

{{query:P06 · Sedes salvo la principal (NOT IN con referencia)|title:The campuses that are not the main one}}

### Range in the HAVING

The minimum number of subjects of every year is chosen in a drop-down:

{{query:P07 · Asignaturas por curso (HAVING con rango)|format:total=#,##0;creditos_medios=0.0|title:Subjects by year|totals:sum(total)=Subjects}}

### A script with a parameter

The code of a script accepts the notation too; the minimum grade is chosen
between 5 and 10:

{{script:P11 · Matrículas con nota mínima (script con parámetro)|rows:8|format:id_matricula=#,##0;nota=0.0|title:Enrolments with grade 5 or more (8 of 32)|totals:avg(nota)=Average grade}}
