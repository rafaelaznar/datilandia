# Pruebas SQL · Salud y hospitales (20 tablas, 246 consultas, 35 scripts)

Documento generado a partir de `pruebas-sql-salud.json`.

**Contenido:** 20 tablas · 246 consultas · 35 scripts.

> Carga `pruebas-sql-salud.json` con el botón **Datos** de markilandia para que las inserciones de este documento ejecuten cada consulta y cada script y muestren su resultado.

## Consultas

### A · Básicas y agregados

#### A01 · Todas las especialidades

SELECT * sobre especialidades, en orden por identificador.

Palabras clave: `salud`, `basicas`, `especialidades`, `todas-las-columnas`

```sql
SELECT * FROM especialidades ORDER BY id_especialidad
```

{{query:A01 · Todas las especialidades}}

#### A02 · Hospitales: camas en centenas

Proyección con alias y aritmética: division numérica y orden por camas.

Palabras clave: `salud`, `basicas`, `hospitales`

```sql
SELECT nombre AS hospital, ciudad, num_camas / 100 AS camas_centenas FROM hospitales ORDER BY num_camas DESC
```

{{query:A02 · Hospitales: camas en centenas}}

#### A03 · Hospitales grandes (>= 800 camas)

Filtro numérico con >= y doble orden.

Palabras clave: `salud`, `basicas`, `hospitales`

```sql
SELECT nombre, num_camas, nivel FROM hospitales WHERE num_camas >= 800 ORDER BY num_camas DESC, nombre
```

{{query:A03 · Hospitales grandes (>= 800 camas)}}

#### A04 · Hospitales universitarios con teléfono

Filtro booleano combinado con IS NOT NULL.

Palabras clave: `salud`, `basicas`, `hospitales`, `null`

```sql
SELECT id_hospital, nombre, ciudad FROM hospitales WHERE es_universitario = true AND telefono IS NOT NULL ORDER BY nombre
```

{{query:A04 · Hospitales universitarios con teléfono}}

#### A05 · Pacientes sin teléfono

Los valores que faltan se preguntan con IS NULL.

Palabras clave: `salud`, `basicas`, `pacientes`, `null`

```sql
SELECT id_paciente, nombre, apellidos, ciudad FROM pacientes WHERE telefono IS NULL ORDER BY apellidos, nombre
```

{{query:A05 · Pacientes sin teléfono}}

#### A06 · Correos de médicos en mayúsculas

UPPER sobre un campo que puede faltar: las filas con email null se filtran.

Palabras clave: `salud`, `basicas`, `medicos`, `null`

```sql
SELECT nombre, apellidos, UPPER(email) AS correo FROM medicos WHERE email IS NOT NULL ORDER BY apellidos, nombre
```

{{query:A06 · Correos de médicos en mayúsculas}}

#### A07 · Apellidos que empiezan por G

LIKE con comodín al final.

Palabras clave: `salud`, `basicas`, `medicos`, `like`

```sql
SELECT nombre, apellidos FROM medicos WHERE apellidos LIKE 'G%' ORDER BY apellidos, nombre
```

{{query:A07 · Apellidos que empiezan por G}}

#### A08 · Medicamentos que no empiezan por A

LIKE combinado con NOT.

Palabras clave: `salud`, `basicas`, `medicamentos`, `like`

```sql
SELECT nombre, principio_activo, precio FROM medicamentos WHERE nombre NOT LIKE 'A%' ORDER BY nombre
```

{{query:A08 · Medicamentos que no empiezan por A}}

#### A09 · Intervenciones de riesgo alto o medio

Pertenencia a una lista con IN.

Palabras clave: `salud`, `basicas`, `intervenciones`, `in`

```sql
SELECT nombre, riesgo, coste FROM intervenciones WHERE riesgo IN ('Alto', 'Medio') ORDER BY coste DESC
```

{{query:A09 · Intervenciones de riesgo alto o medio}}

#### A10 · Especialidades que no son Central ni Quirúrgica

NOT IN sobre el área.

Palabras clave: `salud`, `basicas`, `especialidades`, `in`

```sql
SELECT nombre, area, anios_residencia FROM especialidades WHERE area NOT IN ('Central', 'Quirúrgica') ORDER BY nombre
```

{{query:A10 · Especialidades que no son Central ni Quirúrgica}}

#### A11 · Analíticas con glucosa entre 100 y 200

Range con BETWEEN (extremos incluidos).

Palabras clave: `salud`, `basicas`, `analiticas`, `between`

```sql
SELECT id_analitica, glucosa, colesterol FROM analiticas WHERE glucosa BETWEEN 100 AND 200 ORDER BY glucosa DESC LIMIT 15
```

{{query:A11 · Analíticas con glucosa entre 100 y 200}}

#### A12 · Ingresos urgentes con 2 a 5 días previstos

BETWEEN combinado con filtro booleano.

Palabras clave: `salud`, `basicas`, `ingresos`, `between`

```sql
SELECT id_ingreso, motivo, dias_previstos, coste_dia FROM ingresos WHERE es_urgente = true AND dias_previstos BETWEEN 2 AND 5 ORDER BY dias_previstos, coste_dia DESC LIMIT 20
```

{{query:A12 · Ingresos urgentes con 2 a 5 días previstos}}

#### A13 · Categorías de médico (DISTINCT)

Valores distintos de una columna.

Palabras clave: `salud`, `basicas`, `medicos`, `distinct`

```sql
SELECT DISTINCT categoria FROM medicos ORDER BY categoria
```

{{query:A13 · Categorías de médico (DISTINCT)}}

#### A14 · Tipos y estados de cita (DISTINCT de dos columnas)

Combinaciones distintas de tipo y estado.

Palabras clave: `salud`, `basicas`, `citas`, `distinct`

```sql
SELECT DISTINCT tipo, estado FROM citas ORDER BY tipo, estado
```

{{query:A14 · Tipos y estados de cita (DISTINCT de dos columnas)}}

#### A15 · Totales de facturación

COUNT, SUM, MIN, MAX, AVG redondeada.

Palabras clave: `salud`, `basicas`, `facturas`, `agregado`

```sql
SELECT COUNT(*) AS facturas, SUM(importe) AS total, MIN(importe) AS minima, MAX(importe) AS maxima, ROUND(AVG(importe), 2) AS media FROM facturas
```

{{query:A15 · Totales de facturación}}

#### A16 · Facturas por método de pago

Agrupación con COUNT, incluyendo el grupo null.

Palabras clave: `salud`, `basicas`, `facturas`, `agrupacion`, `agregado`

```sql
SELECT COALESCE(metodo_pago, 'sin método') AS metodo, COUNT(*) AS facturas, SUM(importe) AS total FROM facturas GROUP BY metodo_pago ORDER BY total DESC
```

{{query:A16 · Facturas por método de pago}}

#### A17 · Citas por estado

GROUP BY y ORDER BY descendente.

Palabras clave: `salud`, `basicas`, `citas`, `agrupacion`, `agregado`

```sql
SELECT estado, COUNT(*) AS citas FROM citas GROUP BY estado ORDER BY citas DESC, estado
```

{{query:A17 · Citas por estado}}

#### A18 · Estados de cita con más de 10 citas

Filtro de grupos con HAVING.

Palabras clave: `salud`, `basicas`, `citas`, `agrupacion`, `having`, `agregado`

```sql
SELECT estado, COUNT(*) AS citas FROM citas GROUP BY estado HAVING COUNT(*) > 10 ORDER BY citas DESC
```

{{query:A18 · Estados de cita con más de 10 citas}}

#### A19 · Pacientes con tres o más ingresos

HAVING sobre un join paciente-ingreso.

Palabras clave: `salud`, `basicas`, `pacientes`, `ingresos`, `join`, `agrupacion`, `having`, `agregado`

```sql
SELECT p.id_paciente, p.nombre, p.apellidos, COUNT(*) AS ingresos FROM pacientes AS p INNER JOIN ingresos AS i ON i.id_paciente = p.id_paciente GROUP BY p.id_paciente, p.nombre, p.apellidos HAVING COUNT(*) >= 3 ORDER BY ingresos DESC, p.apellidos
```

{{query:A19 · Pacientes con tres o más ingresos}}

#### A20 · Pacientes por inicial del apellido

GROUP BY con una función de texto.

Palabras clave: `salud`, `basicas`, `pacientes`, `agrupacion`, `agregado`

```sql
SELECT SUBSTR(apellidos, 1, 1) AS inicial, COUNT(*) AS pacientes FROM pacientes GROUP BY SUBSTR(apellidos, 1, 1) ORDER BY inicial
```

{{query:A20 · Pacientes por inicial del apellido}}

#### A21 · Glucosa de un paciente, de mayor a menor

ORDER BY descendente con NULLS LAST explícito.

Palabras clave: `salud`, `basicas`, `analiticas`

```sql
SELECT id_analitica, fecha, glucosa FROM analiticas WHERE id_paciente = 3 ORDER BY glucosa DESC NULLS LAST, fecha
```

{{query:A21 · Glucosa de un paciente, de mayor a menor}}

#### A22 · Glucosa de un paciente, de menor a mayor

Con ASC los null van primero por defecto.

Palabras clave: `salud`, `basicas`, `analiticas`

```sql
SELECT id_analitica, glucosa FROM analiticas WHERE id_paciente = 7 ORDER BY glucosa, id_analitica
```

{{query:A22 · Glucosa de un paciente, de menor a mayor}}

#### A23 · Pacientes más mayores, página 2

LIMIT/OFFSET sobre un orden por fecha de nacimiento.

Palabras clave: `salud`, `basicas`, `pacientes`

```sql
SELECT nombre, apellidos, fecha_nacimiento FROM pacientes ORDER BY fecha_nacimiento LIMIT 6 OFFSET 6
```

{{query:A23 · Pacientes más mayores, página 2}}

#### A24 · Cuatro pacientes desde el octavo

Estilo MySQL LIMIT salto, n.

Palabras clave: `salud`, `basicas`, `pacientes`

```sql
SELECT id_paciente, nombre FROM pacientes ORDER BY id_paciente LIMIT 7, 4
```

{{query:A24 · Cuatro pacientes desde el octavo}}

#### A25 · Ficha de tres pacientes

Concatenación con || y funciones de texto.

Palabras clave: `salud`, `basicas`, `pacientes`, `in`

```sql
SELECT UPPER(apellidos) || ', ' || nombre AS ficha, ciudad, grupo_sanguineo FROM pacientes WHERE id_paciente IN (1, 2, 3) ORDER BY id_paciente
```

{{query:A25 · Ficha de tres pacientes}}

#### A26 · Valores ausentes: COALESCE y ROUND

COALESCE con valor por defecto y NULLIF/ROUND.

Palabras clave: `salud`, `basicas`, `analiticas`, `null`

```sql
SELECT id_analitica, COALESCE(glucosa, -1) AS glucosa, IFNULL(colesterol, 0) AS colesterol FROM analiticas WHERE glucosa IS NULL OR colesterol IS NULL ORDER BY id_analitica
```

{{query:A26 · Valores ausentes: COALESCE y ROUND}}

#### A27 · Teléfono de seguro normalizado

IFNULL + TRIM + NULLIF para dejar null los teléfonos vacíos.

Palabras clave: `salud`, `basicas`, `seguros`

```sql
SELECT nombre, NULLIF(TRIM(IFNULL(telefono, '')), '') AS telefono FROM seguros ORDER BY id_seguro
```

{{query:A27 · Teléfono de seguro normalizado}}

#### A28 · Medicamentos orales frente al resto

Operador <> y NOT.

Palabras clave: `salud`, `basicas`, `medicamentos`

```sql
SELECT nombre, via FROM medicamentos WHERE NOT via = 'Oral' ORDER BY via, nombre
```

{{query:A28 · Medicamentos orales frente al resto}}

#### A29 · Ninguna cita supera las dos horas

Consulta sin resultados: rowCount 0.

Palabras clave: `salud`, `basicas`, `citas`

```sql
SELECT id_cita, duracion_min FROM citas WHERE duracion_min > 120
```

{{query:A29 · Ninguna cita supera las dos horas}}

#### A30 · Precio medio por presentación

HAVING y orden por alias.

Palabras clave: `salud`, `basicas`, `medicamentos`, `agrupacion`, `having`, `agregado`

```sql
SELECT presentacion, COUNT(*) AS n, ROUND(AVG(precio), 2) AS precio_medio FROM medicamentos GROUP BY presentacion HAVING COUNT(*) >= 2 ORDER BY precio_medio DESC
```

{{query:A30 · Precio medio por presentación}}

#### A31 · Pacientes y correos por ciudad

JOIN implícito por comas: pacientes y su seguro.

Palabras clave: `salud`, `basicas`, `pacientes`

```sql
SELECT p.nombre AS paciente, s.nombre AS seguro FROM pacientes AS p, seguros AS s WHERE p.id_seguro = s.id_seguro AND p.ciudad = 'Madrid' ORDER BY p.apellidos
```

{{query:A31 · Pacientes y correos por ciudad}}

#### A32 · Pacientes por grupo sanguíneo

GROUP BY contando también el grupo desconocido.

Palabras clave: `salud`, `basicas`, `pacientes`, `agrupacion`, `agregado`

```sql
SELECT COALESCE(grupo_sanguineo, 'desconocido') AS grupo, COUNT(*) AS pacientes FROM pacientes GROUP BY grupo_sanguineo ORDER BY pacientes DESC
```

{{query:A32 · Pacientes por grupo sanguíneo}}

#### A33 · Enfermeros por turno

Agrupación sencilla con DISTINCT implícito.

Palabras clave: `salud`, `basicas`, `enfermeros`, `agrupacion`, `agregado`

```sql
SELECT turno, COUNT(*) AS enfermeros, ROUND(AVG(anios_experiencia), 1) AS experiencia_media FROM enfermeros GROUP BY turno ORDER BY enfermeros DESC
```

{{query:A33 · Enfermeros por turno}}

#### A34 · Habitaciones con baño y más de una cama

Dos filtros booleanos y numérico.

Palabras clave: `salud`, `basicas`, `habitaciones`

```sql
SELECT codigo, tipo, num_camas FROM habitaciones WHERE tiene_bano = true AND num_camas > 1 ORDER BY num_camas DESC, codigo
```

{{query:A34 · Habitaciones con baño y más de una cama}}

#### A35 · Diagnósticos crónicos moderados o graves

Filtro combinado con AND.

Palabras clave: `salud`, `basicas`, `diagnosticos`

```sql
SELECT codigo, nombre, gravedad FROM diagnosticos WHERE es_cronico = true AND gravedad <> 'Leve' ORDER BY nombre
```

{{query:A35 · Diagnósticos crónicos moderados o graves}}

#### A36 · Intervenciones exitosas por quirófano

GROUP BY con SUM y AVG.

Palabras clave: `salud`, `basicas`, `intervenciones`, `agrupacion`, `agregado`

```sql
SELECT quirofano, COUNT(*) AS intervenciones, SUM(coste) AS coste_total, ROUND(AVG(duracion_min), 0) AS duracion_media FROM intervenciones WHERE exitosa = true GROUP BY quirofano ORDER BY coste_total DESC
```

{{query:A36 · Intervenciones exitosas por quirófano}}

#### A37 · Medicamentos con stock bajo

Umbral numérico y orden ascendente.

Palabras clave: `salud`, `basicas`, `medicamentos`

```sql
SELECT nombre, stock, precio FROM medicamentos WHERE stock <= 150 ORDER BY stock
```

{{query:A37 · Medicamentos con stock bajo}}

#### A38 · Médicos veteranos que no investigan

Dos condiciones con NOT.

Palabras clave: `salud`, `basicas`, `medicos`

```sql
SELECT nombre, apellidos, anios_experiencia, categoria FROM medicos WHERE anios_experiencia > 20 AND NOT es_investigador ORDER BY anios_experiencia DESC
```

{{query:A38 · Médicos veteranos que no investigan}}

#### A39 · Seguros con cobertura alta

Filtro numérico y booleano.

Palabras clave: `salud`, `basicas`, `seguros`

```sql
SELECT nombre, tipo, cobertura_porcentaje FROM seguros WHERE cobertura_porcentaje >= 80 ORDER BY cobertura_porcentaje DESC
```

{{query:A39 · Seguros con cobertura alta}}

#### A40 · Alergias graves

Filtro de texto y fecha.

Palabras clave: `salud`, `basicas`, `alergias`

```sql
SELECT id_alergia, sustancia, severidad, fecha_deteccion FROM alergias WHERE severidad = 'Grave' ORDER BY fecha_deteccion DESC
```

{{query:A40 · Alergias graves}}

#### A41 · Nombres de diagnóstico más largos

ORDER BY de una función de texto.

Palabras clave: `salud`, `basicas`, `diagnosticos`

```sql
SELECT codigo, nombre, LENGTH(nombre) AS letras FROM diagnosticos ORDER BY letras DESC, codigo LIMIT 8
```

{{query:A41 · Nombres de diagnóstico más largos}}

#### A42 · Código CIE por prefijo

SUBSTR para extraer la letra inicial.

Palabras clave: `salud`, `basicas`, `diagnosticos`, `distinct`

```sql
SELECT DISTINCT SUBSTR(codigo, 1, 1) AS letra FROM diagnosticos ORDER BY letra
```

{{query:A42 · Código CIE por prefijo}}

#### A43 · Pacientes con grupos sanguíneos concretos

IN sobre textos.

Palabras clave: `salud`, `basicas`, `pacientes`, `in`

```sql
SELECT nombre, apellidos, grupo_sanguineo FROM pacientes WHERE grupo_sanguineo IN ('O-', 'AB+', 'A-') ORDER BY grupo_sanguineo, apellidos
```

{{query:A43 · Pacientes con grupos sanguíneos concretos}}

#### A44 · Citas de la primera mitad de 2025

BETWEEN sobre fechas en formato texto.

Palabras clave: `salud`, `basicas`, `citas`, `between`

```sql
SELECT id_cita, fecha, hora, tipo FROM citas WHERE fecha BETWEEN '2025-01-01' AND '2025-06-30' ORDER BY fecha, hora LIMIT 20
```

{{query:A44 · Citas de la primera mitad de 2025}}

#### A45 · Pacientes sin grupo sanguíneo

IS NULL y ORDER BY.

Palabras clave: `salud`, `basicas`, `pacientes`, `null`

```sql
SELECT id_paciente, nombre, apellidos FROM pacientes WHERE grupo_sanguineo IS NULL ORDER BY apellidos
```

{{query:A45 · Pacientes sin grupo sanguíneo}}

#### A46 · Fichas de pacientes con CONCAT

Une nombre y apellidos con CONCAT, y el grupo sanguíneo con la ciudad: los 7 primeros pacientes por identificador. El resultado es null cuando no hay grupo sanguíneo.

Palabras clave: `salud`, `basicas`, `pacientes`

```sql
SELECT CONCAT(nombre, ' ', apellidos) AS paciente, CONCAT(grupo_sanguineo, ' · ', ciudad) AS grupo_ciudad FROM pacientes ORDER BY id_paciente LIMIT 7
```

{{query:A46 · Fichas de pacientes con CONCAT}}

#### A47 · Totales globales de ingresos con todas las funciones (sin GROUP BY)

Agregados sin GROUP BY sobre los 50 ingresos: 28 pacientes y 9 motivos distintos; suma 30404, mínimo 192, máximo 950 y media redondeada 608,08.

Palabras clave: `salud`, `basicas`, `ingresos`, `agregado`, `distinct`

```sql
SELECT COUNT(*) AS ingresos, COUNT(coste_dia) AS con_coste, COUNT(DISTINCT id_paciente) AS pacientes, COUNT(DISTINCT motivo) AS motivos, SUM(coste_dia) AS suma, MIN(coste_dia) AS minima, MAX(coste_dia) AS maxima, ROUND(AVG(coste_dia), 2) AS media FROM ingresos
```

{{query:A47 · Totales globales de ingresos con todas las funciones (sin GROUP BY)}}

#### A48 · Facturas por método de pago con COUNT de columna y HAVING (columna con NULL)

GROUP BY sobre una columna con nulos: COALESCE etiqueta el grupo sin método y COUNT(metodo_pago) lo deja a cero; HAVING se queda con los grupos que suman 65000 o más.

Palabras clave: `salud`, `basicas`, `facturas`, `agrupacion`, `having`, `agregado`, `distinct`

```sql
SELECT COALESCE(metodo_pago, 'sin método') AS metodo, COUNT(*) AS facturas, COUNT(metodo_pago) AS con_metodo, COUNT(DISTINCT id_paciente) AS pacientes, SUM(importe) AS total, ROUND(AVG(importe), 2) AS media FROM facturas GROUP BY metodo_pago HAVING SUM(importe) >= 65000 ORDER BY total DESC
```

{{query:A48 · Facturas por método de pago con COUNT de columna y HAVING (columna con NULL)}}

#### A49 · Citas por médico y estado (GROUP BY de dos columnas con HAVING)

Dos columnas de agrupación con COUNT(*) y MIN y MAX de la duración: solo tres combinaciones médico-estado llegan a 4 citas.

Palabras clave: `salud`, `basicas`, `citas`, `agrupacion`, `having`, `agregado`

```sql
SELECT id_medico, estado, COUNT(*) AS citas, MIN(duracion_min) AS minima, MAX(duracion_min) AS maxima FROM citas GROUP BY id_medico, estado HAVING COUNT(*) >= 4 ORDER BY citas DESC, id_medico, estado
```

{{query:A49 · Citas por médico y estado (GROUP BY de dos columnas con HAVING)}}

#### A50 · Pacientes por grupo sanguíneo (COUNT(*) frente a COUNT de columna)

El grupo desconocido sale con COUNT(*) pero con COUNT(grupo_sanguineo) a cero; COUNT(DISTINCT ciudad) cuenta ciudades distintas por grupo y el HAVING pide 3 pacientes.

Palabras clave: `salud`, `basicas`, `pacientes`, `agrupacion`, `having`, `agregado`, `distinct`

