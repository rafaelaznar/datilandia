# Pruebas SQL · Educación (8 tablas, 190 consultas, 32 scripts)

Documento generado a partir de `pruebas-sql-educacion.json`.

**Contenido:** 8 tablas · 190 consultas · 32 scripts.

> Carga `pruebas-sql-educacion.json` con el botón **Datos** de markilandia para que las inserciones de este documento ejecuten cada consulta y cada script y muestren su resultado.

## Consultas

### A · Básicas y agregados

#### A01 · Todas las sedes

SELECT * sobre sedes: columnas en el orden de la tabla y las 4 filas por identificador.

Palabras clave: `educacion`, `basicas`, `sedes`, `todas-las-columnas`

```sql
SELECT * FROM sedes ORDER BY id_sede
```

{{query:A01 · Todas las sedes}}

#### A02 · Proyección con alias y aritmética

Divide el aforo entre 1000 y ordena por aforo descendente: los 4 campus de mayor a menor.

Palabras clave: `educacion`, `basicas`, `sedes`

```sql
SELECT nombre AS sede, ciudad, aforo_maximo / 1000 AS aforo_miles, anio_fundacion FROM sedes ORDER BY aforo_maximo DESC
```

{{query:A02 · Proyección con alias y aritmética}}

#### A03 · Aulas grandes (>= 55 plazas)

Filtro numérico con >= y orden por capacidad descendente y código: 5 aulas.

Palabras clave: `educacion`, `basicas`, `aulas`

```sql
SELECT codigo, capacidad, planta FROM aulas WHERE capacidad >= 55 ORDER BY capacidad DESC, codigo
```

{{query:A03 · Aulas grandes (>= 55 plazas)}}

#### A04 · Aulas con proyector

Filtro booleano (tiene_proyector = true): 10 aulas ordenadas por capacidad.

Palabras clave: `educacion`, `basicas`, `aulas`

```sql
SELECT codigo, capacidad FROM aulas WHERE tiene_proyector = true ORDER BY capacidad DESC, codigo
```

{{query:A04 · Aulas con proyector}}

#### A05 · Docentes no investigadores con carga alta

AND de un booleano (false) y un rango (>= 10 h): 3 docentes.

Palabras clave: `educacion`, `basicas`, `docentes`

```sql
SELECT nombre, apellidos, categoria, horas_semanales FROM docentes WHERE es_investigador = false AND horas_semanales >= 10 ORDER BY horas_semanales DESC, apellidos
```

{{query:A05 · Docentes no investigadores con carga alta}}

#### A06 · Estudiantes sin teléfono

IS NULL sobre un campo con huecos: 8 estudiantes.

Palabras clave: `educacion`, `basicas`, `estudiantes`, `null`

```sql
SELECT nombre, apellidos FROM estudiantes WHERE telefono IS NULL ORDER BY apellidos
```

{{query:A06 · Estudiantes sin teléfono}}

#### A07 · Docentes con despacho (IS NOT NULL) en mayúsculas

IS NOT NULL más UPPER de dos columnas: 9 docentes con despacho, los 3 asociados sin él no salen.

Palabras clave: `educacion`, `basicas`, `docentes`, `null`

```sql
SELECT id_docente, UPPER(nombre) AS nombre, UPPER(apellidos) AS apellidos, despacho FROM docentes WHERE despacho IS NOT NULL ORDER BY id_docente
```

{{query:A07 · Docentes con despacho (IS NOT NULL) en mayúsculas}}

#### A08 · Apellidos que empiezan por G

LIKE con comodín al final: 3 estudiantes llamados García, Gómez y Guerrero.

Palabras clave: `educacion`, `basicas`, `estudiantes`, `like`

```sql
SELECT nombre, apellidos FROM estudiantes WHERE apellidos LIKE 'G%' ORDER BY id_estudiante
```

{{query:A08 · Apellidos que empiezan por G}}

#### A09 · Correos institucionales que no empiezan por a

LIKE y NOT LIKE combinados con LIMIT 5 sobre el correo ordenado.

Palabras clave: `educacion`, `basicas`, `estudiantes`, `like`

```sql
SELECT nombre, apellidos, email FROM estudiantes WHERE email LIKE '%@alu.es' AND email NOT LIKE 'a%' ORDER BY email LIMIT 5
```

{{query:A09 · Correos institucionales que no empiezan por a}}

#### A10 · Tres aulas concretas (IN) y resto de la división

IN con lista de códigos y operador de resto (%): el resto de dividir la capacidad entre 10.

Palabras clave: `educacion`, `basicas`, `aulas`, `in`

```sql
SELECT codigo, capacidad, capacidad % 10 AS resto FROM aulas WHERE codigo IN ('K-101', 'B-SALA', 'M-001') ORDER BY codigo
```

{{query:A10 · Tres aulas concretas (IN) y resto de la división}}

#### A11 · Estudios que no son de informática ni educación (NOT IN)

NOT IN con tres códigos: quedan Ciencia de Datos, Ingeniería del Software y Derecho.

Palabras clave: `educacion`, `basicas`, `estudios`, `in`

```sql
SELECT codigo, nombre, nivel FROM estudios WHERE codigo NOT IN ('GINF', 'GMED', 'GEDU') ORDER BY codigo
```

{{query:A11 · Estudios que no son de informática ni educación (NOT IN)}}

#### A12 · Matrículas aprobadas entre 7 y 8 (BETWEEN)

BETWEEN incluye los extremos: 12 matrículas con nota 7, 7.5 u 8, ordenadas por nota.

Palabras clave: `educacion`, `basicas`, `matriculas`, `between`

```sql
SELECT id_matricula, nota FROM matriculas WHERE nota BETWEEN 7 AND 8 ORDER BY nota, id_matricula
```

{{query:A12 · Matrículas aprobadas entre 7 y 8 (BETWEEN)}}

#### A13 · Asignaturas de primero y segundo con pocos créditos

Paréntesis con OR, un booleano y NOT BETWEEN: 9 asignaturas obligatorias de 1.º y 2.º con menos de 7 créditos.

Palabras clave: `educacion`, `basicas`, `asignaturas`, `between`

```sql
SELECT nombre, curso, creditos FROM asignaturas WHERE (curso = 1 OR curso = 2) AND es_obligatoria = true AND creditos NOT BETWEEN 7 AND 10 ORDER BY nombre
```

{{query:A13 · Asignaturas de primero y segundo con pocos créditos}}

#### A14 · Categorías de docente (DISTINCT)

DISTINCT sobre una columna de texto: 4 categorías profesionales.

Palabras clave: `educacion`, `basicas`, `docentes`, `distinct`

```sql
SELECT DISTINCT categoria FROM docentes ORDER BY categoria
```

{{query:A14 · Categorías de docente (DISTINCT)}}

#### A15 · Combinaciones nivel/oficialidad (DISTINCT de dos columnas)

DISTINCT sobre dos columnas, con booleano incluido: 3 combinaciones.

Palabras clave: `educacion`, `basicas`, `estudios`, `distinct`

```sql
SELECT DISTINCT nivel, es_oficial FROM estudios ORDER BY nivel, es_oficial
```

{{query:A15 · Combinaciones nivel/oficialidad (DISTINCT de dos columnas)}}

#### A16 · Totales del expediente (COUNT, DISTINCT, MIN, MAX, SUM)

Agregados sin GROUP BY: 48 matrículas, 32 calificadas, 21 estudiantes y 14 asignaturas con matrícula.

Palabras clave: `educacion`, `basicas`, `matriculas`, `agregado`, `distinct`

```sql
SELECT COUNT(*) AS matriculas, COUNT(nota) AS con_nota, COUNT(DISTINCT id_estudiante) AS estudiantes, COUNT(DISTINCT id_asignatura) AS asignaturas, MIN(nota) AS nota_minima, MAX(nota) AS nota_maxima, SUM(nota) AS suma_notas FROM matriculas
```

{{query:A16 · Totales del expediente (COUNT, DISTINCT, MIN, MAX, SUM)}}

#### A17 · Matrículas por estado

GROUP BY con dos agregados: las 13 activas y las 3 anuladas no tienen nota, las 32 superadas sí.

Palabras clave: `educacion`, `basicas`, `matriculas`, `agrupacion`, `agregado`

```sql
SELECT estado, COUNT(*) AS total, COUNT(nota) AS calificadas FROM matriculas GROUP BY estado ORDER BY total DESC, estado
```

{{query:A17 · Matrículas por estado}}

#### A18 · Matrículas por curso académico (HAVING)

GROUP BY con HAVING COUNT(*) > 20: los dos cursos superan el umbral.

Palabras clave: `educacion`, `basicas`, `matriculas`, `agrupacion`, `having`, `agregado`

```sql
SELECT curso_academico, COUNT(*) AS total FROM matriculas GROUP BY curso_academico HAVING COUNT(*) > 20 ORDER BY curso_academico
```

{{query:A18 · Matrículas por curso académico (HAVING)}}

#### A19 · Estudiantes con tres o más matrículas

GROUP BY sobre la M:M y HAVING >= 3: 10 estudiantes, uno de ellos con 4 matrículas.

Palabras clave: `educacion`, `basicas`, `matriculas`, `agrupacion`, `having`, `agregado`

```sql
SELECT id_estudiante, COUNT(*) AS matriculas FROM matriculas GROUP BY id_estudiante HAVING COUNT(*) >= 3 ORDER BY matriculas DESC, id_estudiante
```

{{query:A19 · Estudiantes con tres o más matrículas}}

#### A20 · Aulas por inicial del código (GROUP BY con expresión)

GROUP BY sobre SUBSTR: 7 iniciales distintas con su número de aulas y plazas sumadas.

Palabras clave: `educacion`, `basicas`, `aulas`, `agrupacion`, `agregado`

```sql
SELECT SUBSTR(codigo, 1, 1) AS inicial, COUNT(*) AS aulas, SUM(capacidad) AS plazas FROM aulas GROUP BY SUBSTR(codigo, 1, 1) ORDER BY inicial
```

{{query:A20 · Aulas por inicial del código (GROUP BY con expresión)}}

#### A21 · Notas de la asignatura 6, de mayor a menor (NULLS LAST)

ORDER BY ... DESC NULLS LAST: la matrícula anulada sin nota queda al final.

Palabras clave: `educacion`, `basicas`, `matriculas`

```sql
SELECT id_matricula, nota FROM matriculas WHERE id_asignatura = 6 ORDER BY nota DESC NULLS LAST, id_matricula
```

{{query:A21 · Notas de la asignatura 6, de mayor a menor (NULLS LAST)}}

#### A22 · Notas de la asignatura 6, de menor a mayor (nulls primero)

En ASC los null van primero por defecto: la matrícula sin nota encabeza el resultado.

Palabras clave: `educacion`, `basicas`, `matriculas`

```sql
SELECT id_matricula, nota FROM matriculas WHERE id_asignatura = 6 ORDER BY nota ASC, id_matricula
```

{{query:A22 · Notas de la asignatura 6, de menor a mayor (nulls primero)}}

#### A23 · Estudiantes más mayores, página 2 (LIMIT/OFFSET)

Ordena por fecha de nacimiento y salta las dos primeras filas: 4 filas a partir de la tercera.

Palabras clave: `educacion`, `basicas`, `estudiantes`

```sql
SELECT nombre, apellidos, fecha_nacimiento FROM estudiantes ORDER BY fecha_nacimiento LIMIT 4 OFFSET 2
```

{{query:A23 · Estudiantes más mayores, página 2 (LIMIT/OFFSET)}}

#### A24 · Cuatro estudiantes desde el cuarto (LIMIT estilo MySQL)

LIMIT 3, 4 equivale a OFFSET 3 LIMIT 4: devuelve los identificadores 4 a 7.

Palabras clave: `educacion`, `basicas`, `estudiantes`

```sql
SELECT id_estudiante, nombre FROM estudiantes ORDER BY id_estudiante LIMIT 3, 4
```

{{query:A24 · Cuatro estudiantes desde el cuarto (LIMIT estilo MySQL)}}

#### A25 · Ficha de tres estudiantes (concatenación y funciones de texto)

|| concatena, LENGTH cuenta caracteres y SUBSTR recorta: apellidos en mayúsculas y tres iniciales.

Palabras clave: `educacion`, `basicas`, `estudiantes`, `in`

```sql
SELECT UPPER(apellidos) || ', ' || nombre AS ficha, LENGTH(nombre) AS letras, SUBSTR(nombre, 1, 3) AS iniciales FROM estudiantes WHERE id_estudiante IN (1, 9, 14) ORDER BY id_estudiante
```

{{query:A25 · Ficha de tres estudiantes (concatenación y funciones de texto)}}

#### A26 · Notas ausentes: COALESCE, IFNULL, NULLIF y ROUND

Los valores null se sustituyen por -1 y 0; NULLIF convierte «anulada» en null y la distancia a 7.5 propaga el null.

Palabras clave: `educacion`, `basicas`, `matriculas`, `in`

```sql
SELECT id_matricula, COALESCE(nota, -1) AS nota_coalesce, IFNULL(nota, 0) AS nota_ifnull, NULLIF(estado, 'anulada') AS estado_o_null, ROUND(ABS(nota - 7.5), 2) AS distancia FROM matriculas WHERE id_matricula IN (2, 6, 28, 30, 48) ORDER BY id_matricula
```

{{query:A26 · Notas ausentes: COALESCE, IFNULL, NULLIF y ROUND}}

#### A27 · Teléfono de sede normalizado

IFNULL + TRIM + NULLIF: la sede sin teléfono devuelve null en lugar de cadena vacía.

Palabras clave: `educacion`, `basicas`, `sedes`

```sql
SELECT id_sede, NULLIF(TRIM(IFNULL(telefono, '')), '') AS telefono, nombre FROM sedes ORDER BY id_sede
```

{{query:A27 · Teléfono de sede normalizado}}

#### A28 · Pizarras que no son digitales (comentario y corchetes)

El comentario -- se ignora, [codigo] es lo mismo que codigo y <> descarta además los null.

Palabras clave: `educacion`, `basicas`, `aulas`

```sql
-- Aulas con pizarra de tiza (las de pizarra desconocida no cuentan)
SELECT [codigo] AS aula, [tipo_pizarra] AS pizarra, capacidad FROM aulas WHERE tipo_pizarra <> 'digital' ORDER BY codigo
```

{{query:A28 · Pizarras que no son digitales (comentario y corchetes)}}

#### A29 · Docentes que no son asociados (NOT)

NOT sobre una igualdad: quedan 9 de los 12 docentes.

Palabras clave: `educacion`, `basicas`, `docentes`

```sql
SELECT nombre, categoria FROM docentes WHERE NOT (categoria = 'Asociado') ORDER BY id_docente
```

{{query:A29 · Docentes que no son asociados (NOT)}}

#### A30 · Consulta sin resultados

Ninguna aula pasa de 200 plazas: el resultado tiene las columnas y cero filas (rowCount 0).

Palabras clave: `educacion`, `basicas`, `aulas`

```sql
SELECT codigo FROM aulas WHERE capacidad > 200
```

{{query:A30 · Consulta sin resultados}}

#### A31 · Nota media por asignatura (HAVING y ORDER BY por alias)

Solo asignaturas con dos o más notas; se ordena por la media y un empate se rompe por identificador.

Palabras clave: `educacion`, `basicas`, `matriculas`, `agrupacion`, `having`, `agregado`

```sql
SELECT id_asignatura, COUNT(*) AS matriculas, COUNT(nota) AS calificadas, ROUND(AVG(nota), 2) AS nota_media FROM matriculas GROUP BY id_asignatura HAVING COUNT(nota) >= 2 ORDER BY nota_media DESC, id_asignatura
```

{{query:A31 · Nota media por asignatura (HAVING y ORDER BY por alias)}}

#### A32 · Estudiantes y correos por sede

GROUP BY id_sede con COUNT(*) y COUNT(email): 18 de los 24 estudiantes tienen correo.

Palabras clave: `educacion`, `basicas`, `estudiantes`, `agrupacion`, `agregado`

```sql
SELECT id_sede, COUNT(*) AS estudiantes, COUNT(email) AS con_email FROM estudiantes GROUP BY id_sede ORDER BY id_sede
```

{{query:A32 · Estudiantes y correos por sede}}

#### A33 · Fichas con CONCAT (texto y números)

Une nombre y apellidos con CONCAT, y el código de estudio con el identificador: los 7 primeros estudiantes por identificador. El contacto es null cuando no hay correo.

Palabras clave: `educacion`, `basicas`, `estudiantes`

```sql
SELECT CONCAT(nombre, ' ', apellidos) AS estudiante, CONCAT(codigo_estudio, '-', id_estudiante) AS codigo_matricula, CONCAT(codigo_estudio, ' · ', email) AS contacto FROM estudiantes ORDER BY id_estudiante LIMIT 7
```

{{query:A33 · Fichas con CONCAT (texto y números)}}

#### A34 · Totales globales con todas las funciones (sin GROUP BY)

Agregados sin GROUP BY, uno de cada tipo: 48 matrículas, 32 con nota, 21 estudiantes y 3 estados distintos; suma 234, mínimo 5, máximo 10 y media redondeada 7,31.

Palabras clave: `educacion`, `basicas`, `matriculas`, `agregado`, `distinct`

```sql
SELECT COUNT(*) AS matriculas, COUNT(nota) AS calificadas, COUNT(DISTINCT id_estudiante) AS estudiantes, COUNT(DISTINCT estado) AS estados, SUM(nota) AS suma, MIN(nota) AS minima, MAX(nota) AS maxima, ROUND(AVG(nota), 2) AS media FROM matriculas
```

{{query:A34 · Totales globales con todas las funciones (sin GROUP BY)}}

#### A35 · Todas las funciones por estado (GROUP BY)

GROUP BY estado con COUNT(*), COUNT(DISTINCT), SUM, MIN, MAX y AVG: solo las 32 superadas tienen notas; activas y anuladas dan NULL en los agregados de nota.

Palabras clave: `educacion`, `basicas`, `matriculas`, `agrupacion`, `agregado`, `distinct`

