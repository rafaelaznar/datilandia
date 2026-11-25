# Ejemplos de `PIVOT QUERY` v0.3 sobre una tabla de referencia

> Esta versión usa la estructura `PIVOT (...) BY (...) FROM ...`: el `FROM` de la consulta aparece una sola vez y cada eje declara su propio `WHERE` y `ORDER BY`. En los totales, `AS` sustituye a la antigua `LABEL`.

## 1. Tabla de referencia: `sales`

17 filas con casos pensados para ejercitar la gramática: dimensiones `NULL` (filas 13 a 15), un `revenue` nulo (fila 17) y pedidos bajos que un `WHERE orders > 8` descarta (filas 5 y 16).

| id | region | city | fiscal_year | customer_id | orders | revenue |
|---:|---|---|---:|---|---:|---:|
| 1 | America | Lima | 2023 | C1 | 12 | 50 |
| 2 | America | Lima | 2023 | C2 | 9 | 30 |
| 3 | America | Lima | 2024 | C1 | 10 | 40 |
| 4 | America | Lima | 2024 | C2 | 11 | 50 |
| 5 | America | Bogotá | 2023 | C1 | 6 | 60 |
| 6 | America | Bogotá | 2024 | C3 | 14 | 70 |
| 7 | Europe | Valencia | 2023 | C1 | 15 | 70 |
| 8 | Europe | Valencia | 2023 | C2 | 10 | 50 |
| 9 | Europe | Valencia | 2024 | C1 | 12 | 90 |
| 10 | Europe | Valencia | 2024 | C2 | 9 | 60 |
| 11 | Europe | Madrid | 2023 | C3 | 20 | 100 |
| 12 | Europe | Madrid | 2024 | C3 | 13 | 130 |
| 13 | Europe | NULL | 2023 | C1 | 9 | 20 |
| 14 | NULL | NULL | 2023 | NULL | 10 | 15 |
| 15 | NULL | NULL | 2024 | NULL | 12 | 10 |
| 16 | Europe | Madrid | 2024 | C1 | 4 | 25 |
| 17 | America | Bogotá | 2024 | C2 | 10 | NULL |

Control: `SUM(revenue)` total = **870** (America 300, Europe 545, sin región 25). Con `orders > 8` el total es **785**.

---

## 2. Ejemplos

### Ejemplo 1: básico con total general y NULL etiquetado

```sql
PIVOT
  (SELECT region NULLS AS '(sin región)'
   ORDER BY region ASC NULLS LAST)
BY
  (SELECT fiscal_year)
FROM sales
VALUES SUM(revenue) AS ingresos
TOTALS ROWS GRAND COLUMNS GRAND;
```

| region | 2023 | 2024 | Total general |
|---|---:|---:|---:|
| America | 140 | 160 | 300 |
| Europe | 240 | 305 | 545 |
| (sin región) | 15 | 10 | 25 |
| **Total general** | **395** | **475** | **870** |

### Ejemplo 2: subtotales por región, NULLs en ambos niveles y celdas vacías

```sql
PIVOT
  (SELECT region NULLS AS '(sin región)',
          city   NULLS AS '(sin ciudad)'
   WHERE  orders > 8
   ORDER BY region ASC NULLS LAST, city DESC NULLS LAST)
BY
  (SELECT fiscal_year)
FROM sales
VALUES SUM(revenue) AS ingresos
TOTALS ROWS SUBTOTAL BY region GRAND
       COLUMNS GRAND
FILL   EMPTY '–';
```

| region | city | 2023 | 2024 | Total general |
|---|---|---:|---:|---:|
| America | Lima | 80 | 90 | 170 |
| America | Bogotá | – | 70 | 70 |
| **America Subtotal** | | **80** | **160** | **240** |
| Europe | Valencia | 120 | 150 | 270 |
| Europe | Madrid | 100 | 130 | 230 |
| Europe | (sin ciudad) | 20 | – | 20 |
| **Europe Subtotal** | | **240** | **280** | **520** |
| (sin región) | (sin ciudad) | 15 | 10 | 25 |
| **(sin región) Subtotal** | | **15** | **10** | **25** |
| **Total general** | | **335** | **450** | **785** |

Bogotá 2023 está vacía porque su única fila (id 5) la descarta el `WHERE`. Bogotá 2024 vale 70 porque `SUM` ignora el `NULL` de la fila 17.

### Ejemplo 3: columnas jerárquicas con subtotal por año