```sql
SELECT COALESCE(grupo_sanguineo, 'desconocido') AS grupo, COUNT(*) AS pacientes, COUNT(grupo_sanguineo) AS con_grupo, COUNT(DISTINCT ciudad) AS ciudades FROM pacientes GROUP BY grupo_sanguineo HAVING COUNT(*) >= 3 ORDER BY pacientes DESC, grupo
```

{{query:A50 · Pacientes por grupo sanguíneo (COUNT(*) frente a COUNT de columna)}}

#### A51 · Ingresos por médico con todas las funciones numéricas (HAVING doble)

COUNT(*), SUM, MIN, MAX y AVG del coste por médico; HAVING combina COUNT(*) y AVG: tres ingresos como mínimo y media de 600 o más.

Palabras clave: `salud`, `basicas`, `ingresos`, `agrupacion`, `having`, `agregado`

```sql
SELECT id_medico, COUNT(*) AS ingresos, SUM(coste_dia) AS coste_total, MIN(coste_dia) AS minima, MAX(coste_dia) AS maxima, ROUND(AVG(coste_dia), 2) AS media FROM ingresos GROUP BY id_medico HAVING COUNT(*) >= 3 AND AVG(coste_dia) >= 600 ORDER BY coste_total DESC, id_medico
```

{{query:A51 · Ingresos por médico con todas las funciones numéricas (HAVING doble)}}

#### A52 · Habitaciones por hospital y ocupación (GROUP BY de dos columnas con SUM)

Agrupación por hospital y ocupación con COUNT(*) y SUM(num_camas); HAVING deja solo los grupos de 4 habitaciones o más.

Palabras clave: `salud`, `basicas`, `habitaciones`, `agrupacion`, `having`, `agregado`

```sql
SELECT id_hospital, ocupada, COUNT(*) AS habitaciones, SUM(num_camas) AS camas FROM habitaciones GROUP BY id_hospital, ocupada HAVING COUNT(*) >= 4 ORDER BY id_hospital, ocupada
```

{{query:A52 · Habitaciones por hospital y ocupación (GROUP BY de dos columnas con SUM)}}

#### A53 · Intervenciones por riesgo con todas las funciones (HAVING SUM)

COUNT(*), COUNT(DISTINCT quirofano), SUM, MIN, MAX y AVG por riesgo; HAVING sobre la suma descarta el riesgo alto.

Palabras clave: `salud`, `basicas`, `intervenciones`, `agrupacion`, `having`, `agregado`, `distinct`

```sql
SELECT riesgo, COUNT(*) AS intervenciones, COUNT(DISTINCT quirofano) AS quirofanos, SUM(coste) AS coste_total, MIN(duracion_min) AS min_duracion, MAX(duracion_min) AS max_duracion, ROUND(AVG(coste), 0) AS coste_medio FROM intervenciones GROUP BY riesgo HAVING SUM(coste) > 65000 ORDER BY coste_total DESC
```

{{query:A53 · Intervenciones por riesgo con todas las funciones (HAVING SUM)}}

#### A54 · Analíticas por tipo con COUNT(DISTINCT) y AVG (HAVING COUNT DISTINCT)

Por tipo de analítica: COUNT(*) de filas, COUNT(DISTINCT id_paciente) de pacientes y dos AVG redondeados; HAVING exige 15 pacientes distintos.

Palabras clave: `salud`, `basicas`, `analiticas`, `agrupacion`, `having`, `agregado`, `distinct`

```sql
SELECT tipo, COUNT(*) AS analiticas, COUNT(DISTINCT id_paciente) AS pacientes, ROUND(AVG(glucosa), 1) AS glucosa_media, ROUND(AVG(colesterol), 1) AS colesterol_medio FROM analiticas GROUP BY tipo HAVING COUNT(DISTINCT id_paciente) >= 15 ORDER BY analiticas DESC
```

{{query:A54 · Analíticas por tipo con COUNT(DISTINCT) y AVG (HAVING COUNT DISTINCT)}}

#### A55 · Prescripciones por medicamento con SUM, MIN, MAX y AVG (HAVING COUNT)

Por medicamento, COUNT(*) con SUM de unidades y MIN, MAX y AVG de la duración; HAVING pide 8 prescripciones o más.

Palabras clave: `salud`, `basicas`, `prescripciones`, `agrupacion`, `having`, `agregado`

```sql
SELECT id_medicamento, COUNT(*) AS prescripciones, SUM(cantidad) AS unidades, MIN(duracion_dias) AS min_dias, MAX(duracion_dias) AS max_dias, ROUND(AVG(duracion_dias), 1) AS media_dias FROM prescripciones GROUP BY id_medicamento HAVING COUNT(*) >= 8 ORDER BY unidades DESC, id_medicamento
```

{{query:A55 · Prescripciones por medicamento con SUM, MIN, MAX y AVG (HAVING COUNT)}}

#### A56 · Tratamientos por año con SUM y AVG (GROUP BY con expresión y HAVING)

GROUP BY sobre el año de inicio: COUNT(*), COUNT(DISTINCT id_paciente), SUM y AVG redondeado del coste; HAVING deja los años con 10 tratamientos o más.

Palabras clave: `salud`, `basicas`, `tratamientos`, `agrupacion`, `having`, `agregado`, `distinct`

```sql
SELECT SUBSTR(fecha_inicio, 1, 4) AS anio, COUNT(*) AS tratamientos, COUNT(DISTINCT id_paciente) AS pacientes, SUM(coste) AS coste_total, ROUND(AVG(coste), 0) AS coste_medio FROM tratamientos GROUP BY SUBSTR(fecha_inicio, 1, 4) HAVING COUNT(*) >= 10 ORDER BY anio
```

{{query:A56 · Tratamientos por año con SUM y AVG (GROUP BY con expresión y HAVING)}}

#### A57 · Agregados sobre la propia columna agrupada (COUNT de estado)

COUNT(estado) coincide con COUNT(*) y COUNT(DISTINCT estado) vale 1 en cada grupo; MAX(id_cita) y COUNT(duracion_min) resumen el resto de columnas.

Palabras clave: `salud`, `basicas`, `citas`, `agrupacion`, `agregado`, `distinct`

```sql
SELECT estado, COUNT(*) AS filas, COUNT(estado) AS con_estado, COUNT(DISTINCT estado) AS estados, MAX(id_cita) AS ultima, COUNT(duracion_min) AS con_duracion FROM citas GROUP BY estado ORDER BY filas DESC, estado
```

{{query:A57 · Agregados sobre la propia columna agrupada (COUNT de estado)}}

#### A58 · HAVING con agregados que no salen en el SELECT

HAVING con COUNT(*) y MIN(duracion_min) fuera del SELECT: solo las citas pendientes pasan de 10 citas y bajan de 20 minutos.

Palabras clave: `salud`, `basicas`, `citas`, `agrupacion`, `having`, `agregado`

```sql
SELECT estado FROM citas GROUP BY estado HAVING COUNT(*) >= 10 AND MIN(duracion_min) < 20 ORDER BY estado
```

{{query:A58 · HAVING con agregados que no salen en el SELECT}}

#### A59 · Ingresos urgentes por motivo (WHERE, GROUP BY y HAVING)

WHERE deja solo las urgencias y luego agrupa por motivo: COUNT(*) de ingresos y COUNT(coste_dia) de los que tienen coste, con HAVING de 4 o más.

Palabras clave: `salud`, `basicas`, `ingresos`, `agrupacion`, `having`, `agregado`

```sql
SELECT motivo, COUNT(*) AS ingresos, COUNT(coste_dia) AS con_coste FROM ingresos WHERE es_urgente = true GROUP BY motivo HAVING COUNT(*) >= 4 ORDER BY ingresos DESC, motivo
```

{{query:A59 · Ingresos urgentes por motivo (WHERE, GROUP BY y HAVING)}}

#### A60 · REGEXPR_REPLACE: espacios, grupos y reemplazo global

Normaliza espacios, reordena un nombre separado por coma con un grupo y reescribe todos los códigos con una expresión regular global.

Palabras clave: `salud`, `basicas`, `regexpr-replace`, `grupos`, `global`

```sql
SELECT TRIM(REGEXPR_REPLACE('  Cardiología   Médica  ', '[ ]+', ' ')) AS normalizado,
       REGEXPR_REPLACE('Médica, Cardiología', '([^,]+), (.+)', '$2 $1') AS reordenado,
       REGEXPR_REPLACE('A-01 / B-02', '[A-Z]-([0-9]+)', 'code-$1') AS codigos
FROM especialidades
WHERE id_especialidad = 1
```

{{query:A60 · REGEXPR_REPLACE: espacios, grupos y reemplazo global}}

#### A61 · REGEXPR_INSTR: posición de la primera coincidencia y banderas

Los tres ejemplos: 123 empieza en 4, sin dígitos da 0 y el ancla ^ coincide en 1. También comprueba la bandera i, el alias REGEXP_INSTR y un texto NULL.

Palabras clave: `salud`, `regex`, `funciones`, `posicion`

```sql
SELECT REGEXPR_INSTR('abc123def', '[0-9]+') AS digitos, REGEXPR_INSTR('hola', '[0-9]+') AS sin_coincidencia, REGEXPR_INSTR('404 Not Found', '^[0-9]+') AS al_inicio, REGEXPR_INSTR('xxABCyy', 'abc', 'i') AS con_bandera, REGEXPR_INSTR('xxABCyy', 'abc') AS sin_bandera, REGEXP_INSTR('abc123def', '[0-9]+') AS alias, REGEXPR_INSTR(NULL, '[0-9]+') AS texto_nulo FROM hospitales LIMIT 1
```

{{query:A61 · REGEXPR_INSTR: posición de la primera coincidencia y banderas}}

#### A62 · REGEXPR_INSTR sobre apellidos

Busca el primer espacio de cada apellido: Rubio González en 6, Navarro Molina en 8 y Díaz Domínguez en 5.

Palabras clave: `salud`, `regex`, `funciones`, `pacientes`

```sql
SELECT id_paciente, apellidos, REGEXPR_INSTR(apellidos, ' ') AS primer_espacio FROM pacientes WHERE id_paciente <= 5 ORDER BY id_paciente
```

{{query:A62 · REGEXPR_INSTR sobre apellidos}}

### B · Uniones (join)

#### B01 · Hospitales con sus servicios

INNER JOIN 1:M entre hospital y servicio.

Palabras clave: `salud`, `uniones`, `hospitales`, `servicios`, `join`

```sql
SELECT h.nombre AS hospital, s.nombre AS servicio, s.capacidad_camas FROM hospitales AS h INNER JOIN servicios AS s ON s.id_hospital = h.id_hospital ORDER BY h.nombre, s.nombre
```

{{query:B01 · Hospitales con sus servicios}}

#### B02 · Servicios con su hospital y especialidad

Tres tablas encadenadas.

Palabras clave: `salud`, `uniones`, `servicios`, `especialidades`, `hospitales`, `join`

```sql
SELECT s.nombre AS servicio, e.nombre AS especialidad, h.nombre AS hospital FROM servicios AS s INNER JOIN especialidades AS e ON e.id_especialidad = s.id_especialidad INNER JOIN hospitales AS h ON h.id_hospital = s.id_hospital ORDER BY h.nombre, s.nombre
```

{{query:B02 · Servicios con su hospital y especialidad}}

#### B03 · Hospitales sin servicios

LEFT JOIN para encontrar la orfandad.

Palabras clave: `salud`, `uniones`, `hospitales`, `servicios`, `join`, `null`

```sql
SELECT h.nombre AS hospital FROM hospitales AS h LEFT JOIN servicios AS s ON s.id_hospital = h.id_hospital WHERE s.id_servicio IS NULL ORDER BY h.nombre
```

{{query:B03 · Hospitales sin servicios}}

#### B04 · Especialidades sin médicos

LEFT JOIN y filtro por null.

Palabras clave: `salud`, `uniones`, `especialidades`, `medicos`, `join`, `null`

```sql
SELECT e.nombre AS especialidad FROM especialidades AS e LEFT JOIN medicos AS m ON m.id_especialidad = e.id_especialidad WHERE m.id_medico IS NULL ORDER BY e.nombre
```

{{query:B04 · Especialidades sin médicos}}

#### B05 · Join implícito: médicos y servicios

Relación por comas en el FROM.

Palabras clave: `salud`, `uniones`, `medicos`

```sql
SELECT m.nombre, m.apellidos, s.nombre AS servicio FROM medicos AS m, servicios AS s WHERE m.id_servicio = s.id_servicio AND s.id_hospital = 1 ORDER BY m.apellidos
```

{{query:B05 · Join implícito: médicos y servicios}}

#### B06 · Médicos con su especialidad

INNER JOIN por la clave de especialidad.

Palabras clave: `salud`, `uniones`, `medicos`, `especialidades`, `join`

```sql
SELECT m.apellidos, m.nombre, e.nombre AS especialidad, m.categoria FROM medicos AS m INNER JOIN especialidades AS e ON e.id_especialidad = m.id_especialidad ORDER BY m.apellidos LIMIT 15
```

{{query:B06 · Médicos con su especialidad}}

#### B07 · Especialidades con al menos tres médicos

LEFT JOIN + HAVING.

Palabras clave: `salud`, `uniones`, `especialidades`, `medicos`, `join`, `agrupacion`, `having`, `agregado`

```sql
SELECT e.nombre AS especialidad, COUNT(m.id_medico) AS medicos FROM especialidades AS e LEFT JOIN medicos AS m ON m.id_especialidad = e.id_especialidad GROUP BY e.nombre HAVING COUNT(m.id_medico) >= 3 ORDER BY medicos DESC
```

{{query:B07 · Especialidades con al menos tres médicos}}

#### B08 · Pacientes por seguro

Agrupación de un join con la cobertura.

Palabras clave: `salud`, `uniones`, `seguros`, `pacientes`, `join`, `agrupacion`, `agregado`

```sql
SELECT s.nombre AS seguro, COUNT(p.id_paciente) AS pacientes, s.cobertura_porcentaje FROM seguros AS s LEFT JOIN pacientes AS p ON p.id_seguro = s.id_seguro GROUP BY s.nombre, s.cobertura_porcentaje ORDER BY pacientes DESC
```

{{query:B08 · Pacientes por seguro}}

#### B09 · Habitaciones por hospital

LEFT JOIN contando habitaciones, incluso a 0.

Palabras clave: `salud`, `uniones`, `hospitales`, `habitaciones`, `join`, `agrupacion`, `agregado`

```sql
SELECT h.nombre AS hospital, COUNT(ha.id_habitacion) AS habitaciones, SUM(ha.num_camas) AS camas FROM hospitales AS h LEFT JOIN habitaciones AS ha ON ha.id_hospital = h.id_hospital GROUP BY h.nombre ORDER BY habitaciones DESC
```

{{query:B09 · Habitaciones por hospital}}

#### B10 · Ingresos con paciente, habitación y médico

Cuatro tablas en un mismo join.

Palabras clave: `salud`, `uniones`, `ingresos`, `pacientes`, `habitaciones`, `join`

```sql
SELECT i.id_ingreso, p.apellidos AS paciente, ha.codigo AS habitacion, m.apellidos AS medico, i.motivo FROM ingresos AS i INNER JOIN pacientes AS p ON p.id_paciente = i.id_paciente LEFT JOIN habitaciones AS ha ON ha.id_habitacion = i.id_habitacion INNER JOIN medicos AS m ON m.id_medico = i.id_medico ORDER BY i.id_ingreso LIMIT 25
```

{{query:B10 · Ingresos con paciente, habitación y médico}}

#### B11 · Citas con paciente y médico

Join de la agenda.

Palabras clave: `salud`, `uniones`, `citas`, `pacientes`, `medicos`, `join`

```sql
SELECT c.id_cita, c.fecha, p.apellidos AS paciente, m.apellidos AS medico, c.estado FROM citas AS c INNER JOIN pacientes AS p ON p.id_paciente = c.id_paciente INNER JOIN medicos AS m ON m.id_medico = c.id_medico ORDER BY c.fecha DESC, c.hora LIMIT 25
```

{{query:B11 · Citas con paciente y médico}}

#### B12 · Visitas con su médico

Join directo con orden por fecha.

Palabras clave: `salud`, `uniones`, `visitas`, `medicos`, `join`

```sql
SELECT v.id_visita, v.fecha, v.motivo, m.apellidos AS medico FROM visitas AS v INNER JOIN medicos AS m ON m.id_medico = v.id_medico ORDER BY v.fecha, v.id_visita LIMIT 20
```

{{query:B12 · Visitas con su médico}}

#### B13 · Intervenciones con paciente y cirujano

Dos joins y filtro por riesgo.

Palabras clave: `salud`, `uniones`, `intervenciones`, `pacientes`, `medicos`, `join`

```sql
SELECT iv.nombre AS intervencion, p.apellidos AS paciente, m.apellidos AS cirujano, iv.riesgo FROM intervenciones AS iv INNER JOIN pacientes AS p ON p.id_paciente = iv.id_paciente INNER JOIN medicos AS m ON m.id_medico = iv.id_medico WHERE iv.riesgo = 'Alto' ORDER BY iv.coste DESC
```

{{query:B13 · Intervenciones con paciente y cirujano}}

#### B14 · Producto cartesiano especialidades × hospitales

Sin condición de join: todas las combinaciones.

Palabras clave: `salud`, `uniones`, `hospitales`

```sql
SELECT h.nombre AS hospital, e.nombre AS especialidad FROM hospitales AS h, especialidades AS e ORDER BY h.id_hospital, e.id_especialidad
```

{{query:B14 · Producto cartesiano especialidades × hospitales}}

#### B15 · Los cinco medicamentos más caros

Join con prescripciones y límite.

Palabras clave: `salud`, `uniones`, `medicamentos`, `prescripciones`, `join`, `agrupacion`, `agregado`

```sql
SELECT med.nombre, med.precio, COUNT(*) AS prescripciones FROM medicamentos AS med INNER JOIN prescripciones AS pr ON pr.id_medicamento = med.id_medicamento GROUP BY med.nombre, med.precio ORDER BY med.precio DESC LIMIT 5
```

{{query:B15 · Los cinco medicamentos más caros}}

#### B16 · Especialidades con médicos distintos

COUNT DISTINCT dentro de un join.

Palabras clave: `salud`, `uniones`, `especialidades`, `medicos`, `servicios`, `join`, `agrupacion`, `agregado`, `distinct`

```sql
SELECT e.nombre AS especialidad, COUNT(DISTINCT m.id_medico) AS medicos, COUNT(DISTINCT s.id_servicio) AS servicios FROM especialidades AS e LEFT JOIN medicos AS m ON m.id_especialidad = e.id_especialidad LEFT JOIN servicios AS s ON s.id_especialidad = e.id_especialidad GROUP BY e.nombre ORDER BY medicos DESC
```

{{query:B16 · Especialidades con médicos distintos}}

#### B17 · Experiencia por categoría

MIN y MAX por grupo.

Palabras clave: `salud`, `uniones`, `medicos`, `agrupacion`, `agregado`

```sql
SELECT categoria, COUNT(*) AS medicos, MIN(anios_experiencia) AS min_exp, MAX(anios_experiencia) AS max_exp FROM medicos GROUP BY categoria ORDER BY max_exp DESC
```

{{query:B17 · Experiencia por categoría}}

#### B18 · Tratamientos activos con paciente y diagnóstico

Join de tres tablas y filtro booleano.

Palabras clave: `salud`, `uniones`, `tratamientos`, `pacientes`, `diagnosticos`, `join`

```sql
SELECT t.id_tratamiento, p.apellidos AS paciente, d.nombre AS diagnostico, t.coste FROM tratamientos AS t INNER JOIN pacientes AS p ON p.id_paciente = t.id_paciente INNER JOIN diagnosticos AS d ON d.id_diagnostico = t.id_diagnostico WHERE t.activo = true ORDER BY t.coste DESC LIMIT 20
```

{{query:B18 · Tratamientos activos con paciente y diagnóstico}}

#### B19 · Analíticas anormales con paciente y médico

Filtro booleano sobre un join.

Palabras clave: `salud`, `uniones`, `analiticas`, `pacientes`, `medicos`, `join`

```sql
SELECT a.id_analitica, p.apellidos AS paciente, m.apellidos AS medico, a.glucosa, a.colesterol FROM analiticas AS a INNER JOIN pacientes AS p ON p.id_paciente = a.id_paciente INNER JOIN medicos AS m ON m.id_medico = a.id_medico WHERE a.es_anormal = true ORDER BY a.fecha DESC LIMIT 20
```

{{query:B19 · Analíticas anormales con paciente y médico}}

#### B20 · Facturas no pagadas con paciente

LEFT JOIN del seguro y filtro booleano.

Palabras clave: `salud`, `uniones`, `facturas`, `pacientes`, `seguros`, `join`

```sql
SELECT f.id_factura, p.apellidos AS paciente, f.importe, s.nombre AS seguro FROM facturas AS f INNER JOIN pacientes AS p ON p.id_paciente = f.id_paciente LEFT JOIN seguros AS s ON s.id_seguro = p.id_seguro WHERE f.pagada = false ORDER BY f.importe DESC LIMIT 20
```

{{query:B20 · Facturas no pagadas con paciente}}

#### B21 · Ingresos activos con su habitación

IS NULL para los que siguen ingresados.

Palabras clave: `salud`, `uniones`, `ingresos`, `pacientes`, `habitaciones`, `join`, `null`

```sql
SELECT i.id_ingreso, p.apellidos AS paciente, ha.codigo, i.fecha_ingreso FROM ingresos AS i INNER JOIN pacientes AS p ON p.id_paciente = i.id_paciente LEFT JOIN habitaciones AS ha ON ha.id_habitacion = i.id_habitacion WHERE i.fecha_alta IS NULL ORDER BY i.fecha_ingreso
```

{{query:B21 · Ingresos activos con su habitación}}

#### B22 · Enfermeros y su servicio

Join con datos de ambos.

Palabras clave: `salud`, `uniones`, `enfermeros`, `servicios`, `hospitales`, `join`

