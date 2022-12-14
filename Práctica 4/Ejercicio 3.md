Club=(<ins>codigoClub</ins>, nombre, anioFundacion, codigoCiudad(FK))

Ciudad=(<ins>codigoCiudad</ins>, nombre)

Estadio=(<ins>codigoEstadio</ins>, codigoClub(FK), nombre, direccion)

Jugador=(<ins>DNI</ins>, nombre, apellido, edad, codigoCiudad(FK))

ClubJugador=(<ins>codigoClub, DNI</ins>, desde, hasta)

# 1. Reportar nombre y anioFundacion de aquellos clubes de la ciudad de La Plata que no poseen estadio.

*// 2 soluciones posibles*

```sql
SELECT Club.nombre, Club.anioFundacion
FROM Club
INNER JOIN Ciudad c ON (c.codigoCiudad = Club.codigoCiudad)
WHERE (c.nombre = 'La Plata') AND NOT EXISTS (
    SELECT *
    FROM Estadio e
    WHERE (e.codigoClub = Club.codigoClub)
) 
```

```sql
SELECT nombre, anioFundacion
FROM Club
WHERE codigoClub NOT IN (
    SELECT Club.codigoClub
    FROM Club
    INNER JOIN Estadio e ON (e.codigoClub = Club.codigoClub)
    INNER JOIN Ciudad c ON (c.codigoCiudad = e.codigoCiudad)
    WHERE c.nombre = 'La Plata'
)
```

# 2. Listar nombre de los clubes que no hayan tenido ni tengan jugadores de la ciudad de Berisso.

*// Capaz lo podría hacer sin un IN. Quedaría como un NOT EXISTS como el del ej 1*

```sql
SELECT Club.nombre
FROM Club
WHERE Club.códigoClub NOT IN (
    SELECT cj.códigoClub
    FROM ClubJugador cj
    INNER JOIN Jugador j ON (j.dni = cj.dni)
    INNER JOIN Ciudad c ON (c.códigoCiudad = j.códigoCiudad)
    WHERE c.nombre = 'Berisso'
)
```

# 3. Mostrar DNI, nombre y apellido de aquellos jugadores que jugaron o juegan en el club "Gimnasia y Esgrima La Plata".

```sql
SELECT j.dni, j.nombre, j.apellido
FROM Jugador j
INNER JOIN ClubJugador cj ON (cj.dni = j.dni)
INNER JOIN Club ON (Club.códigoClub = cj.códigoClub)
WHERE Club.nombre = 'Gimnasia y Esgrima La Plata'
```

# 4. Mostrar DNI, nombre y apellido de aquellos jugadores que tengan más de 29 años y hayan jugado o juegan en algún club de la ciudad de Córdoba.

```sql
SELECT DISTINCT j.dni, j.nombre, j.apellido
FROM Jugador j
INNER JOIN ClubJugador cj ON (cj.dni = j.dni)
INNER JOIN Club ON (Club.códigoClub = cj.códigoClub)
INNER JOIN Ciudad c ON (c.códigoCiudad = Club.códigoCiudad)
WHERE (j.edad > 29) AND (c.nombre = 'Córdoba')
```

# 5. Mostrar para cada club, nombre de club y la edad promedio de los jugadores que juegan actualmente en cada uno.

*// En el join con ClubJugador necesito que sea un LEFT JOIN para que cuente los clubes sin jugadores. En el join con Jugador también uso LEFT JOIN para que no me elimine las tuplas de clubes sin jugadores*

```sql
SELECT Club.nombre, AVG(j.edad)
FROM Club
LEFT JOIN ClubJugador cj ON (cj.códigoClub = Club.códigoClub)
LEFT JOIN Jugador j ON (j.dni = cj.dni)
WHERE ( cj.hasta > NOW() )
GROUP BY Club.nombre
```

# 6. Listar para cada jugador: nombre, apellido, edad y cantidad de clubes diferentes en los que jugó. (incluido el actual)

*// LEFT JOIN porque podría haber jugadores sin equipo*

```sql
SELECT j.nombre, j.apellido, j.edad, COUNT(*)
FROM Jugador j
LEFT JOIN ClubJugador cj ON (cj.dni = j.dni)
GROUP BY j.dni, j.nombre, j.apellido, j.edad
```

# 7. Mostrar el nombre de los clubes que nunca hayan tenido jugadores de la ciudad de Mar del Plata.

```sql
SELECT nombre
FROM Club
WHERE códigoClub NOT IN (
    SELECT cj.códigoClub
    FROM ClubJugador cj
    INNER JOIN Jugador j ON (j.dni = cj.dni)
    INNER JOIN Ciudad c ON (c.códigoCiudad = j.códigoCiudad)
    WHERE c.nombre = 'Mar del Plata'
)
```

# 8. Reportar el nombre y apellido de aquellos jugadores que hayan jugado en todos los clubes.

```sql
SELECT j.nombre, j.apellido
FROM Jugador j
WHERE NOT EXISTS (
    SELECT *
    FROM Club c
    WHERE NOT EXISTS (
        SELECT *
        FROM ClubJugador cj
        WHERE (cj.códigoClub = c.códigoClub) and (cj.dni = j.dni)
    )
)
```

# 9. Agregar con codigoClub 1234 el club "Estrella de Berisso" que se fundó en 1921 y que pertenece a la ciudad de Berisso. Puede asumir que el codigoClub 1234 no existe en la tabla Club.

```sql
INSERT INTO Club (
    códigoClub,
    nombre,
    anioFundacion,
    códigoCiudad
) VALUES (
    1234,
    'Estrella de Berisso',
    1921,
    (
        SELECT códigoCiudad
        FROM Ciudad
        WHERE nombre = 'Berisso'
    )
)
```