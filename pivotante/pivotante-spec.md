# Especificación `PIVOT QUERY`

**Versión:** v0.3

## 0. Modelo de consulta

Una consulta `PIVOT QUERY` se apoya en **una única tabla de origen**. La cláusula `FROM` de la consulta aparece una sola vez y los dos ejes se declaran como selecciones sin `FROM`:

```sql
PIVOT
  (SELECT ...)          -- filas
BY
  (SELECT ...)          -- columnas
FROM  <origen>
VALUES ...
```

- `PIVOT (SELECT ...)` describe el eje de filas y contiene el filtro base (`WHERE`).
- `BY (SELECT ...)` describe el eje de columnas: qué miembros aparecen y en qué orden.
- `FROM <origen>` alimenta los dos ejes y las medidas. No se repite dentro de `PIVOT` ni de `BY`.
- `VALUES` declara una o varias medidas separadas por comas.

La consulta de referencia queda así:

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

`region` es el eje de filas y su `WHERE` filtra el origen; `customer_id` es el eje de columnas y su `WHERE` solo elige miembros. `FROM sales` se escribe una única vez.

La sintaxis imita un `SELECT`, pero no es SQL literal: los bloques `PIVOT` y `BY` son fragmentos de selección sobre el mismo origen. El motor los combina en una sola agregación y después pivota.

## 1. Gramática EBNF

Notación: `[ ]` opcional, `{ }` repetición, `|` alternativa, `( )` agrupación. Las palabras clave no distinguen mayúsculas.

```ebnf
(* ============ Consulta ============ *)
pivot_query   = "PIVOT" row_axis
                "BY" col_axis
                "FROM" source
                "VALUES" measure { "," measure }
                [ "HAVING" predicate ]
                [ "TOTALS" totals_spec ]
                [ "FILL" fill_spec ]
                [ "MEASURES" "ON" ( "ROWS" | "COLUMNS" ) ]
                [ ";" ] ;

source        = table_ref | "(" select_stmt ")" [ "AS" ident ] ;

(* ============ Ejes ============ *)
(* Cada eje es un SELECT sin FROM: el FROM es único y va después de BY. *)
row_axis      = "(" "SELECT" axis_dim { "," axis_dim }
                [ "WHERE" predicate ]              (* filtro base *)
                [ "ORDER" "BY" order_item { "," order_item } ] ")" ;

col_axis      = "(" "SELECT" axis_dim { "," axis_dim }
                [ "WHERE" predicate ]              (* solo elige miembros *)
                [ "ORDER" "BY" order_item { "," order_item } ] ")" ;

axis_dim      = expr [ "AS" ident ] { axis_option } ;
axis_option   = null_option | limit_option ;

(* ---- NULLs en dimensiones ---- *)
null_option   = "NULLS" ( "AS" string             (* el NULL es un miembro con etiqueta *)
                        | "HIDE"                  (* oculta el miembro, sigue en totales *)
                        | "EXCLUDE" ) ;           (* elimina las filas NULL del origen *)

(* ---- Límite de miembros ---- *)
limit_option  = ( "TOP" | "BOTTOM" ) integer
                [ "BY" agg_expr ]                 (* por defecto, la primera medida *)
                [ "WITHIN" "PARENT" ]             (* ranking dentro del nivel superior *)
                [ others_option ] ;
others_option = "OTHERS" ( "AS" string | "HIDE" ) ;

(* ============ Medidas ============ *)
measure       = agg_expr [ "SHOW" "AS" show_mode ]
                [ "AS" ident ] [ "FILL" fill_value ] ;
agg_expr      = agg_fn "(" [ "DISTINCT" ] ( "*" | expr ) ")" ;
agg_fn        = "SUM" | "COUNT" | "AVG" | "MIN" | "MAX" | "MEDIAN" | "STDDEV" ;
show_mode     = "PCT_OF" ( "ROW" | "COLUMN" | "GRAND"
                         | "PARENT_ROW" | "PARENT_COLUMN" ) ;

(* ============ Orden ============ *)
order_item    = ( expr | ident ) [ "ASC" | "DESC" ] [ "NULLS" ( "FIRST" | "LAST" ) ] ;

(* ============ Celdas vacías / nulas ============ *)
fill_spec     = fill_value | fill_item { "," fill_item } ;
fill_item     = ( "EMPTY" | "NULLVALUE" ) fill_value ;
fill_value    = number | string | "NULL" ;

(* ============ Totales configurables ============ *)
totals_spec   = "NONE"
              | "ALL" { total_opt }               (* subtotales en todos los niveles
                                                     menos el último + GRAND, en ambos ejes *)
              | totals_axis { totals_axis } [ hidden_option ] ;
totals_axis   = ( "ROWS" | "COLUMNS" ) total_item { total_item } ;
total_item    = ( "SUBTOTAL" "BY" ident | "GRAND" ) { total_opt } ;
total_opt     = "POSITION" ( "BEFORE" | "AFTER" )
              | "AS" string                       (* etiqueta; admite {value} {dim} *)
              | "COMPUTE" ( "SOURCE" | "CELLS" )
              | "ONLY" ident { "," ident } ;      (* medidas que llevan total *)
hidden_option = ( "INCLUDE" | "EXCLUDE" ) "HIDDEN" ;
```