```sql
SELECT en.apellidos AS enfermero, en.turno, s.nombre AS servicio, h.nombre AS hospital FROM enfermeros AS en INNER JOIN servicios AS s ON s.id_servicio = en.id_servicio INNER JOIN hospitales AS h ON h.id_hospital = s.id_hospital ORDER BY en.apellidos LIMIT 20
```

{{query:B22 · Enfermeros y su servicio}}

#### B23 · Visitas con su diagnóstico principal

Join visita → relación → diagnóstico.

Palabras clave: `salud`, `uniones`, `visitas`, `visita_diagnosticos`, `diagnosticos`, `join`

```sql
SELECT v.id_visita, v.motivo, d.nombre AS diagnostico, d.gravedad FROM visitas AS v INNER JOIN visita_diagnosticos AS vd ON vd.id_visita = v.id_visita INNER JOIN diagnosticos AS d ON d.id_diagnostico = vd.id_diagnostico WHERE vd.es_principal = true ORDER BY v.id_visita LIMIT 25
```

{{query:B23 · Visitas con su diagnóstico principal}}

#### B24 · Prescripciones con medicamento y tratamiento

Resuelve la N:M mostrando los dos extremos.

Palabras clave: `salud`, `uniones`, `prescripciones`, `medicamentos`, `tratamientos`, `join`

```sql
SELECT pr.id_prescripcion, med.nombre AS medicamento, pr.dosis, t.id_tratamiento, p.apellidos AS paciente FROM prescripciones AS pr INNER JOIN medicamentos AS med ON med.id_medicamento = pr.id_medicamento INNER JOIN tratamientos AS t ON t.id_tratamiento = pr.id_tratamiento INNER JOIN pacientes AS p ON p.id_paciente = t.id_paciente ORDER BY pr.id_prescripcion LIMIT 25
```

{{query:B24 · Prescripciones con medicamento y tratamiento}}

#### B25 · Tratamientos por diagnóstico

GROUP BY de un join con HAVING.

Palabras clave: `salud`, `uniones`, `diagnosticos`, `tratamientos`, `join`, `agrupacion`, `having`, `agregado`

```sql
SELECT d.nombre AS diagnostico, COUNT(t.id_tratamiento) AS tratamientos, ROUND(AVG(t.coste), 0) AS coste_medio FROM diagnosticos AS d INNER JOIN tratamientos AS t ON t.id_diagnostico = d.id_diagnostico GROUP BY d.nombre HAVING COUNT(t.id_tratamiento) >= 2 ORDER BY tratamientos DESC
```

{{query:B25 · Tratamientos por diagnóstico}}

#### B26 · Coste de tratamientos por médico

SUM y COUNT agrupados.

Palabras clave: `salud`, `uniones`, `tratamientos`, `medicos`, `join`, `agrupacion`, `agregado`

```sql
SELECT m.apellidos AS medico, COUNT(*) AS tratamientos, SUM(t.coste) AS coste_total FROM tratamientos AS t INNER JOIN medicos AS m ON m.id_medico = t.id_medico GROUP BY m.apellidos ORDER BY coste_total DESC LIMIT 10
```

{{query:B26 · Coste de tratamientos por médico}}

#### B27 · Intervenciones por nivel de riesgo

Agrupación con coste medio.

Palabras clave: `salud`, `uniones`, `intervenciones`, `agrupacion`, `agregado`

```sql
SELECT riesgo, COUNT(*) AS intervenciones, ROUND(AVG(coste), 0) AS coste_medio FROM intervenciones GROUP BY riesgo ORDER BY coste_medio DESC
```

{{query:B27 · Intervenciones por nivel de riesgo}}

#### B28 · Pacientes con seguro y ciudad

Join con COALESCE para el seguro ausente.

Palabras clave: `salud`, `uniones`, `pacientes`, `seguros`, `join`

```sql
SELECT p.apellidos AS paciente, COALESCE(s.nombre, 'sin seguro') AS seguro, p.ciudad FROM pacientes AS p LEFT JOIN seguros AS s ON s.id_seguro = p.id_seguro ORDER BY p.ciudad, p.apellidos LIMIT 25
```

{{query:B28 · Pacientes con seguro y ciudad}}

#### B29 · Alergias con medicamento implicado

LEFT JOIN porque hay alergias sin medicamento.

Palabras clave: `salud`, `uniones`, `alergias`, `medicamentos`, `pacientes`, `join`

```sql
SELECT al.sustancia, al.severidad, med.nombre AS medicamento, p.apellidos AS paciente FROM alergias AS al LEFT JOIN medicamentos AS med ON med.id_medicamento = al.id_medicamento INNER JOIN pacientes AS p ON p.id_paciente = al.id_paciente ORDER BY al.severidad DESC, al.sustancia LIMIT 25
```

{{query:B29 · Alergias con medicamento implicado}}

#### B30 · Camas por hospital (solo con habitaciones)

INNER JOIN y suma de camas.

Palabras clave: `salud`, `uniones`, `hospitales`, `habitaciones`, `join`, `agrupacion`, `agregado`

```sql
SELECT h.nombre AS hospital, COUNT(ha.id_habitacion) AS habitaciones, SUM(ha.num_camas) AS camas FROM hospitales AS h INNER JOIN habitaciones AS ha ON ha.id_hospital = h.id_hospital GROUP BY h.nombre ORDER BY camas DESC
```

{{query:B30 · Camas por hospital (solo con habitaciones)}}

#### B31 · Ingresos por mes

GROUP BY con SUBSTR sobre la fecha.

Palabras clave: `salud`, `uniones`, `ingresos`, `agrupacion`, `agregado`

```sql
SELECT SUBSTR(fecha_ingreso, 1, 7) AS mes, COUNT(*) AS ingresos FROM ingresos GROUP BY SUBSTR(fecha_ingreso, 1, 7) ORDER BY mes
```

{{query:B31 · Ingresos por mes}}

#### B32 · Facturación por año

SUBSTR para el año y suma de importes.

Palabras clave: `salud`, `uniones`, `facturas`, `agrupacion`, `agregado`

```sql
SELECT SUBSTR(fecha, 1, 4) AS anio, COUNT(*) AS facturas, SUM(importe) AS total FROM facturas GROUP BY SUBSTR(fecha, 1, 4) ORDER BY anio
```

{{query:B32 · Facturación por año}}

#### B33 · Médicos con más visitas

Join y GROUP BY con LIMIT.

Palabras clave: `salud`, `uniones`, `medicos`, `visitas`, `join`, `agrupacion`, `agregado`

```sql
SELECT m.apellidos AS medico, COUNT(v.id_visita) AS visitas FROM medicos AS m INNER JOIN visitas AS v ON v.id_medico = m.id_medico GROUP BY m.apellidos ORDER BY visitas DESC LIMIT 10
```

{{query:B33 · Médicos con más visitas}}

#### B34 · Citas canceladas por médico

Filtro de estado sobre un join.

Palabras clave: `salud`, `uniones`, `citas`, `medicos`, `join`, `agrupacion`, `agregado`

```sql
SELECT m.apellidos AS medico, COUNT(*) AS canceladas FROM citas AS c INNER JOIN medicos AS m ON m.id_medico = c.id_medico WHERE c.estado = 'Cancelada' GROUP BY m.apellidos ORDER BY canceladas DESC LIMIT 10
```

{{query:B34 · Citas canceladas por médico}}

#### B35 · Diagnósticos por especialidad

COUNT sobre un join.

Palabras clave: `salud`, `uniones`, `especialidades`, `diagnosticos`, `join`, `agrupacion`, `agregado`

```sql
SELECT e.nombre AS especialidad, COUNT(d.id_diagnostico) AS diagnosticos FROM especialidades AS e LEFT JOIN diagnosticos AS d ON d.id_especialidad = e.id_especialidad GROUP BY e.nombre ORDER BY diagnosticos DESC
```

{{query:B35 · Diagnósticos por especialidad}}

#### B36 · Medicamentos por tratamiento

Cuenta la N:M con ORDER BY de un agregado.

Palabras clave: `salud`, `uniones`, `tratamientos`, `prescripciones`, `pacientes`, `join`, `agrupacion`, `agregado`

```sql
SELECT t.id_tratamiento, p.apellidos AS paciente, COUNT(pr.id_prescripcion) AS medicamentos FROM tratamientos AS t INNER JOIN prescripciones AS pr ON pr.id_tratamiento = t.id_tratamiento INNER JOIN pacientes AS p ON p.id_paciente = t.id_paciente GROUP BY t.id_tratamiento, p.apellidos ORDER BY medicamentos DESC, t.id_tratamiento LIMIT 15
```

{{query:B36 · Medicamentos por tratamiento}}

#### B37 · Intervenciones por quirófano

Agrupación con conteo y duraciones extremas.

Palabras clave: `salud`, `uniones`, `intervenciones`, `agrupacion`, `agregado`

```sql
SELECT quirofano, COUNT(*) AS intervenciones, MIN(duracion_min) AS min_duracion, MAX(duracion_min) AS max_duracion FROM intervenciones GROUP BY quirofano ORDER BY intervenciones DESC
```

{{query:B37 · Intervenciones por quirófano}}

#### B38 · Pacientes crónicos con tratamientos activos

Dos filtros y join de tres tablas.

Palabras clave: `salud`, `uniones`, `pacientes`, `tratamientos`, `join`, `distinct`

```sql
SELECT DISTINCT p.apellidos AS paciente, p.ciudad FROM pacientes AS p INNER JOIN tratamientos AS t ON t.id_paciente = p.id_paciente WHERE p.es_cronico = true AND t.activo = true ORDER BY p.apellidos
```

{{query:B38 · Pacientes crónicos con tratamientos activos}}

#### B39 · Especialidades con más visitas

Cadena servicio → especialidad → visitas.

Palabras clave: `salud`, `uniones`, `visitas`, `medicos`, `especialidades`, `join`, `agrupacion`, `agregado`

```sql
SELECT e.nombre AS especialidad, COUNT(v.id_visita) AS visitas FROM visitas AS v INNER JOIN medicos AS m ON m.id_medico = v.id_medico INNER JOIN especialidades AS e ON e.id_especialidad = m.id_especialidad GROUP BY e.nombre ORDER BY visitas DESC
```

{{query:B39 · Especialidades con más visitas}}

#### B40 · Visitas por tipo de cita

LEFT JOIN para incluir las espontáneas.

Palabras clave: `salud`, `uniones`, `visitas`, `citas`, `join`, `agrupacion`, `agregado`

```sql
SELECT COALESCE(c.tipo, 'espontánea') AS tipo, COUNT(v.id_visita) AS visitas FROM visitas AS v LEFT JOIN citas AS c ON c.id_cita = v.id_cita GROUP BY c.tipo ORDER BY visitas DESC
```

{{query:B40 · Visitas por tipo de cita}}

#### B41 · Habitaciones ocupadas y libres por hospital

GROUP BY de dos columnas, incluido el estado booleano.

Palabras clave: `salud`, `uniones`, `hospitales`, `habitaciones`, `join`, `agrupacion`, `agregado`

```sql
SELECT h.nombre AS hospital, ha.ocupada, COUNT(*) AS habitaciones FROM hospitales AS h INNER JOIN habitaciones AS ha ON ha.id_hospital = h.id_hospital GROUP BY h.nombre, ha.ocupada ORDER BY h.nombre, ha.ocupada
```

{{query:B41 · Habitaciones ocupadas y libres por hospital}}

#### B42 · Ingresos urgentes por hospital

Join paciente, ingreso y hospital.

Palabras clave: `salud`, `uniones`, `ingresos`, `pacientes`, `habitaciones`, `join`, `agrupacion`, `agregado`

```sql
SELECT h.nombre AS hospital, COUNT(*) AS urgentes FROM ingresos AS i INNER JOIN pacientes AS p ON p.id_paciente = i.id_paciente INNER JOIN habitaciones AS ha ON ha.id_habitacion = i.id_habitacion INNER JOIN hospitales AS h ON h.id_hospital = ha.id_hospital WHERE i.es_urgente = true GROUP BY h.nombre ORDER BY urgentes DESC
```

{{query:B42 · Ingresos urgentes por hospital}}

#### B43 · Cobertura media de facturas por seguro

Join y media agrupada.

Palabras clave: `salud`, `uniones`, `facturas`, `pacientes`, `seguros`, `join`, `agrupacion`, `agregado`

```sql
SELECT s.nombre AS seguro, COUNT(f.id_factura) AS facturas, ROUND(AVG(f.importe), 0) AS importe_medio FROM facturas AS f INNER JOIN pacientes AS p ON p.id_paciente = f.id_paciente INNER JOIN seguros AS s ON s.id_seguro = p.id_seguro GROUP BY s.nombre ORDER BY importe_medio DESC
```

{{query:B43 · Cobertura media de facturas por seguro}}

#### B44 · Analíticas por tipo y resultado

GROUP BY de dos columnas.

Palabras clave: `salud`, `uniones`, `analiticas`, `agrupacion`, `agregado`

```sql
SELECT tipo, es_anormal, COUNT(*) AS analiticas FROM analiticas GROUP BY tipo, es_anormal ORDER BY tipo, es_anormal
```

{{query:B44 · Analíticas por tipo y resultado}}

#### B45 · Diagnósticos graves con tratamientos

Join y filtro por gravedad.

Palabras clave: `salud`, `uniones`, `diagnosticos`, `tratamientos`, `join`, `agrupacion`, `agregado`

```sql
SELECT d.nombre AS diagnostico, d.gravedad, COUNT(t.id_tratamiento) AS tratamientos FROM diagnosticos AS d INNER JOIN tratamientos AS t ON t.id_diagnostico = d.id_diagnostico WHERE d.gravedad = 'Grave' GROUP BY d.nombre, d.gravedad ORDER BY tratamientos DESC
```

{{query:B45 · Diagnósticos graves con tratamientos}}

### C · Consultas relacionadas

#### C01 · Historial de un paciente

Reúne ingresos, citas y visitas de un paciente con conteos distintos.

Palabras clave: `salud`, `consultas-relacionadas`, `pacientes`, `ingresos`, `citas`, `join`, `agrupacion`, `agregado`, `distinct`

```sql
SELECT p.apellidos AS paciente, COUNT(DISTINCT i.id_ingreso) AS ingresos, COUNT(DISTINCT c.id_cita) AS citas, COUNT(DISTINCT v.id_visita) AS visitas FROM pacientes AS p LEFT JOIN ingresos AS i ON i.id_paciente = p.id_paciente LEFT JOIN citas AS c ON c.id_paciente = p.id_paciente LEFT JOIN visitas AS v ON v.id_paciente = p.id_paciente WHERE p.id_paciente = 1 GROUP BY p.apellidos
```

{{query:C01 · Historial de un paciente}}

#### C02 · Visitas con dos o más diagnósticos

GROUP BY sobre la relación N:M con HAVING.

Palabras clave: `salud`, `consultas-relacionadas`, `visitas`, `visita_diagnosticos`, `join`, `agrupacion`, `having`, `agregado`

```sql
SELECT v.id_visita, v.motivo, COUNT(vd.id_diagnostico) AS diagnosticos FROM visitas AS v INNER JOIN visita_diagnosticos AS vd ON vd.id_visita = v.id_visita GROUP BY v.id_visita, v.motivo HAVING COUNT(vd.id_diagnostico) >= 2 ORDER BY diagnosticos DESC, v.id_visita LIMIT 20
```

{{query:C02 · Visitas con dos o más diagnósticos}}

#### C03 · Diagnósticos más frecuentes

Cuenta apariciones en la tabla puente.

Palabras clave: `salud`, `consultas-relacionadas`, `visita_diagnosticos`, `diagnosticos`, `join`, `agrupacion`, `agregado`

```sql
SELECT d.nombre AS diagnostico, COUNT(*) AS veces FROM visita_diagnosticos AS vd INNER JOIN diagnosticos AS d ON d.id_diagnostico = vd.id_diagnostico GROUP BY d.nombre ORDER BY veces DESC, diagnostico LIMIT 10
```

{{query:C03 · Diagnósticos más frecuentes}}

#### C04 · Tratamientos con más de dos medicamentos

HAVING sobre la N:M de prescripciones.

Palabras clave: `salud`, `consultas-relacionadas`, `tratamientos`, `prescripciones`, `join`, `agrupacion`, `having`, `agregado`

```sql
SELECT t.id_tratamiento, COUNT(pr.id_medicamento) AS medicamentos FROM tratamientos AS t INNER JOIN prescripciones AS pr ON pr.id_tratamiento = t.id_tratamiento GROUP BY t.id_tratamiento HAVING COUNT(pr.id_medicamento) > 2 ORDER BY medicamentos DESC LIMIT 15
```

{{query:C04 · Tratamientos con más de dos medicamentos}}

#### C05 · Medicamentos más prescritos

Ranking desde la tabla puente.

Palabras clave: `salud`, `consultas-relacionadas`, `prescripciones`, `medicamentos`, `join`, `agrupacion`, `agregado`

```sql
SELECT med.nombre AS medicamento, COUNT(*) AS prescripciones FROM prescripciones AS pr INNER JOIN medicamentos AS med ON med.id_medicamento = pr.id_medicamento GROUP BY med.nombre ORDER BY prescripciones DESC, medicamento LIMIT 10
```

{{query:C05 · Medicamentos más prescritos}}

#### C06 · Pacientes con más facturas

JOIN y ORDER BY de un agregado con LIMIT.

Palabras clave: `salud`, `consultas-relacionadas`, `pacientes`, `facturas`, `join`, `agrupacion`, `agregado`

```sql
SELECT p.apellidos AS paciente, COUNT(f.id_factura) AS facturas, SUM(f.importe) AS total FROM pacientes AS p INNER JOIN facturas AS f ON f.id_paciente = p.id_paciente GROUP BY p.apellidos ORDER BY total DESC LIMIT 10
```

{{query:C06 · Pacientes con más facturas}}

#### C07 · Pacientes que nunca han tenido cita

LEFT JOIN y comprobación de null.

Palabras clave: `salud`, `consultas-relacionadas`, `pacientes`, `citas`, `join`, `null`

```sql
SELECT p.apellidos AS paciente FROM pacientes AS p LEFT JOIN citas AS c ON c.id_paciente = p.id_paciente WHERE c.id_cita IS NULL ORDER BY p.apellidos
```

{{query:C07 · Pacientes que nunca han tenido cita}}

#### C08 · Médicos sin ninguna cita

Los contactados que no aparecen en la agenda.

Palabras clave: `salud`, `consultas-relacionadas`, `medicos`, `citas`, `join`, `null`

```sql
SELECT m.apellidos AS medico FROM medicos AS m LEFT JOIN citas AS c ON c.id_medico = m.id_medico WHERE c.id_cita IS NULL ORDER BY m.apellidos
```

{{query:C08 · Médicos sin ninguna cita}}

#### C09 · Habitaciones sin ingresos

LEFT JOIN desde habitaciones.

Palabras clave: `salud`, `consultas-relacionadas`, `habitaciones`, `ingresos`, `join`, `null`

```sql
SELECT ha.codigo, ha.tipo FROM habitaciones AS ha LEFT JOIN ingresos AS i ON i.id_habitacion = ha.id_habitacion WHERE i.id_ingreso IS NULL ORDER BY ha.codigo
```

{{query:C09 · Habitaciones sin ingresos}}

#### C10 · Nadie tiene dos seguros (rowCount)

Comprobación de integridad: el resultado debe estar vacío.

Palabras clave: `salud`, `consultas-relacionadas`, `pacientes`, `agrupacion`, `having`, `agregado`, `distinct`

```sql
SELECT id_paciente, COUNT(DISTINCT id_seguro) AS seguros FROM pacientes WHERE id_seguro IS NOT NULL GROUP BY id_paciente HAVING COUNT(DISTINCT id_seguro) >= 2
```

{{query:C10 · Nadie tiene dos seguros (rowCount)}}

#### C11 · Pacientes sin analíticas

LEFT JOIN sobre laboratorio.

Palabras clave: `salud`, `consultas-relacionadas`, `pacientes`, `analiticas`, `join`, `null`

```sql
SELECT p.apellidos AS paciente FROM pacientes AS p LEFT JOIN analiticas AS a ON a.id_paciente = p.id_paciente WHERE a.id_analitica IS NULL ORDER BY p.apellidos
```

{{query:C11 · Pacientes sin analíticas}}

#### C12 · Visitas de un paciente sin importar el orden

Assert con ignoreOrder: se compara el conjunto.

Palabras clave: `salud`, `consultas-relacionadas`, `visitas`

```sql
SELECT v.motivo FROM visitas AS v WHERE v.id_paciente = 5
```

{{query:C12 · Visitas de un paciente sin importar el orden}}

#### C13 · Especialidad con más ingresos

Cadena ingreso → médico → especialidad.

Palabras clave: `salud`, `consultas-relacionadas`, `ingresos`, `medicos`, `especialidades`, `join`, `agrupacion`, `agregado`

```sql
SELECT e.nombre AS especialidad, COUNT(*) AS ingresos FROM ingresos AS i INNER JOIN medicos AS m ON m.id_medico = i.id_medico INNER JOIN especialidades AS e ON e.id_especialidad = m.id_especialidad GROUP BY e.nombre ORDER BY ingresos DESC LIMIT 5
```

{{query:C13 · Especialidad con más ingresos}}

#### C14 · Medicamentos con más alergias

Tabla puente alergias-medicamento.

Palabras clave: `salud`, `consultas-relacionadas`, `alergias`, `medicamentos`, `join`, `agrupacion`, `agregado`

```sql
SELECT med.nombre AS medicamento, COUNT(*) AS alergias FROM alergias AS al INNER JOIN medicamentos AS med ON med.id_medicamento = al.id_medicamento GROUP BY med.nombre ORDER BY alergias DESC, medicamento LIMIT 10
```

{{query:C14 · Medicamentos con más alergias}}

#### C15 · Diagnósticos sin tratamientos

LEFT JOIN desde el catálogo.

Palabras clave: `salud`, `consultas-relacionadas`, `diagnosticos`, `tratamientos`, `join`, `null`

```sql
SELECT d.nombre AS diagnostico FROM diagnosticos AS d LEFT JOIN tratamientos AS t ON t.id_diagnostico = d.id_diagnostico WHERE t.id_tratamiento IS NULL ORDER BY d.nombre
```