```sql
SELECT estado, COUNT(*) AS total, COUNT(DISTINCT id_estudiante) AS estudiantes, SUM(nota) AS suma, MIN(nota) AS minima, MAX(nota) AS maxima, ROUND(AVG(nota), 2) AS media FROM matriculas GROUP BY estado ORDER BY total DESC, estado
```

{{query:A35 · Todas las funciones por estado (GROUP BY)}}

#### A36 · Asignaturas con cuatro matrículas y media alta (HAVING doble)

HAVING con dos agregados a la vez: asignaturas con al menos 4 matrículas y media de 7 o más; cuatro asignaturas cumplen ambos umbrales.

Palabras clave: `educacion`, `basicas`, `matriculas`, `agrupacion`, `having`, `agregado`

```sql
SELECT id_asignatura, COUNT(*) AS matriculas, COUNT(nota) AS calificadas, ROUND(AVG(nota), 2) AS media FROM matriculas GROUP BY id_asignatura HAVING COUNT(*) >= 4 AND AVG(nota) >= 7 ORDER BY media DESC, id_asignatura
```

{{query:A36 · Asignaturas con cuatro matrículas y media alta (HAVING doble)}}

#### A37 · Estudiantes y contactos por sede (COUNT de columnas con NULL)

COUNT(*) cuenta filas y COUNT(columna) solo los valores no nulos: la sede 2 tiene 6 estudiantes pero 4 correos y 3 teléfonos. HAVING deja las sedes con 5 estudiantes o más.

Palabras clave: `educacion`, `basicas`, `estudiantes`, `agrupacion`, `having`, `agregado`

```sql
SELECT id_sede, COUNT(*) AS estudiantes, COUNT(email) AS con_email, COUNT(telefono) AS con_telefono FROM estudiantes GROUP BY id_sede HAVING COUNT(*) >= 5 ORDER BY estudiantes DESC, id_sede
```

{{query:A37 · Estudiantes y contactos por sede (COUNT de columnas con NULL)}}

#### A38 · Docentes por categoría con despachos distintos (HAVING COUNT DISTINCT)

COUNT(despacho) ignora los nulos y COUNT(DISTINCT despacho) cuenta despachos distintos: los tres asociados no tienen despacho y quedan fuera por el HAVING.

Palabras clave: `educacion`, `basicas`, `docentes`, `agrupacion`, `having`, `agregado`, `distinct`

```sql
SELECT categoria, COUNT(*) AS docentes, COUNT(despacho) AS con_despacho, COUNT(DISTINCT despacho) AS despachos FROM docentes GROUP BY categoria HAVING COUNT(DISTINCT despacho) >= 2 ORDER BY despachos DESC, categoria
```

{{query:A38 · Docentes por categoría con despachos distintos (HAVING COUNT DISTINCT)}}

#### A39 · Matrículas por curso y estado (GROUP BY de dos columnas)

Agrupación por dos columnas con HAVING: solo dos combinaciones llegan a 10 matrículas; la de activas no tiene notas y su suma es NULL.

Palabras clave: `educacion`, `basicas`, `matriculas`, `agrupacion`, `having`, `agregado`

```sql
SELECT curso_academico, estado, COUNT(*) AS total, SUM(nota) AS suma FROM matriculas GROUP BY curso_academico, estado HAVING COUNT(*) >= 10 ORDER BY curso_academico, estado
```

{{query:A39 · Matrículas por curso y estado (GROUP BY de dos columnas)}}

#### A40 · Asignaturas con media superior a 7,5 entre las calificadas (WHERE y HAVING)

WHERE quita las matrículas sin nota antes de agrupar y HAVING filtra los grupos por su media: quedan cuatro asignaturas, una con una sola calificación.

Palabras clave: `educacion`, `basicas`, `matriculas`, `agrupacion`, `having`, `agregado`, `null`

```sql
SELECT id_asignatura, COUNT(*) AS matriculas, ROUND(AVG(nota), 2) AS media FROM matriculas WHERE nota IS NOT NULL GROUP BY id_asignatura HAVING AVG(nota) > 7.5 ORDER BY media DESC, id_asignatura
```

{{query:A40 · Asignaturas con media superior a 7,5 entre las calificadas (WHERE y HAVING)}}

#### A41 · Aulas por planta con SUM, MIN y MAX (HAVING SUM)

Por planta, COUNT(*), SUM, MIN y MAX de la capacidad; HAVING deja las plantas que suman 100 plazas o más.

Palabras clave: `educacion`, `basicas`, `aulas`, `agrupacion`, `having`, `agregado`

```sql
SELECT planta, COUNT(*) AS aulas, SUM(capacidad) AS plazas, MIN(capacidad) AS minima, MAX(capacidad) AS maxima FROM aulas GROUP BY planta HAVING SUM(capacidad) >= 100 ORDER BY plazas DESC
```

{{query:A41 · Aulas por planta con SUM, MIN y MAX (HAVING SUM)}}

#### A42 · Horas docentes por categoría con MIN, MAX y AVG (HAVING AVG)

MIN, MAX y AVG redondeado de las horas semanales por categoría; el HAVING exige una media de 10 horas o más.

Palabras clave: `educacion`, `basicas`, `docentes`, `agrupacion`, `having`, `agregado`

```sql
SELECT categoria, COUNT(*) AS docentes, MIN(horas_semanales) AS min_horas, MAX(horas_semanales) AS max_horas, ROUND(AVG(horas_semanales), 1) AS media_horas FROM docentes GROUP BY categoria HAVING AVG(horas_semanales) >= 10 ORDER BY media_horas DESC
```

{{query:A42 · Horas docentes por categoría con MIN, MAX y AVG (HAVING AVG)}}

#### A43 · Estudiantes por año de nacimiento (GROUP BY con expresión y HAVING)

GROUP BY sobre el año de la fecha (SUBSTR) con COUNT(*) y COUNT(telefono); HAVING exige al menos dos estudiantes por año.

Palabras clave: `educacion`, `basicas`, `estudiantes`, `agrupacion`, `having`, `agregado`

```sql
SELECT SUBSTR(fecha_nacimiento, 1, 4) AS anio, COUNT(*) AS estudiantes, COUNT(telefono) AS con_telefono FROM estudiantes GROUP BY SUBSTR(fecha_nacimiento, 1, 4) HAVING COUNT(*) >= 2 ORDER BY anio
```

{{query:A43 · Estudiantes por año de nacimiento (GROUP BY con expresión y HAVING)}}

#### A44 · Agregados sobre la propia columna agrupada (COUNT de estado)

Agregados sobre la columna agrupada y sobre otras: COUNT(estado) coincide con COUNT(*) y COUNT(DISTINCT estado) vale 1; las calificadas solo aparecen en las superadas.

Palabras clave: `educacion`, `basicas`, `matriculas`, `agrupacion`, `agregado`, `distinct`

```sql
SELECT estado, COUNT(*) AS filas, COUNT(estado) AS con_estado, COUNT(DISTINCT estado) AS estados, MAX(id_matricula) AS ultima, COUNT(nota) AS calificadas FROM matriculas GROUP BY estado ORDER BY filas DESC, estado
```

{{query:A44 · Agregados sobre la propia columna agrupada (COUNT de estado)}}

#### A45 · HAVING con agregados que no salen en el SELECT

El HAVING usa COUNT(*) y SUM(nota), que no aparecen en el SELECT: solo las superadas pasan los dos umbrales.

Palabras clave: `educacion`, `basicas`, `matriculas`, `agrupacion`, `having`, `agregado`

```sql
SELECT estado FROM matriculas GROUP BY estado HAVING COUNT(*) >= 13 AND SUM(nota) > 200 ORDER BY estado
```

{{query:A45 · HAVING con agregados que no salen en el SELECT}}

#### A46 · Estudiantes con correo por sede (WHERE, GROUP BY y HAVING)

WHERE filtra antes de agrupar: por sede, COUNT(*) de estudiantes con correo institucional y COUNT(telefono) de los que tienen teléfono; HAVING pide 4 o más.

Palabras clave: `educacion`, `basicas`, `estudiantes`, `agrupacion`, `having`, `agregado`, `like`

```sql
SELECT id_sede, COUNT(*) AS estudiantes, COUNT(telefono) AS con_telefono FROM estudiantes WHERE email LIKE '%@alu.es' GROUP BY id_sede HAVING COUNT(*) >= 4 ORDER BY estudiantes DESC, id_sede
```

{{query:A46 · Estudiantes con correo por sede (WHERE, GROUP BY y HAVING)}}

#### A47 · REGEXPR_REPLACE: espacios, grupos y reemplazo global

Normaliza espacios, reordena un nombre separado por coma con un grupo y reescribe todos los códigos con una expresión regular global.

Palabras clave: `educacion`, `basicas`, `regexpr-replace`, `grupos`, `global`

```sql
SELECT TRIM(REGEXPR_REPLACE('  Campus   Norte  ', '[ ]+', ' ')) AS normalizado,
       REGEXPR_REPLACE('Norte, Campus', '([^,]+), (.+)', '$2 $1') AS reordenado,
       REGEXPR_REPLACE('A-01 / B-02', '[A-Z]-([0-9]+)', 'code-$1') AS codigos
FROM sedes
WHERE id_sede = 1
```

{{query:A47 · REGEXPR_REPLACE: espacios, grupos y reemplazo global}}

#### A48 · REGEXPR_INSTR: posición de la primera coincidencia y banderas

Los tres ejemplos: 123 empieza en 4, sin dígitos da 0 y el ancla ^ coincide en 1. También comprueba la bandera i, el alias REGEXP_INSTR y un texto NULL.

Palabras clave: `educacion`, `regex`, `funciones`, `posicion`

```sql
SELECT REGEXPR_INSTR('abc123def', '[0-9]+') AS digitos, REGEXPR_INSTR('hola', '[0-9]+') AS sin_coincidencia, REGEXPR_INSTR('404 Not Found', '^[0-9]+') AS al_inicio, REGEXPR_INSTR('xxABCyy', 'abc', 'i') AS con_bandera, REGEXPR_INSTR('xxABCyy', 'abc') AS sin_bandera, REGEXP_INSTR('abc123def', '[0-9]+') AS alias, REGEXPR_INSTR(NULL, '[0-9]+') AS texto_nulo FROM sedes LIMIT 1
```

{{query:A48 · REGEXPR_INSTR: posición de la primera coincidencia y banderas}}

#### A49 · REGEXPR_INSTR sobre códigos de aula

Busca el primer dígito de cada código: B-SALA no tiene, mientras que C-LAB1 y K-101 empiezan sus dígitos en 6 y 3.

Palabras clave: `educacion`, `regex`, `funciones`, `aulas`

```sql
SELECT codigo, REGEXPR_INSTR(codigo, '[0-9]+') AS primer_digito FROM aulas WHERE codigo IN ('K-101', 'B-SALA', 'C-LAB1') ORDER BY codigo
```

{{query:A49 · REGEXPR_INSTR sobre códigos de aula}}

### B · Uniones (join)

#### B01 · Sedes con sus edificios (INNER JOIN)

Join 1:M por id_sede: los 8 edificios con el nombre de su sede, ordenados por sede y edificio.

Palabras clave: `educacion`, `uniones`, `sedes`, `edificios`, `join`

```sql
SELECT s.nombre AS sede, e.nombre AS edificio, e.plantas, e.tiene_ascensor FROM sedes AS s INNER JOIN edificios AS e ON e.id_sede = s.id_sede ORDER BY s.id_sede, e.id_edificio
```

{{query:B01 · Sedes con sus edificios (INNER JOIN)}}

#### B02 · Tres tablas: aulas grandes de la sede 1

sedes → edificios → aulas con filtro por sede y capacidad: 3 aulas, de mayor a menor.

Palabras clave: `educacion`, `uniones`, `sedes`, `edificios`, `aulas`, `join`

```sql
SELECT s.nombre AS sede, e.nombre AS edificio, a.codigo, a.capacidad FROM sedes AS s INNER JOIN edificios AS e ON e.id_sede = s.id_sede INNER JOIN aulas AS a ON a.id_edificio = e.id_edificio WHERE s.id_sede = 1 AND a.capacidad >= 45 ORDER BY a.capacidad DESC, a.codigo
```

{{query:B02 · Tres tablas: aulas grandes de la sede 1}}

#### B03 · Sedes sin edificios (LEFT JOIN sin pareja)

LEFT JOIN más IS NULL: las 4 sedes tienen edificios, así que el resultado está vacío.

Palabras clave: `educacion`, `uniones`, `sedes`, `edificios`, `join`, `null`

```sql
SELECT s.nombre AS sede FROM sedes AS s LEFT JOIN edificios AS e ON e.id_sede = s.id_sede WHERE e.id_edificio IS NULL ORDER BY s.id_sede
```

{{query:B03 · Sedes sin edificios (LEFT JOIN sin pareja)}}

#### B03b · Edificios sin aulas

LEFT JOIN con IS NULL: solo el Pabellón Deportivo se queda sin aulas.

Palabras clave: `educacion`, `uniones`, `edificios`, `aulas`, `join`, `null`

```sql
SELECT e.id_edificio, e.nombre FROM edificios AS e LEFT JOIN aulas AS a ON a.id_edificio = e.id_edificio WHERE a.id_aula IS NULL ORDER BY e.id_edificio
```

{{query:B03b · Edificios sin aulas}}

#### B04 · Docentes sin asignaturas

LEFT JOIN docentes → asignaturas con IS NULL: 3 asociados no imparten ninguna asignatura.

Palabras clave: `educacion`, `uniones`, `docentes`, `asignaturas`, `join`, `null`

```sql
SELECT d.id_docente, d.nombre, d.apellidos FROM docentes AS d LEFT JOIN asignaturas AS a ON a.id_docente = d.id_docente WHERE a.id_asignatura IS NULL ORDER BY d.id_docente
```

{{query:B04 · Docentes sin asignaturas}}

#### B05 · Join implícito por comas (aulas de la sede 2)

FROM aulas, edificios con la igualdad en el WHERE: el motor lo resuelve como join.

Palabras clave: `educacion`, `uniones`, `aulas`

```sql
SELECT a.codigo, a.capacidad, e.nombre AS edificio FROM aulas AS a, edificios AS e WHERE a.id_edificio = e.id_edificio AND e.id_sede = 2 ORDER BY a.id_aula
```

{{query:B05 · Join implícito por comas (aulas de la sede 2)}}

#### B06 · Asignaturas y créditos por estudio (join por código de texto)

Join por estudios.codigo = asignaturas.codigo_estudio, con COUNT y SUM por estudio.

Palabras clave: `educacion`, `uniones`, `estudios`, `asignaturas`, `join`, `agrupacion`, `agregado`

```sql
SELECT e.codigo, e.nombre AS estudio, COUNT(a.id_asignatura) AS asignaturas, SUM(a.creditos) AS creditos FROM estudios AS e INNER JOIN asignaturas AS a ON a.codigo_estudio = e.codigo GROUP BY e.codigo, e.nombre ORDER BY creditos DESC, e.codigo
```

{{query:B06 · Asignaturas y créditos por estudio (join por código de texto)}}

#### B07 · Estudios con al menos tres asignaturas (LEFT JOIN + HAVING)

LEFT JOIN con HAVING COUNT >= 3: Grado en Informática, Primaria y Medicina.

Palabras clave: `educacion`, `uniones`, `estudios`, `asignaturas`, `join`, `agrupacion`, `having`, `agregado`

```sql
SELECT e.codigo, e.nombre AS estudio, COUNT(a.id_asignatura) AS asignaturas FROM estudios AS e LEFT JOIN asignaturas AS a ON a.codigo_estudio = e.codigo GROUP BY e.codigo, e.nombre HAVING COUNT(a.id_asignatura) >= 3 ORDER BY asignaturas DESC, e.codigo
```

{{query:B07 · Estudios con al menos tres asignaturas (LEFT JOIN + HAVING)}}

#### B08 · Aulas y plazas por edificio

Agrupación por edificio con COUNT y SUM: el Aulario Pitágoras encabeza con 150 plazas.

Palabras clave: `educacion`, `uniones`, `edificios`, `aulas`, `join`, `agrupacion`, `agregado`

```sql
SELECT e.id_edificio, e.nombre AS edificio, COUNT(a.id_aula) AS aulas, SUM(a.capacidad) AS plazas FROM edificios AS e INNER JOIN aulas AS a ON a.id_edificio = e.id_edificio GROUP BY e.id_edificio, e.nombre ORDER BY plazas DESC, e.id_edificio
```

{{query:B08 · Aulas y plazas por edificio}}

#### B09 · Aulas por edificio, incluidos los que no tienen (LEFT JOIN)

LEFT JOIN con COUNT: el Pabellón Deportivo aparece con 0 aulas.

Palabras clave: `educacion`, `uniones`, `edificios`, `aulas`, `join`, `agrupacion`, `agregado`

```sql
SELECT e.id_edificio, e.nombre AS edificio, COUNT(a.id_aula) AS aulas FROM edificios AS e LEFT JOIN aulas AS a ON a.id_edificio = e.id_edificio GROUP BY e.id_edificio, e.nombre ORDER BY aulas ASC, e.id_edificio
```

{{query:B09 · Aulas por edificio, incluidos los que no tienen (LEFT JOIN)}}

#### B10 · Carga docente: asignaturas y horas presenciales

docentes → asignaturas con COUNT y SUM: Elena y Carmen superan las 145 horas.

Palabras clave: `educacion`, `uniones`, `docentes`, `asignaturas`, `join`, `agrupacion`, `agregado`

```sql
SELECT d.id_docente, d.nombre, COUNT(a.id_asignatura) AS asignaturas, SUM(a.horas_presenciales) AS horas FROM docentes AS d INNER JOIN asignaturas AS a ON a.id_docente = d.id_docente GROUP BY d.id_docente, d.nombre ORDER BY horas DESC, d.id_docente
```

{{query:B10 · Carga docente: asignaturas y horas presenciales}}

#### B11 · Docentes con dos asignaturas y 12 créditos o más (HAVING doble)