### Restricciones semánticas (no expresables en la gramática)

- `PIVOT` y `BY` exigen al menos una dimensión cada uno.
- No se permite `FROM` dentro de `PIVOT (...)` ni de `BY (...)`: el origen es único y va después de `BY`.
- `SUBTOTAL BY d` exige que `d` no sea la última dimensión de su eje.
- `WITHIN PARENT` exige que la dimensión no sea la primera de su eje.
- `COMPUTE CELLS` (sumar las celdas visibles) solo es válido para `SUM`, `COUNT`, `MIN` y `MAX`. Con `AVG`, `MEDIAN`, `STDDEV`, `DISTINCT` o `SHOW AS` es un error de validación.
- `ONLY` debe referirse a alias de medidas existentes.

## 2. Reglas de comportamiento

### Un solo `FROM`, dos ejes

| Bloque | Define | `WHERE` | `ORDER BY` |
|---|---|---|---|
| `PIVOT (...)` | Dimensiones de fila | **Filtro base**: descarta filas del origen y afecta a valores, totales y miembros de ambos ejes | Orden de las filas |
| `BY (...)` | Dimensiones de columna | **Selección de miembros**: no altera los valores; los no elegidos quedan ocultos | Orden de las columnas |

- El `WHERE` del eje de filas es el sustituto del antiguo `WHERE` global. Se aplica antes de agregar y también limita qué miembros de columna existen; el `WHERE` de `BY` añade un filtro de miembros encima.
- El `WHERE` del eje de columnas solo decide qué miembros se pintan. Los miembros no elegidos no se renderizan y se comportan como ocultos: con `INCLUDE HIDDEN` (por defecto) siguen sumando en los totales; con `EXCLUDE HIDDEN` no.
- Para filtrar los valores por una condición de columna hay que ponerla en el `WHERE` del eje de filas o en la subconsulta del `FROM`.
- Los dos `WHERE` son predicados sobre el origen y se evalúan antes de agregar; no admiten alias de medidas. Para filtrar por una medida agregada se usa `HAVING`.
- `NULLS EXCLUDE` es la excepción: actúa siempre sobre el origen, aparezca en el eje que aparezca.
- Cada eje lleva su propio `ORDER BY`; no existen el `ORDER BY` global ni `PIVOT ORDER BY`. Sin `ORDER BY`, un eje se ordena de forma ascendente por el valor de cada dimensión (de izquierda a derecha), con `NULLS LAST`; si hay `TOP`/`BOTTOM`, manda el orden del ranking y `OTHERS` queda al final.
- `ORDER BY` se evalúa después de agregar, por lo que admite medidas (`SUM(revenue) DESC`).

### NULLs en dimensiones

| Opción | Efecto sobre miembros | Efecto sobre totales |
|---|---|---|
| (por defecto) | Aparece un miembro `(null)` | Incluido |
| `NULLS AS 'x'` | Miembro con etiqueta `x` | Incluido |
| `NULLS HIDE` | Miembro oculto | Incluido |
| `NULLS EXCLUDE` | No existe | Excluido (elimina las filas del origen) |

- Las filas de total se identifican con `GROUPING()`, nunca con `IS NULL`, así que un miembro `NULL` real no se confunde con un total.
- El orden por defecto es `NULLS LAST`, y se cambia en `ORDER BY`.
- La etiqueta se aplica **al renderizar**. Internamente el miembro nulo no se mezcla con un valor real que coincida con la etiqueta.
- Un miembro `NULL` participa en el ranking de `TOP`/`BOTTOM` como cualquier otro, salvo que esté excluido.
- `NULLS EXCLUDE` elimina las filas del origen común, aunque se declare en el eje de columnas.

### Celdas vacías y nulas

- **Vacía**: ninguna fila de origen cae en esa intersección.
- **Nula**: hay filas, pero el agregado da `NULL` (por ejemplo `SUM` de valores todos nulos).
- Prioridad de `FILL`: el de la medida, luego el global, luego `NULL`.
- Por defecto, `COUNT` rellena las celdas vacías con `0`.
- `FILL` también se aplica a celdas de totales que no tengan filas.

### Límite de miembros