{{query:C15 · Diagnósticos sin tratamientos}}

#### C16 · Médicos con más intervenciones

Ranking de cirujanos.

Palabras clave: `salud`, `consultas-relacionadas`, `intervenciones`, `medicos`, `join`, `agrupacion`, `agregado`

```sql
SELECT m.apellidos AS medico, COUNT(*) AS intervenciones, SUM(iv.coste) AS coste_total FROM intervenciones AS iv INNER JOIN medicos AS m ON m.id_medico = iv.id_medico GROUP BY m.apellidos ORDER BY intervenciones DESC, coste_total DESC LIMIT 10
```

{{query:C16 · Médicos con más intervenciones}}

#### C17 · Tratamientos sin prescripciones

LEFT JOIN sobre la N:M.

Palabras clave: `salud`, `consultas-relacionadas`, `tratamientos`, `prescripciones`, `join`, `null`

```sql
SELECT t.id_tratamiento FROM tratamientos AS t LEFT JOIN prescripciones AS pr ON pr.id_tratamiento = t.id_tratamiento WHERE pr.id_prescripcion IS NULL ORDER BY t.id_tratamiento
```

{{query:C17 · Tratamientos sin prescripciones}}

#### C18 · Prescripciones de un tratamiento

Filtro concreto sobre la tabla puente.

Palabras clave: `salud`, `consultas-relacionadas`, `prescripciones`, `medicamentos`, `join`

```sql
SELECT med.nombre AS medicamento, pr.dosis, pr.duracion_dias FROM prescripciones AS pr INNER JOIN medicamentos AS med ON med.id_medicamento = pr.id_medicamento WHERE pr.id_tratamiento = 4 ORDER BY med.nombre
```

{{query:C18 · Prescripciones de un tratamiento}}

#### C19 · Seguros con sus pacientes

LEFT JOIN para incluir seguros sin pacientes.

Palabras clave: `salud`, `consultas-relacionadas`, `seguros`, `pacientes`, `join`, `agrupacion`, `agregado`

```sql
SELECT s.nombre AS seguro, COUNT(p.id_paciente) AS pacientes FROM seguros AS s LEFT JOIN pacientes AS p ON p.id_seguro = s.id_seguro GROUP BY s.nombre ORDER BY pacientes DESC
```

{{query:C19 · Seguros con sus pacientes}}

#### C20 · Supervisores de enfermería por servicio

Filtro booleano y join.

Palabras clave: `salud`, `consultas-relacionadas`, `enfermeros`, `servicios`, `join`, `agrupacion`, `agregado`

```sql
SELECT s.nombre AS servicio, COUNT(*) AS supervisores FROM enfermeros AS en INNER JOIN servicios AS s ON s.id_servicio = en.id_servicio WHERE en.es_supervisor = true GROUP BY s.nombre ORDER BY supervisores DESC
```

{{query:C20 · Supervisores de enfermería por servicio}}

#### C21 · Pacientes con más de cinco visitas

Conteo sobre visitas con HAVING.

Palabras clave: `salud`, `consultas-relacionadas`, `visitas`, `pacientes`, `join`, `agrupacion`, `having`, `agregado`

```sql
SELECT p.apellidos AS paciente, COUNT(*) AS visitas FROM visitas AS v INNER JOIN pacientes AS p ON p.id_paciente = v.id_paciente GROUP BY p.apellidos HAVING COUNT(*) >= 5 ORDER BY visitas DESC
```

{{query:C21 · Pacientes con más de cinco visitas}}

#### C22 · Hospitales con más ingresos

Cadena hospital → habitación → ingreso.

Palabras clave: `salud`, `consultas-relacionadas`, `ingresos`, `habitaciones`, `hospitales`, `join`, `agrupacion`, `agregado`

```sql
SELECT h.nombre AS hospital, COUNT(*) AS ingresos FROM ingresos AS i INNER JOIN habitaciones AS ha ON ha.id_habitacion = i.id_habitacion INNER JOIN hospitales AS h ON h.id_hospital = ha.id_hospital GROUP BY h.nombre ORDER BY ingresos DESC
```

{{query:C22 · Hospitales con más ingresos}}

#### C23 · Especialidades con más médicos

Ranking de dotación.

Palabras clave: `salud`, `consultas-relacionadas`, `medicos`, `especialidades`, `join`, `agrupacion`, `agregado`

```sql
SELECT e.nombre AS especialidad, COUNT(*) AS medicos FROM medicos AS m INNER JOIN especialidades AS e ON e.id_especialidad = m.id_especialidad GROUP BY e.nombre ORDER BY medicos DESC, especialidad
```

{{query:C23 · Especialidades con más médicos}}

#### C24 · Facturas que superan el 75% de cobertura

Compara importe con un cálculo sobre el seguro.

Palabras clave: `salud`, `consultas-relacionadas`, `facturas`, `pacientes`, `seguros`, `join`

```sql
SELECT f.id_factura, f.importe, s.cobertura_porcentaje FROM facturas AS f INNER JOIN pacientes AS p ON p.id_paciente = f.id_paciente INNER JOIN seguros AS s ON s.id_seguro = p.id_seguro WHERE f.importe * s.cobertura_porcentaje / 100 > 3000 ORDER BY f.importe DESC LIMIT 20
```

{{query:C24 · Facturas que superan el 75% de cobertura}}

#### C25 · Citas de un día concreto

Filtro exacto de fecha con join (fecha existente en los datos).

Palabras clave: `salud`, `consultas-relacionadas`, `citas`, `pacientes`, `medicos`, `join`

```sql
SELECT c.hora, p.apellidos AS paciente, m.apellidos AS medico, c.estado FROM citas AS c INNER JOIN pacientes AS p ON p.id_paciente = c.id_paciente INNER JOIN medicos AS m ON m.id_medico = c.id_medico WHERE c.fecha = '2024-02-16' ORDER BY c.hora
```

{{query:C25 · Citas de un día concreto}}

#### C26 · Intervenciones sin ingreso asociado

Filtro IS NULL en la clave ajena.

Palabras clave: `salud`, `consultas-relacionadas`, `intervenciones`, `null`

```sql
SELECT iv.nombre, iv.fecha, iv.coste FROM intervenciones AS iv WHERE iv.id_ingreso IS NULL ORDER BY iv.fecha DESC
```

{{query:C26 · Intervenciones sin ingreso asociado}}

#### C27 · Pacientes alérgicos a un medicamento que toman

Cruza alergias y prescripciones del mismo paciente.

Palabras clave: `salud`, `consultas-relacionadas`, `alergias`, `pacientes`, `medicamentos`, `join`, `distinct`

```sql
SELECT DISTINCT p.apellidos AS paciente, med.nombre AS medicamento FROM alergias AS al INNER JOIN pacientes AS p ON p.id_paciente = al.id_paciente INNER JOIN medicamentos AS med ON med.id_medicamento = al.id_medicamento INNER JOIN tratamientos AS t ON t.id_paciente = p.id_paciente INNER JOIN prescripciones AS pr ON pr.id_tratamiento = t.id_tratamiento AND pr.id_medicamento = med.id_medicamento ORDER BY p.apellidos, med.nombre
```

{{query:C27 · Pacientes alérgicos a un medicamento que toman}}

#### C28 · Ingresos por especialidad del médico

Agrupa por la especialidad del responsable.

Palabras clave: `salud`, `consultas-relacionadas`, `ingresos`, `medicos`, `especialidades`, `join`, `agrupacion`, `agregado`

```sql
SELECT e.nombre AS especialidad, COUNT(*) AS ingresos, ROUND(AVG(i.coste_dia), 0) AS coste_medio FROM ingresos AS i INNER JOIN medicos AS m ON m.id_medico = i.id_medico INNER JOIN especialidades AS e ON e.id_especialidad = m.id_especialidad GROUP BY e.nombre ORDER BY ingresos DESC
```

{{query:C28 · Ingresos por especialidad del médico}}

#### C29 · Facturación por tipo de seguro

SUM y COUNT agrupados por el tipo.

Palabras clave: `salud`, `consultas-relacionadas`, `facturas`, `pacientes`, `seguros`, `join`, `agrupacion`, `agregado`

```sql
SELECT s.tipo, COUNT(f.id_factura) AS facturas, SUM(f.importe) AS total FROM facturas AS f INNER JOIN pacientes AS p ON p.id_paciente = f.id_paciente INNER JOIN seguros AS s ON s.id_seguro = p.id_seguro GROUP BY s.tipo ORDER BY total DESC
```

{{query:C29 · Facturación por tipo de seguro}}

#### C30 · Diagnóstico principal más frecuente por gravedad

Filtro de principal y GROUP BY de gravedad.

Palabras clave: `salud`, `consultas-relacionadas`, `visita_diagnosticos`, `diagnosticos`, `join`, `agrupacion`, `agregado`

```sql
SELECT d.gravedad, d.nombre AS diagnostico, COUNT(*) AS veces FROM visita_diagnosticos AS vd INNER JOIN diagnosticos AS d ON d.id_diagnostico = vd.id_diagnostico WHERE vd.es_principal = true GROUP BY d.gravedad, d.nombre ORDER BY veces DESC LIMIT 10
```

{{query:C30 · Diagnóstico principal más frecuente por gravedad}}

#### C31 · Pacientes con más de un diagnóstico distinto

COUNT DISTINCT sobre la N:M.

Palabras clave: `salud`, `consultas-relacionadas`, `pacientes`, `visitas`, `visita_diagnosticos`, `join`, `agrupacion`, `having`, `agregado`

```sql
SELECT p.apellidos AS paciente, COUNT(DISTINCT d.id_diagnostico) AS diagnosticos FROM pacientes AS p INNER JOIN visitas AS v ON v.id_paciente = p.id_paciente INNER JOIN visita_diagnosticos AS vd ON vd.id_visita = v.id_visita INNER JOIN diagnosticos AS d ON d.id_diagnostico = vd.id_diagnostico GROUP BY p.apellidos HAVING COUNT(DISTINCT d.id_diagnostico) >= 4 ORDER BY diagnosticos DESC LIMIT 15
```

{{query:C31 · Pacientes con más de un diagnóstico distinto}}

#### C32 · Hospitales con analíticas anormales

Cadena hospital → servicio → médico → analítica.

Palabras clave: `salud`, `consultas-relacionadas`, `analiticas`, `medicos`, `servicios`, `join`, `agrupacion`, `agregado`

```sql
SELECT h.nombre AS hospital, COUNT(*) AS anormales FROM analiticas AS a INNER JOIN medicos AS m ON m.id_medico = a.id_medico INNER JOIN servicios AS s ON s.id_servicio = m.id_servicio INNER JOIN hospitales AS h ON h.id_hospital = s.id_hospital WHERE a.es_anormal = true GROUP BY h.nombre ORDER BY anormales DESC
```

{{query:C32 · Hospitales con analíticas anormales}}

#### C33 · Media de coste por tipo de visita

Agrupa visitas con/sin seguimiento.

Palabras clave: `salud`, `consultas-relacionadas`, `visitas`, `agrupacion`, `agregado`

```sql
SELECT requiere_seguimiento, COUNT(*) AS visitas, ROUND(AVG(coste), 2) AS coste_medio FROM visitas GROUP BY requiere_seguimiento ORDER BY coste_medio DESC
```

{{query:C33 · Media de coste por tipo de visita}}

#### C34 · Stock total por vía de administración

SUM y COUNT agrupados.

Palabras clave: `salud`, `consultas-relacionadas`, `medicamentos`, `agrupacion`, `agregado`

```sql
SELECT via, COUNT(*) AS medicamentos, SUM(stock) AS stock_total FROM medicamentos GROUP BY via ORDER BY stock_total DESC
```

{{query:C34 · Stock total por vía de administración}}

#### C35 · Ranking de pacientes por gasto en tratamientos

SUM agrupado con HAVING y LIMIT.

Palabras clave: `salud`, `consultas-relacionadas`, `tratamientos`, `pacientes`, `join`, `agrupacion`, `having`, `agregado`

```sql
SELECT p.apellidos AS paciente, SUM(t.coste) AS gasto FROM tratamientos AS t INNER JOIN pacientes AS p ON p.id_paciente = t.id_paciente GROUP BY p.apellidos HAVING SUM(t.coste) > 2000 ORDER BY gasto DESC LIMIT 10
```

{{query:C35 · Ranking de pacientes por gasto en tratamientos}}

### D · Integridad

#### D01 · Ingresos sin paciente

Clave ajena rota: no encuentra la fila de pacientes.

Palabras clave: `salud`, `integridad`, `ingresos`, `pacientes`, `join`, `null`

```sql
SELECT i.id_ingreso, i.id_paciente FROM ingresos AS i LEFT JOIN pacientes AS p ON p.id_paciente = i.id_paciente WHERE p.id_paciente IS NULL ORDER BY i.id_ingreso
```

{{query:D01 · Ingresos sin paciente}}

#### D02 · Ingresos sin médico

La clave del médico no encuentra fila.

Palabras clave: `salud`, `integridad`, `ingresos`, `medicos`, `join`, `null`

```sql
SELECT i.id_ingreso, i.id_medico FROM ingresos AS i LEFT JOIN medicos AS m ON m.id_medico = i.id_medico WHERE m.id_medico IS NULL ORDER BY i.id_ingreso
```

{{query:D02 · Ingresos sin médico}}

#### D03 · Citas sin paciente

Referencia rota en la agenda.

Palabras clave: `salud`, `integridad`, `citas`, `pacientes`, `join`, `null`

```sql
SELECT c.id_cita, c.id_paciente FROM citas AS c LEFT JOIN pacientes AS p ON p.id_paciente = c.id_paciente WHERE p.id_paciente IS NULL ORDER BY c.id_cita
```

{{query:D03 · Citas sin paciente}}

#### D04 · Citas sin médico

Referencia rota al facultativo.

Palabras clave: `salud`, `integridad`, `citas`, `medicos`, `join`, `null`

```sql
SELECT c.id_cita, c.id_medico FROM citas AS c LEFT JOIN medicos AS m ON m.id_medico = c.id_medico WHERE m.id_medico IS NULL ORDER BY c.id_cita
```

{{query:D04 · Citas sin médico}}

#### D05 · Pacientes con seguro inexistente

La clave apunta a un seguro que no existe.

Palabras clave: `salud`, `integridad`, `pacientes`, `seguros`, `join`, `null`

```sql
SELECT p.id_paciente, p.id_seguro FROM pacientes AS p LEFT JOIN seguros AS s ON s.id_seguro = p.id_seguro WHERE p.id_seguro IS NOT NULL AND s.id_seguro IS NULL ORDER BY p.id_paciente
```

{{query:D05 · Pacientes con seguro inexistente}}

#### D06 · Prescripciones sin medicamento

Línea de prescripción huérfana.

Palabras clave: `salud`, `integridad`, `prescripciones`, `medicamentos`, `join`, `null`

```sql
SELECT pr.id_prescripcion, pr.id_medicamento FROM prescripciones AS pr LEFT JOIN medicamentos AS med ON med.id_medicamento = pr.id_medicamento WHERE med.id_medicamento IS NULL ORDER BY pr.id_prescripcion
```

{{query:D06 · Prescripciones sin medicamento}}

#### D07 · Tratamientos sin paciente

Referencia rota al paciente.

Palabras clave: `salud`, `integridad`, `tratamientos`, `pacientes`, `join`, `null`

```sql
SELECT t.id_tratamiento, t.id_paciente FROM tratamientos AS t LEFT JOIN pacientes AS p ON p.id_paciente = t.id_paciente WHERE p.id_paciente IS NULL ORDER BY t.id_tratamiento
```

{{query:D07 · Tratamientos sin paciente}}

#### D08 · Visitas espontáneas sin cita

No es un error: son visitas que no vienen de la agenda.

Palabras clave: `salud`, `integridad`, `visitas`, `null`

```sql
SELECT v.id_visita, v.fecha, v.motivo FROM visitas AS v WHERE v.id_cita IS NULL ORDER BY v.id_visita
```

{{query:D08 · Visitas espontáneas sin cita}}

#### D09 · Pacientes sin seguro

Dato opcional que falta.

Palabras clave: `salud`, `integridad`, `pacientes`, `null`

```sql
SELECT id_paciente, nombre, apellidos FROM pacientes WHERE id_seguro IS NULL ORDER BY id_paciente
```

{{query:D09 · Pacientes sin seguro}}

#### D10 · Diagnósticos sin especialidad

Catálogo incompleto.

Palabras clave: `salud`, `integridad`, `diagnosticos`, `especialidades`, `join`, `null`

```sql
SELECT d.id_diagnostico, d.nombre FROM diagnosticos AS d LEFT JOIN especialidades AS e ON e.id_especialidad = d.id_especialidad WHERE e.id_especialidad IS NULL ORDER BY d.id_diagnostico
```

{{query:D10 · Diagnósticos sin especialidad}}

#### D11 · Facturas pagadas sin método de pago

Incoherencia: cobrada pero sin forma de pago.

Palabras clave: `salud`, `integridad`, `facturas`, `null`

```sql
SELECT id_factura, importe, pagada, metodo_pago FROM facturas WHERE pagada = true AND metodo_pago IS NULL ORDER BY id_factura
```

{{query:D11 · Facturas pagadas sin método de pago}}

#### D12 · Facturas sin pagar con método de pago

Incoherencia simétrica a la anterior.

Palabras clave: `salud`, `integridad`, `facturas`, `null`

```sql
SELECT id_factura, importe, pagada, metodo_pago FROM facturas WHERE pagada = false AND metodo_pago IS NOT NULL ORDER BY id_factura
```

{{query:D12 · Facturas sin pagar con método de pago}}

#### D13 · Relaciones visita-diagnóstico huérfanas

La tabla puente cita una visita que no existe.

Palabras clave: `salud`, `integridad`, `visita_diagnosticos`, `visitas`, `join`, `null`

```sql
SELECT vd.id_vd, vd.id_visita FROM visita_diagnosticos AS vd LEFT JOIN visitas AS v ON v.id_visita = vd.id_visita WHERE v.id_visita IS NULL ORDER BY vd.id_vd
```

{{query:D13 · Relaciones visita-diagnóstico huérfanas}}

#### D14 · Habitaciones sin camas

Habitación dada de alta con aforo cero.

Palabras clave: `salud`, `integridad`, `habitaciones`

```sql
SELECT id_habitacion, codigo, tipo, num_camas FROM habitaciones WHERE num_camas <= 0 ORDER BY codigo
```

{{query:D14 · Habitaciones sin camas}}

#### D15 · Citas atendidas sin visita registrada

El estado dice atendida pero no hay visita.

Palabras clave: `salud`, `integridad`, `citas`, `visitas`, `join`, `null`

```sql
SELECT c.id_cita, c.fecha FROM citas AS c LEFT JOIN visitas AS v ON v.id_cita = c.id_cita WHERE c.estado = 'Atendida' AND v.id_visita IS NULL ORDER BY c.id_cita
```

{{query:D15 · Citas atendidas sin visita registrada}}

#### D16 · Diagnósticos secundarios marcados como principales

Solo puede haber un principal por visita.

Palabras clave: `salud`, `integridad`, `visita_diagnosticos`, `agrupacion`, `having`, `agregado`

```sql
SELECT vd.id_visita, COUNT(*) AS principales FROM visita_diagnosticos AS vd WHERE vd.es_principal = true GROUP BY vd.id_visita HAVING COUNT(*) > 1 ORDER BY vd.id_visita
```

{{query:D16 · Diagnósticos secundarios marcados como principales}}

#### D17 · Intervenciones sin ingreso previo

Operadas sin constar estancia.

Palabras clave: `salud`, `integridad`, `intervenciones`, `null`

```sql
SELECT id_intervencion, nombre, fecha FROM intervenciones WHERE id_ingreso IS NULL ORDER BY id_intervencion
```

{{query:D17 · Intervenciones sin ingreso previo}}

#### D18 · Costes de tratamiento nulos o negativos

Valores fuera de rango.

Palabras clave: `salud`, `integridad`, `tratamientos`, `null`

```sql
SELECT id_tratamiento, coste FROM tratamientos WHERE coste IS NULL OR coste < 0 ORDER BY id_tratamiento
```

{{query:D18 · Costes de tratamiento nulos o negativos}}

#### D19 · Duración de intervención fuera de rango

Más de cinco horas es sospechoso.

Palabras clave: `salud`, `integridad`, `intervenciones`

```sql
SELECT id_intervencion, nombre, duracion_min FROM intervenciones WHERE duracion_min > 300 ORDER BY duracion_min DESC
```

{{query:D19 · Duración de intervención fuera de rango}}

#### D20 · Prescripciones con dosis sospechosa

Frecuencia y duración fuera de los rangos razonables.

Palabras clave: `salud`, `integridad`, `prescripciones`

```sql
SELECT id_prescripcion, dosis, frecuencia_horas, duracion_dias FROM prescripciones WHERE frecuencia_horas <= 0 OR duracion_dias > 90 ORDER BY id_prescripcion
```

{{query:D20 · Prescripciones con dosis sospechosa}}

### E · Vistas

#### E01 · Vista sencilla: SELECT * de otra consulta

El resultado de A03 se lee como tabla: los 3 hospitales grandes con sus tres columnas (nombre, num_camas, nivel).

Palabras clave: `salud`, `vistas`, `todas-las-columnas`

```sql
SELECT * FROM [A03 · Hospitales grandes (>= 800 camas)] ORDER BY num_camas DESC, nombre
```

{{query:E01 · Vista sencilla: SELECT * de otra consulta}}

#### E02 · Aritmética sobre columnas de una vista

num_camas sigue siendo un número después de pasar por la vista A03: se puede dividir entre 100 y calcular el resto.

Palabras clave: `salud`, `vistas`

```sql
SELECT nombre AS hospital, num_camas, num_camas / 100 AS centenas, num_camas % 100 AS resto FROM [A03 · Hospitales grandes (>= 800 camas)] ORDER BY num_camas DESC, nombre
```

{{query:E02 · Aritmética sobre columnas de una vista}}

