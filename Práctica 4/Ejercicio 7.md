Banda(<ins>codigoB</ins>, nombreBanda, genero_musical, año_creacion)

Integrante (<ins>DNI</ins>, nombre, apellido,dirección,email, fecha_nacimiento,codigoB(fk))

Escenario(<ins>nroEscenario</ins>, nombre_escenario, ubicación,cubierto, m2, descripción)

Recital(<ins>fecha,hora,nroEscenario</ins>, codigoB (fk))

# 1. Listar DNI, nombre, apellido,dirección y email de integrantes nacidos entre 1980 y 1990 y hayan realizado algún recital durante 2018.

*// Está bien con tener las 2 condiciones en el WHERE o necesito un IN para saber lo de los recitales en 2018 ??? (CONSULTAR)*

*// Si está bien lo de las condiciones en el WHERE necesito un DISTINCT porque puede haber tocado más de un recital en 2018*

```sql
SELECT DISTINCT i.dni, i.nombre, i.apellido, i.dirección, i.email
FROM Integrante i
INNER JOIN Recital r ON (r.códigoB = i.códigoB)
WHERE (i.fecha_nacimiento BETWEEN '1980-01-01' AND '1990-12-31') AND (r.fecha BETWEEN '2018-01-01' AND '2018-12-31')
```

# 2. Reportar nombre, género musical y año de creación de bandas que hayan realizado recitales durante 2018, pero no hayan tocado durante 2017.

```sql
(
    SELECT DISTINCT b.nombre, b.género_musical, b.año_creación
    FROM Banda b
    INNER JOIN Recital r ON (r.códigoB = b.códigoB)
    WHERE r.fecha BETWEEN '2018-01-01' AND '2018-12-31'
)
EXCEPT
(
    SELECT DISTINCT b.nombre, b.género_musical, b.año_creación
    FROM Banda b
    INNER JOIN Recital r ON (r.códigoB = b.códigoB)
    WHERE r.fecha BETWEEN '2017-01-01' AND '2017-12-31'
)
```

# 3. Listar el cronograma de recitales del dia 04/12/2018. Se deberá listar: nombre de la banda que ejecutará el recital, fecha, hora, y el nombre y ubicación del escenario correspondiente.

```sql
SELECT b.nombreBanda, r.fecha, r.hora, e.nombre_escenario, e.ubicación
FROM Recital r
INNER JOIN Banda b ON (b.códigoB = r.códigoB)
INNER JOIN Escenario e ON (e.nroEscenario = r.nroEscenario)
WHERE r.fecha = '2018-04-12'
```

# 4. Listar DNI, nombre, apellido,email de integrantes que hayan tocado en el escenario con nombre ‘Gustavo Cerati’ y en el escenario con nombre ‘Carlos Gardel’.

```sql
(
    SELECT i.dni, i.nombre, i.apellido, i.email
    FROM Integrante i
    INNER JOIN Recital r ON (r.códigoB = i.códigoB)
    INNER JOIN Escenario e ON (e.nroEscenario = r.nroEscenario)
    WHERE e.nombre_escenario = 'Gustavo Cerati'
)
INTERSECT
(
    SELECT i.dni, i.nombre, i.apellido, i.email
    FROM Integrante i
    INNER JOIN Recital r ON (r.códigoB = i.códigoB)
    INNER JOIN Escenario e ON (e.nroEscenario = r.nroEscenario)
    WHERE e.nombre_escenario = 'Carlos Gardel'
)
```

# 5. Reportar nombre, género musical y año de creación de bandas que tengan más de 8 integrantes.

```sql
SELECT b.nombreBanda, b.género_musical, b.año_creación
FROM Banda b
INNER JOIN Integrante i ON (i.códigoB = b.códigoB)
GROUP BY b.códigoB, b.nombreBanda, b.género_musical, b.año_creación
HAVING COUNT(*) > 8
```

# 6. Listar nombre de escenario, ubicación y descripción de escenarios que solo tuvieron recitales con género musical rock and roll. Ordenar por nombre de escenario

```sql
SELECT e.nombre_escenario, e.ubicación, e.escenario
FROM Escenario e
EXCEPT (
    SELECT e.nombre_escenario, e.ubicación, e.escenario
    FROM Escenario e
    INNER JOIN Recital r ON (r.nroEscenario = e.nroEscenario)
    INNER JOIN Banda b ON (b.códigoB = r.códigoB)
    WHERE (b.género_musical = 'Rock and roll')
)
ORDER BY e.nombre_escenario
```

# 7. Listar nombre, género musical y año de creación de bandas que hayan realizado recitales en escenarios cubiertos durante 2018. *// cubierto es true/false según corresponda*

```sql
SELECT b.nombreBanda, b.género_musical, b.año_creación
FROM Banda b
WHERE b.códigoB IN (
    SELECT r.códigoB
    FROM Recital r
    INNER JOIN Escenario e ON (e.nroEscenario = r.nroEscenario)
    WHERE (e.cubierto = True) AND (r.fecha BETWEEN '2018-01-01' AND '2018-12-31')
)
```

*// Otra solución sin subconsultas (necesita un DISTINCT por si la banda tocó en más de un escenario cubierto)*

```sql
SELECT DISTINCT b.nombreBanda, b.género_musical, b.año_creación
FROM Banda b
INNER JOIN Recital r ON (r.códigoB = b.códigoB)
INNER JOIN Escenario e ON (e.nroEscenario = r.nroEscenario)
WHERE (e.cubierto = True) AND (r.fecha BETWEEN '2018-01-01' AND '2018-12-31')
```

# 8. Reportar para cada escenario, nombre del escenario y cantidad de recitales durante 2018.

*// LEFT JOIN por si no hubo recitales en el escenario*

```sql
SELECT e.nombre_escenario, COUNT(*)
FROM Escenario e
LEFT JOIN Recital r ON (r.nroEscenario = e.nroEscenario)
WHERE r.fecha BETWEEN '2018-01-01' AND '2018-12-31'
```

# 9. Modificar el nombre de la banda ‘Mempis la Blusera’ a: ‘Memphis la Blusera’.

```sql
UPDATE Banda
SET nombreBanda = 'Memphis la Blusera'
WHERE nombreBanda = 'Mempis la Blusera'
```