- `TOP n BY agg` conserva los `n` mejores miembros. Los demás se **ocultan** salvo que haya `OTHERS`.
- `TOP`/`BOTTOM` fijan además el orden por defecto del eje: los miembros conservados en orden de ranking y `OTHERS` al final. Un `ORDER BY` explícito lo puede cambiar.
- `OTHERS AS 'x'` agrupa el resto en un miembro sintético, que siempre queda al final de su nivel.
- `OTHERS HIDE` lo oculta explícitamente.
- Los empates se desempatan por el valor del miembro (determinista).
- Con `INCLUDE HIDDEN` (por defecto), los totales incluyen los miembros ocultos. Con `EXCLUDE HIDDEN`, solo suman lo visible.

### Totales: valores por defecto

`POSITION AFTER`, `COMPUTE SOURCE`, `INCLUDE HIDDEN`, etiqueta `'Subtotal'` o `'Total general'`. Con `TOTALS NONE` o sin cláusula no se generan totales. La etiqueta de un total se declara con `AS`: `GRAND AS 'Total'`, `SUBTOTAL BY region AS 'Σ {value}'`.

### Orden de evaluación

1. `WHERE` del eje de filas (filtro base) y `NULLS EXCLUDE` de ambos ejes.
2. Selección de miembros: `WHERE` del eje de columnas, `NULLS HIDE`, `TOP`/`BOTTOM` y `OTHERS` de ambos ejes.
3. Agregación con `GROUPING SETS` más un `COUNT(*)` interno para detectar celdas vacías.
4. `HAVING`. Se evalúa sobre el valor del miembro en el total del otro eje.
5. `SHOW AS`, `FILL` y etiquetas (incluidas las de totales).
6. `ORDER BY` de cada eje, y por último el pivotado.

## 3. Ejemplos

### Ejemplo 1: NULLs en dimensiones

```sql
PIVOT
  (SELECT region NULLS AS '(sin región)',
          city   NULLS AS '(sin ciudad)'
   ORDER BY region ASC NULLS LAST, city ASC NULLS LAST)
BY
  (SELECT fiscal_year)
FROM     pasted
VALUES   SUM(revenue) AS ingresos
TOTALS   ROWS SUBTOTAL BY region GRAND;
```

| region | city | 2023 | 2024 |
|---|---|---:|---:|
| Europe | Madrid | 100 | 130 |
| Europe | (sin ciudad) | 20 | 20 |
| **Europe Subtotal** | | **120** | **150** |
| (sin región) | (sin ciudad) | 15 | 10 |
| **(sin región) Subtotal** | | **15** | **10** |
| **Total general** | | **135** | **160** |

Observa que `(sin región)` es un miembro normal, con su subtotal y su orden al final.

### Ejemplo 2: celdas vacías frente a nulas

Datos: Valencia no tiene ninguna fila en 2024 (vacía). Lima tiene filas en 2024, pero con `revenue` nulo (nula).

```sql
PIVOT
  (SELECT city)
BY
  (SELECT fiscal_year)
FROM   pasted
VALUES SUM(revenue) AS ingresos
FILL   EMPTY '–', NULLVALUE 0;
```

| city | 2023 | 2024 |
|---|---|---:|
| Lima | 80 | 0 |
| Valencia | 120 | – |

Sin la cláusula `FILL`, las dos celdas saldrían como `NULL` y no se podrían distinguir. Para un relleno único se puede usar el atajo por medida: `SUM(revenue) AS ingresos FILL 0`.

### Ejemplo 3: `TOP n` con `OTHERS`

```sql
PIVOT
  (SELECT city TOP 2 BY SUM(revenue) OTHERS AS 'Otras ciudades')
BY
  (SELECT fiscal_year)
FROM     pasted
VALUES   SUM(revenue) AS ingresos
TOTALS   ROWS GRAND COLUMNS GRAND;
```

| city | 2023 | 2024 | Total general |
|---|---:|---:|---:|
| Valencia | 120 | 150 | 270 |
| Madrid | 100 | 130 | 230 |
| Otras ciudades | 140 | 160 | 300 |
| **Total general** | **360** | **440** | **800** |

Otras ciudades agrupa Lima y Bogotá (80+60 y 90+70).

### Ejemplo 4: límite jerárquico `WITHIN PARENT`

```sql
PIVOT
  (SELECT region,
          city TOP 1 BY SUM(revenue) WITHIN PARENT OTHERS AS 'Resto')
BY
  (SELECT fiscal_year)
FROM     pasted
VALUES   SUM(revenue)
TOTALS   ROWS SUBTOTAL BY region;
```