```sql
PIVOT
  (SELECT region
   WHERE  orders > 8
     AND  customer_id IN ('C1','C2')
     AND  region IS NOT NULL)
BY
  (SELECT fiscal_year, customer_id)
FROM sales
VALUES SUM(revenue) AS ingresos
TOTALS ROWS GRAND
       COLUMNS SUBTOTAL BY fiscal_year GRAND;
```

| region | 2023 · C1 | 2023 · C2 | **2023 Subtotal** | 2024 · C1 | 2024 · C2 | **2024 Subtotal** | **Total general** |
|---|---:|---:|---:|---:|---:|---:|---:|
| America | 50 | 30 | **80** | 40 | 50 | **90** | **170** |
| Europe | 90 | 50 | **140** | 90 | 60 | **150** | **290** |
| **Total general** | **140** | **80** | **220** | **130** | **110** | **240** | **460** |

### Ejemplo 4: `TOP n` con `OTHERS`

```sql
PIVOT
  (SELECT city TOP 2 BY SUM(revenue) OTHERS AS 'Otras ciudades')
BY
  (SELECT fiscal_year)
FROM sales
VALUES SUM(revenue) AS ingresos
TOTALS ROWS GRAND COLUMNS GRAND;
```

Ranking por ingresos: Valencia 270, Madrid 255, Lima 170, Bogotá 130 y `NULL` 45. El miembro `NULL` (filas 13, 14 y 15 fusionadas) también participa en el ranking.

| city | 2023 | 2024 | Total general |
|---|---:|---:|---:|
| Valencia | 120 | 150 | 270 |
| Madrid | 100 | 155 | 255 |
| Otras ciudades | 175 | 170 | 345 |
| **Total general** | **395** | **475** | **870** |

*Otras ciudades* = Lima + Bogotá + `NULL`.

### Ejemplo 5: límite jerárquico con `WITHIN PARENT`

```sql
PIVOT
  (SELECT region,
          city TOP 1 BY SUM(revenue) WITHIN PARENT OTHERS AS 'Resto'
   WHERE  region IS NOT NULL)
BY
  (SELECT fiscal_year)
FROM sales
VALUES SUM(revenue) AS ingresos
TOTALS ROWS SUBTOTAL BY region GRAND
       COLUMNS GRAND;
```

| region | city | 2023 | 2024 | Total general |
|---|---|---:|---:|---:|
| America | Lima | 80 | 90 | 170 |
| America | Resto | 60 | 70 | 130 |
| **America Subtotal** | | **140** | **160** | **300** |
| Europe | Valencia | 120 | 150 | 270 |
| Europe | Resto | 120 | 155 | 275 |
| **Europe Subtotal** | | **240** | **305** | **545** |
| **Total general** | | **380** | **465** | **845** |

El ranking se hace dentro de cada región. En Europe, *Resto* = Madrid + la ciudad `NULL`.

### Ejemplo 6: `NULLS HIDE` frente a `NULLS EXCLUDE`

```sql
-- (a) oculta el miembro, pero sigue en los totales
PIVOT
  (SELECT region NULLS HIDE)
BY
  (SELECT fiscal_year)
FROM sales
VALUES SUM(revenue) AS ingresos
TOTALS ROWS GRAND COLUMNS GRAND;

-- (b) elimina las filas de origen con region NULL
PIVOT
  (SELECT region NULLS EXCLUDE)
BY
  (SELECT fiscal_year)
FROM sales
VALUES SUM(revenue) AS ingresos
TOTALS ROWS GRAND COLUMNS GRAND;
```

**(a) `HIDE`**

| region | 2023 | 2024 | Total general |
|---|---:|---:|---:|
| America | 140 | 160 | 300 |
| Europe | 240 | 305 | 545 |
| **Total general** | **395** | **475** | **870** |

**(b) `EXCLUDE`**

| region | 2023 | 2024 | Total general |
|---|---:|---:|---:|
| America | 140 | 160 | 300 |
| Europe | 240 | 305 | 545 |
| **Total general** | **380** | **465** | **845** |

En (a) las filas visibles no suman el total (hay 25 ocultos). En (b) sí suman, porque esas filas ya no existen.

### Ejemplo 7: varias medidas con `COUNT(DISTINCT)` y `MEASURES ON COLUMNS`