HAVING con COUNT y SUM a la vez: solo Carmen, Elena y Marc pasan el corte.

Palabras clave: `educacion`, `uniones`, `docentes`, `asignaturas`, `join`, `agrupacion`, `having`, `agregado`

```sql
SELECT d.id_docente, d.apellidos, COUNT(*) AS asignaturas, SUM(a.creditos) AS creditos FROM docentes AS d INNER JOIN asignaturas AS a ON a.id_docente = d.id_docente GROUP BY d.id_docente, d.apellidos HAVING COUNT(*) >= 2 AND SUM(a.creditos) >= 12 ORDER BY creditos DESC, d.id_docente
```

{{query:B11 · Docentes con dos asignaturas y 12 créditos o más (HAVING doble)}}

#### B12 · Asignaturas sin idioma definido, con estudio y docente

Tres tablas (asignaturas + estudios + docentes) y IS NULL sobre el idioma.

Palabras clave: `educacion`, `uniones`, `asignaturas`, `estudios`, `docentes`, `join`, `null`

```sql
SELECT a.id_asignatura, a.nombre AS asignatura, e.nombre AS estudio, d.apellidos AS docente, a.creditos FROM asignaturas AS a INNER JOIN estudios AS e ON e.codigo = a.codigo_estudio INNER JOIN docentes AS d ON d.id_docente = a.id_docente WHERE a.idioma IS NULL ORDER BY a.id_asignatura
```

{{query:B12 · Asignaturas sin idioma definido, con estudio y docente}}

#### B13 · Estudiantes, correos y teléfonos por sede

LEFT JOIN sedes → estudiantes con tres recuentos: 18 correos y 16 teléfonos en total.

Palabras clave: `educacion`, `uniones`, `sedes`, `estudiantes`, `join`, `agrupacion`, `agregado`

```sql
SELECT s.id_sede, s.nombre AS sede, COUNT(e.id_estudiante) AS estudiantes, COUNT(e.email) AS con_email, COUNT(e.telefono) AS con_telefono FROM sedes AS s LEFT JOIN estudiantes AS e ON e.id_sede = s.id_sede GROUP BY s.id_sede, s.nombre ORDER BY estudiantes DESC, s.id_sede
```

{{query:B13 · Estudiantes, correos y teléfonos por sede}}

#### B14 · Producto cartesiano (6 estudios × 4 sedes)

Sin condición de unión se obtienen todas las combinaciones; con LIMIT 6 se ven las primeras.

Palabras clave: `educacion`, `uniones`, `estudios`

```sql
SELECT e.codigo, s.nombre AS sede FROM estudios AS e, sedes AS s ORDER BY e.codigo, s.id_sede LIMIT 6
```

{{query:B14 · Producto cartesiano (6 estudios × 4 sedes)}}

#### B15 · Las cinco aulas más grandes (JOIN + ORDER BY alias + LIMIT)

Se ordena por el alias de una columna y por el código del aula; el desempate a 55 plazas lo decide el aula.

Palabras clave: `educacion`, `uniones`, `aulas`, `edificios`, `join`

```sql
SELECT a.codigo AS aula, a.capacidad AS plazas, e.nombre AS edificio FROM aulas AS a INNER JOIN edificios AS e ON e.id_edificio = a.id_edificio ORDER BY plazas DESC, aula LIMIT 5
```

{{query:B15 · Las cinco aulas más grandes (JOIN + ORDER BY alias + LIMIT)}}

#### B16 · Docentes distintos por estudio (COUNT DISTINCT)

COUNT(DISTINCT id_docente) por estudio: Informática es el que reparte su docencia entre más profesores.

Palabras clave: `educacion`, `uniones`, `estudios`, `asignaturas`, `join`, `agrupacion`, `agregado`, `distinct`

```sql
SELECT e.codigo, COUNT(DISTINCT a.id_docente) AS docentes_distintos, COUNT(*) AS asignaturas FROM estudios AS e INNER JOIN asignaturas AS a ON a.codigo_estudio = e.codigo GROUP BY e.codigo ORDER BY docentes_distintos DESC, e.codigo
```

{{query:B16 · Docentes distintos por estudio (COUNT DISTINCT)}}

#### B17 · Horas de docencia por categoría (MIN y MAX)

GROUP BY categoria con recuento y extremos: los titulares son los más numerosos.

Palabras clave: `educacion`, `uniones`, `docentes`, `agrupacion`, `agregado`

```sql
SELECT categoria, COUNT(*) AS docentes, MIN(horas_semanales) AS min_horas, MAX(horas_semanales) AS max_horas FROM docentes GROUP BY categoria ORDER BY docentes DESC, categoria
```

{{query:B17 · Horas de docencia por categoría (MIN y MAX)}}

#### B18 · Asignaturas básicas de primero y segundo con su docente

Join docentes → asignaturas con BETWEEN, >= y booleano en el WHERE: 8 asignaturas.

Palabras clave: `educacion`, `uniones`, `docentes`, `asignaturas`, `join`, `between`

```sql
SELECT d.nombre, d.apellidos, a.nombre AS asignatura, a.curso FROM docentes AS d INNER JOIN asignaturas AS a ON a.id_docente = d.id_docente WHERE a.curso BETWEEN 1 AND 2 AND a.creditos >= 6 AND a.es_obligatoria = true ORDER BY a.id_asignatura
```

{{query:B18 · Asignaturas básicas de primero y segundo con su docente}}

#### B19 · Matrículas y nota media por nivel (Grado / Máster)

Cuatro tablas y ROUND(AVG()): los másteres suben la media del grado (43 matrículas frente a 5).

Palabras clave: `educacion`, `uniones`, `estudios`, `asignaturas`, `matriculas`, `join`, `agrupacion`, `agregado`, `distinct`

```sql
SELECT e.nivel, COUNT(DISTINCT m.id_matricula) AS matriculas, ROUND(AVG(m.nota), 2) AS media FROM estudios AS e INNER JOIN asignaturas AS a ON a.codigo_estudio = e.codigo INNER JOIN matriculas AS m ON m.id_asignatura = a.id_asignatura GROUP BY e.nivel ORDER BY media DESC
```

{{query:B19 · Matrículas y nota media por nivel (Grado / Máster)}}

#### B20 · Asignaturas optativas y su estudio

Filtro booleano (es_obligatoria = false) sobre el join estudios ↔ asignaturas: 5 optativas.

Palabras clave: `educacion`, `uniones`, `asignaturas`, `estudios`, `join`

```sql
SELECT a.id_asignatura, a.nombre, e.nombre AS estudio, a.idioma FROM asignaturas AS a INNER JOIN estudios AS e ON e.codigo = a.codigo_estudio WHERE a.es_obligatoria = false ORDER BY a.id_asignatura
```

{{query:B20 · Asignaturas optativas y su estudio}}

#### B21 · Estudiantes de máster con su titulación

Join estudiantes → estudios por el código de texto, filtrando por nivel Máster: 4 estudiantes.

Palabras clave: `educacion`, `uniones`, `estudiantes`, `estudios`, `join`

```sql
SELECT es.id_estudiante, es.nombre, es.apellidos, e.nombre AS estudio, e.nivel FROM estudiantes AS es INNER JOIN estudios AS e ON e.codigo = es.codigo_estudio WHERE e.nivel = 'Máster' ORDER BY es.id_estudiante
```

{{query:B21 · Estudiantes de máster con su titulación}}

#### B22 · Jornada diaria del profesorado investigador

Filtro booleano y ROUND sobre una división, ordenado por horas semanales: 6 investigadores.

Palabras clave: `educacion`, `uniones`, `docentes`

```sql
SELECT d.nombre, d.horas_semanales, ROUND(d.horas_semanales / 5, 1) AS horas_dia FROM docentes AS d WHERE d.es_investigador = true ORDER BY d.horas_semanales DESC, d.id_docente
```

{{query:B22 · Jornada diaria del profesorado investigador}}

### C · Consultas relacionadas

#### C01 · Expediente completo de una estudiante (M:M resuelta)

Tres tablas sobre la M:M: las 4 asignaturas de Ana, con nota y estado.

Palabras clave: `educacion`, `consultas-relacionadas`, `matriculas`, `estudiantes`, `asignaturas`, `join`

```sql
SELECT m.id_matricula, es.apellidos AS estudiante, a.nombre AS asignatura, m.nota, m.estado FROM matriculas AS m INNER JOIN estudiantes AS es ON es.id_estudiante = m.id_estudiante INNER JOIN asignaturas AS a ON a.id_asignatura = m.id_asignatura WHERE m.id_estudiante = 1 ORDER BY m.id_matricula
```

{{query:C01 · Expediente completo de una estudiante (M:M resuelta)}}

#### C02 · Asignaturas con cuatro o más estudiantes (grupo grande)

Recorre la M:M en sentido asignatura → matrículas y agrupa con HAVING >= 4: 8 asignaturas.

Palabras clave: `educacion`, `consultas-relacionadas`, `asignaturas`, `matriculas`, `join`, `agrupacion`, `having`, `agregado`

```sql
SELECT a.id_asignatura, a.nombre AS asignatura, COUNT(DISTINCT m.id_estudiante) AS estudiantes FROM asignaturas AS a INNER JOIN matriculas AS m ON m.id_asignatura = a.id_asignatura GROUP BY a.id_asignatura, a.nombre HAVING COUNT(DISTINCT m.id_estudiante) >= 4 ORDER BY estudiantes DESC, a.id_asignatura
```

{{query:C02 · Asignaturas con cuatro o más estudiantes (grupo grande)}}

#### C03 · Carga de cada estudiante (asignaturas y créditos)

La otra cara de la M:M: 21 estudiantes con matrícula, ordenados por créditos y desempatando por identificador.

Palabras clave: `educacion`, `consultas-relacionadas`, `estudiantes`, `matriculas`, `asignaturas`, `join`, `agrupacion`, `agregado`

```sql
SELECT es.id_estudiante, es.nombre, es.apellidos, COUNT(*) AS asignaturas, SUM(a.creditos) AS creditos FROM estudiantes AS es INNER JOIN matriculas AS m ON m.id_estudiante = es.id_estudiante INNER JOIN asignaturas AS a ON a.id_asignatura = m.id_asignatura GROUP BY es.id_estudiante, es.nombre, es.apellidos ORDER BY creditos DESC, es.id_estudiante
```

{{query:C03 · Carga de cada estudiante (asignaturas y créditos)}}

#### C04 · Notas de un estudiante con COALESCE

COALESCE convierte el null de la asignatura activa en 0 en lugar de dejarlo vacío.

Palabras clave: `educacion`, `consultas-relacionadas`, `matriculas`, `asignaturas`, `join`

```sql
SELECT a.nombre AS asignatura, m.estado, COALESCE(m.nota, 0) AS nota FROM matriculas AS m INNER JOIN asignaturas AS a ON a.id_asignatura = m.id_asignatura WHERE m.id_estudiante = 4 ORDER BY a.id_asignatura
```

{{query:C04 · Notas de un estudiante con COALESCE}}

#### C05 · Matrículas todavía sin nota (IS NULL)

Las seis primeras matrículas sin calificar, con estudiante y asignatura.

Palabras clave: `educacion`, `consultas-relacionadas`, `matriculas`, `estudiantes`, `asignaturas`, `join`, `null`

```sql
SELECT m.id_matricula, es.apellidos AS estudiante, a.nombre AS asignatura, m.estado FROM matriculas AS m INNER JOIN estudiantes AS es ON es.id_estudiante = m.id_estudiante INNER JOIN asignaturas AS a ON a.id_asignatura = m.id_asignatura WHERE m.nota IS NULL ORDER BY m.id_matricula LIMIT 6
```

{{query:C05 · Matrículas todavía sin nota (IS NULL)}}

#### C06 · Matrículas con sobresaliente (nota >= 9)

Seis matrículas de 9 o más, de mayor a menor nota y desempatando por estudiante.

Palabras clave: `educacion`, `consultas-relacionadas`, `matriculas`, `estudiantes`, `asignaturas`, `join`

```sql
SELECT es.apellidos AS estudiante, a.nombre AS asignatura, m.nota FROM matriculas AS m INNER JOIN estudiantes AS es ON es.id_estudiante = m.id_estudiante INNER JOIN asignaturas AS a ON a.id_asignatura = m.id_asignatura WHERE m.nota >= 9 ORDER BY m.nota DESC, es.id_estudiante
```

{{query:C06 · Matrículas con sobresaliente (nota >= 9)}}

#### C07 · Estudiantes sin ninguna matrícula (LEFT JOIN y media nula)

Tres estudiantes no tienen matrículas: COUNT vale 0 y ROUND(AVG()) es null.

Palabras clave: `educacion`, `consultas-relacionadas`, `estudiantes`, `matriculas`, `join`, `agrupacion`, `having`, `agregado`

```sql
SELECT es.id_estudiante, es.nombre, es.apellidos, COUNT(m.id_matricula) AS matriculas, ROUND(AVG(m.nota), 2) AS media FROM estudiantes AS es LEFT JOIN matriculas AS m ON m.id_estudiante = es.id_estudiante GROUP BY es.id_estudiante, es.nombre, es.apellidos HAVING COUNT(m.id_matricula) = 0 ORDER BY es.id_estudiante
```

{{query:C07 · Estudiantes sin ninguna matrícula (LEFT JOIN y media nula)}}

#### C08 · Asignaturas sin ninguna matrícula

LEFT JOIN con IS NULL: Pruebas y Calidad y Derecho Mercantil se quedan sin alumnado.

Palabras clave: `educacion`, `consultas-relacionadas`, `asignaturas`, `matriculas`, `join`, `null`

```sql
SELECT a.id_asignatura, a.nombre, a.codigo_estudio FROM asignaturas AS a LEFT JOIN matriculas AS m ON m.id_asignatura = a.id_asignatura WHERE m.id_matricula IS NULL ORDER BY a.id_asignatura
```

{{query:C08 · Asignaturas sin ninguna matrícula}}

#### C09 · Cobertura: estudiantes con matrícula por estudio

Cuatro tablas (estudios → asignaturas → matrículas → estudiantes) para contar el alumnado real de cada titulación.

Palabras clave: `educacion`, `consultas-relacionadas`, `estudios`, `asignaturas`, `matriculas`, `join`, `agrupacion`, `agregado`, `distinct`

```sql
SELECT e.codigo, COUNT(DISTINCT es.id_estudiante) AS estudiantes FROM estudios AS e INNER JOIN asignaturas AS a ON a.codigo_estudio = e.codigo INNER JOIN matriculas AS m ON m.id_asignatura = a.id_asignatura INNER JOIN estudiantes AS es ON es.id_estudiante = m.id_estudiante GROUP BY e.codigo ORDER BY estudiantes DESC, e.codigo
```

{{query:C09 · Cobertura: estudiantes con matrícula por estudio}}

#### C10 · Nadie cursa asignaturas de dos estudios (solo rowCount)

Assert sin filas esperadas: basta con comprobar que el resultado está vacío (rowCount 0).

Palabras clave: `educacion`, `consultas-relacionadas`, `estudiantes`, `matriculas`, `asignaturas`, `join`, `agrupacion`, `having`, `agregado`

```sql
SELECT es.id_estudiante, COUNT(DISTINCT a.codigo_estudio) AS estudios FROM estudiantes AS es INNER JOIN matriculas AS m ON m.id_estudiante = es.id_estudiante INNER JOIN asignaturas AS a ON a.id_asignatura = m.id_asignatura GROUP BY es.id_estudiante HAVING COUNT(DISTINCT a.codigo_estudio) >= 2 ORDER BY es.id_estudiante
```

{{query:C10 · Nadie cursa asignaturas de dos estudios (solo rowCount)}}

#### C11 · Matrículas convalidadas

Booleano es_convalidada = true sobre la M:M: 6 matrículas, una por estudiante.

Palabras clave: `educacion`, `consultas-relacionadas`, `matriculas`, `estudiantes`, `asignaturas`, `join`

```sql
SELECT m.id_matricula, es.apellidos AS estudiante, a.nombre AS asignatura, e.codigo AS estudio FROM matriculas AS m INNER JOIN estudiantes AS es ON es.id_estudiante = m.id_estudiante INNER JOIN asignaturas AS a ON a.id_asignatura = m.id_asignatura INNER JOIN estudios AS e ON e.codigo = a.codigo_estudio WHERE m.es_convalidada = true ORDER BY m.id_matricula
```

{{query:C11 · Matrículas convalidadas}}

#### C12 · Asignaturas de un estudiante sin importar el orden (ignoreOrder)

El assert no ordena: se comprueba que el conjunto de filas coincide, ignoreOrder true.

Palabras clave: `educacion`, `consultas-relacionadas`, `asignaturas`, `matriculas`, `join`

```sql
SELECT a.nombre AS asignatura FROM asignaturas AS a INNER JOIN matriculas AS m ON m.id_asignatura = a.id_asignatura WHERE m.id_estudiante = 4
```

{{query:C12 · Asignaturas de un estudiante sin importar el orden (ignoreOrder)}}

#### C13 · Asignaturas con más matrículas (ORDER BY de un agregado)

Se ordena por COUNT(*) descendente y se cortan las 5 primeras: tres asignaturas empatan a 5 matrículas.

Palabras clave: `educacion`, `consultas-relacionadas`, `asignaturas`, `matriculas`, `join`, `agrupacion`, `agregado`

```sql
SELECT a.nombre AS asignatura, COUNT(*) AS matriculas FROM asignaturas AS a INNER JOIN matriculas AS m ON m.id_asignatura = a.id_asignatura GROUP BY a.nombre ORDER BY COUNT(*) DESC, a.nombre LIMIT 5
```

{{query:C13 · Asignaturas con más matrículas (ORDER BY de un agregado)}}

#### C14 · Nombres de asignatura más largos (ORDER BY de una función)

Se ordena por LENGTH(nombre) descendente, no por el texto: 5 filas.

Palabras clave: `educacion`, `consultas-relacionadas`, `asignaturas`

```sql
SELECT a.nombre AS asignatura, LENGTH(a.nombre) AS letras FROM asignaturas AS a ORDER BY LENGTH(a.nombre) DESC, a.nombre LIMIT 5
```