| region | city | 2023 | 2024 |
|---|---|---:|---:|
| America | Lima | 80 | 90 |
| America | Resto | 60 | 70 |
| **America Subtotal** | | **140** | **160** |
| Europe | Valencia | 120 | 150 |
| Europe | Resto | 100 | 130 |
| **Europe Subtotal** | | **220** | **280** |

### Ejemplo 5: totales configurables

```sql
PIVOT
  (SELECT region, city)
BY
  (SELECT fiscal_year)
FROM     pasted
VALUES   SUM(revenue) AS ingresos
TOTALS
  ROWS    GRAND POSITION BEFORE AS 'TOTAL'
          SUBTOTAL BY region POSITION BEFORE AS 'Σ {value}'
  COLUMNS GRAND AS 'Total'
  INCLUDE HIDDEN;
```

| region | city | 2023 | 2024 | Total |
|---|---|---:|---:|---:|
| **TOTAL** | | **360** | **440** | **800** |
| **Σ America** | | **140** | **160** | **300** |
| America | Bogotá | 60 | 70 | 130 |
| America | Lima | 80 | 90 | 170 |
| **Σ Europe** | | **220** | **280** | **500** |
| Europe | Madrid | 100 | 130 | 230 |
| Europe | Valencia | 120 | 150 | 270 |

`AS` sustituye a la antigua `LABEL` para las etiquetas de totales. Con varias medidas se puede limitar qué medidas llevan total:

```sql
VALUES SUM(revenue) AS ingresos, SUM(revenue) SHOW AS PCT_OF COLUMN AS pct
TOTALS ROWS GRAND ONLY ingresos
```

### Ejemplo 6: todo combinado

```sql
PIVOT
  (SELECT region NULLS AS '(sin región)'
   WHERE  orders > 8
   ORDER BY region ASC NULLS LAST)
BY
  (SELECT customer_id NULLS AS '(sin cliente)'
          TOP 2 BY SUM(revenue) OTHERS AS 'Resto')
FROM     pasted
VALUES   SUM(revenue) AS ingresos FILL 0
TOTALS   ROWS GRAND COLUMNS GRAND
FILL     EMPTY '–';
```

| region | C1 | C2 | Resto | Total general |
|---|---:|---:|---:|---:|
| America | 150 | 90 | 60 | 300 |
| Europe | 200 | 180 | 120 | 500 |
| **Total general** | **350** | **270** | **180** | **800** |

`(sin cliente)` es un miembro más del ranking. Si no está entre los dos primeros, cae en `Resto`. El `WHERE orders > 8` está en el eje de filas porque filtra el origen: afecta a los valores y a los totales. Un `WHERE` dentro de `BY` solo serviría para elegir miembros de columna.

## 4. Traducción a SQL estándar (Ejemplo 3)

```sql
WITH base AS (
  SELECT * FROM pasted                     -- único FROM; aquí iría el WHERE de PIVOT
),
rank_city AS (
  SELECT city,
         ROW_NUMBER() OVER (ORDER BY SUM(revenue) DESC, city) AS rn
  FROM base GROUP BY city
),
mapped AS (
  SELECT CASE WHEN r.rn <= 2 THEN b.city END AS city_m,
         (r.rn > 2)                          AS is_others,
         b.fiscal_year, b.revenue
  FROM base b
  JOIN rank_city r ON b.city IS NOT DISTINCT FROM r.city  -- respeta NULLs
)
SELECT city_m, is_others, fiscal_year,
       SUM(revenue)  AS v,
       COUNT(*)      AS n,                 -- n = 0 ⇒ celda vacía
       GROUPING(city_m)      AS g_city,    -- distingue totales de NULL real
       GROUPING(fiscal_year) AS g_year
FROM mapped
GROUP BY GROUPING SETS (
  (city_m, is_others, fiscal_year),
  (city_m, is_others),                     -- columna Total general
  (fiscal_year),                           -- fila Total general
  ()
);
```

El motor aplica después las etiquetas, el `FILL` según `n` y `v IS NULL`, el orden definido en cada eje (con `OTHERS` al final) y el pivotado de `fiscal_year`. Los bloques `PIVOT` y `BY` no repiten `FROM`: ambos se evalúan sobre `base`.

## 5. Equivalencia con la sintaxis anterior

| Antes | Ahora |
|---|---|
| `SELECT r FROM t WHERE p PIVOT c` | `PIVOT (SELECT r WHERE p) BY (SELECT c) FROM t` |
| `PIVOT (SELECT c FROM t WHERE p ORDER BY o)` | `BY (SELECT c WHERE p ORDER BY o)` |
| `LABEL 'x'` en un total | `AS 'x'` |
| `ORDER BY ...` global | `ORDER BY ...` dentro de `PIVOT (...)` |
| `PIVOT ORDER BY ...` | `ORDER BY ...` dentro de `BY (...)` |
