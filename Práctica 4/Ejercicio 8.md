Equipo(<ins>codigoE</ins>, nombreE, descripcionE)

Integrante (<ins>DNI</ins>, nombre, apellido,ciudad,email, telefono,codigoE(fk))

Laguna(<ins>nroLaguna</ins>, nombreL, ubicación,extension, descripción)

TorneoPesca(<ins>codTorneo</ins>, fecha,hora,nroLaguna(fk), descripcion)

Inscripcion(<ins>codTorneo,codigoE</ins>,asistio, gano) *// asistió y ganó son true/false según corresponda*

# 1. Listar DNI, nombre, apellido y email de integrantes que sean de la ciudad ‘La Plata’ y estén inscriptos en torneos a disputarse durante 2019.

```sql
SELECT DISTINCT i.dni, i.nombre, i.apellido, i.email
FROM Integrante i
INNER JOIN Inscripcion ins ON (ins.códigoE = i.códigoE)
INNER JOIN TorneoPesca tp ON (tp.codTorneo = ins.codTorneo)
WHERE (i.ciudad = 'La Plata') AND (tp.fecha BETWEEN '2019-01-01' AND '2019-12-31')
```

# 2. Reportar nombre y descripción de equipos que solo se hayan inscripto en torneos de 2018.

*// Se podría hacer también con un EXCEPT o dos NOT IN (CONSULTAR)*

```sql
SELECT e.nombreE, e.descripción
FROM Equipo e
WHERE NOT EXISTS (
    SELECT *
    FROM Inscripción ins
    INNER JOIN TorneoPesca tp ON (tp.codTorneo = ins.codTorneo)
    WHERE (ins.códigoE = e.códigoE) AND NOT (tp.fecha BETWEEN '2018-01-01' AND '2018-12-31')
)
```

# 3. Listar DNI, nombre, apellido,email y ciudad de integrantes que asistieron a torneos en la laguna con nombre ‘La Salada, Coronel Granada’ y su equipo no tenga inscripciones a torneos a disputarse en 2019.

```sql
SELECT i.dni, i.nombre, i.apellido, i.email, i.ciudad
FROM Integrante i
INNER JOIN Inscripción ins ON (ins.códigoE = i.códigoE)
INNER JOIN TorneoPesca tp ON (tp.codTorneo = ins.codTorneo)
INNER JOIN Laguna l ON (l.nroLaguna = tp.nroLaguna)
WHERE (ins.asistió = True) AND (l.nombre = 'La Salada, Coronel Granada') AND i.códigoE NOT IN (
    SELECT ins.códigoE
    FROM Inscripción ins
    INNER JOIN TorneoPesca tp ON (tp.codTorneo = ins.codTorneo)
    WHERE tp.fecha BETWEEN '2019-01-01' AND '2019-12-31'
)
```

# 4. Reportar nombre, y descripción de equipos que tengan al menos 5 integrantes. Ordenar por nombre y descripción.

*// No sé si es necesario el GROUP BY acá (CONSULTAR)*

```sql
SELECT e.nombreE, e.descripción
FROM Equipo e
INNER JOIN Integrante i ON (i.códigoE = e.códigoE)
GROUP BY e.códigoE
HAVING COUNT(*) >= 5
ORDER BY e.nombre, e.descripción
```

# 5. Reportar nombre, y descripción de equipos que tengan inscripciones en todas las lagunas.

```sql
SELECT e.nombre, e.descripción
FROM Equipo e
WHERE NOT EXISTS (
    SELECT *
    FROM Laguna
    WHERE NOT EXISTS (
        SELECT *
        FROM Inscripción ins
        INNER JOIN TorneoPesca tp ON (tp.codTorneo = ins.codTorneo)
        WHERE (ins.códigoE = e.códigoE) AND (tp.nroLaguna = l.nroLaguna)
    )
)
```

# 6. Eliminar el equipo con código: 10000.

```sql
DELETE FROM Inscripción
WHERE códigoE = 10000

DELETE FROM Integrante
WHERE códigoE = 10000

DELETE FROM Equipo
WHERE códigoE = 10000
```

# 7. Listar nombreL, ubicación,extensión y descripción de lagunas que no tuvieron torneos.

```sql
SELECT nombreL, ubicación, extensión, descripción
FROM Laguna
WHERE nroLaguna NOT IN (
    SELECT nroLaguna
    FROM TorneoPesca
)
```

# 8. Reportar nombre, y descripción de equipos que tengan inscripciones a torneos a disputarse durante 2019, pero no tienen inscripciones a torneos de 2018.

```sql
(
    SELECT DISTINCT e.nombre, e.descripción
    FROM Equipo e
    INNER JOIN Inscripción ins ON (ins.códigoE = e.códigoE)
    INNER JOIN TorneoPesca tp ON (tp.nroTorneo = ins.nroTorneo)
    WHERE tp.fecha BETWEEN '2019-01-01' AND '2019-12-31'
)
EXCEPT
(
    SELECT DISTINCT e.nombre, e.descripción
    FROM Equipo e
    INNER JOIN Inscripción ins ON (ins.códigoE = e.códigoE)
    INNER JOIN TorneoPesca tp ON (tp.nroTorneo = ins.nroTorneo)
    WHERE tp.fecha BETWEEN '2018-01-01' AND '2018-01-01'
)
```

# 9. Listar DNI, nombre, apellido, ciudad y email de integrantes que ganaron algún torneo que se disputó en la laguna con nombre: ‘Laguna de Chascomús’.

*// 2 soluciones posibles (CONSULTAR)*

```sql
SELECT dni, nombre, apellido, ciudad
FROM Integrante
WHERE códigoE IN (
    SELECT ins.códigoE
    FROM Inscripción ins
    INNER JOIN TorneoPesca tp ON (tp.codTorneo = ins.codTorneo)
    INNER JOIN Laguna l ON (l.nroLaguna = tp.nroLaguna)
    WHERE (l.nombreL = 'Laguna de Chascomús') AND (ins.ganó = True)
)
```

*// DISTINCT por si ganó varios torneos en la misma laguna*

```sql
SELECT DISTINCT i.dni, i.nombre, i.apellido, i.ciudad
FROM Integrante i
INNER JOIN Inscripción ins ON (ins.códigoB = i.códigoB)
INNER JOIN TorneoPesca tp ON (tp.codTorneo = ins.codTorneo)
INNER JOIN Laguna l ON (l.nroLaguna = tp.nroLaguna)
WHERE (l.nombreL = 'Laguna de Chascomús') AND (ins.ganó = True)
```