{{query:C14 · Nombres de asignatura más largos (ORDER BY de una función)}}

#### C15 · Estudiantes con más créditos matriculados (ORDER BY alias + LIMIT)

Se ordena por el alias creditos: Ana encabeza con 24 créditos y el resto empata a 19.

Palabras clave: `educacion`, `consultas-relacionadas`, `estudiantes`, `matriculas`, `asignaturas`, `join`, `agrupacion`, `agregado`

```sql
SELECT es.id_estudiante, es.apellidos, COUNT(*) AS asignaturas, SUM(a.creditos) AS creditos FROM estudiantes AS es INNER JOIN matriculas AS m ON m.id_estudiante = es.id_estudiante INNER JOIN asignaturas AS a ON a.id_asignatura = m.id_asignatura GROUP BY es.id_estudiante, es.apellidos ORDER BY creditos DESC, es.id_estudiante LIMIT 4
```

{{query:C15 · Estudiantes con más créditos matriculados (ORDER BY alias + LIMIT)}}

#### C16 · Nota media de las asignaturas de Informática

Filtro por código de estudio, agregados y NULLS LAST: la asignatura sin calificar queda al final.

Palabras clave: `educacion`, `consultas-relacionadas`, `asignaturas`, `matriculas`, `join`, `agrupacion`, `agregado`

```sql
SELECT a.nombre AS asignatura, COUNT(m.id_matricula) AS matriculas, COUNT(m.nota) AS calificadas, ROUND(AVG(m.nota), 2) AS media FROM asignaturas AS a INNER JOIN matriculas AS m ON m.id_asignatura = a.id_asignatura WHERE a.codigo_estudio = 'GINF' GROUP BY a.id_asignatura, a.nombre ORDER BY media DESC NULLS LAST, a.id_asignatura
```

{{query:C16 · Nota media de las asignaturas de Informática}}

#### C17 · Becados en Anatomía Humana

Booleano de estudiante y filtro por asignatura sobre la M:M: 2 becadas de las 4 matriculadas.

Palabras clave: `educacion`, `consultas-relacionadas`, `estudiantes`, `matriculas`, `asignaturas`, `join`

```sql
SELECT es.apellidos AS estudiante, es.es_becado, a.nombre AS asignatura FROM estudiantes AS es INNER JOIN matriculas AS m ON m.id_estudiante = es.id_estudiante INNER JOIN asignaturas AS a ON a.id_asignatura = m.id_asignatura WHERE es.es_becado = true AND m.id_asignatura = 5 ORDER BY es.id_estudiante
```

{{query:C17 · Becados en Anatomía Humana}}

#### C18 · Matrículas por estudio y curso académico

GROUP BY con dos claves de texto: 12 combinaciones, en el orden estudio + curso.

Palabras clave: `educacion`, `consultas-relacionadas`, `matriculas`, `asignaturas`, `join`, `agrupacion`, `agregado`

```sql
SELECT a.codigo_estudio, m.curso_academico, COUNT(*) AS matriculas FROM matriculas AS m INNER JOIN asignaturas AS a ON a.id_asignatura = m.id_asignatura GROUP BY a.codigo_estudio, m.curso_academico ORDER BY a.codigo_estudio, m.curso_academico
```

{{query:C18 · Matrículas por estudio y curso académico}}

#### C19 · Asignaturas con sobresalientes

Filtro de nota y GROUP BY: Estructuras de Datos es la única con dos sobresalientes.

Palabras clave: `educacion`, `consultas-relacionadas`, `matriculas`, `asignaturas`, `join`, `agrupacion`, `agregado`

```sql
SELECT a.nombre AS asignatura, COUNT(*) AS sobresalientes FROM matriculas AS m INNER JOIN asignaturas AS a ON a.id_asignatura = m.id_asignatura WHERE m.nota >= 9 GROUP BY a.nombre ORDER BY sobresalientes DESC, a.nombre
```

{{query:C19 · Asignaturas con sobresalientes}}

#### C20 · Media por estudiante de Informática con dos o más notas

Cuatro tablas, IS NOT NULL y HAVING COUNT >= 2: Ana y Sara superan el 7.

Palabras clave: `educacion`, `consultas-relacionadas`, `estudiantes`, `matriculas`, `asignaturas`, `join`, `agrupacion`, `having`, `agregado`

```sql
SELECT es.id_estudiante, es.apellidos, ROUND(AVG(m.nota), 2) AS media FROM estudiantes AS es INNER JOIN matriculas AS m ON m.id_estudiante = es.id_estudiante INNER JOIN asignaturas AS a ON a.id_asignatura = m.id_asignatura INNER JOIN estudios AS e ON e.codigo = a.codigo_estudio WHERE e.codigo = 'GINF' AND m.nota IS NOT NULL GROUP BY es.id_estudiante, es.apellidos HAVING COUNT(*) >= 2 ORDER BY media DESC, es.id_estudiante
```

{{query:C20 · Media por estudiante de Informática con dos o más notas}}

### D · Integridad

#### D01 · Matrículas sin estudiante (integridad de la 1:M)

Anti-join matriculas → estudiantes: ninguna matrícula apunta a un estudiante inexistente.

Palabras clave: `educacion`, `integridad`, `matriculas`, `estudiantes`, `join`, `null`

```sql
SELECT m.id_matricula, m.id_estudiante FROM matriculas AS m LEFT JOIN estudiantes AS es ON es.id_estudiante = m.id_estudiante WHERE es.id_estudiante IS NULL ORDER BY m.id_matricula
```

{{query:D01 · Matrículas sin estudiante (integridad de la 1:M)}}

#### D02 · Matrículas sin asignatura (integridad de la otra 1:M)

Anti-join matriculas → asignaturas: la M:M no tiene referencias colgando.

Palabras clave: `educacion`, `integridad`, `matriculas`, `asignaturas`, `join`, `null`

```sql
SELECT m.id_matricula, m.id_asignatura FROM matriculas AS m LEFT JOIN asignaturas AS a ON a.id_asignatura = m.id_asignatura WHERE a.id_asignatura IS NULL ORDER BY m.id_matricula
```

{{query:D02 · Matrículas sin asignatura (integridad de la otra 1:M)}}

#### D03 · Asignaturas sin estudio (integridad del código de texto)

Anti-join asignaturas → estudios por codigo_estudio = codigo: las 16 asignaturas tienen estudio.

Palabras clave: `educacion`, `integridad`, `asignaturas`, `estudios`, `join`, `null`

```sql
SELECT a.id_asignatura, a.codigo_estudio FROM asignaturas AS a LEFT JOIN estudios AS e ON e.codigo = a.codigo_estudio WHERE e.id_estudio IS NULL ORDER BY a.id_asignatura
```

{{query:D03 · Asignaturas sin estudio (integridad del código de texto)}}

#### D04 · Asignaturas sin docente

Anti-join asignaturas → docentes: todas las asignaturas tienen profesor asignado.

Palabras clave: `educacion`, `integridad`, `asignaturas`, `docentes`, `join`, `null`

```sql
SELECT a.id_asignatura, a.id_docente FROM asignaturas AS a LEFT JOIN docentes AS d ON d.id_docente = a.id_docente WHERE d.id_docente IS NULL ORDER BY a.id_asignatura
```

{{query:D04 · Asignaturas sin docente}}

#### D05 · Aulas sin edificio

Anti-join aulas → edificios: ningún aula queda fuera de un edificio.

Palabras clave: `educacion`, `integridad`, `aulas`, `edificios`, `join`, `null`

```sql
SELECT a.id_aula, a.id_edificio FROM aulas AS a LEFT JOIN edificios AS e ON e.id_edificio = a.id_edificio WHERE e.id_edificio IS NULL ORDER BY a.id_aula
```

{{query:D05 · Aulas sin edificio}}

#### D06 · Edificios sin sede

Anti-join edificios → sedes: los 8 edificios cuelgan de una sede existente.

Palabras clave: `educacion`, `integridad`, `edificios`, `sedes`, `join`, `null`

```sql
SELECT e.id_edificio, e.id_sede FROM edificios AS e LEFT JOIN sedes AS s ON s.id_sede = e.id_sede WHERE s.id_sede IS NULL ORDER BY e.id_edificio
```

{{query:D06 · Edificios sin sede}}

#### D07 · Estudiantes sin sede o sin estudio

Dos anti-joins a la vez con OR: todo el alumnado tiene sede y titulación válidas.

Palabras clave: `educacion`, `integridad`, `estudiantes`, `sedes`, `estudios`, `join`, `null`

```sql
SELECT es.id_estudiante FROM estudiantes AS es LEFT JOIN sedes AS s ON s.id_sede = es.id_sede LEFT JOIN estudios AS e ON e.codigo = es.codigo_estudio WHERE s.id_sede IS NULL OR e.id_estudio IS NULL ORDER BY es.id_estudiante
```

{{query:D07 · Estudiantes sin sede o sin estudio}}

#### D08 · Matrículas repetidas del mismo par estudiante/asignatura

GROUP BY + HAVING sobre la M:M: no hay pares duplicados, la clave natural es única.

Palabras clave: `educacion`, `integridad`, `matriculas`, `agrupacion`, `having`, `agregado`

```sql
SELECT m.id_estudiante, m.id_asignatura, COUNT(*) AS veces FROM matriculas AS m GROUP BY m.id_estudiante, m.id_asignatura HAVING COUNT(*) > 1
```

{{query:D08 · Matrículas repetidas del mismo par estudiante/asignatura}}

#### D09 · Notas fuera del rango 0–10

Regla de negocio sobre el rango de las notas: ninguna fuera de 0–10.

Palabras clave: `educacion`, `integridad`, `matriculas`, `null`

```sql
SELECT id_matricula, nota FROM matriculas WHERE nota IS NOT NULL AND (nota < 0 OR nota > 10)
```

{{query:D09 · Notas fuera del rango 0–10}}

#### D10 · Estados de matrícula inventados

NOT IN contra la lista de estados permitidos: solo existen activa, superada y anulada.

Palabras clave: `educacion`, `integridad`, `matriculas`, `in`

```sql
SELECT id_matricula, estado FROM matriculas WHERE estado NOT IN ('activa', 'superada', 'anulada') ORDER BY id_matricula
```

{{query:D10 · Estados de matrícula inventados}}

#### D11 · Matrículas superadas sin nota

Regla «superada ⇒ nota informada»: ninguna superada se queda sin calificación.

Palabras clave: `educacion`, `integridad`, `matriculas`, `null`

```sql
SELECT id_matricula, estado, nota FROM matriculas WHERE estado = 'superada' AND nota IS NULL ORDER BY id_matricula
```

{{query:D11 · Matrículas superadas sin nota}}

#### D12 · Matrículas calificadas que no están superadas

La regla inversa: si hay nota, la matrícula está superada (activas y anuladas no se califican).

Palabras clave: `educacion`, `integridad`, `matriculas`, `null`

```sql
SELECT id_matricula, estado, nota FROM matriculas WHERE nota IS NOT NULL AND estado <> 'superada' ORDER BY id_matricula
```

{{query:D12 · Matrículas calificadas que no están superadas}}

#### D13 · Aulas sin plazas

Regla de negocio: toda aula oferta al menos una plaza.

Palabras clave: `educacion`, `integridad`, `aulas`

```sql
SELECT id_aula, codigo, capacidad FROM aulas WHERE capacidad <= 0 ORDER BY id_aula
```

{{query:D13 · Aulas sin plazas}}

### E · Vistas

#### E01 · Vista sencilla: SELECT * de otra consulta

El resultado de A03 se lee como tabla: las 5 aulas grandes con sus tres columnas (codigo, capacidad, planta), ordenadas por capacidad.

Palabras clave: `educacion`, `vistas`, `todas-las-columnas`

```sql
SELECT * FROM [A03 · Aulas grandes (>= 55 plazas)] ORDER BY capacidad DESC, codigo
```

{{query:E01 · Vista sencilla: SELECT * de otra consulta}}

#### E02 · Aritmética sobre columnas de una vista

La capacidad sigue siendo un número después de pasar por la vista A03: se puede dividir entre 10 y calcular el resto.

Palabras clave: `educacion`, `vistas`

```sql
SELECT codigo AS aula, capacidad, capacidad / 10 AS decenas, capacidad % 10 AS resto FROM [A03 · Aulas grandes (>= 55 plazas)] ORDER BY capacidad DESC, codigo
```

{{query:E02 · Aritmética sobre columnas de una vista}}

#### E03 · WHERE y DISTINCT sobre una vista

Filtra la vista A03 por capacidad >= 60 y deja las plantas distintas: quedan las plantas 1 y 3.

Palabras clave: `educacion`, `vistas`, `distinct`

```sql
SELECT DISTINCT planta FROM [A03 · Aulas grandes (>= 55 plazas)] WHERE capacidad >= 60 ORDER BY planta
```

{{query:E03 · WHERE y DISTINCT sobre una vista}}

#### E04 · Agregados sobre una vista

Cinco agregados en una fila sobre el resultado de A03: 5 aulas, 320 plazas, de 55 a 80 y media 64.

Palabras clave: `educacion`, `vistas`, `agregado`

```sql
SELECT COUNT(*) AS aulas, SUM(capacidad) AS plazas, MIN(capacidad) AS minima, MAX(capacidad) AS maxima, ROUND(AVG(capacidad), 2) AS media FROM [A03 · Aulas grandes (>= 55 plazas)]
```

{{query:E04 · Agregados sobre una vista}}

#### E05 · Plantas con dos o más aulas grandes

Agrupa la vista A03 por planta y se queda con las plantas con dos o más aulas: solo la planta 1 (4 aulas, 240 plazas).

Palabras clave: `educacion`, `vistas`, `agrupacion`, `having`, `agregado`

```sql
SELECT planta, COUNT(*) AS aulas, SUM(capacidad) AS plazas FROM [A03 · Aulas grandes (>= 55 plazas)] GROUP BY planta HAVING COUNT(*) >= 2 ORDER BY planta
```

{{query:E05 · Plantas con dos o más aulas grandes}}

#### E06 · Vista unida a una tabla base (INNER JOIN)

La vista C02 unida a la tabla asignaturas por id_asignatura: las 8 asignaturas con cuatro o más estudiantes, con su curso y sus créditos.

Palabras clave: `educacion`, `vistas`, `asignaturas`, `join`

```sql
SELECT c.asignatura, c.estudiantes, a.curso, a.creditos FROM [C02 · Asignaturas con cuatro o más estudiantes (grupo grande)] AS c INNER JOIN asignaturas AS a ON a.id_asignatura = c.id_asignatura ORDER BY c.estudiantes DESC, c.id_asignatura
```

{{query:E06 · Vista unida a una tabla base (INNER JOIN)}}

#### E07 · Dos vistas unidas por asignatura

Las vistas C02 y A31 unidas por id_asignatura: las 6 asignaturas grandes que además tienen nota media calculada.

Palabras clave: `educacion`, `vistas`, `join`, `having`

```sql
SELECT c.asignatura, c.estudiantes, n.nota_media FROM [C02 · Asignaturas con cuatro o más estudiantes (grupo grande)] AS c INNER JOIN [A31 · Nota media por asignatura (HAVING y ORDER BY por alias)] AS n ON n.id_asignatura = c.id_asignatura ORDER BY c.estudiantes DESC, c.id_asignatura
```

{{query:E07 · Dos vistas unidas por asignatura}}

#### E08 · Tabla LEFT JOIN vista con IS NULL

LEFT JOIN de la tabla edificios contra la vista B08 más IS NULL: el único edificio sin aulas, el Pabellón Deportivo.

Palabras clave: `educacion`, `vistas`, `edificios`, `join`, `null`

```sql
SELECT e.id_edificio, e.nombre AS edificio FROM edificios AS e LEFT JOIN [B08 · Aulas y plazas por edificio] AS v ON v.id_edificio = e.id_edificio WHERE v.aulas IS NULL ORDER BY e.id_edificio
```

{{query:E08 · Tabla LEFT JOIN vista con IS NULL}}

#### E09 · Vista de vista (dos niveles de consulta)

Lee la vista E05, que a su vez lee la vista A03: tres niveles de consulta encadenados y una sola fila (planta 1, 240 plazas).

Palabras clave: `educacion`, `vistas`

```sql
SELECT planta, aulas, plazas FROM [E05 · Plantas con dos o más aulas grandes] WHERE plazas >= 100 ORDER BY plazas DESC
```

{{query:E09 · Vista de vista (dos niveles de consulta)}}

#### E10 · La misma vista dos veces (auto-unión)

La vista A03 aparece dos veces con alias distintos y se une consigo misma por capacidad: solo P-101 y P-102 empatan a 55 plazas.

Palabras clave: `educacion`, `vistas`, `join`

```sql
SELECT a.codigo AS aula_1, b.codigo AS aula_2, a.capacidad FROM [A03 · Aulas grandes (>= 55 plazas)] AS a INNER JOIN [A03 · Aulas grandes (>= 55 plazas)] AS b ON b.capacidad = a.capacidad AND b.codigo > a.codigo ORDER BY a.capacidad DESC, a.codigo, b.codigo
```

{{query:E10 · La misma vista dos veces (auto-unión)}}

#### E11 · Join implícito por comas con una vista

Producto cartesiano de la vista B06 con la tabla sedes, filtrado por la sede 1: los 6 estudios con sus créditos, de más a menos.

Palabras clave: `educacion`, `vistas`, `join`

```sql
SELECT c.codigo, c.estudio, c.creditos, s.nombre AS sede FROM [B06 · Asignaturas y créditos por estudio (join por código de texto)] AS c, sedes AS s WHERE s.id_sede = 1 ORDER BY c.creditos DESC, c.codigo
```

{{query:E11 · Join implícito por comas con una vista}}

#### E12 · ORDER BY por alias y LIMIT sobre una vista

Ordena por el alias materia y recorta a 3 filas la vista C02: las tres asignaturas con 5 estudiantes.

Palabras clave: `educacion`, `vistas`