```sql
PIVOT
  (SELECT region NULLS EXCLUDE)
BY
  (SELECT fiscal_year)
FROM sales
VALUES SUM(revenue)                AS ingresos,
       COUNT(DISTINCT customer_id) AS clientes
TOTALS ROWS GRAND COLUMNS GRAND
MEASURES ON COLUMNS;
```

| region | 2023 ingresos | 2023 clientes | 2024 ingresos | 2024 clientes | Total ingresos | Total clientes |
|---|---:|---:|---:|---:|---:|---:|
| America | 140 | 2 | 160 | 3 | 300 | 3 |
| Europe | 240 | 3 | 305 | 3 | 545 | 3 |
| **Total general** | **380** | **3** | **465** | **3** | **845** | **3** |

America tiene 3 clientes distintos en total, no 2 + 3 = 5. Esto sale de calcular el total desde el origen (`COMPUTE SOURCE`).

### Ejemplo 8: porcentajes con `SHOW AS`, `ONLY` y `MEASURES ON ROWS`

```sql
PIVOT
  (SELECT region NULLS EXCLUDE)
BY
  (SELECT fiscal_year)
FROM sales
VALUES SUM(revenue) AS ingresos,
       SUM(revenue) SHOW AS PCT_OF ROW AS pct_fila
TOTALS ROWS GRAND
       COLUMNS GRAND ONLY ingresos
MEASURES ON ROWS;
```

| region | medida | 2023 | 2024 | Total general |
|---|---|---:|---:|---:|
| America | ingresos | 140 | 160 | 300 |
| America | pct_fila | 46,7 % | 53,3 % | |
| Europe | ingresos | 240 | 305 | 545 |
| Europe | pct_fila | 44,0 % | 56,0 % | |
| **Total general** | ingresos | **380** | **465** | **845** |
| **Total general** | pct_fila | **45,0 %** | **55,0 %** | |

`ONLY ingresos` hace que la columna de total solo exista para `ingresos`. El porcentaje de la fila de total general se calcula sobre 845.

### Ejemplo 9: `HAVING`, orden por medida y `EXCLUDE HIDDEN`

```sql
PIVOT
  (SELECT city
   WHERE  orders > 8
   ORDER BY SUM(revenue) DESC)
BY
  (SELECT fiscal_year
   ORDER BY fiscal_year DESC)
FROM sales
VALUES SUM(revenue) AS ingresos
HAVING SUM(revenue) > 100
TOTALS ROWS GRAND COLUMNS GRAND EXCLUDE HIDDEN;
```

| city | 2024 | 2023 | Total general |
|---|---:|---:|---:|
| Valencia | 150 | 120 | 270 |
| Madrid | 130 | 100 | 230 |
| Lima | 90 | 80 | 170 |
| **Total general** | **370** | **300** | **670** |

`HAVING` oculta Bogotá (70) y la ciudad `NULL` (45). Con `EXCLUDE HIDDEN` el total general suma solo lo visible (670). Con `INCLUDE HIDDEN`, que es el valor por defecto, serían 450, 335 y 785. El `ORDER BY` del eje de filas ordena por la medida; el del eje de columnas invierte los años. Ya no hay un `PIVOT ORDER BY` aparte.

### Ejemplo 10: miembros de columna con subselect

```sql
PIVOT
  (SELECT region
   WHERE  region IS NOT NULL)
BY
  (SELECT customer_id
   WHERE  orders > 12
   ORDER BY customer_id DESC)
FROM sales
VALUES SUM(revenue) AS ingresos
TOTALS ROWS GRAND COLUMNS GRAND EXCLUDE HIDDEN;
```

Este es el ejemplo canónico de la nueva estructura: `FROM sales` aparece una sola vez. El `WHERE` del eje de filas filtra el origen; el del eje de columnas solo elige los miembros `C3` y `C1`. Los clientes no elegidos quedan ocultos y `EXCLUDE HIDDEN` los saca de los totales.

| region | C3 | C1 | Total general |
|---|---:|---:|---:|
| America | 70 | 150 | 220 |
| Europe | 230 | 205 | 435 |
| **Total general** | **300** | **355** | **655** |

### Ejemplo 11: `FILL` para celdas vacías y nulas

```sql
PIVOT
  (SELECT city
   WHERE  region = 'America'
   ORDER BY city DESC)
BY
  (SELECT customer_id)
FROM sales
VALUES SUM(revenue) AS ingresos
FILL   EMPTY '–', NULLVALUE 'n/d';
```

