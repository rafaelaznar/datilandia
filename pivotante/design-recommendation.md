Recomendación de diseño

Para análisis OLAP resulta mucho más práctico añadir un filtro de origen:

```ebnf
pivot_query =
    "PIVOT" row_axis
    "BY" col_axis
    "FROM" source
    [ "WHERE" predicate ]
    "VALUES" measure { "," measure }
    [ "HAVING" predicate ]
```

de modo que:

WHERE ⇒ filtra registros fuente.
HAVING ⇒ filtra agregados resultantes.

Ejemplos:

```sql
PIVOT
(SELECT municipio)
BY (SELECT anoacademico_id)
FROM matricula
WHERE descnivel4='DESARROLLO DE APLICACIONES MULTIPLATAFORMA'
VALUES SUM(matricula);
```

y

```sql
PIVOT
(SELECT municipio)
BY (SELECT anoacademico_id)
FROM matricula
VALUES SUM(matricula) AS alumnos
HAVING SUM(matricula) > 100;
```

Esta separación evita exactamente el error que estás obteniendo y es coherente con SQL, MDX y la mayoría de motores analíticos.
