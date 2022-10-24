Cine (<ins>idCine</ins>, nombreC, direccion)

Sala (<ins>nroSala</ins>, nombreS, descripción, capacidad,idCine(fk))

Pelicula (<ins>idPeli</ins>, nombre, descripción, genero)

Funcion (<ins>nroFuncion</ins>, nroSala(fk), idPeli(fk), fecha, hora, ocupación) *//ocupación indica cantidad de espectadores de la función*

# 1- Listar nombre, descripción y género de películas con funciones durante 2020.

```sql
SELECT p.nombre, p.función
FROM Película p
INNER JOIN Función f ON (f.idPeli = p.idPeli)
WHERE f.fecha BETWEEN '2019-01-01' AND '2019-12-31'
```

```sql
SELECT nombre, función
FROM Película
WHERE idPeli IN (
    SELECT idPeli
    FROM Función
    WHERE fecha BETWEEN '2019-01-01' AND '2019-12-31'
)
```

# 2- Listar para cada Sala cantidad de espectadores que asistieron durante 2020. Indicar nombre de la sala, nombre del cine y total de espectadores.

```sql
SELECT s.nombreS, c.nombreC, SUM(ocupación) as total
FROM Sala s
INNER JOIN Cine c ON (c.idCine = s.idCine)
LEFT JOIN Función f ON (f.nroSala = s.nroSala)
WHERE f.fecha BETWEEN '2020-01-01' AND '2020-12-31'
GROUP BY s.nroSala, c.nombreC
```

# 3- Listar nombre de cine y dirección para los cines que tienen o tuvieron función para la película ‘Relic’. Ordenar por nombre de Cine y dirección desc.

```sql
SELECT nombreC, dirección
FROM Cine
WHERE c.idCine IN (
    SELECT s.idCine
    FROM Sala s
    INNER JOIN Función f ON (f.nroSala = s.nroSala)
    INNER JOIN Película p ON (p.idPeli = f.idPeli)
    WHERE p.nombre = 'Relic'
)
ORDER BY nombreC, dirección DESC
```

# 4- Listar nombre, descripción y género de películas que tienen función en la sala con nombre ‘Sala Lola Membrives’ o tienen función en el cine con nombre ‘Gran Rex’.

```sql
(
    SELECT p.nombre, p.descripción, p.género
    FROM Película p
    INNER JOIN Función f ON (f.idPeli = p.idPeli)
    INNER JOIN Sala s ON (s.nroSala = f.nroSala)
    WHERE s.nombreS = 'Sala Lola Membrives'
)
UNION
(
    SELECT p.nombre, p.descripción, p.género
    FROM Película p
    INNER JOIN Función f ON (f.idPeli = p.idPeli)
    INNER JOIN Sala s ON (s.nroSala = f.nroSala)
    INNER JOIN Cine c ON (c.idCine = s.idCine)
    WHERE c.nombreC = 'Gran Rex'
)
```

# 5- Listar nombre del cine y dirección de cines que tengan salas con capacidad superior a los 300 espectadores.

```sql
SELECT nombreC, dirección
FROM Cine
WHERE idCine IN (
    SELECT idCine
    FROM Sala
    WHERE capacidad > 300
)
```

```sql
SELECT DISTINCT c.nombreC, c.dirección
FROM Cine c
INNER JOIN Sala s ON (s.idCine = c.idCine)
WHERE s.capacidad > 300
```

# 6- Agregar un cine con nombre cine ‘Cine Ricardo Darin’, dirección: ‘calle 2 nro 1900, La Plata’ e idCine: 5000, asuma que no existe dicho id.

```sql
INSERT INTO Cine(
    idCine,
    nombreC,
    dirección
) VALUES (
    5000,
    'Cine Ricardo Darín',
    'Calle 2 nro. 1900, La Plata'
)
```