#### E03 · WHERE y DISTINCT sobre una vista

Filtra la vista A03 por num_camas >= 900 y deja los niveles distintos: los tres hospitales grandes son de nivel 4.

Palabras clave: `salud`, `vistas`, `distinct`

```sql
SELECT DISTINCT nivel FROM [A03 · Hospitales grandes (>= 800 camas)] WHERE num_camas >= 900 ORDER BY nivel
```

{{query:E03 · WHERE y DISTINCT sobre una vista}}

#### E04 · Agregados sobre una vista

Cinco agregados en una fila sobre el resultado de A03: 3 hospitales, 3450 camas, de 900 a 1350 y media 1150.

Palabras clave: `salud`, `vistas`, `agregado`

```sql
SELECT COUNT(*) AS hospitales, SUM(num_camas) AS camas, MIN(num_camas) AS minima, MAX(num_camas) AS maxima, ROUND(AVG(num_camas), 2) AS media FROM [A03 · Hospitales grandes (>= 800 camas)]
```

{{query:E04 · Agregados sobre una vista}}

#### E05 · Niveles con dos o más hospitales grandes

Agrupa la vista A03 por nivel y se queda con los niveles con dos o más hospitales: solo el nivel 4, con tres.

Palabras clave: `salud`, `vistas`, `agrupacion`, `having`, `agregado`

```sql
SELECT nivel, COUNT(*) AS hospitales, SUM(num_camas) AS camas FROM [A03 · Hospitales grandes (>= 800 camas)] GROUP BY nivel HAVING COUNT(*) >= 2 ORDER BY nivel
```

{{query:E05 · Niveles con dos o más hospitales grandes}}

#### E06 · Vista unida a una tabla base (INNER JOIN)

La vista B07 unida a la tabla especialidades por el nombre: las 5 especialidades con tres o más médicos, con su área y si requieren UCI.

Palabras clave: `salud`, `vistas`, `especialidades`, `join`

```sql
SELECT b.especialidad, b.medicos, e.area, e.requiere_uci FROM [B07 · Especialidades con al menos tres médicos] AS b INNER JOIN especialidades AS e ON e.nombre = b.especialidad ORDER BY b.medicos DESC, b.especialidad
```

{{query:E06 · Vista unida a una tabla base (INNER JOIN)}}

#### E07 · Dos vistas unidas por especialidad

Las vistas C13 y C23 unidas por especialidad: las 5 especialidades del top de ingresos que además aparecen en el ranking de médicos.

Palabras clave: `salud`, `vistas`, `join`

```sql
SELECT c.especialidad, c.ingresos, m.medicos FROM [C13 · Especialidad con más ingresos] AS c INNER JOIN [C23 · Especialidades con más médicos] AS m ON m.especialidad = c.especialidad ORDER BY c.ingresos DESC, c.especialidad
```

{{query:E07 · Dos vistas unidas por especialidad}}

#### E08 · Tabla LEFT JOIN vista con IS NULL

LEFT JOIN de la tabla hospitales contra la vista B30 más IS NULL: el único hospital sin habitaciones, el Hospital San José.

Palabras clave: `salud`, `vistas`, `hospitales`, `join`, `null`

```sql
SELECT h.nombre AS hospital FROM hospitales AS h LEFT JOIN [B30 · Camas por hospital (solo con habitaciones)] AS b ON b.hospital = h.nombre WHERE b.camas IS NULL ORDER BY h.nombre
```

{{query:E08 · Tabla LEFT JOIN vista con IS NULL}}

#### E09 · Vista de vista (dos niveles de consulta)

Lee la vista E05, que a su vez lee la vista A03: tres niveles de consulta encadenados y una sola fila (nivel 4, 3450 camas).

Palabras clave: `salud`, `vistas`

```sql
SELECT nivel, hospitales, camas FROM [E05 · Niveles con dos o más hospitales grandes] WHERE camas >= 2000 ORDER BY camas DESC
```

{{query:E09 · Vista de vista (dos niveles de consulta)}}

#### E10 · La misma vista dos veces (auto-unión)

La vista C05 aparece dos veces con alias distintos y se une consigo misma por número de prescripciones: las 7 parejas de medicamentos empatados.

Palabras clave: `salud`, `vistas`, `join`

```sql
SELECT a.medicamento AS uno, b.medicamento AS otro, a.prescripciones FROM [C05 · Medicamentos más prescritos] AS a INNER JOIN [C05 · Medicamentos más prescritos] AS b ON b.prescripciones = a.prescripciones AND b.medicamento > a.medicamento ORDER BY a.prescripciones DESC, a.medicamento, b.medicamento
```

{{query:E10 · La misma vista dos veces (auto-unión)}}

#### E11 · Join implícito por comas con una vista

Producto cartesiano de la vista C19 con la tabla seguros, filtrado por cobertura >= 80: los 2 seguros con la cobertura más alta.

Palabras clave: `salud`, `vistas`

```sql
SELECT s.nombre AS seguro, s.cobertura_porcentaje, c.pacientes FROM [C19 · Seguros con sus pacientes] AS c, seguros AS s WHERE s.nombre = c.seguro AND s.cobertura_porcentaje >= 80 ORDER BY c.pacientes DESC, s.nombre
```

{{query:E11 · Join implícito por comas con una vista}}

#### E12 · ORDER BY por alias y LIMIT sobre una vista

Ordena por el alias cobertura y recorta a 3 filas la vista C19: los tres seguros con más pacientes.

Palabras clave: `salud`, `vistas`

```sql
SELECT seguro AS cobertura, pacientes FROM [C19 · Seguros con sus pacientes] ORDER BY pacientes DESC, seguro LIMIT 3
```

{{query:E12 · ORDER BY por alias y LIMIT sobre una vista}}

#### E13 · Booleano a través de una vista

El booleano es_anormal viaja intacto desde la tabla analiticas hasta la vista B44: solo los tipos Orina y Sangre lo activan.

Palabras clave: `salud`, `vistas`

```sql
SELECT tipo, es_anormal, analiticas FROM [B44 · Analíticas por tipo y resultado] WHERE es_anormal = true ORDER BY tipo
```

{{query:E13 · Booleano a través de una vista}}

#### E14 · Nulos a través de una vista (COALESCE)

El teléfono ausente de «Andes Salud» sigue siendo nulo tras leer la vista A27, y COALESCE lo sustituye por «sin teléfono».

Palabras clave: `salud`, `vistas`

```sql
SELECT nombre AS seguro, COALESCE(telefono, 'sin teléfono') AS contacto FROM [A27 · Teléfono de seguro normalizado] ORDER BY nombre
```

{{query:E14 · Nulos a través de una vista (COALESCE)}}

#### E15 · Agregados sobre una vista ya agregada

Resume la vista ya agregada B08 (una fila por seguro): 6 seguros, 35 pacientes y entre 2 y 9 por seguro.

Palabras clave: `salud`, `vistas`, `agregado`

```sql
SELECT COUNT(*) AS seguros, SUM(pacientes) AS pacientes, MIN(pacientes) AS minimo, MAX(pacientes) AS maximo FROM [B08 · Pacientes por seguro]
```

{{query:E15 · Agregados sobre una vista ya agregada}}

#### E16 · Vista que lee una consulta con LIMIT

La vista C05 lleva LIMIT 10: al leerla como tabla llegan solo esas 10 filas (72 prescripciones en total).

Palabras clave: `salud`, `vistas`, `agregado`

```sql
SELECT COUNT(*) AS medicamentos, SUM(prescripciones) AS prescripciones FROM [C05 · Medicamentos más prescritos]
```

{{query:E16 · Vista que lee una consulta con LIMIT}}

#### E17 · Vista de un resultado vacío

La vista C10 no devuelve filas: leerla como tabla da una tabla vacía, y COUNT(*) devuelve 0 en lugar de un error.

Palabras clave: `salud`, `vistas`, `agregado`

```sql
SELECT COUNT(*) AS filas FROM [C10 · Nadie tiene dos seguros (rowCount)]
```

{{query:E17 · Vista de un resultado vacío}}

#### E18 · LIMIT y OFFSET sobre una vista

LIMIT 3 OFFSET 2 sobre las 8 filas de la vista A41: los diagnósticos cuarto, quinto y sexto por longitud del nombre.

Palabras clave: `salud`, `vistas`

```sql
SELECT codigo, nombre, letras FROM [A41 · Nombres de diagnóstico más largos] ORDER BY letras DESC, codigo LIMIT 3 OFFSET 2
```

{{query:E18 · LIMIT y OFFSET sobre una vista}}

#### E19 · Filtro con LIKE sobre una vista

LIKE sobre una columna de la vista A37: los 2 medicamentos con stock bajo cuyo nombre empieza por A.

Palabras clave: `salud`, `vistas`, `like`

```sql
SELECT nombre, stock, precio FROM [A37 · Medicamentos con stock bajo] WHERE nombre LIKE 'A%' ORDER BY nombre
```

{{query:E19 · Filtro con LIKE sobre una vista}}

#### E20 · Vista de una vista que une dos vistas

Lee la vista E07 (que une C13 y C23) y se queda con las especialidades con tres médicos o más: 4 filas.

Palabras clave: `salud`, `vistas`

```sql
SELECT especialidad, ingresos, medicos FROM [E07 · Dos vistas unidas por especialidad] WHERE medicos >= 3 ORDER BY medicos DESC, especialidad
```

{{query:E20 · Vista de una vista que une dos vistas}}

#### E21 · LEFT JOIN entre dos vistas

C13 LEFT JOIN C23: las 5 especialidades del top de ingresos con el número de médicos de cada una, de menos a más.

Palabras clave: `salud`, `vistas`, `join`

```sql
SELECT c.especialidad, c.ingresos, m.medicos FROM [C13 · Especialidad con más ingresos] AS c LEFT JOIN [C23 · Especialidades con más médicos] AS m ON m.especialidad = c.especialidad ORDER BY m.medicos ASC NULLS FIRST, c.especialidad
```

{{query:E21 · LEFT JOIN entre dos vistas}}

#### E22 · Proyección con alias y asterisco sobre una vista

El asterisco del alias (v.*) sobre la vista A37: los 6 medicamentos con stock bajo, de menor a mayor stock.

Palabras clave: `salud`, `vistas`

```sql
SELECT v.* FROM [A37 · Medicamentos con stock bajo] AS v ORDER BY v.stock
```

{{query:E22 · Proyección con alias y asterisco sobre una vista}}

#### E23 · Vista que lee una consulta con varias tablas

La vista B10 une cuatro tablas por dentro (ingresos, pacientes, habitaciones y médicos): al leerla como tabla llegan sus 25 filas.

Palabras clave: `salud`, `vistas`, `agregado`

```sql
SELECT COUNT(*) AS filas FROM [B10 · Ingresos con paciente, habitación y médico]
```

{{query:E23 · Vista que lee una consulta con varias tablas}}

#### E24 · Agrupar por un booleano de una vista

GROUP BY sobre el booleano requiere_seguimiento que llega de la vista C33: las visitas con y sin seguimiento.

Palabras clave: `salud`, `vistas`, `agrupacion`, `agregado`

```sql
SELECT requiere_seguimiento, COUNT(*) AS tramos, SUM(visitas) AS visitas FROM [C33 · Media de coste por tipo de visita] GROUP BY requiere_seguimiento ORDER BY requiere_seguimiento
```

{{query:E24 · Agrupar por un booleano de una vista}}

### F · Subconsultas

#### F01 · Seguros de los pacientes de Madrid (IN con subconsulta)

IN con subconsulta: los seguros cuyo identificador usa algún paciente de Madrid.

Palabras clave: `salud`, `subconsultas`, `seguros`, `pacientes`, `subconsulta`, `in`

```sql
SELECT id_seguro, nombre FROM seguros WHERE id_seguro IN (SELECT id_seguro FROM pacientes WHERE ciudad = 'Madrid') ORDER BY id_seguro
```

{{query:F01 · Seguros de los pacientes de Madrid (IN con subconsulta)}}

#### F02 · Medicamentos sin ninguna prescripción (NOT IN con subconsulta)

NOT IN con subconsulta: los medicamentos que no usa ninguna prescripción.

Palabras clave: `salud`, `subconsultas`, `medicamentos`, `prescripciones`, `subconsulta`, `in`

```sql
SELECT id_medicamento, nombre FROM medicamentos WHERE id_medicamento NOT IN (SELECT id_medicamento FROM prescripciones) ORDER BY id_medicamento
```

{{query:F02 · Medicamentos sin ninguna prescripción (NOT IN con subconsulta)}}

#### F03 · Especialidades sin ningún diagnóstico (NOT IN con subconsulta)

Las especialidades a las que no está ligado ningún diagnóstico.

Palabras clave: `salud`, `subconsultas`, `especialidades`, `diagnosticos`, `subconsulta`, `in`

```sql
SELECT id_especialidad, nombre FROM especialidades WHERE id_especialidad NOT IN (SELECT id_especialidad FROM diagnosticos) ORDER BY id_especialidad
```

{{query:F03 · Especialidades sin ningún diagnóstico (NOT IN con subconsulta)}}

#### F04 · Hospitales con una habitación UCI (EXISTS)

EXISTS con subconsulta correlacionada: se comprueba otra vez para cada hospital.

Palabras clave: `salud`, `subconsultas`, `hospitales`, `habitaciones`, `subconsulta`, `exists`

```sql
SELECT h.id_hospital, h.nombre FROM hospitales AS h WHERE EXISTS (SELECT 1 FROM habitaciones AS r WHERE r.id_hospital = h.id_hospital AND r.tipo = 'UCI') ORDER BY h.id_hospital
```

{{query:F04 · Hospitales con una habitación UCI (EXISTS)}}

#### F05 · Hospitales sin ninguna habitación UCI (NOT EXISTS)

La otra cara del ejemplo anterior: NOT EXISTS se queda con los hospitales sin coincidencia.

Palabras clave: `salud`, `subconsultas`, `hospitales`, `habitaciones`, `subconsulta`, `exists`

```sql
SELECT h.id_hospital, h.nombre FROM hospitales AS h WHERE NOT EXISTS (SELECT 1 FROM habitaciones AS r WHERE r.id_hospital = h.id_hospital AND r.tipo = 'UCI') ORDER BY h.id_hospital
```

{{query:F05 · Hospitales sin ninguna habitación UCI (NOT EXISTS)}}

#### F06 · Pacientes con una intervención de riesgo alto (EXISTS correlacionada)

La subconsulta apunta al paciente de fuera (p.id_paciente): una EXISTS correlacionada.

Palabras clave: `salud`, `subconsultas`, `pacientes`, `intervenciones`, `subconsulta`, `exists`

```sql
SELECT p.id_paciente, p.apellidos FROM pacientes AS p WHERE EXISTS (SELECT 1 FROM intervenciones AS i WHERE i.id_paciente = p.id_paciente AND i.riesgo = 'Alto') ORDER BY p.id_paciente
```

{{query:F06 · Pacientes con una intervención de riesgo alto (EXISTS correlacionada)}}

#### F07 · Médicos de Dermatología con más experiencia que algún médico de Cardiología (> ANY)

x > ANY (…) es cierto cuando la experiencia supera al menos uno de los valores internos.

Palabras clave: `salud`, `subconsultas`, `medicos`, `subconsulta`

```sql
SELECT id_medico, apellidos, anios_experiencia FROM medicos WHERE id_especialidad = 6 AND anios_experiencia > ANY (SELECT anios_experiencia FROM medicos WHERE id_especialidad = 1) ORDER BY id_medico
```

{{query:F07 · Médicos de Dermatología con más experiencia que algún médico de Cardiología (> ANY)}}

#### F08 · El médico con más experiencia de cada especialidad (>= ALL correlacionada)

x >= ALL (…) exige que la comparación se cumpla con todos los valores internos: solo pasa el máximo de cada especialidad.

Palabras clave: `salud`, `subconsultas`, `medicos`, `subconsulta`

```sql
SELECT m.id_medico, m.apellidos, m.anios_experiencia FROM medicos AS m WHERE m.anios_experiencia >= ALL (SELECT m2.anios_experiencia FROM medicos AS m2 WHERE m2.id_especialidad = m.id_especialidad) ORDER BY m.id_especialidad, m.id_medico
```

{{query:F08 · El médico con más experiencia de cada especialidad (>= ALL correlacionada)}}

#### F09 · Crónicos de una ciudad con seguro privado (<> ALL)

x <> ALL (…) es lo mismo que x NOT IN (…): deja los valores distintos de todos ellos.

Palabras clave: `salud`, `subconsultas`, `pacientes`, `seguros`, `subconsulta`

```sql
SELECT id_paciente, apellidos FROM pacientes WHERE es_cronico = TRUE AND id_seguro <> ALL (SELECT id_seguro FROM seguros WHERE tipo = 'Público') ORDER BY id_paciente
```

{{query:F09 · Crónicos de una ciudad con seguro privado (<> ALL)}}

#### F10 · Hospitales universitarios

Esta consulta no es un test por sí sola: es la vista que leen las subconsultas siguientes.

Palabras clave: `salud`, `subconsultas`, `hospitales`

```sql
SELECT id_hospital, nombre, ciudad FROM hospitales WHERE es_universitario = TRUE
```

{{query:F10 · Hospitales universitarios}}

#### F11 · Habitaciones de los hospitales universitarios leídas desde la vista (subconsulta sobre una vista)

La subconsulta lee la vista F10 como si fuera una tabla: [F10 · …] dentro del IN.

Palabras clave: `salud`, `subconsultas`, `habitaciones`, `subconsulta`, `in`

```sql
SELECT r.id_habitacion, r.codigo FROM habitaciones AS r WHERE r.id_hospital IN (SELECT id_hospital FROM [F10 · Hospitales universitarios]) ORDER BY r.id_habitacion
```

{{query:F11 · Habitaciones de los hospitales universitarios leídas desde la vista (subconsulta sobre una vista)}}

#### F12 · Coste medio por día de los ingresos de 700 o más (tabla derivada)

Una subconsulta en el FROM con alias (t) es una tabla derivada: se consulta como cualquier otra.

Palabras clave: `salud`, `subconsultas`, `ingresos`, `habitaciones`, `subconsulta`, `join`, `agrupacion`, `agregado`

```sql
SELECT t.id_hospital, t.coste_medio FROM (SELECT r.id_hospital, AVG(i.coste_dia) AS coste_medio FROM ingresos AS i INNER JOIN habitaciones AS r ON r.id_habitacion = i.id_habitacion GROUP BY r.id_hospital) AS t WHERE t.coste_medio >= 700 ORDER BY t.id_hospital
```

{{query:F12 · Coste medio por día de los ingresos de 700 o más (tabla derivada)}}

#### F13 · Hospitales con diez ingresos o más (tabla derivada y JOIN)

La tabla derivada se une a la tabla hospitales y se filtra por su columna total.

Palabras clave: `salud`, `subconsultas`, `ingresos`, `habitaciones`, `hospitales`, `subconsulta`, `join`, `agrupacion`, `agregado`

```sql
SELECT t.id_hospital, h.nombre, t.total FROM (SELECT r.id_hospital, COUNT(*) AS total FROM ingresos AS i INNER JOIN habitaciones AS r ON r.id_habitacion = i.id_habitacion GROUP BY r.id_hospital) AS t INNER JOIN hospitales AS h ON h.id_hospital = t.id_hospital WHERE t.total >= 10 ORDER BY t.total DESC, t.id_hospital
```

{{query:F13 · Hospitales con diez ingresos o más (tabla derivada y JOIN)}}

#### F14 · Visitas de cada paciente de una ciudad (subconsulta escalar en el SELECT)

La subconsulta de la lista de columnas devuelve un valor por fila: las visitas de ese paciente.

Palabras clave: `salud`, `subconsultas`, `visitas`, `pacientes`, `subconsulta`, `agregado`

```sql
SELECT p.id_paciente, p.apellidos, (SELECT COUNT(*) FROM visitas AS v WHERE v.id_paciente = p.id_paciente) AS visitas FROM pacientes AS p WHERE p.ciudad = 'Bilbao' ORDER BY p.id_paciente
```

{{query:F14 · Visitas de cada paciente de una ciudad (subconsulta escalar en el SELECT)}}

#### F15 · Ingresos más caros que la media (subconsulta escalar en el WHERE)

La subconsulta devuelve un solo valor (la media) con el que compara el WHERE.

Palabras clave: `salud`, `subconsultas`, `ingresos`, `subconsulta`, `agregado`

```sql
SELECT id_ingreso, coste_dia FROM ingresos WHERE coste_dia > (SELECT AVG(coste_dia) FROM ingresos) ORDER BY id_ingreso
```

{{query:F15 · Ingresos más caros que la media (subconsulta escalar en el WHERE)}}

#### F16 · Especialidades con tres o más diagnósticos

Una consulta con subconsulta también sirve de vista: esta usa una EXISTS correlacionada con HAVING.

Palabras clave: `salud`, `subconsultas`, `especialidades`, `diagnosticos`, `subconsulta`, `having`, `agregado`, `exists`

```sql
SELECT s.id_especialidad, s.nombre FROM especialidades AS s WHERE EXISTS (SELECT 1 FROM diagnosticos AS d WHERE d.id_especialidad = s.id_especialidad HAVING COUNT(*) >= 3) ORDER BY s.id_especialidad
```

{{query:F16 · Especialidades con tres o más diagnósticos}}

#### F17 · La vista anterior leída como tabla

F16 se lee desde el FROM como una tabla, aunque por dentro lleve una subconsulta.

Palabras clave: `salud`, `subconsultas`

```sql
SELECT id_especialidad, nombre FROM [F16 · Especialidades con tres o más diagnósticos] ORDER BY id_especialidad
```

{{query:F17 · La vista anterior leída como tabla}}

#### F18 · Médicos de las especialidades con algún diagnóstico grave (subconsultas anidadas)

Una subconsulta dentro de otra: la interna elige las especialidades y la externa los médicos.

Palabras clave: `salud`, `subconsultas`, `medicos`, `diagnosticos`, `subconsulta`, `distinct`, `in`