```sql
SELECT asignatura AS materia, estudiantes FROM [C02 · Asignaturas con cuatro o más estudiantes (grupo grande)] ORDER BY estudiantes DESC, asignatura LIMIT 3
```

{{query:E12 · ORDER BY por alias y LIMIT sobre una vista}}

#### E13 · Booleano a través de una vista

El booleano es_principal viaja intacto desde la tabla sedes hasta la vista A01: solo el Campus Norte es la sede principal.

Palabras clave: `educacion`, `vistas`

```sql
SELECT nombre AS sede, es_principal FROM [A01 · Todas las sedes] WHERE es_principal = true ORDER BY id_sede
```

{{query:E13 · Booleano a través de una vista}}

#### E14 · Nulos a través de una vista (COALESCE)

El teléfono ausente de la sede 3 sigue siendo nulo tras leer la vista A01, y COALESCE lo sustituye por «sin teléfono».

Palabras clave: `educacion`, `vistas`

```sql
SELECT nombre AS sede, COALESCE(telefono, 'sin teléfono') AS contacto FROM [A01 · Todas las sedes] ORDER BY id_sede
```

{{query:E14 · Nulos a través de una vista (COALESCE)}}

#### E15 · Agregados sobre una vista ya agregada

Resume la vista ya agregada A32 (una fila por sede): 4 sedes, 24 estudiantes y entre 3 y 9 por sede.

Palabras clave: `educacion`, `vistas`, `agregado`

```sql
SELECT COUNT(*) AS sedes, SUM(estudiantes) AS estudiantes, MIN(estudiantes) AS minimo, MAX(estudiantes) AS maximo FROM [A32 · Estudiantes y correos por sede]
```

{{query:E15 · Agregados sobre una vista ya agregada}}

#### E16 · Vista que lee una consulta con LIMIT

La vista C13 lleva LIMIT 5: al leerla como tabla llegan solo esas 5 filas (23 matrículas en total).

Palabras clave: `educacion`, `vistas`, `agregado`

```sql
SELECT COUNT(*) AS filas, SUM(matriculas) AS matriculas FROM [C13 · Asignaturas con más matrículas (ORDER BY de un agregado)]
```

{{query:E16 · Vista que lee una consulta con LIMIT}}

#### E17 · Vista de un resultado vacío

La vista C10 no devuelve filas: leerla como tabla da una tabla vacía, y COUNT(*) devuelve 0 en lugar de un error.

Palabras clave: `educacion`, `vistas`, `agregado`

```sql
SELECT COUNT(*) AS filas FROM [C10 · Nadie cursa asignaturas de dos estudios (solo rowCount)]
```

{{query:E17 · Vista de un resultado vacío}}

#### E18 · LIMIT y OFFSET sobre una vista

LIMIT 3 OFFSET 2 sobre las 5 filas de la vista A03: la tercera, cuarta y quinta aulas por capacidad.

Palabras clave: `educacion`, `vistas`

```sql
SELECT codigo, capacidad FROM [A03 · Aulas grandes (>= 55 plazas)] ORDER BY capacidad DESC, codigo LIMIT 3 OFFSET 2
```

{{query:E18 · LIMIT y OFFSET sobre una vista}}

#### E19 · Filtro con LIKE sobre una vista

LIKE sobre una columna de la vista A03: la única aula grande cuyo código empieza por K.

Palabras clave: `educacion`, `vistas`, `like`

```sql
SELECT codigo, capacidad FROM [A03 · Aulas grandes (>= 55 plazas)] WHERE codigo LIKE 'K%' ORDER BY codigo
```

{{query:E19 · Filtro con LIKE sobre una vista}}

#### E20 · Vista de una vista que une dos vistas

Lee la vista E07 (que une C02 y A31) y se queda con las notas medias de 7 o más: 4 asignaturas.

Palabras clave: `educacion`, `vistas`

```sql
SELECT asignatura, estudiantes, nota_media FROM [E07 · Dos vistas unidas por asignatura] WHERE nota_media >= 7 ORDER BY nota_media DESC, asignatura
```

{{query:E20 · Vista de una vista que une dos vistas}}

#### E21 · LEFT JOIN entre dos vistas

C02 LEFT JOIN A31: las asignaturas grandes que no llegan al HAVING de A31 (Redes de Computadores y Salud Pública) salen con nota_media nula, las primeras.

Palabras clave: `educacion`, `vistas`, `join`, `having`

```sql
SELECT c.asignatura, c.estudiantes, n.nota_media FROM [C02 · Asignaturas con cuatro o más estudiantes (grupo grande)] AS c LEFT JOIN [A31 · Nota media por asignatura (HAVING y ORDER BY por alias)] AS n ON n.id_asignatura = c.id_asignatura ORDER BY n.nota_media ASC NULLS FIRST, c.asignatura
```

{{query:E21 · LEFT JOIN entre dos vistas}}

#### E22 · Proyección con alias y asterisco sobre una vista

El asterisco del alias (v.*) sobre la vista A04: las 10 aulas con proyector, con sus dos columnas.

Palabras clave: `educacion`, `vistas`

```sql
SELECT v.* FROM [A04 · Aulas con proyector] AS v ORDER BY v.capacidad DESC, v.codigo
```

{{query:E22 · Proyección con alias y asterisco sobre una vista}}

#### E23 · Vista que lee una consulta con tres tablas

La vista B02 une tres tablas por dentro (sedes, edificios y aulas): al leerla como tabla llegan sus 3 filas.

Palabras clave: `educacion`, `vistas`, `agregado`

```sql
SELECT COUNT(*) AS filas FROM [B02 · Tres tablas: aulas grandes de la sede 1]
```

{{query:E23 · Vista que lee una consulta con tres tablas}}

#### E24 · Agrupar por un booleano de una vista

GROUP BY sobre un booleano que llega de la vista A15: cuántas combinaciones nivel/oficialidad hay de cada tipo.

Palabras clave: `educacion`, `vistas`, `agrupacion`, `agregado`, `distinct`

```sql
SELECT es_oficial, COUNT(*) AS combinaciones FROM [A15 · Combinaciones nivel/oficialidad (DISTINCT de dos columnas)] GROUP BY es_oficial ORDER BY es_oficial
```

{{query:E24 · Agrupar por un booleano de una vista}}

### F · Subconsultas

#### F01 · Estudiantes con matrícula activa (IN con subconsulta)

IN con subconsulta: los estudiantes cuyo identificador aparece en el SELECT interno.

Palabras clave: `educacion`, `subconsultas`, `estudiantes`, `matriculas`, `subconsulta`, `in`

```sql
SELECT id_estudiante, apellidos FROM estudiantes WHERE id_estudiante IN (SELECT id_estudiante FROM matriculas WHERE estado = 'activa') ORDER BY id_estudiante
```

{{query:F01 · Estudiantes con matrícula activa (IN con subconsulta)}}

#### F02 · Estudiantes sin ninguna matrícula (NOT IN con subconsulta)

NOT IN con subconsulta: solo los estudiantes que no están matriculados en nada.

Palabras clave: `educacion`, `subconsultas`, `estudiantes`, `matriculas`, `subconsulta`, `in`

```sql
SELECT id_estudiante, apellidos FROM estudiantes WHERE id_estudiante NOT IN (SELECT id_estudiante FROM matriculas) ORDER BY id_estudiante
```

{{query:F02 · Estudiantes sin ninguna matrícula (NOT IN con subconsulta)}}

#### F03 · Asignaturas sin ninguna matrícula (NOT IN con subconsulta)

Las asignaturas que no aparecen en ninguna matrícula.

Palabras clave: `educacion`, `subconsultas`, `asignaturas`, `matriculas`, `subconsulta`, `in`

```sql
SELECT id_asignatura, nombre FROM asignaturas WHERE id_asignatura NOT IN (SELECT id_asignatura FROM matriculas) ORDER BY id_asignatura
```

{{query:F03 · Asignaturas sin ninguna matrícula (NOT IN con subconsulta)}}

#### F04 · Sedes con un edificio de más de cuatro plantas (EXISTS)

EXISTS con subconsulta correlacionada: se comprueba otra vez para cada sede.

Palabras clave: `educacion`, `subconsultas`, `sedes`, `edificios`, `subconsulta`, `exists`

```sql
SELECT s.id_sede, s.nombre FROM sedes AS s WHERE EXISTS (SELECT 1 FROM edificios AS e WHERE e.id_sede = s.id_sede AND e.plantas > 4) ORDER BY s.id_sede
```

{{query:F04 · Sedes con un edificio de más de cuatro plantas (EXISTS)}}

#### F05 · Sedes sin edificios de más de cuatro plantas (NOT EXISTS)

La otra cara del ejemplo anterior: NOT EXISTS se queda con las sedes sin coincidencia.

Palabras clave: `educacion`, `subconsultas`, `sedes`, `edificios`, `subconsulta`, `exists`

```sql
SELECT s.id_sede, s.nombre FROM sedes AS s WHERE NOT EXISTS (SELECT 1 FROM edificios AS e WHERE e.id_sede = s.id_sede AND e.plantas > 4) ORDER BY s.id_sede
```

{{query:F05 · Sedes sin edificios de más de cuatro plantas (NOT EXISTS)}}

#### F06 · Estudios con algún estudiante becado (EXISTS correlacionada)

La subconsulta apunta al estudio de fuera (e.codigo): una EXISTS correlacionada.

Palabras clave: `educacion`, `subconsultas`, `estudios`, `estudiantes`, `subconsulta`, `exists`

```sql
SELECT e.codigo, e.nombre FROM estudios AS e WHERE EXISTS (SELECT 1 FROM estudiantes AS es WHERE es.codigo_estudio = e.codigo AND es.es_becado = TRUE) ORDER BY e.codigo
```

{{query:F06 · Estudios con algún estudiante becado (EXISTS correlacionada)}}

#### F07 · Matrículas de Bases de Datos mejores que alguna nota de Estructuras (> ANY)

x > ANY (…) es cierto cuando la nota supera al menos uno de los valores internos.

Palabras clave: `educacion`, `subconsultas`, `matriculas`, `subconsulta`

```sql
SELECT id_matricula, nota FROM matriculas WHERE id_asignatura = 2 AND nota > ANY (SELECT nota FROM matriculas WHERE id_asignatura = 1) ORDER BY id_matricula
```

{{query:F07 · Matrículas de Bases de Datos mejores que alguna nota de Estructuras (> ANY)}}

#### F08 · Los créditos más altos del primer curso (>= ALL)

x >= ALL (…) exige que la comparación se cumpla con todos los valores internos: solo pasa el máximo.

Palabras clave: `educacion`, `subconsultas`, `asignaturas`, `subconsulta`

```sql
SELECT id_asignatura, nombre, creditos FROM asignaturas WHERE creditos >= ALL (SELECT creditos FROM asignaturas WHERE curso = 1) ORDER BY id_asignatura
```

{{query:F08 · Los créditos más altos del primer curso (>= ALL)}}

#### F09 · Asignaturas sin docente investigador (<> ALL)

x <> ALL (…) es lo mismo que x NOT IN (…): deja los valores distintos de todos ellos.

Palabras clave: `educacion`, `subconsultas`, `asignaturas`, `docentes`, `subconsulta`

```sql
SELECT id_asignatura, nombre FROM asignaturas WHERE id_docente <> ALL (SELECT id_docente FROM docentes WHERE es_investigador = TRUE) ORDER BY id_asignatura
```

{{query:F09 · Asignaturas sin docente investigador (<> ALL)}}

#### F10 · Estudiantes becados

Esta consulta no es un test por sí sola: es la vista que leen las subconsultas siguientes.

Palabras clave: `educacion`, `subconsultas`, `estudiantes`

```sql
SELECT id_estudiante, apellidos, codigo_estudio FROM estudiantes WHERE es_becado = TRUE
```

{{query:F10 · Estudiantes becados}}

#### F11 · Becados leídos desde la vista (subconsulta sobre una vista)

La subconsulta lee la vista F10 como si fuera una tabla: [F10 · …] dentro del IN.

Palabras clave: `educacion`, `subconsultas`, `estudiantes`, `subconsulta`, `in`

```sql
SELECT es.id_estudiante, es.apellidos FROM estudiantes AS es WHERE es.id_estudiante IN (SELECT id_estudiante FROM [F10 · Estudiantes becados]) ORDER BY es.id_estudiante
```

{{query:F11 · Becados leídos desde la vista (subconsulta sobre una vista)}}

#### F12 · Nota media por asignatura de 8 o más (tabla derivada)

Una subconsulta en el FROM con alias (t) es una tabla derivada: se consulta como cualquier otra.

Palabras clave: `educacion`, `subconsultas`, `matriculas`, `subconsulta`, `agrupacion`, `agregado`, `null`

```sql
SELECT t.id_asignatura, t.nota_media FROM (SELECT id_asignatura, AVG(nota) AS nota_media FROM matriculas WHERE nota IS NOT NULL GROUP BY id_asignatura) AS t WHERE t.nota_media >= 8 ORDER BY t.id_asignatura
```

{{query:F12 · Nota media por asignatura de 8 o más (tabla derivada)}}

#### F13 · Asignaturas con cuatro o más matrículas (tabla derivada y JOIN)

La tabla derivada se une a la tabla asignaturas y se filtra por su columna total.

Palabras clave: `educacion`, `subconsultas`, `matriculas`, `asignaturas`, `subconsulta`, `join`, `agrupacion`, `agregado`

```sql
SELECT t.id_asignatura, a.nombre, t.total FROM (SELECT id_asignatura, COUNT(*) AS total FROM matriculas GROUP BY id_asignatura) AS t INNER JOIN asignaturas AS a ON a.id_asignatura = t.id_asignatura WHERE t.total >= 4 ORDER BY t.total DESC, t.id_asignatura
```

{{query:F13 · Asignaturas con cuatro o más matrículas (tabla derivada y JOIN)}}

#### F14 · Matrículas de cada estudiante de una sede (subconsulta escalar en el SELECT)

La subconsulta de la lista de columnas devuelve un valor por fila: las matrículas de ese estudiante.

Palabras clave: `educacion`, `subconsultas`, `matriculas`, `estudiantes`, `subconsulta`, `agregado`

```sql
SELECT es.id_estudiante, es.apellidos, (SELECT COUNT(*) FROM matriculas AS m WHERE m.id_estudiante = es.id_estudiante) AS matriculas FROM estudiantes AS es WHERE es.id_sede = 4 ORDER BY es.id_estudiante
```

{{query:F14 · Matrículas de cada estudiante de una sede (subconsulta escalar en el SELECT)}}

#### F15 · Asignaturas por encima de la media de créditos (subconsulta escalar en el WHERE)

La subconsulta devuelve un solo valor (la media) con el que compara el WHERE.

Palabras clave: `educacion`, `subconsultas`, `asignaturas`, `subconsulta`, `agregado`

```sql
SELECT id_asignatura, nombre, creditos FROM asignaturas WHERE creditos > (SELECT AVG(creditos) FROM asignaturas) ORDER BY id_asignatura
```

{{query:F15 · Asignaturas por encima de la media de créditos (subconsulta escalar en el WHERE)}}

#### F16 · Estudios con cinco estudiantes o más

Una consulta con subconsulta también sirve de vista: esta usa una EXISTS correlacionada con HAVING.

Palabras clave: `educacion`, `subconsultas`, `estudios`, `estudiantes`, `subconsulta`, `having`, `agregado`, `exists`

```sql
SELECT e.codigo, e.nombre FROM estudios AS e WHERE EXISTS (SELECT 1 FROM estudiantes AS es WHERE es.codigo_estudio = e.codigo HAVING COUNT(*) >= 5) ORDER BY e.codigo
```

{{query:F16 · Estudios con cinco estudiantes o más}}

#### F17 · La vista anterior leída como tabla

F16 se lee desde el FROM como una tabla, aunque por dentro lleve una subconsulta.

Palabras clave: `educacion`, `subconsultas`

```sql
SELECT codigo, nombre FROM [F16 · Estudios con cinco estudiantes o más] ORDER BY codigo
```

{{query:F17 · La vista anterior leída como tabla}}

#### F18 · Asignaturas de estudios con algún becado (subconsultas anidadas)

Una subconsulta dentro de otra: la interna elige los estudios y la externa las asignaturas.

Palabras clave: `educacion`, `subconsultas`, `asignaturas`, `estudiantes`, `subconsulta`, `distinct`, `in`

```sql
SELECT DISTINCT a.id_asignatura, a.nombre FROM asignaturas AS a WHERE a.codigo_estudio IN (SELECT es.codigo_estudio FROM estudiantes AS es WHERE es.es_becado = TRUE) ORDER BY a.id_asignatura
```

{{query:F18 · Asignaturas de estudios con algún becado (subconsultas anidadas)}}

#### F19 · Asignaturas que no llegan a los créditos máximos (NOT con ALL)

NOT (… >= ALL (…)) es la negación del predicado ALL: todo menos los máximos.

Palabras clave: `educacion`, `subconsultas`, `asignaturas`, `subconsulta`

```sql
SELECT id_asignatura, nombre FROM asignaturas WHERE NOT (creditos >= ALL (SELECT creditos FROM asignaturas WHERE curso = 1)) ORDER BY id_asignatura
```

{{query:F19 · Asignaturas que no llegan a los créditos máximos (NOT con ALL)}}

#### F20 · Edificios de la sede principal (= ANY)

x = ANY (…) es lo mismo que x IN (…): se incluye para completar los cuantificadores.

Palabras clave: `educacion`, `subconsultas`, `edificios`, `sedes`, `subconsulta`, `distinct`

```sql
SELECT DISTINCT e.id_sede FROM edificios AS e WHERE e.id_sede = ANY (SELECT id_sede FROM sedes WHERE es_principal = TRUE) ORDER BY e.id_sede
```

{{query:F20 · Edificios de la sede principal (= ANY)}}

#### F21 · Asignaturas con más créditos que alguna de Educación Primaria (> SOME)

SOME es sinónimo de ANY: la comparación debe cumplirse al menos con un valor interno.

Palabras clave: `educacion`, `subconsultas`, `asignaturas`, `subconsulta`

