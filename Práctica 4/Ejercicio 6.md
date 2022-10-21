Técnico (<ins>codTec</ins>, nombre, especialidad)

Repuesto (<ins>codRep</ins>, nombre, stock, precio)

RepuestoReparacion (<ins>nroReparac, codRep</ins>, cantidad, precio)

Reparación (<ins>nroReparac</ins>, codTec, precio_total, fecha)

# 1. Listar todos los repuestos, informando el nombre, stock y precio. Ordenar el resultado por precio.

```sql
SELECT nombre, stock, precio
FROM Repuesto
ORDER BY precio
```

# 2. Listar nombre, stock, precio de repuesto que participaron en reparaciones durante 2019 y además no participaron en reparaciones del técnico ‘José Gonzalez’.

```sql
SELECT r.nombre, r.stock, r.precio
FROM Repuesto r
INNER JOIN RepuestoReparación rr ON (rr.codRep = r.codRep)
INNER JOIN Reparación rep ON (rep.nroReparac = rr.nroReparac)
WHERE (rep.fecha BETWEEN '2019-01-01' AND '2019-12-31') AND r.codRep NOT IN (
    SELECT r.codRep
    FROM Repuesto r
    INNER JOIN RepuestoReparacion rr ON (rr.codRep = r.codRep)
    INNER JOIN Reparación rep ON (rep.nroReparac = rr.nroReparac)
    INNER JOIN Técnico t ON (rep.codTec = t.codTec)
    WHERE t.nombre = 'José González'
```

# 3. Listar el nombre, especialidad de técnicos que no participaron en ninguna reparación. Ordenar por nombre ascendentemente.

```sql
SELECT t.nombre, t.especialidad
FROM Técnico t
WHERE t.codTec NOT IN (
    SELECT codTec
    FROM Reparación
)
```

# 4. Listar el nombre, especialidad de técnicos solo participaron en reparaciones durante 2018.

```sql
(
    SELECT t.nombre, t.especialidad
    FROM Técnico t
    INNER JOIN Reparación r ON (r.codTec = t.codTec)
    WHERE r.fecha BETWEEN '2018-01-01' AND '2018-12-31'
)
EXCEPT
(
    SELECT t.nombre, t.especialidad
    FROM Técnico t
    INNER JOIN Reparación r ON (r.codTec = t.codTec)
    WHERE NOT (r.fecha BETWEEN '2018-01-01' AND '2018-12-31')
)

```

# 5. Listar para cada repuesto nombre, stock y cantidad de técnicos distintos que lo utilizaron. Si un repuesto no participó en alguna reparación igual debe aparecer en dicho listado.

*// No sé cómo buscar los Técnicos distintos, creo que con el DISTINCT no es suficiente (CONSULTAR)*

```sql
SELECT DISTINCT r.nombre, r.stock, COUNT(*) AS Técnicos
FROM Repuesto r
LEFT JOIN RepuestoReparación rr ON (rr.codRep = r.codRep)
LEFT JOIN Reparación rep ON (rep.nroReparac = rr.nroReparac)
GROUP BY r.nombre, r.stock
```

# 6. Listar nombre y especialidad del técnico con mayor cantidad de reparaciones realizadas y el técnico con menor cantidad de reparaciones.

```sql
(
    SELECT t.nombre, t.especialidad
    FROM Técnico t
    LEFT JOIN Reparación r ON (r.codTec = t.codTec)
    GROUP BY t.codTec, t.nombre, t.especialidad
    HAVING COUNT(*) >= ALL (
        SELECT COUNT(*)
        FROM Técnico t
        LEFT JOIN Reparación r ON (r.codTec = t.codTec)
        GROUP BY t.codTec
    )
)
UNION ALL
(
    SELECT t.nombre, t.especialidad
    FROM Técnico t
    LEFT JOIN Reparación r ON (r.codTec = t.codTec)
    GROUP BY t.codTec, t.nombre, t.especialidad
    HAVING COUNT(*) <= ALL (
        SELECT COUNT(*)
        FROM Técnico t
        LEFT JOIN Reparación r ON (r.codTec = t.codTec)
        GROUP BY t.codTec
    )
)
```

# 7. Listar nombre, stock y precio de todos los repuestos con stock mayor a 0 y que dicho repuesto no haya estado en reparaciones con precio_total superior a 10000.

```sql
SELECT r.nombre, r.stock, r.precio
FROM Repuesto r
WHERE r.stock > 0 AND r.codRep NOT IN (
    SELECT rr.codRep
    FROM RepuestoReparacion rr
    INNER JOIN Reparación rep ON (rep.nroReparac = rr.nroReparac)
    WHERE rep.precio_total > 10000
)
```

# 8. Proyectar precio, fecha y precio total de aquellas reparaciones donde se utilizó algún repuesto con precio en el momento de la reparación mayor a $1000 y menor a $5000.

*// Pongo el DISTINCT porque podría haber una reparación con más de un producto entre $1000 y $5000*

*// Dos soluciones posibles (CONSULTAR)*

```sql
SELECT DISTINCT rep.precio_total, rep.fecha
FROM Reparación rep
INNER JOIN RepuestoReparación rr ON (rr.nroReparac = rep.nroReparac)
INNER JOIN Repuesto r ON (r.codRep = rr.codRep)
WHERE (r.precio BETWEEN 1000 AND 5000)
```

```sql
SELECT rep.precio_total, rep.fecha
FROM Reparación rep
WHERE EXISTS (
    SELECT *
    FROM RepuestoReparación rr
    INNER JOIN Repuesto r ON (r.codRep = rr.codRep)
    WHERE (rr.nroReparac = rep.nroReparac) AND (r.precio BETWEEN 1000 AND 5000)
)
```

# 9. Listar nombre, stock y precio de repuestos que hayan sido utilizados en todas las reparaciones

```sql
SELECT r.nombre, r.stock, r.precio
FROM Repuesto r
WHERE NOT EXISTS (
    SELECT *
    FROM Reparación rep
    WHERE NOT EXISTS (
        SELECT *
        FROM RepuestoReparación rr
        WHERE (rr.nroReparac = rep.nroReparac) AND (rr.codRep = r.codRep)
    )
)
```

# 10. Listar fecha, técnico y precio total de aquellas reparaciones que necesitaron al menos 10 repuestos distintos.

```sql
SELECT rep.fecha, t.nombre, rep.precio_total
FROM Reparación rep
INNER JOIN RepuestoReparación rr ON (rr.nroReparac = rep.nroReparac)
GROUP BY rep.codRep, rep.fecha, t.nombre, rep.precio_total
HAVING COUNT(*) >= 10
```