```sql
SELECT DISTINCT m.id_medico, m.apellidos FROM medicos AS m WHERE m.id_especialidad IN (SELECT id_especialidad FROM diagnosticos WHERE gravedad = 'Grave') ORDER BY m.id_medico
```

{{query:F18 · Médicos de las especialidades con algún diagnóstico grave (subconsultas anidadas)}}

#### F19 · Médicos de Oncología menos el más experto (NOT con ALL)

NOT (… >= ALL (…)) es la negación del predicado ALL: toda la especialidad menos su médico más experto.

Palabras clave: `salud`, `subconsultas`, `medicos`, `subconsulta`

```sql
SELECT m.id_medico, m.apellidos, m.anios_experiencia FROM medicos AS m WHERE m.id_especialidad = 5 AND NOT (m.anios_experiencia >= ALL (SELECT m2.anios_experiencia FROM medicos AS m2 WHERE m2.id_especialidad = 5)) ORDER BY m.id_medico
```

{{query:F19 · Médicos de Oncología menos el más experto (NOT con ALL)}}

#### F20 · Habitaciones de los hospitales universitarios (= ANY)

x = ANY (…) es lo mismo que x IN (…): se incluye para completar los cuantificadores.

Palabras clave: `salud`, `subconsultas`, `habitaciones`, `hospitales`, `subconsulta`, `distinct`

```sql
SELECT DISTINCT r.id_hospital FROM habitaciones AS r WHERE r.id_hospital = ANY (SELECT id_hospital FROM hospitales WHERE es_universitario = TRUE) ORDER BY r.id_hospital
```

{{query:F20 · Habitaciones de los hospitales universitarios (= ANY)}}

#### F21 · Médicos con más experiencia que algún médico de Cardiología (> SOME)

SOME es sinónimo de ANY: la comparación debe cumplirse al menos con un valor interno.

Palabras clave: `salud`, `subconsultas`, `medicos`, `subconsulta`

```sql
SELECT id_medico, apellidos FROM medicos WHERE anios_experiencia > SOME (SELECT anios_experiencia FROM medicos WHERE id_especialidad = 1) ORDER BY id_medico
```

{{query:F21 · Médicos con más experiencia que algún médico de Cardiología (> SOME)}}

### G · Uniones

#### G01 · Nombres de hospitales y de aseguradoras, sin repetir

UNION de dos tablas de una columna: los nombres de los hospitales y los de las aseguradoras, cada uno una vez y ordenados por nombre.

Palabras clave: `salud`, `uniones`, `hospitales`, `seguros`, `union`

```sql
SELECT nombre FROM hospitales UNION SELECT nombre FROM seguros ORDER BY nombre
```

{{query:G01 · Nombres de hospitales y de aseguradoras, sin repetir}}

#### G02 · El país de cada hospital, dos veces (UNION ALL)

El mismo SELECT a los dos lados de UNION ALL: las filas se ponen una debajo de otra sin quitar repetidos, así que salen 14.

Palabras clave: `salud`, `uniones`, `hospitales`, `union`

```sql
SELECT pais FROM hospitales UNION ALL SELECT pais FROM hospitales ORDER BY pais
```

{{query:G02 · El país de cada hospital, dos veces (UNION ALL)}}

#### G03 · Cada país una vez (UNION)

La misma unión sin ALL: UNION quita las filas repetidas, así que cada país sale una sola vez.

Palabras clave: `salud`, `uniones`, `hospitales`, `union`

```sql
SELECT pais FROM hospitales UNION SELECT pais FROM hospitales ORDER BY pais
```

{{query:G03 · Cada país una vez (UNION)}}

#### G04 · Una unión leída como tabla

La unión va en el FROM con alias (tabla derivada): se cuentan los identificadores de médico de la plantilla y de las citas, sin repetir.

Palabras clave: `salud`, `uniones`, `medicos`, `citas`, `subconsulta`, `union`, `agregado`

```sql
SELECT COUNT(*) AS n FROM (SELECT id_medico FROM medicos UNION SELECT id_medico FROM citas) AS t
```

{{query:G04 · Una unión leída como tabla}}

#### G05 · Unión con alias, ORDER BY y LIMIT

Los dos lados llaman item a su columna: el ORDER BY de la unión ordena el resultado combinado por ese nombre y el LIMIT deja los 5 primeros.

Palabras clave: `salud`, `uniones`, `hospitales`, `seguros`, `union`

```sql
SELECT nombre AS item FROM hospitales UNION SELECT nombre AS item FROM seguros ORDER BY item LIMIT 5
```

{{query:G05 · Unión con alias, ORDER BY y LIMIT}}

### H · Scripts como tablas

#### H01 · Pacientes por ciudad, leídos del script

Lee el resultado del script «Pacientes por ciudad» como si fuera una tabla.

Palabras clave: `salud`, `scripts-como-tablas`

```sql
SELECT ciudad, pacientes FROM [Pacientes por ciudad] ORDER BY pacientes DESC, ciudad
```

{{query:H01 · Pacientes por ciudad, leídos del script}}

#### H02 · Cuántas ciudades devuelve el script

Lee el resultado del script «Pacientes por ciudad» como si fuera una tabla.

Palabras clave: `salud`, `scripts-como-tablas`, `agregado`

```sql
SELECT COUNT(*) AS total FROM [Pacientes por ciudad]
```

{{query:H02 · Cuántas ciudades devuelve el script}}

#### H03 · Ciudades con 5 o más pacientes

Lee el resultado del script «Pacientes por ciudad» como si fuera una tabla.

Palabras clave: `salud`, `scripts-como-tablas`

```sql
SELECT ciudad, pacientes FROM [Pacientes por ciudad] WHERE pacientes >= 5 ORDER BY pacientes DESC, ciudad
```

{{query:H03 · Ciudades con 5 o más pacientes}}

### I · Parámetros

#### P01 · Ciudades de los hospitales (origen del parámetro)

Origen de un parámetro: cada fila ofrece una ciudad y cuántos hospitales tiene.

Palabras clave: `salud`, `parametros`, `origen`, `referencia`

```sql
SELECT ciudad, COUNT(*) AS hospitales FROM hospitales GROUP BY ciudad ORDER BY ciudad
```

{{query:P01 · Ciudades de los hospitales (origen del parámetro)}}

#### P02 · Factura más cara (origen del parámetro)

Origen de un parámetro que devuelve una sola fila: la factura de mayor importe.

Palabras clave: `salud`, `parametros`, `origen`, `referencia`

```sql
SELECT id_factura, importe FROM facturas ORDER BY importe DESC, id_factura LIMIT 1
```

{{query:P02 · Factura más cara (origen del parámetro)}}

#### P03 · Habitaciones de una planta (rango)

Parámetro de rango: la planta se elige en un desplegable de 0 a 5.

Palabras clave: `salud`, `parametros`, `rango`, `habitaciones`

```sql
SELECT codigo, tipo, num_camas, planta FROM habitaciones WHERE planta = {{ 0..5 | Planta }} ORDER BY codigo
```

{{query:P03 · Habitaciones de una planta (rango)}}

#### P04 · Citas por estado de los médicos investigadores (subconsulta)

Parámetro con una subconsulta: los médicos investigadores salen de la tabla de médicos.

Palabras clave: `salud`, `parametros`, `subconsulta`, `citas`

```sql
SELECT estado, COUNT(*) AS citas FROM citas WHERE id_medico IN {{ SELECT id_medico FROM medicos WHERE es_investigador = true ORDER BY id_medico | Médico investigador }} GROUP BY estado ORDER BY estado
```

{{query:P04 · Citas por estado de los médicos investigadores (subconsulta)}}

#### P05 · Pacientes de las ciudades de los hospitales (referencia a consulta)

Parámetro que referencia una consulta guardada: sus pares ciudad/número llenan el desplegable.

Palabras clave: `salud`, `parametros`, `referencia`, `pacientes`

```sql
SELECT nombre, apellidos, ciudad FROM pacientes WHERE ciudad IN {{ P01 · Ciudades de los hospitales (origen del parámetro) | Ciudad }} ORDER BY apellidos, nombre
```

{{query:P05 · Pacientes de las ciudades de los hospitales (referencia a consulta)}}

#### P06 · Facturas salvo la más cara (NOT IN con referencia)

Parámetro en un NOT IN que referencia la consulta de la factura más cara.

Palabras clave: `salud`, `parametros`, `referencia`, `not-in`

```sql
SELECT id_factura, importe, metodo_pago FROM facturas WHERE id_factura NOT IN {{ P02 · Factura más cara (origen del parámetro) | Factura más cara }} ORDER BY importe DESC, id_factura LIMIT 10
```

{{query:P06 · Facturas salvo la más cara (NOT IN con referencia)}}

#### P07 · Ingresos por hospital (HAVING con rango)

Parámetro de rango dentro de un HAVING: filtra los hospitales por su número de ingresos.

Palabras clave: `salud`, `parametros`, `rango`, `having`

```sql
SELECT h.nombre, COUNT(*) AS ingresos FROM hospitales AS h INNER JOIN habitaciones AS r ON r.id_hospital = h.id_hospital INNER JOIN ingresos AS i ON i.id_habitacion = r.id_habitacion GROUP BY h.nombre HAVING COUNT(*) >= {{ 1..20 | Ingresos mínimos }} ORDER BY ingresos DESC, h.nombre
```

{{query:P07 · Ingresos por hospital (HAVING con rango)}}

#### P08 · Hospitales del script (referencia a script)

Parámetro que referencia un script: sus filas dan los valores y las etiquetas del desplegable.

Palabras clave: `salud`, `parametros`, `referencia`, `script`

```sql
SELECT nombre, ciudad, num_camas FROM hospitales WHERE id_hospital IN {{ P10 · Hospitales para el parámetro | Hospital }} ORDER BY nombre
```

{{query:P08 · Hospitales del script (referencia a script)}}

#### P11 · Ingresos entre dos costes (dos rangos)

Dos parámetros de rango en un BETWEEN: coste diario mínimo y máximo.

Palabras clave: `salud`, `parametros`, `varios`, `rango`

```sql
SELECT id_ingreso, coste_dia, dias_previstos FROM ingresos WHERE coste_dia BETWEEN {{ 200..500 | Coste mínimo }} AND {{ 500..900 | Coste máximo }} ORDER BY coste_dia, id_ingreso
```

{{query:P11 · Ingresos entre dos costes (dos rangos)}}

#### P12 · Citas por estado y tipo (dos subconsultas)

Dos parámetros de subconsulta: el estado y el tipo de cita, a la vez.

Palabras clave: `salud`, `parametros`, `varios`, `subconsulta`

```sql
SELECT estado, tipo, COUNT(*) AS citas FROM citas WHERE estado = {{ SELECT estado FROM citas GROUP BY estado ORDER BY estado | Estado }} AND tipo = {{ SELECT tipo FROM citas GROUP BY tipo ORDER BY tipo | Tipo }} GROUP BY estado, tipo ORDER BY estado, tipo
```

{{query:P12 · Citas por estado y tipo (dos subconsultas)}}

#### P13 · Pacientes de una ciudad y un seguro (referencia y subconsulta)

Dos parámetros: las ciudades de una consulta guardada y un seguro de una subconsulta.

Palabras clave: `salud`, `parametros`, `varios`, `pacientes`

```sql
SELECT nombre, apellidos, ciudad FROM pacientes WHERE ciudad IN {{ P01 · Ciudades de los hospitales (origen del parámetro) | Ciudad }} AND id_seguro = {{ SELECT id_seguro FROM seguros ORDER BY id_seguro | Seguro }} ORDER BY apellidos, nombre
```

{{query:P13 · Pacientes de una ciudad y un seguro (referencia y subconsulta)}}

#### P14 · Habitaciones de una planta con camas mínimas (dos rangos)

Dos parámetros de rango: la planta y el número mínimo de camas.

Palabras clave: `salud`, `parametros`, `varios`, `habitaciones`

```sql
SELECT codigo, tipo, num_camas, planta FROM habitaciones WHERE planta = {{ 0..5 | Planta }} AND num_camas >= {{ 0..2 | Camas mínimas }} ORDER BY codigo
```

{{query:P14 · Habitaciones de una planta con camas mínimas (dos rangos)}}

#### P15 · Ingresos de un hospital con coste mínimo (referencia y rango)

Dos parámetros: el hospital (lista de un script) y el coste diario mínimo (rango).

Palabras clave: `salud`, `parametros`, `varios`, `referencia`

```sql
SELECT h.nombre AS hospital, i.id_ingreso, i.coste_dia FROM hospitales AS h INNER JOIN habitaciones AS r ON r.id_hospital = h.id_hospital INNER JOIN ingresos AS i ON i.id_habitacion = r.id_habitacion WHERE h.id_hospital IN {{ P10 · Hospitales para el parámetro | Hospital }} AND i.coste_dia >= {{ 200..900 | Coste mínimo }} ORDER BY i.coste_dia DESC, i.id_ingreso LIMIT 10
```

{{query:P15 · Ingresos de un hospital con coste mínimo (referencia y rango)}}

### J · PIVOT

#### P01 · Citas por estado y tipo

PIVOT con estados en filas, tipos de cita en columnas y COUNT(*), con totales.

Palabras clave: `pivot`, `tabla dinámica`, `recuento`, `media`, `porcentaje`

```sql
PIVOT (SELECT estado) BY (SELECT tipo) FROM citas VALUES COUNT(*) AS citas TOTALS ROWS GRAND COLUMNS GRAND;
```

{{query:P01 · Citas por estado y tipo}}

#### P02 · Facturación por método de pago y cobro

Importe facturado y descuento medio por método de pago y cobro.

Palabras clave: `pivot`, `tabla dinámica`, `recuento`, `media`, `porcentaje`

```sql
PIVOT (SELECT metodo_pago NULLS AS '(sin método)') BY (SELECT pagada) FROM facturas VALUES SUM(importe) AS facturado, AVG(descuento) AS descuento_medio TOTALS ROWS GRAND COLUMNS GRAND;
```

{{query:P02 · Facturación por método de pago y cobro}}

#### P03 · Intervenciones por riesgo y éxito

Intervenciones y coste medio por riesgo y éxito.

Palabras clave: `pivot`, `tabla dinámica`, `recuento`, `media`, `porcentaje`

```sql
PIVOT (SELECT riesgo) BY (SELECT exitosa) FROM intervenciones VALUES COUNT(*) AS intervenciones, AVG(coste) AS coste_medio TOTALS ROWS GRAND COLUMNS GRAND;
```

{{query:P03 · Intervenciones por riesgo y éxito}}

#### P04 · Enfermeros por turno y supervisión

Enfermeros y niveles de experiencia distintos por turno y supervisión.

Palabras clave: `pivot`, `tabla dinámica`, `recuento`, `media`, `porcentaje`

```sql
PIVOT (SELECT turno) BY (SELECT es_supervisor) FROM enfermeros VALUES COUNT(*) AS enfermeros, COUNT(DISTINCT anios_experiencia) AS niveles_experiencia TOTALS ROWS GRAND COLUMNS GRAND;
```

{{query:P04 · Enfermeros por turno y supervisión}}

#### P05 · Diagnósticos por gravedad y cronicidad

Diagnósticos y PCT_OF ROW por gravedad y cronicidad, con medidas en filas.

Palabras clave: `pivot`, `tabla dinámica`, `recuento`, `media`, `porcentaje`

```sql
PIVOT (SELECT gravedad) BY (SELECT es_cronico) FROM diagnosticos VALUES COUNT(*) AS diagnosticos, COUNT(*) SHOW AS PCT_OF ROW AS porcentaje TOTALS ROWS GRAND COLUMNS GRAND ONLY diagnosticos MEASURES ON ROWS;
```

{{query:P05 · Diagnósticos por gravedad y cronicidad}}

### K · CASE e IF

#### K01 · Aseguradoras por cobertura (CASE buscado)

CASE buscado con el límite en 60: menos de 60 es baja, entre 60 y 85 media y el resto alta.

Palabras clave: `salud`, `case`, `funciones`, `seguros`

```sql
SELECT nombre, cobertura_porcentaje, CASE WHEN cobertura_porcentaje < 60 THEN 'Baja' WHEN cobertura_porcentaje BETWEEN 60 AND 85 THEN 'Media' ELSE 'Alta' END AS cobertura FROM seguros ORDER BY cobertura_porcentaje
```

{{query:K01 · Aseguradoras por cobertura (CASE buscado)}}

#### K02 · Intervenciones por riesgo (CASE simple)

CASE simple: compara el riesgo con cada WHEN usando = y devuelve una etiqueta por valor distinto.

Palabras clave: `salud`, `case`, `funciones`, `intervenciones`

```sql
SELECT DISTINCT riesgo, CASE riesgo WHEN 'Bajo' THEN 'Bajo' WHEN 'Medio' THEN 'Medio' WHEN 'Alto' THEN 'Alto' ELSE 'Desconocido' END AS etiqueta FROM intervenciones ORDER BY riesgo
```

{{query:K02 · Intervenciones por riesgo (CASE simple)}}

#### K03 · Pacientes sin teléfono (CASE IS NULL)

El CASE buscado detecta el teléfono ausente con IS NULL y conserva el teléfono real en el resto.

Palabras clave: `salud`, `case`, `nulos`, `pacientes`

```sql
SELECT nombre, apellidos, CASE WHEN telefono IS NULL THEN 'Desconocido' ELSE telefono END AS contacto FROM pacientes WHERE telefono IS NULL ORDER BY apellidos LIMIT 5
```

{{query:K03 · Pacientes sin teléfono (CASE IS NULL)}}

#### K04 · Pacientes con IF

IF(condición, valor, sustituto) es la abreviatura de CASE WHEN condición THEN valor ELSE sustituto END.

Palabras clave: `salud`, `if`, `funciones`, `pacientes`

```sql
SELECT id_paciente, IF(es_cronico, 'Crónico', 'Agudo') AS tipo FROM pacientes WHERE id_paciente <= 6 ORDER BY id_paciente
```

{{query:K04 · Pacientes con IF}}

#### K05 · Médicos por experiencia (CASE en GROUP BY)

La misma expresión CASE clasifica a los médicos y se repite en GROUP BY: 14 veteranos y 16 jóvenes.

Palabras clave: `salud`, `case`, `group-by`, `medicos`

```sql
SELECT CASE WHEN anios_experiencia >= 20 THEN 'Veterano' ELSE 'Joven' END AS grupo, COUNT(*) AS n FROM medicos GROUP BY CASE WHEN anios_experiencia >= 20 THEN 'Veterano' ELSE 'Joven' END ORDER BY grupo
```

{{query:K05 · Médicos por experiencia (CASE en GROUP BY)}}

### L · Funciones en SELECT y PIVOT

#### L01 · Funciones numéricas: ABS y ROUND

Aplica ABS y ROUND a los precios de los medicamentos: un decimal y distancia a 5.

Palabras clave: `salud`, `funciones`, `numericas`, `abs`, `round`

```sql
SELECT id_medicamento, precio, ROUND(precio, 1) AS redondeado, ROUND(ABS(precio - 5), 2) AS distancia FROM medicamentos WHERE id_medicamento <= 4 ORDER BY id_medicamento
```

{{query:L01 · Funciones numéricas: ABS y ROUND}}

#### L02 · Funciones de texto: UPPER, LOWER, LENGTH, TRIM, SUBSTR y CONCAT

Aplica las seis funciones de texto a los tres primeros medicamentos.

Palabras clave: `salud`, `funciones`, `texto`, `upper`, `lower`, `length`, `trim`, `substr`, `concat`

```sql
SELECT id_medicamento, UPPER(nombre) AS nombre_mayus, LOWER(presentacion) AS presentacion_minus, LENGTH(principio_activo) AS letras, TRIM(nombre) AS recortado, SUBSTR(nombre, 1, 4) AS prefijo, CONCAT(nombre, ' (', presentacion, ')') AS etiqueta FROM medicamentos WHERE id_medicamento <= 3 ORDER BY id_medicamento
```

{{query:L02 · Funciones de texto: UPPER, LOWER, LENGTH, TRIM, SUBSTR y CONCAT}}

#### L03 · Expresiones regulares: INSTR y REPLACE

Usa REGEXPR_INSTR para localizar la @ en los correos de médicos y REGEXPR_REPLACE para enmascarar los dígitos del número de colegiado.

Palabras clave: `salud`, `funciones`, `regex`, `instr`, `replace`

```sql
SELECT id_medico, email, REGEXPR_INSTR(email, '@') AS posicion_arroba, REGEXPR_REPLACE(num_colegiado, '[0-9]', '#') AS enmascarado FROM medicos WHERE id_medico <= 3 ORDER BY id_medico
```

{{query:L03 · Expresiones regulares: INSTR y REPLACE}}

#### L04 · Funciones de NULL: COALESCE, IFNULL y NULLIF

Usa COALESCE, IFNULL y NULLIF sobre el teléfono y el tipo de seguro.

Palabras clave: `salud`, `funciones`, `null`, `coalesce`, `ifnull`, `nullif`

```sql
SELECT id_seguro, COALESCE(telefono, 'Sin teléfono') AS telefono, IFNULL(tipo, 'Desconocido') AS tipo, NULLIF(tipo, 'Público') AS no_publico FROM seguros ORDER BY id_seguro
```

{{query:L04 · Funciones de NULL: COALESCE, IFNULL y NULLIF}}

#### L05 · Funciones condicionales: IF y CASE

Usa IF y CASE para clasificar pacientes por cronicidad y longitud del teléfono.

Palabras clave: `salud`, `funciones`, `condicionales`, `if`, `case`

```sql
SELECT id_paciente, IF(es_cronico, 'Crónico', 'Agudo') AS tipo, CASE WHEN telefono IS NULL THEN 'Sin teléfono' WHEN LENGTH(telefono) = 9 THEN 'Válido' ELSE 'Otro' END AS estado_telefono FROM pacientes WHERE id_paciente <= 6 ORDER BY id_paciente
```

{{query:L05 · Funciones condicionales: IF y CASE}}

#### L06 · Funciones de agregado: COUNT, SUM, AVG, MIN y MAX