```sql
SELECT id_asignatura, nombre FROM asignaturas WHERE creditos > SOME (SELECT creditos FROM asignaturas WHERE codigo_estudio = 'GEDU') ORDER BY id_asignatura
```

{{query:F21 · Asignaturas con más créditos que alguna de Educación Primaria (> SOME)}}

### G · Uniones

#### G01 · Nombres de campus y de titulaciones, sin repetir

UNION de dos tablas de una columna: los nombres de los campus y los de las titulaciones, cada uno una vez y ordenados por nombre.

Palabras clave: `educacion`, `uniones`, `sedes`, `estudios`, `union`

```sql
SELECT nombre FROM sedes UNION SELECT nombre FROM estudios ORDER BY nombre
```

{{query:G01 · Nombres de campus y de titulaciones, sin repetir}}

#### G02 · El país de cada campus, dos veces (UNION ALL)

El mismo SELECT a los dos lados de UNION ALL: las filas se ponen una debajo de otra sin quitar repetidos, así que salen 8.

Palabras clave: `educacion`, `uniones`, `sedes`, `union`

```sql
SELECT pais FROM sedes UNION ALL SELECT pais FROM sedes ORDER BY pais
```

{{query:G02 · El país de cada campus, dos veces (UNION ALL)}}

#### G03 · Cada país una vez (UNION)

La misma unión sin ALL: UNION quita las filas repetidas, así que cada país sale una sola vez.

Palabras clave: `educacion`, `uniones`, `sedes`, `union`

```sql
SELECT pais FROM sedes UNION SELECT pais FROM sedes ORDER BY pais
```

{{query:G03 · Cada país una vez (UNION)}}

#### G04 · Una unión leída como tabla

La unión va en el FROM con alias (tabla derivada): se cuentan los identificadores de asignatura del catálogo y de las matrículas, sin repetir.

Palabras clave: `educacion`, `uniones`, `asignaturas`, `matriculas`, `subconsulta`, `union`, `agregado`

```sql
SELECT COUNT(*) AS n FROM (SELECT id_asignatura FROM asignaturas UNION SELECT id_asignatura FROM matriculas) AS t
```

{{query:G04 · Una unión leída como tabla}}

#### G05 · Unión con alias, ORDER BY y LIMIT

Los dos lados llaman item a su columna: el ORDER BY de la unión ordena el resultado combinado por ese nombre y el LIMIT deja los 5 primeros.

Palabras clave: `educacion`, `uniones`, `edificios`, `aulas`, `union`

```sql
SELECT nombre AS item FROM edificios UNION SELECT codigo AS item FROM aulas ORDER BY item LIMIT 5
```

{{query:G05 · Unión con alias, ORDER BY y LIMIT}}

### H · Scripts como tablas

#### H01 · Estudiantes por sede, leídos del script

Lee el resultado del script «Estudiantes por sede» como si fuera una tabla.

Palabras clave: `educacion`, `scripts-como-tablas`

```sql
SELECT sede, estudiantes FROM [Estudiantes por sede] ORDER BY estudiantes DESC, sede
```

{{query:H01 · Estudiantes por sede, leídos del script}}

#### H02 · Cuántas sedes devuelve el script

Lee el resultado del script «Estudiantes por sede» como si fuera una tabla.

Palabras clave: `educacion`, `scripts-como-tablas`, `agregado`

```sql
SELECT COUNT(*) AS total FROM [Estudiantes por sede]
```

{{query:H02 · Cuántas sedes devuelve el script}}

#### H03 · Sedes con 6 o más estudiantes

Lee el resultado del script «Estudiantes por sede» como si fuera una tabla.

Palabras clave: `educacion`, `scripts-como-tablas`

```sql
SELECT sede, estudiantes FROM [Estudiantes por sede] WHERE estudiantes >= 6 ORDER BY estudiantes DESC, sede
```

{{query:H03 · Sedes con 6 o más estudiantes}}

### I · Parámetros

#### P01 · Sedes que dan valores al parámetro

Origen de un parámetro: cada fila ofrece un valor (id_sede) y su etiqueta (nombre).

Palabras clave: `educacion`, `parametros`, `origen`, `referencia`

```sql
SELECT id_sede, nombre FROM sedes ORDER BY nombre
```

{{query:P01 · Sedes que dan valores al parámetro}}

#### P02 · Sede principal (origen del parámetro)

Origen de un parámetro que devuelve una sola fila: la sede principal.

Palabras clave: `educacion`, `parametros`, `origen`, `referencia`

```sql
SELECT id_sede, nombre FROM sedes WHERE es_principal = true ORDER BY nombre
```

{{query:P02 · Sede principal (origen del parámetro)}}

#### P03 · Aulas por capacidad mínima (rango)

Parámetro de rango: la capacidad mínima se elige en un desplegable de 50 a 120.

Palabras clave: `educacion`, `parametros`, `rango`, `aulas`

```sql
SELECT codigo, capacidad FROM aulas WHERE capacidad >= {{ 50..120 | Capacidad mínima }} ORDER BY capacidad DESC, codigo
```

{{query:P03 · Aulas por capacidad mínima (rango)}}

#### P04 · Matrículas del curso elegido (subconsulta)

Parámetro con una subconsulta: los cursos salen de la propia tabla de matrículas.

Palabras clave: `educacion`, `parametros`, `subconsulta`, `matriculas`

```sql
SELECT curso_academico, COUNT(*) AS total FROM matriculas WHERE curso_academico IN {{ SELECT curso_academico FROM matriculas GROUP BY curso_academico ORDER BY curso_academico | Curso académico }} GROUP BY curso_academico ORDER BY curso_academico
```

{{query:P04 · Matrículas del curso elegido (subconsulta)}}

#### P05 · Estudiantes de las sedes elegidas (referencia a consulta)

Parámetro que referencia una consulta guardada: sus pares id/nombre llenan el desplegable.

Palabras clave: `educacion`, `parametros`, `referencia`, `estudiantes`

```sql
SELECT nombre, apellidos, id_sede FROM estudiantes WHERE id_sede IN {{ P01 · Sedes que dan valores al parámetro | Sede }} ORDER BY apellidos, nombre
```

{{query:P05 · Estudiantes de las sedes elegidas (referencia a consulta)}}

#### P06 · Sedes salvo la principal (NOT IN con referencia)

Parámetro en un NOT IN que referencia la consulta de la sede principal.

Palabras clave: `educacion`, `parametros`, `referencia`, `not-in`

```sql
SELECT nombre, ciudad, pais FROM sedes WHERE id_sede NOT IN {{ P02 · Sede principal (origen del parámetro) | Sede principal }} ORDER BY nombre
```

{{query:P06 · Sedes salvo la principal (NOT IN con referencia)}}

#### P07 · Asignaturas por curso (HAVING con rango)

Parámetro de rango dentro de un HAVING: filtra los grupos por su número de filas.

Palabras clave: `educacion`, `parametros`, `rango`, `having`

```sql
SELECT curso, COUNT(*) AS total, ROUND(AVG(creditos), 1) AS creditos_medios FROM asignaturas GROUP BY curso HAVING COUNT(*) >= {{ 1..10 | Asignaturas mínimas }} ORDER BY curso
```

{{query:P07 · Asignaturas por curso (HAVING con rango)}}

#### P08 · Aulas de mayor capacidad (subconsulta escalar)

Parámetro con una subconsulta escalar: MAX devuelve el único valor que se elige.

Palabras clave: `educacion`, `parametros`, `subconsulta`, `aulas`

```sql
SELECT codigo, capacidad FROM aulas WHERE capacidad = {{ SELECT MAX(capacidad) FROM aulas | Capacidad máxima }} ORDER BY codigo
```

{{query:P08 · Aulas de mayor capacidad (subconsulta escalar)}}

#### P09 · Becados de las sedes del script (referencia a script)

Parámetro que referencia un script: sus filas dan los valores y las etiquetas del desplegable.

Palabras clave: `educacion`, `parametros`, `referencia`, `script`

```sql
SELECT nombre, apellidos FROM estudiantes WHERE es_becado = true AND id_sede IN {{ P10 · Sedes para el parámetro | Sede }} ORDER BY apellidos, nombre
```

{{query:P09 · Becados de las sedes del script (referencia a script)}}

#### P12 · Aulas entre dos capacidades (dos rangos)

Dos parámetros de rango en un BETWEEN: capacidad mínima y máxima.

Palabras clave: `educacion`, `parametros`, `varios`, `rango`

```sql
SELECT codigo, capacidad, planta FROM aulas WHERE capacidad BETWEEN {{ 20..60 | Capacidad mínima }} AND {{ 50..120 | Capacidad máxima }} ORDER BY capacidad, codigo
```

{{query:P12 · Aulas entre dos capacidades (dos rangos)}}

#### P13 · Matrículas por curso y estado (dos subconsultas)

Dos parámetros de subconsulta: el curso (lista) y el estado (un solo valor).

Palabras clave: `educacion`, `parametros`, `varios`, `subconsulta`

```sql
SELECT curso_academico, estado, COUNT(*) AS total FROM matriculas WHERE curso_academico IN {{ SELECT curso_academico FROM matriculas GROUP BY curso_academico ORDER BY curso_academico | Curso }} AND estado = {{ SELECT estado FROM matriculas GROUP BY estado ORDER BY estado | Estado }} GROUP BY curso_academico, estado ORDER BY curso_academico, estado
```

{{query:P13 · Matrículas por curso y estado (dos subconsultas)}}

#### P14 · Docentes de una categoría con horas mínimas (subconsulta y rango)

Dos parámetros: la categoría sale de una subconsulta y las horas de un rango.

Palabras clave: `educacion`, `parametros`, `varios`, `docentes`

```sql
SELECT nombre, apellidos, categoria, horas_semanales FROM docentes WHERE categoria = {{ SELECT DISTINCT categoria FROM docentes ORDER BY categoria | Categoría }} AND horas_semanales >= {{ 5..20 | Horas mínimas }} ORDER BY horas_semanales DESC, apellidos
```

{{query:P14 · Docentes de una categoría con horas mínimas (subconsulta y rango)}}

#### P15 · Edificios de una sede con plantas mínimas (referencia y rango)

Dos parámetros: la sede de una consulta guardada y el número mínimo de plantas.

Palabras clave: `educacion`, `parametros`, `varios`, `referencia`

```sql
SELECT e.nombre, e.plantas, e.anio_construccion FROM edificios AS e WHERE e.id_sede IN {{ P01 · Sedes que dan valores al parámetro | Sede }} AND e.plantas >= {{ 1..5 | Plantas mínimas }} ORDER BY e.plantas DESC, e.nombre
```

{{query:P15 · Edificios de una sede con plantas mínimas (referencia y rango)}}

#### P16 · Becados de un estudio y una sede (dos referencias)

Dos parámetros de subconsulta que filtran por estudio y por sede a la vez.

Palabras clave: `educacion`, `parametros`, `varios`, `estudiantes`

```sql
SELECT nombre, apellidos, codigo_estudio, id_sede FROM estudiantes WHERE es_becado = true AND codigo_estudio IN {{ SELECT codigo FROM estudios ORDER BY codigo | Estudio }} AND id_sede IN {{ P01 · Sedes que dan valores al parámetro | Sede }} ORDER BY apellidos, nombre
```

{{query:P16 · Becados de un estudio y una sede (dos referencias)}}

### J · PIVOT

#### P01 · Matrículas por estado y curso

PIVOT con estados en filas, cursos en columnas y COUNT(*), con totales de fila y columna.

Palabras clave: `pivot`, `tabla dinámica`, `recuento`, `media`, `subtotal`

```sql
PIVOT (SELECT estado) BY (SELECT curso_academico) FROM matriculas VALUES COUNT(*) AS matriculas TOTALS ROWS GRAND COLUMNS GRAND;
```

{{query:P01 · Matrículas por estado y curso}}

#### P02 · Plazas y aulas por planta y proyector

Dos medidas en columnas (plazas y aulas) por planta y proyector, con totales.

Palabras clave: `pivot`, `tabla dinámica`, `recuento`, `media`, `subtotal`

```sql
PIVOT (SELECT planta) BY (SELECT tiene_proyector) FROM aulas VALUES SUM(capacidad) AS plazas, COUNT(*) AS aulas TOTALS ROWS GRAND COLUMNS GRAND MEASURES ON COLUMNS;
```

{{query:P02 · Plazas y aulas por planta y proyector}}

#### P03 · Nota media por estado y curso

AVG(nota) por estado y curso; una celda vacía se rellena con «–» y un agregado nulo con «n/d».

Palabras clave: `pivot`, `tabla dinámica`, `recuento`, `media`, `subtotal`

```sql
PIVOT (SELECT estado) BY (SELECT curso_academico) FROM matriculas VALUES AVG(nota) AS nota_media TOTALS ROWS GRAND COLUMNS GRAND FILL EMPTY '–', NULLVALUE 'n/d';
```

{{query:P03 · Nota media por estado y curso}}

#### P04 · Carga docente por categoría e investigación

AVG(horas_semanales) y COUNT(*) de docentes por categoría y perfil investigador.

Palabras clave: `pivot`, `tabla dinámica`, `recuento`, `media`, `subtotal`

```sql
PIVOT (SELECT categoria) BY (SELECT es_investigador) FROM docentes VALUES AVG(horas_semanales) AS horas_medias, COUNT(*) AS docentes TOTALS ROWS GRAND COLUMNS GRAND;
```

{{query:P04 · Carga docente por categoría e investigación}}

#### P05 · Aulas por planta, proyector y pizarra

Columnas jerárquicas (proyector y tipo de pizarra) con subtotal por el primer nivel.

Palabras clave: `pivot`, `tabla dinámica`, `recuento`, `media`, `subtotal`

```sql
PIVOT (SELECT planta) BY (SELECT tiene_proyector, tipo_pizarra) FROM aulas VALUES COUNT(*) AS aulas TOTALS ROWS GRAND COLUMNS SUBTOTAL BY tiene_proyector GRAND;
```

{{query:P05 · Aulas por planta, proyector y pizarra}}

### K · CASE e IF

#### K01 · Aulas por tamaño (CASE buscado)

CASE buscado con condiciones solapadas: menos de 25 es pequeña, entre 25 y 55 mediana y el resto grande. El límite 55 es mediana y 60 es grande.

Palabras clave: `educacion`, `case`, `funciones`, `aulas`

```sql
SELECT codigo, capacidad, CASE WHEN capacidad < 25 THEN 'Pequeña' WHEN capacidad BETWEEN 25 AND 55 THEN 'Mediana' ELSE 'Grande' END AS tamano FROM aulas WHERE capacidad >= 55 ORDER BY capacidad DESC, codigo
```

{{query:K01 · Aulas por tamaño (CASE buscado)}}

#### K02 · Estudios por ciclo (CASE simple)

CASE simple: compara el nivel con cada WHEN usando = y convierte Grado y Máster en un ciclo.

Palabras clave: `educacion`, `case`, `funciones`, `estudios`

```sql
SELECT codigo, CASE nivel WHEN 'Grado' THEN 'Grado' WHEN 'Máster' THEN 'Posgrado' ELSE 'Otro' END AS ciclo FROM estudios ORDER BY codigo
```

{{query:K02 · Estudios por ciclo (CASE simple)}}

#### K03 · Docentes sin despacho (CASE IS NULL)

El CASE buscado detecta el despacho ausente con IS NULL, así que no cae en ELSE en silencio.

Palabras clave: `educacion`, `case`, `nulos`, `docentes`

```sql
SELECT nombre, apellidos, CASE WHEN despacho IS NULL THEN 'Sin despacho' ELSE despacho END AS sala FROM docentes WHERE despacho IS NULL ORDER BY apellidos
```

{{query:K03 · Docentes sin despacho (CASE IS NULL)}}

#### K04 · Aulas grandes con IF

IF(condición, valor, sustituto) es la abreviatura de CASE WHEN condición THEN valor ELSE sustituto END.

Palabras clave: `educacion`, `if`, `funciones`, `aulas`

```sql
SELECT codigo, IF(capacidad >= 55, 'Grande', 'Pequeña') AS tamano FROM aulas WHERE codigo IN ('B-SALA', 'K-102', 'C-LAB1') ORDER BY capacidad DESC
```

{{query:K04 · Aulas grandes con IF}}

#### K05 · Docentes por carga (CASE en GROUP BY)

La misma expresión CASE clasifica las filas y se repite en GROUP BY: seis docentes en cada grupo.

Palabras clave: `educacion`, `case`, `group-by`, `docentes`

```sql
SELECT CASE WHEN horas_semanales >= 15 THEN 'Carga alta' ELSE 'Carga baja' END AS carga, COUNT(*) AS n FROM docentes GROUP BY CASE WHEN horas_semanales >= 15 THEN 'Carga alta' ELSE 'Carga baja' END ORDER BY carga
```

{{query:K05 · Docentes por carga (CASE en GROUP BY)}}

### L · Funciones en SELECT y PIVOT

#### L01 · Funciones numéricas: ABS y ROUND

Aplica ABS y ROUND al aforo de las aulas: distancia a 50 y un decimal de capacidad / 3.

Palabras clave: `educacion`, `funciones`, `numericas`, `abs`, `round`

```sql
SELECT codigo, capacidad, ABS(capacidad - 50) AS distancia, ROUND(capacidad / 3.0, 1) AS tercio FROM aulas WHERE capacidad >= 55 ORDER BY capacidad DESC, codigo
```

{{query:L01 · Funciones numéricas: ABS y ROUND}}

#### L02 · Funciones de texto: UPPER, LOWER, LENGTH, TRIM, SUBSTR y CONCAT

Aplica las seis funciones de texto a los tres primeros docentes.

Palabras clave: `educacion`, `funciones`, `texto`, `upper`, `lower`, `length`, `trim`, `substr`, `concat`

```sql
SELECT id_docente, UPPER(nombre) AS nombre_mayus, LOWER(apellidos) AS apellidos_minus, LENGTH(apellidos) AS letras, TRIM(nombre) AS recortado, SUBSTR(apellidos, 1, 5) AS prefijo, CONCAT(nombre, ' ', apellidos) AS nombre_completo FROM docentes WHERE id_docente <= 3 ORDER BY id_docente
```