| city | C1 | C2 | C3 |
|---|---:|---:|---:|
| Lima | 90 | 80 | – |
| Bogotá | 60 | n/d | 70 |

Lima/C3 no tiene filas, así que es una celda **vacía** (`–`). Bogotá/C2 tiene la fila 17, pero con `revenue` nulo, así que es una celda **nula** (`n/d`).

### Ejemplo 12: `AVG` con totales calculados desde el origen

```sql
PIVOT
  (SELECT region NULLS EXCLUDE)
BY
  (SELECT fiscal_year)
FROM sales
VALUES AVG(revenue) AS media
TOTALS ROWS GRAND COLUMNS GRAND;
```

| region | 2023 | 2024 | Total general |
|---|---:|---:|---:|
| America | 46,67 | 53,33 | 50,00 |
| Europe | 60,00 | 76,25 | 68,13 |
| **Total general** | **54,29** | **66,43** | **60,36** |

Cada total es el promedio de las filas de origen: 380/7 = 54,29 y 465/7 = 66,43. Si se promediaran las celdas, se obtendría 53,33 y 64,79, que es incorrecto porque los grupos tienen distinto número de filas con valor.

### Ejemplo 13: error de validación (`COMPUTE CELLS` con `AVG`)

```sql
PIVOT
  (SELECT region NULLS EXCLUDE)
BY
  (SELECT fiscal_year)
FROM sales
VALUES AVG(revenue) AS media
TOTALS ROWS GRAND COMPUTE CELLS;
```

```
ERROR: COMPUTE CELLS no es válido para AVG (media).
       Solo se permite con SUM, COUNT, MIN, MAX.
```

La versión válida es `SUM(revenue) ... TOTALS ROWS GRAND COMPUTE CELLS`. Con un `SUM` que no tenga miembros ocultos, da el mismo resultado que `COMPUTE SOURCE`.

### Ejemplo 14: etiquetas de totales con `AS`

```sql
PIVOT
  (SELECT region NULLS AS '(sin región)')
BY
  (SELECT fiscal_year)
FROM sales
VALUES SUM(revenue) AS ingresos
TOTALS ROWS GRAND POSITION BEFORE AS 'TOTAL'
       COLUMNS GRAND AS 'Total';
```

| region | 2023 | 2024 | Total |
|---|---:|---:|---:|
| **TOTAL** | **395** | **475** | **870** |
| America | 140 | 160 | 300 |
| Europe | 240 | 305 | 545 |
| (sin región) | 15 | 10 | 25 |

`AS` es la forma de etiquetar un total; ya no se usa `LABEL`. `POSITION BEFORE` coloca la fila de total al principio.

---

## 3. Cobertura de la gramática

| Elemento de la gramática | Ejemplos |
|---|---|
| `PIVOT (SELECT ...)` (eje de filas) | todos |
| `BY (SELECT ...)` (eje de columnas) | todos |
| `WHERE` del eje de filas (filtro base) | 2, 3, 5, 9, 10, 11 |
| `WHERE` del eje de columnas (selección de miembros) | 10 |
| `ORDER BY` en el eje de filas | 1, 2, 9, 11 |
| `ORDER BY` en el eje de columnas | 9, 10 |
| `NULLS AS / HIDE / EXCLUDE` | 1, 2, 6, 7, 8, 12, 14 |
| `NULLS FIRST/LAST` en `order_item` | 1, 2 |
| `TOP ... BY ... OTHERS` | 4 |
| `WITHIN PARENT` | 5 |
| `SUBTOTAL BY`, `GRAND` | 2, 3, 5, 14 |
| `COLUMNS SUBTOTAL BY` (columnas jerárquicas) | 3 |
| `POSITION`, `AS` (etiqueta de total), `ONLY` | 8 (`ONLY`), 14 (`AS` y `POSITION`) |
| `INCLUDE / EXCLUDE HIDDEN` | 9, 10 |
| `COMPUTE SOURCE / CELLS` | 7, 12, 13 |
| `HAVING`, `ORDER BY` por medida | 9 |
| `FILL EMPTY / NULLVALUE` | 2, 11 |
| `SHOW AS PCT_OF` | 8 |
| `COUNT(DISTINCT)` en totales | 7 |
| `MEASURES ON ROWS / COLUMNS` | 7, 8 |
| Restricción semántica violada | 13 |

La especificación, §3, ejemplo 5, muestra además `AS` en un `SUBTOTAL BY`.