Ejecuta COUNT(*), COUNT(columna), COUNT(DISTINCT), SUM, AVG, MIN y MAX sobre las visitas.

Palabras clave: `salud`, `funciones`, `agregados`, `count`, `sum`, `avg`, `min`, `max`

```sql
SELECT COUNT(*) AS total, COUNT(motivo) AS con_motivo, COUNT(DISTINCT id_medico) AS medicos, SUM(coste) AS coste, AVG(coste) AS coste_medio, MIN(coste) AS coste_min, MAX(coste) AS coste_max FROM visitas
```

{{query:L06 · Funciones de agregado: COUNT, SUM, AVG, MIN y MAX}}

#### L07 · PIVOT con todas las funciones de agregado

PIVOT con COUNT, SUM, AVG, MIN y MAX como medidas, una medida por fila, por tipo de habitación y ocupación.

Palabras clave: `salud`, `funciones`, `pivot`, `agregados`

```sql
PIVOT (SELECT tipo) BY (SELECT ocupada) FROM habitaciones VALUES COUNT(*) AS habitaciones, SUM(num_camas) AS camas, AVG(num_camas) AS camas_medias, MIN(num_camas) AS camas_min, MAX(num_camas) AS camas_max TOTALS ROWS GRAND COLUMNS GRAND MEASURES ON ROWS;
```

{{query:L07 · PIVOT con todas las funciones de agregado}}

#### L08 · PIVOT con funciones escalares en el eje de filas

PIVOT con funciones escalares en el eje de filas: UPPER y CASE crean el grupo de experiencia, REGEXPR_INSTR filtra los médicos con correo y COUNT/AVG agregan.

Palabras clave: `salud`, `funciones`, `pivot`, `escalares`, `case`, `regexpr_instr`

```sql
PIVOT (SELECT UPPER(CASE WHEN anios_experiencia >= 20 THEN 'veterano' ELSE 'joven' END) AS grupo WHERE REGEXPR_INSTR(email, '@') > 0) BY (SELECT es_investigador) FROM medicos VALUES COUNT(*) AS medicos, AVG(anios_experiencia) AS experiencia_media TOTALS ROWS GRAND COLUMNS GRAND;
```

{{query:L08 · PIVOT con funciones escalares en el eje de filas}}

## Scripts

### A · Básicas y agregados

#### Pacientes crónicos

Filtra los pacientes crónicos y muestra nombre, ciudad, grupo sanguíneo y teléfono.

Palabras clave: `salud`, `filtros`, `pacientes`, `filtro`, `map`

```js
const datos = tables["pacientes"];
return datos
  .filter((fila) => fila.es_cronico)
  .map((fila) => ({
    "nombre_completo": `${fila.nombre} ${fila.apellidos}`,
    "ciudad": fila.ciudad,
    "grupo_sanguineo": fila.grupo_sanguineo,
    "telefono": fila.telefono,
  }));
```

{{script:Pacientes crónicos}}

#### Edad de los pacientes

Calcula la edad a 1 de septiembre de 2025 y ordena de mayor a menor.

Palabras clave: `salud`, `fechas`, `pacientes`, `map`, `orden`

```js
const datos = tables["pacientes"];
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

{{script:Edad de los pacientes}}

#### Pacientes por ciudad

Cuenta los pacientes de cada ciudad y calcula su porcentaje sobre el total.

Palabras clave: `salud`, `agregados`, `pacientes`, `map`, `reduce`, `orden`, `math`

```js
const datos = tables["pacientes"];
const groups = datos.reduce((acc, fila) => {
  const key = fila.ciudad || "—";
  acc[key] = (acc[key] || 0) + 1;
  return acc;
}, {});
const total = datos.length;
return Object.entries(groups)
  .map(([city, count]) => ({
    "ciudad": city,
    "pacientes": count,
    "porcentaje": total ? Math.round((count / total) * 1000) / 10 : 0,
  }))
  .sort((a, b) => b["pacientes"] - a["pacientes"]);
```

{{script:Pacientes por ciudad}}

#### Contactos incompletos

Lista los pacientes a los que les falta el teléfono, el correo o los dos.

Palabras clave: `salud`, `filtros`, `pacientes`, `filtro`, `map`

```js
const datos = tables["pacientes"];
return datos
  .filter((fila) => !fila.telefono || !fila.email)
  .map((fila) => {
    const missing = [];
    if (!fila.telefono) missing.push("telefono");
    if (!fila.email) missing.push("email");
    return {
      "nombre_completo": `${fila.nombre} ${fila.apellidos}`,
      "telefono": fila.telefono,
      "email": fila.email,
      "faltan": missing.join(", "),
    };
  });
```

{{script:Contactos incompletos}}

#### Grupos sanguíneos

Cuenta los pacientes por grupo sanguíneo (sin los nulos) y calcula el porcentaje.

Palabras clave: `salud`, `agregados`, `pacientes`, `map`, `reduce`, `orden`, `math`

```js
const datos = tables["pacientes"];
const groups = datos.reduce((acc, fila) => {
  const key = fila.grupo_sanguineo;
  if (!key) return acc;
  acc[key] = (acc[key] || 0) + 1;
  return acc;
}, {});
const total = Object.values(groups).reduce((a, b) => a + b, 0);
return Object.entries(groups)
  .map(([group, count]) => ({
    "grupo_sanguineo": group,
    "pacientes": count,
    "porcentaje": total ? Math.round((count / total) * 1000) / 10 : 0,
  }))
  .sort((a, b) => b["pacientes"] - a["pacientes"]);
```

{{script:Grupos sanguíneos}}

#### Ingresos por motivo

Agrupa los ingresos por motivo, cuenta cuántos son urgentes y calcula el coste diario medio.

Palabras clave: `salud`, `agregados`, `ingresos`, `map`, `reduce`, `orden`, `math`

```js
const datos = tables["ingresos"];
const groups = datos.reduce((acc, fila) => {
  const key = fila.motivo || "—";
  acc[key] = acc[key] || { count: 0, cost: 0, emergency: 0 };
  acc[key].count += 1;
  acc[key].cost += fila.coste_dia || 0;
  if (fila.es_urgente) acc[key].emergency += 1;
  return acc;
}, {});
return Object.entries(groups)
  .map(([reason, g]) => ({
    "motivo": reason,
    "ingresos": g.count,
    "urgentes": g.emergency,
    "coste_medio": Math.round(g.cost / g.count),
  }))
  .sort((a, b) => b["ingresos"] - a["ingresos"]);
```

{{script:Ingresos por motivo}}

#### Facturación por método de pago

Agrupa las facturas por método de pago (incluidos los nulos) y suma importes y descuentos.

Palabras clave: `salud`, `agregados`, `facturas`, `map`, `reduce`, `orden`, `math`

```js
const datos = tables["facturas"];
const groups = datos.reduce((acc, fila) => {
  const key = fila.metodo_pago || "—";
  acc[key] = acc[key] || { count: 0, amount: 0, discount: 0 };
  acc[key].count += 1;
  acc[key].amount += fila.importe || 0;
  acc[key].discount += fila.descuento || 0;
  return acc;
}, {});
const total = Object.values(groups).reduce((a, b) => a + b.amount, 0);
return Object.entries(groups)
  .map(([method, g]) => ({
    "metodo_pago": method,
    "facturas": g.count,
    "importe": Math.round(g.amount),
    "media": Math.round(g.amount / g.count),
    "descuento": Math.round((g.discount / g.count) * 10) / 10,
    "porcentaje": total ? Math.round((g.amount / total) * 1000) / 10 : 0,
  }))
  .sort((a, b) => b["importe"] - a["importe"]);
```

{{script:Facturación por método de pago}}

#### Citas por estado y tipo (pivot)

Convierte los tipos de cita en columnas dinámicas y cuenta cuántas hay por estado.

Palabras clave: `salud`, `agregados`, `citas`, `map`, `reduce`, `orden`, `foreach`

```js
const datos = tables["citas"];
const types = Array.from(new Set(datos.map((fila) => fila.tipo))).sort();
const groups = datos.reduce((acc, fila) => {
  const status = fila.estado;
  const type = fila.tipo;
  acc[status] = acc[status] || { status };
  acc[status][type] = (acc[status][type] || 0) + 1;
  return acc;
}, {});
return Object.values(groups)
  .map((row) => {
    const out = { "estado": row.status };
    types.forEach((type) => { out[type] = row[type] || 0; });
    return out;
  })
  .sort((a, b) => String(a["estado"]).localeCompare(String(b["estado"])));
```

{{script:Citas por estado y tipo (pivot)}}

#### Analíticas anormales por tipo

Agrupa las analíticas por tipo y calcula el porcentaje de anormales y las medias de glucosa y colesterol.

Palabras clave: `salud`, `agregados`, `analiticas`, `map`, `reduce`, `orden`, `math`

```js
const datos = tables["analiticas"];
const groups = datos.reduce((acc, fila) => {
  const key = fila.tipo || "—";
  acc[key] = acc[key] || { count: 0, abnormal: 0, glucose: 0, glucoseCount: 0, cholesterol: 0, cholesterolCount: 0 };
  const g = acc[key];
  g.count += 1;
  if (fila.es_anormal) g.abnormal += 1;
  if (fila.glucosa !== null && fila.glucosa !== undefined) { g.glucose += fila.glucosa; g.glucoseCount += 1; }
  if (fila.colesterol !== null && fila.colesterol !== undefined) { g.cholesterol += fila.colesterol; g.cholesterolCount += 1; }
  return acc;
}, {});
return Object.entries(groups)
  .map(([type, g]) => ({
    "tipo": type,
    "total": g.count,
    "anormales": g.abnormal,
    "porcentaje": Math.round((g.abnormal / g.count) * 1000) / 10,
    "glucosa_media": g.glucoseCount ? Math.round((g.glucose / g.glucoseCount) * 10) / 10 : null,
    "colesterol_medio": g.cholesterolCount ? Math.round((g.cholesterol / g.cholesterolCount) * 10) / 10 : null,
  }))
  .sort((a, b) => b["total"] - a["total"]);
```

{{script:Analíticas anormales por tipo}}

#### Medicamentos con stock bajo

Lista los medicamentos con menos de 50 unidades y calcula cuánto reponer.

Palabras clave: `salud`, `filtros`, `medicamentos`, `filtro`, `map`, `orden`, `math`

```js
const datos = tables["medicamentos"];
return datos
  .filter((fila) => (fila.stock || 0) < 50)
  .map((fila) => ({
    "medicamento": fila.nombre,
    "stock": fila.stock,
    "reponer": Math.max(0, 100 - (fila.stock || 0)),
  }))
  .sort((a, b) => a["stock"] - b["stock"]);
```

{{script:Medicamentos con stock bajo}}

#### Pacientes por grupo de edad

Clasifica a los pacientes en menores, jóvenes, adultos y mayores, y calcula su edad media.

Palabras clave: `salud`, `fechas`, `pacientes`, `map`, `reduce`, `orden`, `math`

```js
const datos = tables["pacientes"];
const reference = new Date("2025-09-01");
const groups = datos.reduce((acc, fila) => {
  const birth = new Date(fila.fecha_nacimiento);
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
    "grupo_edad": group,
    "pacientes": g.count,
    "edad_media": Math.round((g.sum / g.count) * 10) / 10,
  }))
  .sort((a, b) => String(a["grupo_edad"]).localeCompare(String(b["grupo_edad"])));
```

{{script:Pacientes por grupo de edad}}

#### Citas por mes

Agrupa las citas por año y mes, cuenta las canceladas y ordena cronológicamente.

Palabras clave: `salud`, `fechas`, `citas`, `map`, `reduce`, `orden`, `slice`

```js
const datos = tables["citas"];
const groups = datos.reduce((acc, fila) => {
  const key = String(fila.fecha).slice(0, 7);
  acc[key] = acc[key] || { count: 0, cancelled: 0 };
  acc[key].count += 1;
  if (fila.estado === "Cancelada") acc[key].cancelled += 1;
  return acc;
}, {});
return Object.entries(groups)
  .map(([month, g]) => ({
    "mes": month,
    "citas": g.count,
    "anuladas": g.cancelled,
  }))
  .sort((a, b) => String(a["mes"]).localeCompare(String(b["mes"])));
```

{{script:Citas por mes}}

#### JavaScript · Línea temporal clínica

Une ingresos, citas, analíticas e intervenciones del paciente con más actividad en una sola línea temporal.

Palabras clave: `salud`, `fechas`, `ingresos`, `citas`, `analiticas`, `filtro`, `map`, `orden`, `foreach`

```js
const cuentas = new Map();
const sumar = (id) => cuentas.set(id, (cuentas.get(id) || 0) + 1);
tables["ingresos"].forEach((fila) => sumar(fila.id_paciente));
tables["citas"].forEach((fila) => sumar(fila.id_paciente));
tables["analiticas"].forEach((fila) => sumar(fila.id_paciente));
tables["intervenciones"].forEach((fila) => sumar(fila.id_paciente));
const ocupado = [...cuentas.entries()].sort((a, b) => b[1] - a[1] || a[0] - b[0])[0];
const idPaciente = ocupado[0];
const paciente = tables["pacientes"].find((fila) => fila.id_paciente === idPaciente);
const eventos = [];
tables["ingresos"].filter((fila) => fila.id_paciente === idPaciente).forEach((fila) => {
  eventos.push({
    "fecha": fila.fecha_ingreso,
    "origen": "ingreso",
    "detalle": fila.motivo,
    "estado": fila.fecha_alta ? "alta" : "activo",
  });
});
tables["citas"].filter((fila) => fila.id_paciente === idPaciente).forEach((fila) => {
  eventos.push({
    "fecha": fila.fecha,
    "origen": "cita",
    "detalle": `${fila.tipo} · ${fila.duracion_min} min`,
    "estado": fila.estado,
  });
});
tables["analiticas"].filter((fila) => fila.id_paciente === idPaciente).forEach((fila) => {
  eventos.push({
    "fecha": fila.fecha,
    "origen": "analítica",
    "detalle": `${fila.tipo}: glucosa ${fila.glucosa}`,
    "estado": fila.es_anormal ? "anormal" : "normal",
  });
});
tables["intervenciones"].filter((fila) => fila.id_paciente === idPaciente).forEach((fila) => {
  eventos.push({
    "fecha": fila.fecha,
    "origen": "intervención",
    "detalle": fila.nombre,
    "estado": fila.exitosa ? "exitosa" : "fallida",
  });
});
return eventos
  .sort((a, b) => String(a.fecha).localeCompare(String(b.fecha)) || a.origen.localeCompare(b.origen))
  .map((evento, indice) => ({
    "orden": indice + 1,
    "paciente": `${paciente.apellidos}, ${paciente.nombre}`,
    "fecha": evento.fecha,
    "origen": evento.origen,
    "detalle": evento.detalle,
    "estado": evento.estado,
  }));
```

{{script:JavaScript · Línea temporal clínica}}

#### JavaScript · Previsión de coste de farmacia

Precio del medicamento × unidades prescritas, con stock y las recetas que hay detrás.

Palabras clave: `salud`, `agregados`, `medicamentos`, `prescripciones`, `map`, `orden`, `foreach`, `math`

```js
const medicamentos = new Map(tables["medicamentos"].map((fila) => [fila.id_medicamento, fila]));
const grupos = new Map();
tables["prescripciones"].forEach((fila) => {
  const medicamento = medicamentos.get(fila.id_medicamento);
  if (!medicamento) return;
  const grupo = grupos.get(fila.id_medicamento) || { medicamento, prescripciones: 0, unidades: 0 };
  grupo.prescripciones += 1;
  grupo.unidades += (fila.cantidad || 0) * (fila.duracion_dias || 0);
  grupos.set(fila.id_medicamento, grupo);
});
return [...grupos.values()]
  .map((grupo) => {
    const coste = grupo.unidades * (grupo.medicamento.precio || 0);
    return {
      "medicamento": grupo.medicamento.nombre,
      "via": grupo.medicamento.via,
      "prescripciones": grupo.prescripciones,
      "unidades": grupo.unidades,
      "coste_previsto": Math.round(coste),
      "stock": grupo.medicamento.stock,
      "alerta_stock": (grupo.medicamento.stock || 0) < 50 ? "bajo" : "correcto",
      "coste_unitario": grupo.medicamento.precio,
    };
  })
  .sort((a, b) => b["coste_previsto"] - a["coste_previsto"] || a["medicamento"].localeCompare(b["medicamento"]));
```

{{script:JavaScript · Previsión de coste de farmacia}}

#### JavaScript · Informe mensual de facturación

Facturas por mes con importes pagados y pendientes, tasa de cobro y total acumulado.

Palabras clave: `salud`, `fechas`, `facturas`, `map`, `orden`, `foreach`, `math`

```js
const grupos = new Map();
tables["facturas"].forEach((fila) => {
  const fecha = new Date(fila.fecha);
  const mes = `${fecha.getFullYear()}-${String(fecha.getMonth() + 1).padStart(2, "0")}`;
  const grupo = grupos.get(mes) || { mes, facturas: 0, total: 0, pagado: 0, pendiente: 0 };
  grupo.facturas += 1;
  grupo.total += fila.importe || 0;
  if (fila.pagada) grupo.pagado += fila.importe || 0;
  else grupo.pendiente += fila.importe || 0;
  grupos.set(mes, grupo);
});
let acumulado = 0;
return [...grupos.values()]
  .sort((a, b) => a.mes.localeCompare(b.mes))
  .map((grupo) => {
    acumulado += grupo.total;
    return {
      "mes": grupo.mes,
      "facturas": grupo.facturas,
      "importe": Math.round(grupo.total),
      "pagado": Math.round(grupo.pagado),
      "pendiente": Math.round(grupo.pendiente),
      "porcentaje_cobrado": grupo.total ? Math.round((grupo.pagado / grupo.total) * 1000) / 10 : 0,
      "acumulado": Math.round(acumulado),
    };
  });
```

{{script:JavaScript · Informe mensual de facturación}}

### B · Uniones (join)

#### Pacientes por seguro

Cruza pacientes con seguros, cuenta cuántos hay por seguro y muestra su cobertura.

Palabras clave: `salud`, `cruces`, `pacientes`, `seguros`, `map`, `reduce`, `orden`, `math`

```js
const datos = tables["pacientes"];
const insurers = new Map(tables["seguros"].map((i) => [i.id_seguro, i]));
const groups = datos.reduce((acc, fila) => {
  const id = fila.id_seguro;
  const insurer = insurers.get(id);
  const name = insurer ? insurer.nombre : "—";
  acc[name] = acc[name] || { count: 0, coverage: insurer ? insurer.cobertura_porcentaje : null };
  acc[name].count += 1;
  return acc;
}, {});
const total = datos.length;
return Object.entries(groups)
  .map(([name, g]) => ({
    "seguro": name,
    "pacientes": g.count,
    "cobertura": g.coverage,
    "porcentaje": total ? Math.round((g.count / total) * 1000) / 10 : 0,
  }))
  .sort((a, b) => b["pacientes"] - a["pacientes"]);
```

{{script:Pacientes por seguro}}

#### Ingresos y coste por hospital

Cruza ingresos, habitaciones y hospitales; cuenta ingresos, urgentes y coste estimado por estancia.

Palabras clave: `salud`, `cruces`, `ingresos`, `habitaciones`, `hospitales`, `map`, `reduce`, `orden`, `math`

```js
const datos = tables["ingresos"];
const daysBetween = (from, to) => {
  if (!from || !to) return 0;
  return Math.max(0, Math.round((new Date(to) - new Date(from)) / 86400000));
};
const rooms = new Map(tables["habitaciones"].map((r) => [r.id_habitacion, r]));
const hospitals = new Map(tables["hospitales"].map((h) => [h.id_hospital, h.nombre]));
const groups = datos.reduce((acc, fila) => {
  const room = rooms.get(fila.id_habitacion);
  const hospital = room ? hospitals.get(room.id_hospital) : null;
  const key = hospital || "—";
  acc[key] = acc[key] || { count: 0, cost: 0, emergency: 0 };
  const days = daysBetween(fila.fecha_ingreso, fila.fecha_alta);
  acc[key].count += 1;
  acc[key].cost += days * (fila.coste_dia || 0);
  if (fila.es_urgente) acc[key].emergency += 1;
  return acc;
}, {});
return Object.entries(groups)
  .map(([hospital, g]) => ({
    "hospital": hospital,
    "ingresos": g.count,
    "urgentes": g.emergency,
    "coste": Math.round(g.cost),
    "coste_medio": Math.round(g.cost / g.count),
  }))
  .sort((a, b) => b["coste"] - a["coste"]);
```

{{script:Ingresos y coste por hospital}}

#### Coste y días de estancia

Calcula los días ingresados y el coste de los ingresos ya dados de alta; ordena por coste.

Palabras clave: `salud`, `cruces`, `ingresos`, `pacientes`, `filtro`, `map`, `orden`, `slice`

```js
const datos = tables["ingresos"];
const daysBetween = (from, to) => {
  if (!from || !to) return 0;
  return Math.max(0, Math.round((new Date(to) - new Date(from)) / 86400000));
};
const patients = new Map(tables["pacientes"].map((p) => [p.id_paciente, `${p.nombre} ${p.apellidos}`]));
return datos
  .filter((fila) => fila.fecha_alta)
  .map((fila) => {
    const days = daysBetween(fila.fecha_ingreso, fila.fecha_alta);
    return {
      "paciente": patients.get(fila.id_paciente) || `#${fila.id_paciente}`,
      "motivo": fila.motivo,
      "dias": days,
      "coste": Math.round(days * (fila.coste_dia || 0)),
    };
  })
  .sort((a, b) => b["coste"] - a["coste"])
  .slice(0, 20);
```

{{script:Coste y días de estancia}}

#### Facturas impagadas por paciente

Filtra las facturas no pagadas, las agrupa por paciente y suma lo que deben.

Palabras clave: `salud`, `cruces`, `facturas`, `pacientes`, `filtro`, `map`, `reduce`, `orden`

```js
const datos = tables["facturas"];
const patients = new Map(tables["pacientes"].map((p) => [p.id_paciente, `${p.nombre} ${p.apellidos}`]));
const groups = datos
  .filter((fila) => fila.pagada === false)
  .reduce((acc, fila) => {
    const key = fila.id_paciente;
    acc[key] = acc[key] || { count: 0, amount: 0 };
    acc[key].count += 1;
    acc[key].amount += fila.importe || 0;
    return acc;
  }, {});