{{query:L02 · Funciones de texto: UPPER, LOWER, LENGTH, TRIM, SUBSTR y CONCAT}}

#### L03 · Expresiones regulares: REPLACE e INSTR

Usa REGEXPR_REPLACE para enmascarar dígitos y REGEXPR_INSTR para localizar el primero en tres códigos de aula.

Palabras clave: `educacion`, `funciones`, `regex`, `replace`, `instr`

```sql
SELECT codigo, REGEXPR_REPLACE(codigo, '[0-9]', '#') AS enmascarado, REGEXPR_INSTR(codigo, '[0-9]+') AS primer_digito FROM aulas WHERE codigo IN ('K-101', 'B-SALA', 'C-LAB1') ORDER BY codigo
```

{{query:L03 · Expresiones regulares: REPLACE e INSTR}}

#### L04 · Funciones de NULL: COALESCE, IFNULL y NULLIF

Usa COALESCE, IFNULL y NULLIF sobre despacho, correo y categoría: los valores ausentes y el valor Asociado se transforman.

Palabras clave: `educacion`, `funciones`, `null`, `coalesce`, `ifnull`, `nullif`

```sql
SELECT id_docente, COALESCE(despacho, 'Sin despacho') AS despacho, IFNULL(email, 'Sin correo') AS email, NULLIF(categoria, 'Asociado') AS no_asociado FROM docentes WHERE id_docente <= 4 ORDER BY id_docente
```

{{query:L04 · Funciones de NULL: COALESCE, IFNULL y NULLIF}}

#### L05 · Funciones condicionales: IF y CASE

Usa IF y las dos formas de CASE para clasificar tres aulas por capacidad.

Palabras clave: `educacion`, `funciones`, `condicionales`, `if`, `case`

```sql
SELECT codigo, IF(capacidad >= 55, 'Grande', 'Pequeña') AS tamano, CASE WHEN capacidad < 25 THEN 'Diminuta' WHEN capacidad BETWEEN 25 AND 55 THEN 'Mediana' ELSE 'Grande' END AS categoria FROM aulas WHERE codigo IN ('B-SALA', 'K-102', 'C-LAB1') ORDER BY capacidad DESC
```

{{query:L05 · Funciones condicionales: IF y CASE}}

#### L06 · Funciones de agregado: COUNT, SUM, AVG, MIN y MAX

Ejecuta COUNT(*), COUNT(columna), COUNT(DISTINCT), SUM, AVG, MIN y MAX sobre los docentes.

Palabras clave: `educacion`, `funciones`, `agregados`, `count`, `sum`, `avg`, `min`, `max`

```sql
SELECT COUNT(*) AS total, COUNT(despacho) AS con_despacho, COUNT(DISTINCT categoria) AS categorias, SUM(horas_semanales) AS horas, AVG(horas_semanales) AS horas_medias, MIN(horas_semanales) AS horas_min, MAX(horas_semanales) AS horas_max FROM docentes
```

{{query:L06 · Funciones de agregado: COUNT, SUM, AVG, MIN y MAX}}

#### L07 · PIVOT con todas las funciones de agregado

PIVOT con COUNT, SUM, AVG, MIN y MAX como medidas, una medida por fila, por planta y proyector.

Palabras clave: `educacion`, `funciones`, `pivot`, `agregados`

```sql
PIVOT (SELECT planta) BY (SELECT tiene_proyector) FROM aulas VALUES COUNT(*) AS aulas, SUM(capacidad) AS plazas, AVG(capacidad) AS plazas_medias, MIN(capacidad) AS plazas_min, MAX(capacidad) AS plazas_max TOTALS ROWS GRAND COLUMNS GRAND MEASURES ON ROWS;
```

{{query:L07 · PIVOT con todas las funciones de agregado}}

#### L08 · PIVOT con funciones escalares en el eje de filas

PIVOT con funciones escalares en el eje de filas: UPPER y CASE crean la carga, REGEXPR_INSTR filtra los docentes con correo y COUNT/AVG agregan.

Palabras clave: `educacion`, `funciones`, `pivot`, `escalares`, `case`, `regexpr_instr`

```sql
PIVOT (SELECT UPPER(CASE WHEN horas_semanales >= 15 THEN 'alta' ELSE 'baja' END) AS carga WHERE REGEXPR_INSTR(email, '@') > 0) BY (SELECT es_investigador) FROM docentes VALUES COUNT(*) AS docentes, AVG(horas_semanales) AS horas_medias TOTALS ROWS GRAND COLUMNS GRAND;
```

{{query:L08 · PIVOT con funciones escalares en el eje de filas}}

## Scripts

### A · Básicas y agregados

#### Becados con su contacto

Filtra los estudiantes becados y muestra nombre completo, correo y sede.

Palabras clave: `educacion`, `filtros`, `estudiantes`, `filtro`, `map`

```js
const datos = tables["estudiantes"];
return datos
  .filter((fila) => fila.es_becado)
  .map((fila) => ({
    "nombre_completo": `${fila.nombre} ${fila.apellidos}`,
    "email": fila.email,
    "sede": fila.id_sede,
  }));
```

{{script:Becados con su contacto}}

#### Edad de los estudiantes

Calcula la edad a 1 de septiembre de 2025 desde la fecha de nacimiento y ordena de mayor a menor.

Palabras clave: `educacion`, `fechas`, `estudiantes`, `map`, `orden`

```js
const datos = tables["estudiantes"];
const reference = new Date("2025-09-01");
return datos
  .map((fila) => {
    const birth = new Date(fila.fecha_nacimiento);
    let age = reference.getFullYear() - birth.getFullYear();
    const m = reference.getMonth() - birth.getMonth();
    if (m < 0 || (m === 0 && reference.getDate() < birth.getDate())) {
      age -= 1;
    }
    return {
      "nombre_completo": `${fila.nombre} ${fila.apellidos}`,
      "fecha_nacimiento": fila.fecha_nacimiento,
      "edad": age,
    };
  })
  .sort((a, b) => b["edad"] - a["edad"]);
```

{{script:Edad de los estudiantes}}

#### Diez estudiantes de más edad

Ordena por fecha de nacimiento y devuelve las diez primeras filas con su puesto.

Palabras clave: `educacion`, `filtros`, `estudiantes`, `map`, `orden`, `slice`

```js
const datos = tables["estudiantes"];
return datos
  .slice()
  .sort((a, b) => String(a.fecha_nacimiento).localeCompare(String(b.fecha_nacimiento)))
  .slice(0, 10)
  .map((fila, index) => ({
    "puesto": index + 1,
    "nombre_completo": `${fila.nombre} ${fila.apellidos}`,
    "fecha_nacimiento": fila.fecha_nacimiento,
  }));
```

{{script:Diez estudiantes de más edad}}

#### Estudiantes por sede

Cruza estudiantes con la tabla de sedes para contar cuántos hay en cada una.

Palabras clave: `educacion`, `agregados`, `estudiantes`, `sedes`, `map`, `reduce`, `orden`, `objetos`

```js
const datos = tables["estudiantes"];
const campuses = new Map(tables["sedes"].map((c) => [c.id_sede, c.nombre]));
const counts = datos.reduce((acc, fila) => {
  const name = campuses.get(fila.id_sede) || "—";
  acc[name] = (acc[name] || 0) + 1;
  return acc;
}, {});
return Object.entries(counts)
  .map(([name, count]) => ({ "sede": name, "estudiantes": count }))
  .sort((a, b) => b["estudiantes"] - a["estudiantes"]);
```

{{script:Estudiantes por sede}}

#### Contactos incompletos

Lista los estudiantes a los que les falta el correo, el teléfono o los dos.

Palabras clave: `educacion`, `filtros`, `estudiantes`, `filtro`, `map`

```js
const datos = tables["estudiantes"];
return datos
  .filter((fila) => !fila.email || !fila.telefono)
  .map((fila) => {
    const missing = [];
    if (!fila.email) missing.push("email");
    if (!fila.telefono) missing.push("telefono");
    return {
      "nombre_completo": `${fila.nombre} ${fila.apellidos}`,
      "email": fila.email,
      "telefono": fila.telefono,
      "faltan": missing.join(", "),
    };
  });
```

{{script:Contactos incompletos}}

#### Becas por estudio

Agrupa por código de estudio y calcula el total de estudiantes, becados y porcentaje.

Palabras clave: `educacion`, `agregados`, `estudiantes`, `map`, `reduce`, `orden`, `math`

```js
const datos = tables["estudiantes"];
const groups = datos.reduce((acc, fila) => {
  const key = fila.codigo_estudio;
  acc[key] = acc[key] || { total: 0, scholarship: 0 };
  acc[key].total += 1;
  if (fila.es_becado) acc[key].scholarship += 1;
  return acc;
}, {});
return Object.entries(groups)
  .map(([degree, g]) => ({
    "estudio": degree,
    "estudiantes": g.total,
    "becados": g.scholarship,
    "porcentaje": Math.round((g.scholarship / g.total) * 1000) / 10,
  }))
  .sort((a, b) => a["estudio"].localeCompare(b["estudio"]));
```

{{script:Becas por estudio}}

#### Notas por estado

Ignora las notas nulas y calcula cuántas hay, su media, el mínimo y el máximo por estado.

Palabras clave: `educacion`, `agregados`, `matriculas`, `filtro`, `map`, `reduce`, `math`

```js
const datos = tables["matriculas"];
const groups = datos
  .filter((fila) => fila.nota !== null && fila.nota !== undefined)
  .reduce((acc, fila) => {
    const key = fila.estado;
    acc[key] = acc[key] || { count: 0, sum: 0, min: Infinity, max: -Infinity };
    const g = acc[key];
    g.count += 1;
    g.sum += fila.nota;
    g.min = Math.min(g.min, fila.nota);
    g.max = Math.max(g.max, fila.nota);
    return acc;
  }, {});
return Object.entries(groups).map(([status, g]) => ({
  "estado": status,
  "total": g.count,
  "media": Math.round((g.sum / g.count) * 100) / 100,
  "minimo": g.min,
  "maximo": g.max,
}));
```

{{script:Notas por estado}}

#### Tasa de aprobados por curso

Cuenta matrículas, superadas y nota media de las calificadas en cada curso académico.

Palabras clave: `educacion`, `agregados`, `matriculas`, `map`, `reduce`, `math`, `objetos`

```js
const datos = tables["matriculas"];
const groups = datos.reduce((acc, fila) => {
  const key = fila.curso_academico;
  acc[key] = acc[key] || { total: 0, passed: 0, graded: 0, sum: 0 };
  const g = acc[key];
  g.total += 1;
  if (fila.estado === "superada") g.passed += 1;
  if (fila.nota !== null && fila.nota !== undefined) {
    g.graded += 1;
    g.sum += fila.nota;
  }
  return acc;
}, {});
return Object.entries(groups).map(([year, g]) => ({
  "curso": year,
  "total": g.total,
  "superadas": g.passed,
  "porcentaje": Math.round((g.passed / g.total) * 1000) / 10,
  "media": g.graded ? Math.round((g.sum / g.graded) * 100) / 100 : null,
}));
```

{{script:Tasa de aprobados por curso}}

#### Estados por curso (pivot)

Convierte los estados en columnas: activas, superadas y anuladas por curso académico.

Palabras clave: `educacion`, `agregados`, `matriculas`, `map`, `reduce`, `orden`, `objetos`

```js
const datos = tables["matriculas"];
const groups = datos.reduce((acc, fila) => {
  const year = fila.curso_academico;
  const status = fila.estado;
  acc[year] = acc[year] || { year, active: 0, passed: 0, cancelled: 0 };
  if (status === "activa") acc[year].active += 1;
  else if (status === "superada") acc[year].passed += 1;
  else if (status === "anulada") acc[year].cancelled += 1;
  return acc;
}, {});
return Object.values(groups)
  .map((g) => ({
    "curso": g.year,
    "activas": g.active,
    "superadas": g.passed,
    "anuladas": g.cancelled,
  }))
  .sort((a, b) => String(a["curso"]).localeCompare(String(b["curso"])));
```

{{script:Estados por curso (pivot)}}

#### Distribución de notas

Clasifica las notas en cuatro tramos y calcula cuántas hay y su porcentaje.

Palabras clave: `educacion`, `agregados`, `matriculas`, `map`, `reduce`, `foreach`, `math`

```js
const datos = tables["matriculas"];
const buckets = { "0-4.9": 0, "5-6.9": 0, "7-8.9": 0, "9-10": 0 };
datos.forEach((fila) => {
  const g = fila.nota;
  if (g === null || g === undefined) return;
  if (g < 5) buckets["0-4.9"] += 1;
  else if (g < 7) buckets["5-6.9"] += 1;
  else if (g < 9) buckets["7-8.9"] += 1;
  else buckets["9-10"] += 1;
});
const total = Object.values(buckets).reduce((a, b) => a + b, 0);
return Object.entries(buckets).map(([range, count]) => ({
  "rango": range,
  "total": count,
  "porcentaje": total ? Math.round((count / total) * 1000) / 10 : 0,
}));
```

{{script:Distribución de notas}}

#### Asignaturas por curso

Cuenta asignaturas, créditos y media de horas presenciales por curso.

Palabras clave: `educacion`, `agregados`, `asignaturas`, `map`, `reduce`, `orden`, `math`

```js
const datos = tables["asignaturas"];
const groups = datos.reduce((acc, fila) => {
  const key = fila.curso;
  acc[key] = acc[key] || { count: 0, credits: 0, hours: 0 };
  acc[key].count += 1;
  acc[key].credits += fila.creditos;
  acc[key].hours += fila.horas_presenciales;
  return acc;
}, {});
return Object.entries(groups)
  .map(([year, g]) => ({
    "curso": Number(year),
    "total": g.count,
    "creditos": g.credits,
    "media_horas": Math.round((g.hours / g.count) * 10) / 10,
  }))
  .sort((a, b) => a["curso"] - b["curso"]);
```

{{script:Asignaturas por curso}}

#### Docentes por categoría

Agrupa por categoría y calcula cuántos hay, la media de horas y cuántos investigan.

Palabras clave: `educacion`, `agregados`, `docentes`, `map`, `reduce`, `orden`, `math`

```js
const datos = tables["docentes"];
const groups = datos.reduce((acc, fila) => {
  const key = fila.categoria;
  acc[key] = acc[key] || { count: 0, hours: 0, researchers: 0 };
  acc[key].count += 1;
  acc[key].hours += fila.horas_semanales;
  if (fila.es_investigador) acc[key].researchers += 1;
  return acc;
}, {});
return Object.entries(groups)
  .map(([category, g]) => ({
    "categoria": category,
    "total": g.count,
    "media_horas": Math.round((g.hours / g.count) * 10) / 10,
    "investigadores": g.researchers,
  }))
  .sort((a, b) => b["total"] - a["total"]);
```

{{script:Docentes por categoría}}

#### Docentes sin despacho o correo

Lista los docentes a los que les falta el despacho, el correo o los dos.

Palabras clave: `educacion`, `filtros`, `docentes`, `filtro`, `map`

```js
const datos = tables["docentes"];
return datos
  .filter((fila) => !fila.email || !fila.despacho)
  .map((fila) => {
    const missing = [];
    if (!fila.email) missing.push("email");
    if (!fila.despacho) missing.push("despacho");
    return {
      "docente": `${fila.nombre} ${fila.apellidos}`,
      "categoria": fila.categoria,
      "email": fila.email,
      "despacho": fila.despacho,
      "faltan": missing.join(", "),
    };
  });
```

{{script:Docentes sin despacho o correo}}

#### Resumen de sedes

Calcula el porcentaje de aforo de cada sede sobre el total y ordena por capacidad.

Palabras clave: `educacion`, `agregados`, `sedes`, `map`, `reduce`, `orden`, `math`

```js
const datos = tables["sedes"];
const total = datos.reduce((sum, fila) => sum + fila.aforo_maximo, 0);
return datos
  .map((fila) => ({
    "sede": fila.nombre,
    "ciudad": fila.ciudad,
    "capacidad": fila.aforo_maximo,
    "porcentaje": Math.round((fila.aforo_maximo / total) * 1000) / 10,
  }))
  .sort((a, b) => b["capacidad"] - a["capacidad"]);
```

{{script:Resumen de sedes}}

#### Media ponderada por créditos

Pondera la nota de cada matrícula por los créditos de su asignatura y devuelve una fila.

Palabras clave: `educacion`, `agregados`, `matriculas`, `asignaturas`, `map`, `foreach`, `math`

```js
const datos = tables["matriculas"];
const subjects = new Map(tables["asignaturas"].map((s) => [s.id_asignatura, s.creditos]));
let sum = 0;
let credits = 0;
datos.forEach((fila) => {
  if (fila.nota === null || fila.nota === undefined) return;
  const c = subjects.get(fila.id_asignatura) || 0;
  sum += fila.nota * c;
  credits += c;
});
return [{
  "media": credits ? Math.round((sum / credits) * 100) / 100 : null,
  "creditos": credits,
}];
```

{{script:Media ponderada por créditos}}

#### JavaScript · Línea temporal de matrículas

Matrículas por mes con Date, columnas por estado y un total acumulado.

Palabras clave: `educacion`, `fechas`, `matriculas`, `map`, `orden`, `foreach`

```js
const grupos = new Map();
tables["matriculas"].forEach((fila) => {
  const fecha = new Date(fila.fecha_matricula);
  const mes = `${fecha.getFullYear()}-${String(fecha.getMonth() + 1).padStart(2, "0")}`;
  const grupo = grupos.get(mes) || { mes, total: 0, activa: 0, superada: 0, anulada: 0 };
  grupo.total += 1;
  if (grupo[fila.estado] !== undefined) grupo[fila.estado] += 1;
  grupos.set(mes, grupo);
});
let acumulado = 0;
return [...grupos.values()]
  .sort((a, b) => a.mes.localeCompare(b.mes))
  .map((grupo) => {
    acumulado += grupo.total;
    return {
      "mes": grupo.mes,
      "matriculas": grupo.total,
      "activas": grupo.activa,
      "superadas": grupo.superada,
      "anuladas": grupo.anulada,
      "acumulado": acumulado,
    };
  });
```

