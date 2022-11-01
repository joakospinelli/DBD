Club(<ins>IdClub</ins>,nombreClub,ciudad)

Complejo(<ins>IdComplejo</ins>,nombreComplejo, IdClub(fk))

Cancha(<ins>IdCancha</ins>,nombreCancha,IdComplejo(fk))

Entrenador(<ins>IdEntrenador</ins>, nombreEntrenador,fechaNacimiento, direccion)

Entrenamiento(<ins>IdEntrenamiento</ins>, fecha, IdEntrenador(fk), IdCancha(fk))

# 1- Listar nombre, fecha nacimiento y dirección de entrenadores que hayan tenido entrenamientos durante 2020.

```sql
SELECT DISTINCT e.nombreEntrenador, e.fechaNacimiento, e.dirección
FROM Entrenador e
INNER JOIN Entrenamiento ent ON (ent.idEntrenador = e.idEntrenador)
WHERE ent.fecha BETWEEN '2020-01-01' AND '2020-12-31'
```

# 2- Listar para cada cancha del complejo “Complejo 1” , la cantidad de entrenamientos que se realizaron durante el 2019. Informar nombre de la cancha y cantidad de entrenamientos.

```sql
SELECT c.nombre, COUNT(*) AS cantidad
FROM Cancha c
INNER JOIN Complejo cm ON (cm.idComplejo = c.idComplejo)
LEFT JOIN Entrenamiento ent ON (ent.idCancha = c.idCancha)
WHERE (ent.fecha BETWEEN '2019-01-01' AND '2019-12-31') AND (cm.nombreComplejo = 'Complejo 1')
GROUP BY c.idCancha, c.nombre
```

# 3- Listar los complejos donde haya realizado entrenamientos el entrenador “Jorge Gonzalez”. Informar nombre de complejo, ordenar el resultado de manera ascendente.

```sql
SELECT DISTINCT cm.nombre
FROM Complejo cm
INNER JOIN Cancha c ON (c.idComplejo = cm.idComplejo)
INNER JOIN Entrenamiento ent ON (ent.idCancha = c.idCancha)
INNER JOIN Entrenador e ON (e.idEntrenador = ent.idEntrenador)
WHERE e.nombreEntrenador = 'Jorge González'
ORDER BY cm.nombre
```

# 4- Listar nombre , fecha de nacimiento y dirección de entrenadores que hayan entrenado en la cancha “Cancha 1” y en la Cancha “Cancha 2”.

```sql
(
    SELECT e.nombreEntrenador, e.fechaNacimiento, e.dirección
    FROM Entrenador e
    INNER JOIN Entrenamiento ent ON (ent.idEntrenador = e.idEntrenador)
    INNER JOIN Cancha c ON (c.idCancha = ent.idCancha)
    WHERE c.nombreCancha = 'Cancha 1'
)
INTERSECT
(
    SELECT e.nombreEntrenador, e.fechaNacimiento, e.dirección
    FROM Entrenador e
    INNER JOIN Entrenamiento ent ON (ent.idEntrenador = e.idEntrenador)
    INNER JOIN Cancha c ON (c.idCancha = ent.idCancha)
    WHERE c.nombreCancha = 'Cancha 2'
)
```

# 5- Listar todos los clubes en los que entrena el entrenador “Marcos Perez”. Informar nombre del club y ciudad.

```sql
SELECT DISTINCT cl.nombre, cl.ciudad
FROM Club cl
INNER JOIN Complejo cm ON (cm.idClub = cl.idClub)
INNER JOIN Cancha c ON (c.idComplejo = cm.idComplejo)
INNER JOIN Entrenamiento ent ON (ent.idCancha = c.idCancha)
INNER JOIN Entrenador e ON (e.idEntrenador = ent.idEntrenador)
WHERE e.nombreEntrenador = 'Marcos Pérez'
```

# 6- Eliminar los entrenamientos del entrenador ‘Juan Perez’

*// No sé si está bien el DELETE con INNER JOIN (CONSULTAR)*

```sql
DELETE ent
FROM Entrenamiento ent
INNER JOIN Entrenador e ON (e.idEntrenador = ent.idEntrenador)
WHERE e.nombreEntrenador = 'Juan Pérez'
```

```sql
DELETE FROM Entrenamiento
WHERE idEntrenador IN (
    SELECT idEntrenador
    FROM Entrenador
    WHERE nombreEntrenador = 'Juan Pérez'
)
```