return Object.entries(groups)
  .map(([id, g]) => ({
    "paciente": patients.get(Number(id)) || `#${id}`,
    "facturas": g.count,
    "importe": Math.round(g.amount),
  }))
  .sort((a, b) => b["importe"] - a["importe"]);
```

{{script:Facturas impagadas por paciente}}

#### Duración media de las citas por médico

Cruza las citas con los médicos, calcula la duración media y devuelve los diez primeros.

Palabras clave: `salud`, `cruces`, `citas`, `medicos`, `map`, `reduce`, `orden`, `slice`

```js
const datos = tables["citas"];
const doctors = new Map(tables["medicos"].map((d) => [d.id_medico, `${d.nombre} ${d.apellidos}`]));
const groups = datos.reduce((acc, fila) => {
  const id = fila.id_medico;
  acc[id] = acc[id] || { count: 0, total: 0 };
  acc[id].count += 1;
  acc[id].total += fila.duracion_min || 0;
  return acc;
}, {});
return Object.entries(groups)
  .map(([id, g]) => ({
    "medico": doctors.get(Number(id)) || `#${id}`,
    "citas": g.count,
    "duracion_media": Math.round((g.total / g.count) * 10) / 10,
  }))
  .sort((a, b) => b["duracion_media"] - a["duracion_media"])
  .slice(0, 10);
```

{{script:Duración media de las citas por médico}}

#### Médicos por especialidad

Agrupa los médicos por especialidad y calcula cuántos hay, su experiencia media y cuántos investigan.

Palabras clave: `salud`, `cruces`, `medicos`, `especialidades`, `map`, `reduce`, `orden`, `math`

```js
const datos = tables["medicos"];
const specialties = new Map(tables["especialidades"].map((s) => [s.id_especialidad, s.nombre]));
const groups = datos.reduce((acc, fila) => {
  const key = fila.id_especialidad;
  acc[key] = acc[key] || { count: 0, experience: 0, researchers: 0 };
  acc[key].count += 1;
  acc[key].experience += fila.anios_experiencia || 0;
  if (fila.es_investigador) acc[key].researchers += 1;
  return acc;
}, {});
return Object.entries(groups)
  .map(([id, g]) => ({
    "especialidad": specialties.get(Number(id)) || `#${id}`,
    "medicos": g.count,
    "experiencia_media": Math.round((g.experience / g.count) * 10) / 10,
    "investigadores": g.researchers,
  }))
  .sort((a, b) => b["medicos"] - a["medicos"]);
```

{{script:Médicos por especialidad}}

#### Pacientes con más ingresos

Agrupa los ingresos por paciente, suma su coste estimado y devuelve los diez primeros.

Palabras clave: `salud`, `cruces`, `ingresos`, `pacientes`, `map`, `reduce`, `orden`, `slice`

```js
const datos = tables["ingresos"];
const daysBetween = (from, to) => {
  if (!from || !to) return 0;
  return Math.max(0, Math.round((new Date(to) - new Date(from)) / 86400000));
};
const patients = new Map(tables["pacientes"].map((p) => [p.id_paciente, `${p.nombre} ${p.apellidos}`]));
const groups = datos.reduce((acc, fila) => {
  const id = fila.id_paciente;
  acc[id] = acc[id] || { count: 0, cost: 0 };
  const days = daysBetween(fila.fecha_ingreso, fila.fecha_alta);
  acc[id].count += 1;
  acc[id].cost += days * (fila.coste_dia || 0);
  return acc;
}, {});
return Object.entries(groups)
  .map(([id, g]) => ({
    "paciente": patients.get(Number(id)) || `#${id}`,
    "ingresos": g.count,
    "coste": Math.round(g.cost),
  }))
  .sort((a, b) => b["ingresos"] - a["ingresos"])
  .slice(0, 10);
```

{{script:Pacientes con más ingresos}}

#### Ocupación de habitaciones por hospital

Cuenta habitaciones, ocupadas y camas por hospital y calcula el porcentaje de ocupación.

Palabras clave: `salud`, `cruces`, `habitaciones`, `hospitales`, `map`, `reduce`, `orden`, `math`

```js
const datos = tables["habitaciones"];
const hospitals = new Map(tables["hospitales"].map((h) => [h.id_hospital, h.nombre]));
const groups = datos.reduce((acc, fila) => {
  const id = fila.id_hospital;
  acc[id] = acc[id] || { rooms: 0, occupied: 0, beds: 0 };
  acc[id].rooms += 1;
  if (fila.ocupada) acc[id].occupied += 1;
  acc[id].beds += fila.num_camas || 0;
  return acc;
}, {});
return Object.entries(groups)
  .map(([id, g]) => ({
    "hospital": hospitals.get(Number(id)) || `#${id}`,
    "habitaciones": g.rooms,
    "ocupadas": g.occupied,
    "camas": g.beds,
    "ocupacion": Math.round((g.occupied / g.rooms) * 1000) / 10,
  }))
  .sort((a, b) => b["ocupacion"] - a["ocupacion"]);
```

{{script:Ocupación de habitaciones por hospital}}

#### Ingresos huérfanos

Busca ingresos cuyo paciente, médico o habitación ya no existe en las tablas.

Palabras clave: `salud`, `cruces`, `ingresos`, `pacientes`, `medicos`, `filtro`, `map`

```js
const datos = tables["ingresos"];
const patientIds = new Set(tables["pacientes"].map((p) => p.id_paciente));
const doctorIds = new Set(tables["medicos"].map((d) => d.id_medico));
const roomIds = new Set(tables["habitaciones"].map((r) => r.id_habitacion));
return datos
  .filter((fila) => !patientIds.has(fila.id_paciente) || !doctorIds.has(fila.id_medico) || !roomIds.has(fila.id_habitacion))
  .map((fila) => {
    const missing = [];
    if (!patientIds.has(fila.id_paciente)) missing.push("paciente");
    if (!doctorIds.has(fila.id_medico)) missing.push("medico");
    if (!roomIds.has(fila.id_habitacion)) missing.push("habitacion");
    return {
      "ingreso": fila.id_ingreso,
      "paciente": fila.id_paciente,
      "medico": fila.id_medico,
      "habitacion": fila.id_habitacion,
      "faltan": missing.join(", "),
    };
  });
```

{{script:Ingresos huérfanos}}

#### Coste por especialidad

Cruza ingresos con médicos y especialidades y calcula el coste estimado y medio por especialidad.

Palabras clave: `salud`, `cruces`, `ingresos`, `medicos`, `especialidades`, `map`, `reduce`, `orden`, `math`

```js
const datos = tables["ingresos"];
const daysBetween = (from, to) => {
  if (!from || !to) return 0;
  return Math.max(0, Math.round((new Date(to) - new Date(from)) / 86400000));
};
const doctors = new Map(tables["medicos"].map((d) => [d.id_medico, d]));
const specialties = new Map(tables["especialidades"].map((s) => [s.id_especialidad, s.nombre]));
const groups = datos.reduce((acc, fila) => {
  const doctor = doctors.get(fila.id_medico);
  const specialty = doctor ? specialties.get(doctor.id_especialidad) : null;
  const key = specialty || "—";
  const days = daysBetween(fila.fecha_ingreso, fila.fecha_alta);
  acc[key] = acc[key] || { count: 0, cost: 0 };
  acc[key].count += 1;
  acc[key].cost += days * (fila.coste_dia || 0);
  return acc;
}, {});
return Object.entries(groups)
  .map(([specialty, g]) => ({
    "especialidad": specialty,
    "ingresos": g.count,
    "coste_medio": Math.round(g.cost / g.count),
    "coste": Math.round(g.cost),
  }))
  .sort((a, b) => b["coste"] - a["coste"]);
```

{{script:Coste por especialidad}}

#### JavaScript · Perfil de riesgo de pacientes

Puntos de riesgo por cronicidad, alergias graves, analíticas anormales y la consulta C31.

Palabras clave: `salud`, `cruces`, `alergias`, `analiticas`, `pacientes`, `map`, `orden`, `slice`, `foreach`

```js
const diagnosticos = new Map(queries["C31 · Pacientes con más de un diagnóstico distinto"]().map((fila) => [fila.paciente, fila.diagnosticos]));
const alergias = new Map();
tables["alergias"].forEach((fila) => {
  const grupo = alergias.get(fila.id_paciente) || { total: 0, graves: 0 };
  grupo.total += 1;
  if (fila.severidad === "Grave") grupo.graves += 1;
  alergias.set(fila.id_paciente, grupo);
});
const anormales = new Map();
tables["analiticas"].forEach((fila) => {
  if (!fila.es_anormal) return;
  anormales.set(fila.id_paciente, (anormales.get(fila.id_paciente) || 0) + 1);
});
return tables["pacientes"]
  .map((paciente) => {
    const alergia = alergias.get(paciente.id_paciente) || { total: 0, graves: 0 };
    const analiticas = anormales.get(paciente.id_paciente) || 0;
    const distintos = diagnosticos.get(paciente.apellidos) || 0;
    const puntos = (paciente.es_cronico ? 2 : 0) + alergia.graves * 2 + alergia.total + analiticas + (distintos >= 4 ? 2 : 0);
    return {
      "paciente": `${paciente.apellidos}, ${paciente.nombre}`,
      "edad": Math.floor((new Date("2025-09-01") - new Date(paciente.fecha_nacimiento)) / (365.25 * 86400000)),
      "cronico": paciente.es_cronico ? "sí" : "no",
      "alergias": alergia.total,
      "alergias_graves": alergia.graves,
      "analiticas_anormales": analiticas,
      "puntos_riesgo": puntos,
      "riesgo": puntos >= 6 ? "alto" : puntos >= 3 ? "medio" : "bajo",
    };
  })
  .sort((a, b) => b["puntos_riesgo"] - a["puntos_riesgo"] || a["paciente"].localeCompare(b["paciente"]))
  .slice(0, 20);
```

{{script:JavaScript · Perfil de riesgo de pacientes}}

#### JavaScript · Auditoría de cobertura de seguros

Facturado, cubierto y parte del paciente por seguro, con los pacientes de la consulta B08.

Palabras clave: `salud`, `cruces`, `seguros`, `pacientes`, `facturas`, `map`, `orden`, `foreach`, `math`

```js
const seguros = new Map(tables["seguros"].map((fila) => [fila.id_seguro, fila]));
const pacientes = new Map(tables["pacientes"].map((fila) => [fila.id_paciente, fila]));
const cuentas = new Map(queries["B08 · Pacientes por seguro"]().map((fila) => [fila.seguro, fila.pacientes]));
const facturacion = new Map();
tables["facturas"].forEach((fila) => {
  const paciente = pacientes.get(fila.id_paciente);
  if (!paciente) return;
  const clave = paciente.id_seguro;
  const grupo = facturacion.get(clave) || { total: 0, facturas: 0, pagado: 0 };
  grupo.total += fila.importe || 0;
  grupo.facturas += 1;
  if (fila.pagada) grupo.pagado += fila.importe || 0;
  facturacion.set(clave, grupo);
});
return [...seguros.values()]
  .map((seguro) => {
    const factura = facturacion.get(seguro.id_seguro) || { total: 0, facturas: 0, pagado: 0 };
    const cubierto = factura.total * ((seguro.cobertura_porcentaje || 0) / 100);
    return {
      "seguro": seguro.nombre,
      "tipo": seguro.tipo,
      "cobertura_porcentaje": seguro.cobertura_porcentaje,
      "pacientes": cuentas.get(seguro.nombre) || 0,
      "facturas": factura.facturas,
      "facturado": Math.round(factura.total),
      "cubierto": Math.round(cubierto),
      "parte_paciente": Math.round(factura.total - cubierto),
      "pagado": Math.round(factura.pagado),
      "pendiente": Math.round(factura.total - factura.pagado),
    };
  })
  .sort((a, b) => b["facturado"] - a["facturado"] || a["seguro"].localeCompare(b["seguro"]));
```

{{script:JavaScript · Auditoría de cobertura de seguros}}

### C · Consultas relacionadas

#### Usar una consulta guardada

Llama a la consulta A19 (pacientes con tres o más ingresos) y muestra su resultado.

Palabras clave: `salud`, `informes`, `map`

```js
const rows = queries["A19 · Pacientes con tres o más ingresos"]();
return rows.map((row) => ({
  "paciente": `${row.nombre} ${row.apellidos}`,
  "ingresos": row.ingresos,
}));
```

{{script:Usar una consulta guardada}}

#### Informe con forEach y resultado

Construye un informe de facturas pagadas e impagadas con forEach, añade una fila TOTAL y lo guarda en resultado.

Palabras clave: `salud`, `informes`, `facturas`, `map`, `reduce`, `foreach`, `math`

```js
const datos = tables["facturas"];
const groups = {};
datos.forEach((fila) => {
  const key = fila.pagada ? "pagada" : "impagada";
  groups[key] = groups[key] || { count: 0, amount: 0 };
  groups[key].count += 1;
  groups[key].amount += fila.importe || 0;
});
const rows = Object.entries(groups).map(([status, g]) => ({
  "estado_pago": status,
  "facturas": g.count,
  "importe": Math.round(g.amount),
  "media": Math.round(g.amount / g.count),
}));
rows.push({
  "estado_pago": "TOTAL",
  "facturas": datos.length,
  "importe": Math.round(datos.reduce((sum, fila) => sum + (fila.importe || 0), 0)),
  "media": null,
});
resultado = rows;
```

{{script:Informe con forEach y resultado}}

#### JavaScript · Directorio de pacientes con Intl

Directorio ordenado con Intl.Collator, con edad, iniciales, seguro y cobertura.

Palabras clave: `salud`, `javascript`, `pacientes`, `seguros`, `map`, `orden`, `slice`, `intl`

```js
const pacientes = tables["pacientes"];
const seguros = new Map(tables["seguros"].map((s) => [s.id_seguro, s]));
const referencia = new Date("2025-09-01");
const collator = new Intl.Collator("es", { sensitivity: "base" });
return pacientes
  .map((fila) => {
    const nacimiento = new Date(fila.fecha_nacimiento);
    let edad = referencia.getFullYear() - nacimiento.getFullYear();
    const mes = referencia.getMonth() - nacimiento.getMonth();
    if (mes < 0 || (mes === 0 && referencia.getDate() < nacimiento.getDate())) edad -= 1;
    const email = fila.email ? String(fila.email) : "";
    const at = email.indexOf("@");
    const seguro = seguros.get(fila.id_seguro);
    return {
      "paciente": `${fila.apellidos}, ${fila.nombre}`,
      "iniciales": `${fila.nombre.charAt(0)}${fila.apellidos.charAt(0)}`.toUpperCase(),
      "edad": edad,
      "ciudad": fila.ciudad,
      "seguro": seguro ? seguro.nombre : "—",
      "cobertura": seguro ? seguro.cobertura_porcentaje : null,
      "dominio_correo": at > 0 ? email.slice(at + 1) : null,
      "cronico": fila.es_cronico ? "sí" : "no",
    };
  })
  .sort((a, b) => collator.compare(a["paciente"], b["paciente"]));
```

{{script:JavaScript · Directorio de pacientes con Intl}}

#### JavaScript · Ficha por hospital

Capacidad, servicios, ocupación y facturación por hospital unidos a la consulta C22.

Palabras clave: `salud`, `javascript`, `hospitales`, `servicios`, `habitaciones`, `map`, `orden`, `foreach`, `math`

```js
const hospitales = tables["hospitales"];
const servicios = new Map();
tables["servicios"].forEach((fila) => {
  const lista = servicios.get(fila.id_hospital) || [];
  lista.push(fila.nombre);
  servicios.set(fila.id_hospital, lista);
});
const habitaciones = new Map(tables["habitaciones"].map((fila) => [fila.id_habitacion, fila]));
const totales = new Map(hospitales.map((h) => [h.nombre, { habitaciones: 0, camas: 0, ocupadas: 0, coste: 0 }]));
tables["habitaciones"].forEach((habitacion) => {
  const hospital = hospitales.find((h) => h.id_hospital === habitacion.id_hospital);
  if (!hospital) return;
  const total = totales.get(hospital.nombre);
  total.habitaciones += 1;
  total.camas += habitacion.num_camas || 0;
  if (habitacion.ocupada) total.ocupadas += 1;
});
const diasEntre = (desde, hasta) => {
  if (!desde || !hasta) return 0;
  return Math.max(0, Math.round((new Date(hasta) - new Date(desde)) / 86400000));
};
tables["ingresos"].forEach((fila) => {
  const habitacion = habitaciones.get(fila.id_habitacion);
  const hospital = habitacion ? hospitales.find((h) => h.id_hospital === habitacion.id_hospital) : null;
  if (!hospital) return;
  totales.get(hospital.nombre).coste += diasEntre(fila.fecha_ingreso, fila.fecha_alta) * (fila.coste_dia || 0);
});
const ingresos = new Map(queries["C22 · Hospitales con más ingresos"]().map((fila) => [fila.hospital, fila.ingresos]));
return hospitales
  .map((hospital) => {
    const total = totales.get(hospital.nombre);
    const cuenta = ingresos.get(hospital.nombre) || 0;
    return {
      "hospital": hospital.nombre,
      "nivel": hospital.nivel,
      "universitario": hospital.es_universitario ? "sí" : "no",
      "servicios": (servicios.get(hospital.id_hospital) || []).length,
      "habitaciones": total.habitaciones,
      "camas": total.camas,
      "ocupadas": total.ocupadas,
      "porcentaje_ocupacion": total.habitaciones ? Math.round((total.ocupadas / total.habitaciones) * 1000) / 10 : 0,
      "ingresos": cuenta,
      "facturacion": Math.round(total.coste),
      "coste_por_ingreso": cuenta ? Math.round(total.coste / cuenta) : null,
    };
  })
  .sort((a, b) => b["ingresos"] - a["ingresos"] || a["hospital"].localeCompare(b["hospital"]));
```

{{script:JavaScript · Ficha por hospital}}

#### JavaScript · Clasificación de médicos

Médicos clasificados por una puntuación con citas, intervenciones y la consulta C16.

Palabras clave: `salud`, `informes`, `medicos`, `citas`, `map`, `orden`, `slice`, `foreach`

```js
const medicos = new Map(tables["medicos"].map((fila) => [fila.id_medico, fila]));
const intervenciones = new Map(queries["C16 · Médicos con más intervenciones"]().map((fila) => [fila.medico, fila]));
const citas = new Map();
tables["citas"].forEach((fila) => {
  const grupo = citas.get(fila.id_medico) || { cuenta: 0, duracion: 0 };
  grupo.cuenta += 1;
  grupo.duracion += fila.duracion_min || 0;
  citas.set(fila.id_medico, grupo);
});
const filas = [...medicos.values()].map((medico) => {
  const agenda = citas.get(medico.id_medico) || { cuenta: 0, duracion: 0 };
  const intervencion = intervenciones.get(medico.apellidos) || { intervenciones: 0, coste_total: 0 };
  return {
    "medico": `${medico.apellidos}, ${medico.nombre}`,
    "especialidad_id": medico.id_especialidad,
    "experiencia": medico.anios_experiencia,
    "citas": agenda.cuenta,
    "duracion_media": agenda.cuenta ? Math.round((agenda.duracion / agenda.cuenta) * 10) / 10 : 0,
    "intervenciones": intervencion.intervenciones || 0,
    "coste_intervenciones": Math.round(intervencion.coste_total || 0),
    "puntuacion": agenda.cuenta * 2 + (intervencion.intervenciones || 0) * 5 + Math.round((medico.anios_experiencia || 0) / 2),
  };
});
let anterior = null;
let puesto = 0;
return filas
  .sort((a, b) => b["puntuacion"] - a["puntuacion"] || a["medico"].localeCompare(b["medico"]))
  .slice(0, 20)
  .map((fila, indice) => {
    if (fila["puntuacion"] !== anterior) {
      puesto = indice + 1;
      anterior = fila["puntuacion"];
    }
    return { "puesto": puesto, ...fila };
  });
```

{{script:JavaScript · Clasificación de médicos}}

### I · Parámetros

#### P10 · Hospitales para el parámetro

Script que sirve de origen de un parámetro: devuelve el id y el nombre de cada hospital.

Palabras clave: `salud`, `parametros`, `origen`, `script`

```js
return tables["hospitales"].map((hospital) => ({
  id_hospital: hospital.id_hospital,
  nombre: hospital.nombre,
}));
```

{{script:P10 · Hospitales para el parámetro}}

#### P09 · Ingresos con coste mínimo (script con parámetro)

Script parametrizado: el coste diario mínimo se elige en un desplegable de 200 a 900.

Palabras clave: `salud`, `parametros`, `rango`, `script`

```js
const costeMinimo = {{ 200..900 | Coste mínimo }};
return tables["ingresos"]
  .filter((ingreso) => ingreso.coste_dia >= costeMinimo)
  .map((ingreso) => ({
    id_ingreso: ingreso.id_ingreso,
    coste_dia: ingreso.coste_dia,
    es_urgente: ingreso.es_urgente,
  }));
```

{{script:P09 · Ingresos con coste mínimo (script con parámetro)}}

#### P16 · Facturas entre dos importes (script con dos parámetros)

Script parametrizado dos veces: importe mínimo y máximo, cada uno con su desplegable.

Palabras clave: `salud`, `parametros`, `varios`, `script`

```js
const importeMinimo = {{ 100..500 | Importe mínimo }};
const importeMaximo = {{ 200..1000 | Importe máximo }};
return tables["facturas"]
  .filter((factura) => factura.importe >= importeMinimo && factura.importe <= importeMaximo)
  .map((factura) => ({
    id_factura: factura.id_factura,
    importe: factura.importe,
    pagada: factura.pagada,
  }));
```

{{script:P16 · Facturas entre dos importes (script con dos parámetros)}}