{{script:JavaScript · Línea temporal de matrículas}}

#### JavaScript · Pivot de popularidad por asignatura

Una fila por asignatura con estados, tasa de aprobado y marca de las cinco más demandadas de C13.

Palabras clave: `educacion`, `agregados`, `asignaturas`, `matriculas`, `map`, `orden`, `foreach`, `math`

```js
const asignaturas = new Map(tables["asignaturas"].map((a) => [a.id_asignatura, a]));
const top = new Set(queries["C13 · Asignaturas con más matrículas (ORDER BY de un agregado)"]().map((fila) => fila.asignatura));
const grupos = new Map();
tables["matriculas"].forEach((fila) => {
  const asignatura = asignaturas.get(fila.id_asignatura);
  if (!asignatura) return;
  const grupo = grupos.get(fila.id_asignatura) || { asignatura, total: 0, superada: 0, activa: 0, anulada: 0 };
  grupo.total += 1;
  if (grupo[fila.estado] !== undefined) grupo[fila.estado] += 1;
  grupos.set(fila.id_asignatura, grupo);
});
return [...grupos.values()]
  .map((grupo) => ({
    "asignatura": grupo.asignatura.nombre,
    "estudio": grupo.asignatura.codigo_estudio,
    "matriculas": grupo.total,
    "superadas": grupo.superada,
    "activas": grupo.activa,
    "anuladas": grupo.anulada,
    "porcentaje_aprobado": grupo.total ? Math.round((grupo.superada / grupo.total) * 1000) / 10 : 0,
    "top_cinco": top.has(grupo.asignatura.nombre) ? "sí" : "no",
  }))
  .sort((a, b) => b["matriculas"] - a["matriculas"] || a["asignatura"].localeCompare(b["asignatura"]));
```

{{script:JavaScript · Pivot de popularidad por asignatura}}

### B · Uniones (join)

#### Ranking de asignaturas por nota media

Agrupa las matrículas calificadas por asignatura, cruza su nombre y ordena por media.

Palabras clave: `educacion`, `cruces`, `matriculas`, `asignaturas`, `map`, `reduce`, `orden`, `math`

```js
const datos = tables["matriculas"];
const subjects = new Map(tables["asignaturas"].map((s) => [s.id_asignatura, s.nombre]));
const groups = datos.reduce((acc, fila) => {
  if (fila.nota === null || fila.nota === undefined) return acc;
  const key = fila.id_asignatura;
  acc[key] = acc[key] || { sum: 0, count: 0 };
  acc[key].sum += fila.nota;
  acc[key].count += 1;
  return acc;
}, {});
return Object.entries(groups)
  .map(([id, g]) => ({
    "asignatura": subjects.get(Number(id)) || `#${id}`,
    "total": g.count,
    "media": Math.round((g.sum / g.count) * 100) / 100,
  }))
  .sort((a, b) => b["media"] - a["media"])
  .map((row, index) => ({ "puesto": index + 1, ...row }));
```

{{script:Ranking de asignaturas por nota media}}

#### Resumen de matrículas por estudiante

Agrupa por estudiante, lista sus asignaturas y calcula su nota media.

Palabras clave: `educacion`, `cruces`, `matriculas`, `estudiantes`, `asignaturas`, `map`, `reduce`, `orden`, `math`

```js
const datos = tables["matriculas"];
const students = new Map(tables["estudiantes"].map((s) => [s.id_estudiante, `${s.nombre} ${s.apellidos}`]));
const subjects = new Map(tables["asignaturas"].map((s) => [s.id_asignatura, s.nombre]));
const groups = datos.reduce((acc, fila) => {
  const id = fila.id_estudiante;
  acc[id] = acc[id] || { subjects: new Set(), grades: [] };
  acc[id].subjects.add(subjects.get(fila.id_asignatura) || `#${fila.id_asignatura}`);
  if (fila.nota !== null && fila.nota !== undefined) acc[id].grades.push(fila.nota);
  return acc;
}, {});
return Object.entries(groups)
  .map(([id, g]) => ({
    "estudiante": students.get(Number(id)) || `#${id}`,
    "asignaturas": g.subjects.size,
    "media": g.grades.length ? Math.round((g.grades.reduce((a, b) => a + b, 0) / g.grades.length) * 100) / 100 : null,
    "lista": Array.from(g.subjects).join(" · "),
  }))
  .sort((a, b) => b["asignaturas"] - a["asignaturas"]);
```

{{script:Resumen de matrículas por estudiante}}

#### Capacidad de aulas por edificio

Suma la capacidad de las aulas de cada edificio y cruza su nombre.

Palabras clave: `educacion`, `cruces`, `aulas`, `edificios`, `map`, `reduce`, `orden`, `math`

```js
const datos = tables["aulas"];
const buildings = new Map(tables["edificios"].map((b) => [b.id_edificio, b.nombre]));
const groups = datos.reduce((acc, fila) => {
  const key = fila.id_edificio;
  acc[key] = acc[key] || { count: 0, capacity: 0 };
  acc[key].count += 1;
  acc[key].capacity += fila.capacidad;
  return acc;
}, {});
return Object.entries(groups)
  .map(([id, g]) => ({
    "edificio": buildings.get(Number(id)) || `#${id}`,
    "aulas": g.count,
    "capacidad": g.capacity,
    "media": Math.round((g.capacity / g.count) * 10) / 10,
  }))
  .sort((a, b) => b["capacidad"] - a["capacidad"]);
```

{{script:Capacidad de aulas por edificio}}

#### Matrículas huérfanas

Busca matrículas cuyo estudiante o asignatura ya no existe en las tablas.

Palabras clave: `educacion`, `cruces`, `matriculas`, `estudiantes`, `asignaturas`, `filtro`, `map`

```js
const datos = tables["matriculas"];
const studentIds = new Set(tables["estudiantes"].map((s) => s.id_estudiante));
const subjectIds = new Set(tables["asignaturas"].map((s) => s.id_asignatura));
return datos
  .filter((fila) => !studentIds.has(fila.id_estudiante) || !subjectIds.has(fila.id_asignatura))
  .map((fila) => ({
    "matricula": fila.id_matricula,
    "estudiante": fila.id_estudiante,
    "asignatura": fila.id_asignatura,
    "faltan": !studentIds.has(fila.id_estudiante) ? "estudiante" : "asignatura",
  }));
```

{{script:Matrículas huérfanas}}

#### JavaScript · Ocupación por sede

La consulta de estudiantes por sede cruzada con el aforo de la tabla de sedes.

Palabras clave: `educacion`, `cruces`, `sedes`, `map`, `reduce`, `math`

```js
const conteos = queries["H01 · Estudiantes por sede, leídos del script"]();
const sedes = new Map(tables["sedes"].map((s) => [s.nombre, s]));
let acumulado = 0;
const total = conteos.reduce((suma, fila) => suma + fila.estudiantes, 0);
return conteos.map((fila) => {
  const sede = sedes.get(fila.sede);
  const aforo = sede ? sede.aforo_maximo : 0;
  const proporcion = aforo ? Math.round((fila.estudiantes / aforo) * 1000) / 10 : null;
  acumulado += fila.estudiantes;
  return {
    "sede": fila.sede,
    "estudiantes": fila.estudiantes,
    "aforo": aforo,
    "porcentaje_ocupacion": proporcion,
    "porcentaje_total": total ? Math.round((fila.estudiantes / total) * 1000) / 10 : 0,
    "acumulado": acumulado,
    "estado": proporcion === null ? "sin aforo" : proporcion >= 0.25 ? "con presión" : "con holgura",
  };
});
```

{{script:JavaScript · Ocupación por sede}}

#### JavaScript · Lista de control de calidad

Ejecuta cinco consultas de integridad y convierte sus recuentos en una lista de control.

Palabras clave: `educacion`, `cruces`, `map`

```js
const controles = [
  ["Matrículas sin estudiante", "D01 · Matrículas sin estudiante (integridad de la 1:M)"],
  ["Matrículas sin asignatura", "D02 · Matrículas sin asignatura (integridad de la otra 1:M)"],
  ["Asignaturas sin estudio", "D03 · Asignaturas sin estudio (integridad del código de texto)"],
  ["Asignaturas sin docente", "D04 · Asignaturas sin docente"],
  ["Pares estudiante/asignatura repetidos", "D08 · Matrículas repetidas del mismo par estudiante/asignatura"],
];
return controles.map(([etiqueta, consulta]) => {
  const filas = queries[consulta]();
  return {
    "control": etiqueta,
    "incidencias": filas.length,
    "estado": filas.length === 0 ? "correcto" : "revisar",
    "consulta": consulta,
  };
});
```

{{script:JavaScript · Lista de control de calidad}}

### C · Consultas relacionadas

#### Usar una consulta guardada

Llama a la consulta A19 (estudiantes con tres o más matrículas) y muestra su resultado.

Palabras clave: `educacion`, `informes`, `map`

```js
const rows = queries["A19 · Estudiantes con tres o más matrículas"]();
return rows.map((row) => ({
  "estudiante": row.id_estudiante,
  "matriculas": row.matriculas,
}));
```

{{script:Usar una consulta guardada}}

#### Informe con forEach y resultado

Construye un informe por estado con forEach, añade una fila TOTAL y lo guarda en resultado.

Palabras clave: `educacion`, `informes`, `matriculas`, `map`, `foreach`, `math`, `objetos`

```js
const datos = tables["matriculas"];
const groups = {};
datos.forEach((fila) => {
  const key = fila.estado;
  groups[key] = groups[key] || { count: 0, sum: 0 };
  groups[key].count += 1;
  if (fila.nota !== null && fila.nota !== undefined) groups[key].sum += fila.nota;
});
const rows = Object.entries(groups).map(([status, g]) => ({
  "estado": status,
  "total": g.count,
  "media": g.count ? Math.round((g.sum / g.count) * 100) / 100 : null,
}));
rows.push({
  "estado": "TOTAL",
  "total": datos.length,
  "media": null,
});
resultado = rows;
```

{{script:Informe con forEach y resultado}}

#### JavaScript · Directorio de estudiantes con Intl

Directorio ordenado con Intl.Collator, con iniciales, partes del correo y sede.

Palabras clave: `educacion`, `javascript`, `estudiantes`, `sedes`, `map`, `orden`, `slice`, `intl`

```js
const estudiantes = tables["estudiantes"];
const sedes = new Map(tables["sedes"].map((s) => [s.id_sede, s.nombre]));
const collator = new Intl.Collator("es", { sensitivity: "base" });
return estudiantes
  .map((fila) => {
    const email = fila.email ? String(fila.email) : "";
    const at = email.indexOf("@");
    return {
      "estudiante": `${fila.apellidos}, ${fila.nombre}`,
      "iniciales": `${fila.nombre.charAt(0)}${fila.apellidos.charAt(0)}`.toUpperCase(),
      "sede": sedes.get(fila.id_sede) || "—",
      "usuario_correo": at > 0 ? email.slice(0, at) : null,
      "dominio_correo": at > 0 ? email.slice(at + 1) : null,
      "becado": fila.es_becado ? "sí" : "no",
    };
  })
  .sort((a, b) => collator.compare(a["estudiante"], b["estudiante"]));
```

{{script:JavaScript · Directorio de estudiantes con Intl}}

#### JavaScript · Ficha por estudio

Totales por estudio de las tablas unidos a las notas medias por asignatura de la consulta A31.

Palabras clave: `educacion`, `javascript`, `estudios`, `asignaturas`, `map`, `orden`, `foreach`, `math`

```js
const estudios = tables["estudios"];
const asignaturasPorId = new Map(tables["asignaturas"].map((a) => [a.id_asignatura, a]));
const medias = queries["A31 · Nota media por asignatura (HAVING y ORDER BY por alias)"]();
const grupos = new Map(estudios.map((e) => [e.codigo, { estudio: e, asignaturas: 0, calificadas: 0, ponderado: 0 }]));
tables["asignaturas"].forEach((asignatura) => {
  const grupo = grupos.get(asignatura.codigo_estudio);
  if (grupo) grupo.asignaturas += 1;
});
medias.forEach((fila) => {
  const asignatura = asignaturasPorId.get(fila.id_asignatura);
  const grupo = asignatura ? grupos.get(asignatura.codigo_estudio) : null;
  if (!grupo) return;
  grupo.calificadas += fila.calificadas;
  grupo.ponderado += fila.nota_media * fila.calificadas;
});
return [...grupos.values()]
  .map((grupo) => ({
    "estudio": grupo.estudio.nombre,
    "nivel": grupo.estudio.nivel,
    "asignaturas": grupo.asignaturas,
    "matriculas_calificadas": grupo.calificadas,
    "nota_media": grupo.calificadas ? Math.round((grupo.ponderado / grupo.calificadas) * 100) / 100 : null,
    "oficial": grupo.estudio.es_oficial ? "sí" : "no",
  }))
  .sort((a, b) => (b["nota_media"] || 0) - (a["nota_media"] || 0));
```

{{script:JavaScript · Ficha por estudio}}

#### JavaScript · Clasificación de estudiantes

La consulta C03 clasificada con puestos compartidos y enriquecida con notas y becas.

Palabras clave: `educacion`, `informes`, `estudiantes`, `matriculas`, `map`, `foreach`, `math`

```js
const carga = queries["C03 · Carga de cada estudiante (asignaturas y créditos)"]();
const estudiantes = new Map(tables["estudiantes"].map((e) => [e.id_estudiante, e]));
const notas = new Map();
tables["matriculas"].forEach((fila) => {
  if (fila.nota === null || fila.nota === undefined) return;
  const grupo = notas.get(fila.id_estudiante) || { suma: 0, cuenta: 0 };
  grupo.suma += fila.nota;
  grupo.cuenta += 1;
  notas.set(fila.id_estudiante, grupo);
});
let anterior = null;
let puesto = 0;
return carga.map((fila, indice) => {
  if (fila.creditos !== anterior) {
    puesto = indice + 1;
    anterior = fila.creditos;
  }
  const nota = notas.get(fila.id_estudiante);
  const estudiante = estudiantes.get(fila.id_estudiante);
  return {
    "puesto": puesto,
    "estudiante": `${fila.apellidos}, ${fila.nombre}`,
    "asignaturas": fila.asignaturas,
    "creditos": fila.creditos,
    "nota_media": nota ? Math.round((nota.suma / nota.cuenta) * 100) / 100 : null,
    "becado": estudiante && estudiante.es_becado ? "sí" : "no",
  };
});
```

{{script:JavaScript · Clasificación de estudiantes}}

#### JavaScript · Equilibrio de carga docente

Horas semanales frente a la media del proyecto y asignaturas que imparte cada docente.

Palabras clave: `educacion`, `informes`, `docentes`, `asignaturas`, `map`, `reduce`, `orden`, `foreach`

```js
const docentes = tables["docentes"];
const asignaturasPorDocente = new Map();
tables["asignaturas"].forEach((asignatura) => {
  const lista = asignaturasPorDocente.get(asignatura.id_docente) || [];
  lista.push(asignatura.nombre);
  asignaturasPorDocente.set(asignatura.id_docente, lista);
});
const media = docentes.length
  ? docentes.reduce((suma, docente) => suma + docente.horas_semanales, 0) / docentes.length
  : 0;
return docentes
  .map((docente) => {
    const asignaturas = asignaturasPorDocente.get(docente.id_docente) || [];
    const diferencia = Math.round((docente.horas_semanales - media) * 10) / 10;
    return {
      "docente": `${docente.apellidos}, ${docente.nombre}`,
      "categoria": docente.categoria,
      "asignaturas": asignaturas.length,
      "horas_semanales": docente.horas_semanales,
      "media_horas": Math.round(media * 10) / 10,
      "diferencia": diferencia,
      "carga": diferencia >= 6 ? "sobrecargado" : diferencia <= -6 ? "ligero" : "equilibrado",
      "lista_asignaturas": asignaturas.join(" · "),
    };
  })
  .sort((a, b) => b["diferencia"] - a["diferencia"] || a["docente"].localeCompare(b["docente"]));
```

{{script:JavaScript · Equilibrio de carga docente}}

### I · Parámetros

#### P10 · Sedes para el parámetro

Script que sirve de origen de un parámetro: devuelve el id y el nombre de cada sede.

Palabras clave: `educacion`, `parametros`, `origen`, `script`

```js
return tables["sedes"].map((sede) => ({
  id_sede: sede.id_sede,
  nombre: sede.nombre,
}));
```

{{script:P10 · Sedes para el parámetro}}

#### P11 · Matrículas con nota mínima (script con parámetro)

Script parametrizado: la nota mínima se elige en un desplegable de 5 a 10.

Palabras clave: `educacion`, `parametros`, `rango`, `script`

```js
const notaMinima = {{ 5..10 | Nota mínima }};
return tables["matriculas"]
  .filter((matricula) => matricula.nota !== null && matricula.nota >= notaMinima)
  .map((matricula) => ({
    id_matricula: matricula.id_matricula,
    nota: matricula.nota,
    estado: matricula.estado,
  }));
```

{{script:P11 · Matrículas con nota mínima (script con parámetro)}}

#### P17 · Matrículas entre dos notas (script con dos parámetros)

Script parametrizado dos veces: nota mínima y nota máxima, cada una con su desplegable.

Palabras clave: `educacion`, `parametros`, `varios`, `script`

```js
const notaMinima = {{ 5..10 | Nota mínima }};
const notaMaxima = {{ 6..10 | Nota máxima }};
return tables["matriculas"]
  .filter((matricula) => matricula.nota !== null && matricula.nota >= notaMinima && matricula.nota <= notaMaxima)
  .map((matricula) => ({
    id_matricula: matricula.id_matricula,
    nota: matricula.nota,
    estado: matricula.estado,
  }));
```

{{script:P17 · Matrículas entre dos notas (script con dos parámetros)}}
