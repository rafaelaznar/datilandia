# Informe de ejemplo · Educación

> Ejemplo de **markilandia** con el proyecto `pruebas-sql-educacion.json`.
> Carga ese archivo con el botón **Datos** antes de abrir o previsualizar este informe.

Este documento recorre las tablas, consultas y scripts del proyecto de ejemplo y
muestra todas las opciones de las inserciones: filas, columnas, títulos, máscaras
de formato y totales al pie.

## 1. Tablas

### Tabla completa

{{table:sedes}}

### Columnas, máscaras y totales con título

{{table:sedes|columns:nombre,ciudad,pais,aforo_maximo,anio_fundacion|format:aforo_maximo=#,##0;anio_fundacion=0|headers:nombre=Nombre;ciudad=Ciudad;pais=País;aforo_maximo=Aforo máximo;anio_fundacion=Año de fundación|highlight:rows=1;cols=4|title:Sedes y aforo|totals:sum(aforo_maximo)=Aforo total;avg(aforo_maximo)=Aforo medio;count(*)=Sedes|countFormat:#,##0}}

### Sin cabecera, límite de filas y media de una columna

{{table:aulas|columns:codigo,capacidad,tipo_pizarra,planta|rows:6|format:capacidad=#,##0;planta=0|title:none|totals:avg(capacidad)=Capacidad media}}

### Todas las filas y las tres operaciones

{{table:estudios|columns:codigo,nombre,nivel,creditos,duracion_anios|rows:0|format:creditos=#,##0;duracion_anios=0|title:Estudios|totals}}

### Tabla grande, formato y totales por columnas

{{table:matriculas|columns:id_matricula,curso_academico,nota,estado|rows:12|format:id_matricula=#,##0;nota=0.0|title:Matrículas (12 de 48)|totals:sum(nota)=Suma de notas;avg(nota)=Nota media;count(nota)=Calificadas}}

## 2. Consultas

### Consulta simple, límite, formato y recuento

{{query:A01 · Todas las sedes|rows:2|format:id_sede=#,##0;aforo_maximo=#,##0|title:Sedes (2 filas)|totals:count(*)=Filas|countFormat:#,##0}}

### Agregados con suma, recuento y media

{{query:A17 · Matrículas por estado|format:total=#,##0;calificadas=#,##0|title:Matrículas por estado|totals:sum(total)=Total;avg(calificadas)=Calificadas media;count(*)=Estados|countFormat:0}}

### Columnas concretas y suma

{{query:A18 · Matrículas por curso académico (HAVING)|columns:curso_academico,total|format:total=#,##0|title:Matrículas por curso|totals:sum(total)=Total}}

### Sin cabecera y recuento

{{query:A23 · Estudiantes más mayores, página 2 (LIMIT/OFFSET)|title:none|totals:count(*)=Filas}}

### Todas las filas y las tres operaciones

{{query:A01 · Todas las sedes|rows:0|format:id_sede=#,##0;aforo_maximo=#,##0;anio_fundacion=0|title:Todas las sedes|totals}}

## 3. Scripts

### Script con límite

{{script:Becados con su contacto|rows:5|title:Becados (5 filas)|totals:count(*)=Filas}}

### Script agregado con suma, recuento y media

{{script:Asignaturas por curso|format:curso=0;total=#,##0;creditos=#,##0;media_horas=0.0|title:Asignaturas por curso|totals:sum(creditos)=Créditos;avg(media_horas)=Media de horas;count(*)=Cursos}}

### Columnas, porcentaje y alias en español

{{script:Distribución de notas|columns:rango,total,porcentaje|format:total=#,##0;porcentaje=0.0 %|title:Distribución de notas|totals:sum(total)=Notas;avg(porcentaje)=Porcentaje medio;count(*)=Tramos}}

### Sin cabecera y todas las filas

{{script:Becados con su contacto|rows:0|title:none|totals:count(*)=Filas}}

## 4. Las opciones

Cada inserción se escribe en una línea propia:

```markdown
{{table:sedes}}
{{query:A01 · Todas las sedes}}
{{script:Becados con su contacto}}
```

Las opciones se añaden después del nombre, separadas por barras verticales:

- `rows:N`: muestra como máximo N filas. Por defecto se muestran 100.
- `rows:0`: muestra todas las filas del resultado.
- `columns:a,b`: muestra solo esas columnas, en ese orden.
- `title:Texto`: cambia el rótulo del grid.
- `title:none`: oculta la cabecera del grid.
- `headers:columna=Título`: cambia el título visible de esa columna. `columns`,
  `format` y `totals` siguen usando el nombre original.
- `highlight:rows=1,3;cols=2,4`: resalta con color de fondo las filas y columnas
  indicadas (índices desde 1, sobre las filas y columnas mostradas). También se
  admiten `highlightRows` y `highlightCols`.
- `format:columna=máscara`: aplica una máscara a esa columna. Varias máscaras se
  separan con `;`. `format:*=máscara` establece el valor por defecto.
- Máscaras numéricas: `#,##0` (miles con coma), `#,##0.00` (miles con coma y dos
  decimales con punto), `#.##0,00` (miles con punto y decimales con coma),
  `0.0` (un decimal), `#,##0.00 €` (con sufijo), `0.0 %` (porcentaje literal) y
  `0.00%` (multiplica por 100 antes de añadir el porcentaje).
- `number:...`, `date:...` y `text` fuerzan el tipo de la máscara.
- Fechas: `dd/MM/yyyy`, `yyyy-MM-dd`, `dd-MM-yyyy HH:mm`, etc.
- `countFormat:máscara`: formato de los recuentos de los totales.
- `totals:sum(columna)=Título;avg(columna)=Título;count(*)=Título`: añade una
  fila por operación, muestra solo las columnas indicadas y escribe el título y
  la función usada. `count(*)` cuenta todas las columnas.
- `totals:sum`, `totals:count`, `totals:avg`, `totals:sum,count,avg` y `totals`
  siguen funcionando y se aplican a todas las columnas.

```markdown
{{table:sedes|columns:nombre,aforo_maximo,anio_fundacion|format:aforo_maximo=#,##0;anio_fundacion=0|totals:sum(aforo_maximo)=Aforo total;avg(aforo_maximo)=Aforo medio}}
```

Los totales se calculan sobre las filas mostradas. Usa `rows:0` si quieres que
abarquen todas las filas del resultado.
