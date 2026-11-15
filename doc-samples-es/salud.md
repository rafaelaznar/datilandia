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
