# Informe de ejemplo · Salud

> Ejemplo de **markilandia** con el proyecto `pruebas-sql-salud.json`.
> Carga ese archivo con el botón **Datos** antes de abrir o previsualizar este informe.

Este documento recorre las tablas, consultas y scripts del proyecto de ejemplo y
muestra todas las opciones de las inserciones: filas, columnas, títulos, máscaras
de formato y totales al pie.

## 1. Tablas

### Tabla completa

{{table:hospitales}}

### Columnas, máscaras y totales con título

{{table:hospitales|columns:nombre,ciudad,pais,nivel,num_camas,anio_fundacion|format:nivel=0;num_camas=#,##0;anio_fundacion=0|title:Hospitales y capacidad|totals:sum(num_camas)=Camas totales;avg(num_camas)=Camas medias;count(*)=Hospitales|countFormat:#,##0}}

### Sin cabecera, límite de filas y media de una columna

{{table:especialidades|columns:nombre,area,anios_residencia|rows:6|format:anios_residencia=0|title:none|totals:avg(anios_residencia)=Residencia media}}

### Todas las filas y totales por columnas

{{table:seguros|columns:nombre,tipo,cobertura_porcentaje,cubre_cirugia|rows:0|format:cobertura_porcentaje=0.0 %|title:Seguros|totals:sum(cobertura_porcentaje)=Cobertura total;avg(cobertura_porcentaje)=Cobertura media;count(*)=Seguros}}

### Tabla grande, fechas, moneda y totales

{{table:facturas|columns:id_factura,fecha,importe,pagada,metodo_pago,descuento|rows:10|format:id_factura=#,##0;fecha=dd/MM/yyyy;importe=#.##0,00 €;descuento=0.0 %|headers:id_factura=Nº;fecha=Fecha;importe=Importe;pagada=Pagada;metodo_pago=Método de pago;descuento=Descuento|highlight:rows=1,3;cols=3|title:Facturas (10 de 60)|totals:sum(importe)=Total facturado;avg(importe)=Importe medio;count(*)=Facturas|countFormat:#,##0}}

## 2. Consultas

### Consulta simple, límite, formato y recuento

{{query:A01 · Todas las especialidades|rows:4|format:id_especialidad=#,##0;anios_residencia=0|title:Especialidades (4 filas)|totals:count(*)=Filas|countFormat:#,##0}}

### Agregados con suma, recuento y media

{{query:A16 · Facturas por método de pago|format:facturas=#,##0;total=#,##0.00 €|title:Facturación por método de pago|totals:sum(total)=Facturado;avg(total)=Ticket medio;count(*)=Métodos|countFormat:0}}

### Columnas concretas y suma

{{query:A17 · Citas por estado|columns:estado,citas|format:citas=#,##0|title:Citas por estado|totals:sum(citas)=Citas}}

### Sin cabecera y recuento

{{query:A01 · Todas las especialidades|title:none|totals:count(*)=Filas}}

### Todas las filas y las tres operaciones

{{query:A01 · Todas las especialidades|rows:0|format:id_especialidad=#,##0;anios_residencia=0|title:Todas las especialidades|totals}}

## 3. Scripts

### Script con límite

{{script:Pacientes crónicos|rows:5|title:Pacientes crónicos (5 filas)|totals:count(*)=Filas}}

### Script agregado con suma, recuento y media

{{script:Pacientes por ciudad|format:pacientes=#,##0;porcentaje=0.0 %|title:Pacientes por ciudad|totals:sum(pacientes)=Pacientes;avg(porcentaje)=Porcentaje medio;count(*)=Ciudades}}

### Varias sumas, columnas y moneda

{{script:Ingresos y coste por hospital|columns:hospital,ingresos,urgentes,coste|format:ingresos=#,##0;urgentes=#,##0;coste=#,##0.00 €|title:Ingresos y coste|totals:sum(ingresos)=Ingresos;sum(coste)=Coste;count(*)=Hospitales}}

### Sin cabecera y todas las filas

{{script:Médicos por especialidad|rows:0|format:medicos=#,##0;experiencia_media=0.0;investigadores=#,##0|title:none|totals:count(*)=Filas}}

## 4. Las opciones

Cada inserción se escribe en una línea propia:

```markdown
{{table:hospitales}}
{{query:A01 · Todas las especialidades}}
{{script:Pacientes crónicos}}
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
{{table:facturas|columns:fecha,importe|format:fecha=dd/MM/yyyy;importe=#.##0,00 €|totals:sum(importe)=Total facturado;avg(importe)=Importe medio}}
```

Los totales se calculan sobre las filas mostradas. Usa `rows:0` si quieres que
abarquen todas las filas del resultado.

## 5. Parámetros

Las consultas y los scripts pueden ser **parametrizados** con la notación
`{{ … }}`: se escribe en el SQL (en el `WHERE`, el `HAVING` o cualquier otra
expresión) o en el código de un script. Al ejecutarlos, **datilandia** y
**planilandia** muestran una **barra de desplegables encima de los resultados**,
justo después de la barra de paginación; cada desplegable lleva una **cabecera
con el nombre del campo** que filtra o con la **etiqueta** escrita tras una barra
vertical (y la opción **«Todos»** cuando el origen tiene varias filas) y, al cambiar el valor, recalculan el resultado. La barra no
aparece hasta que la consulta o el script se ejecuta. El primer valor es el
valor por defecto: es el que usan las pruebas del proyecto, las vistas, los
scripts que leen la consulta y los informes de markilandia (por eso las tablas
de esta página muestran ese valor).

El contenido de `{{ … }}` puede ser:

- un **rango de enteros**: `{{ 0..5 }}` ofrece 0, 1, … 5;
- una **subconsulta**: `{{ SELECT id_medico FROM medicos WHERE es_investigador = true }}`
  ofrece cada fila del resultado (el primer campo es el valor y el resto, su
  etiqueta). Con `IN {{ … }}` se añade la opción «Todos» para usar de una vez
  todos los valores;
- el **nombre de una tabla, una consulta o un script** del proyecto, por
  ejemplo `{{ P01 · Ciudades de los hospitales (origen del parámetro) }}`.

El origen se puede acompañar de una **etiqueta** tras una barra vertical; se
muestra como cabecera del desplegable y sustituye al nombre del campo:

```markdown
{{ 0..5 | Planta }}
{{ SELECT id_medico FROM medicos WHERE es_investigador = true | Médico }}
{{ hospitales | Hospital }}
```

El operador `||` de concatenación del SQL no se confunde con el separador.

### Rango de enteros en el WHERE

La planta se elige entre 0 y 5; el informe muestra la planta por defecto (0):

{{query:P03 · Habitaciones de una planta (rango)|format:num_camas=0;planta=0|title:Planta 0|totals:count(*)=Habitaciones}}

### Subconsulta en el WHERE

Los médicos investigadores salen de la tabla de médicos; el valor por defecto,
«Todos», usa sus identificadores a la vez:

{{query:P04 · Citas por estado de los médicos investigadores (subconsulta)|format:citas=#,##0|title:Citas por estado (todos los investigadores)|totals:sum(citas)=Citas}}

### Referencia a una consulta en el IN

El desplegable ofrece las siete ciudades de la consulta `P01` con el número de
hospitales de cada una:

{{query:P05 · Pacientes de las ciudades de los hospitales (referencia a consulta)|rows:6|title:Pacientes de las ciudades con hospital (6 de 29)|totals:count(*)=Filas}}

### Referencia en un NOT IN

La consulta de la factura más cara devuelve una sola fila, así que se elige sin
más opciones:

{{query:P06 · Facturas salvo la más cara (NOT IN con referencia)|format:id_factura=#,##0;importe=#,##0.00 €|title:Facturas salvo la más cara|totals:sum(importe)=Importe}}

### Rango en el HAVING

El número mínimo de ingresos de cada hospital se elige en un desplegable:

{{query:P07 · Ingresos por hospital (HAVING con rango)|format:ingresos=#,##0|title:Ingresos por hospital|totals:sum(ingresos)=Ingresos}}

### Un script con parámetro

El código del script también acepta la notación; el coste diario mínimo se elige
entre 200 y 900:

{{script:P09 · Ingresos con coste mínimo (script con parámetro)|rows:8|format:id_ingreso=#,##0;coste_dia=#,##0|title:Ingresos con coste 200 o más (8 de 49)|totals:avg(coste_dia)=Coste medio}}

## 6. Varios parámetros

Una misma consulta puede llevar **varios parámetros**. Cada uno tiene su propio
desplegable y su cabecera, y al cambiar uno se recalcula el resultado con los
valores actuales de los demás. Se pueden mezclar rangos, subconsultas y
referencias a consultas o scripts.

### Dos rangos en un BETWEEN

Coste diario mínimo y máximo, cada uno en su desplegable:

{{query:P11 · Ingresos entre dos costes (dos rangos)|format:id_ingreso=#,##0;coste_dia=#,##0|title:Coste diario entre 200 y 500 (valores por defecto)|totals:avg(coste_dia)=Coste medio}}

### Dos subconsultas: estado y tipo

El estado y el tipo de cita salen de sendas subconsultas:

{{query:P12 · Citas por estado y tipo (dos subconsultas)|format:citas=#,##0|title:Un estado y un tipo|totals:sum(citas)=Citas}}

### Referencia y rango: hospital y coste mínimo

El hospital (lista de un script) y el coste diario mínimo se combinan:

{{query:P15 · Ingresos de un hospital con coste mínimo (referencia y rango)|format:id_ingreso=#,##0;coste_dia=#,##0|title:Hospital y coste mínimo|totals:count(*)=Filas}}

### Un script con dos parámetros

El importe mínimo y el máximo se eligen por separado:

{{script:P16 · Facturas entre dos importes (script con dos parámetros)|format:id_factura=#,##0;importe=#,##0.00 €|title:Importes entre 100 y 200 (valores por defecto)|totals:avg(importe)=Importe medio}}
