Box = (<ins>nroBox</ins>,m2, ubicación, capacidad, ocupacion) *// ocupación es un numérico indicando cantidad de mascotas en el box actualmente, capacidad es una descripción.*

Mascota = (<ins>codMascota</ins>,nombre, edad, raza, peso, telefonoContacto)

Veterinario = (<ins>matricula</ins>, CUIT, nombYAp, direccion, telefono)

Supervision = (<ins>codMascota, nroBox, fechaEntra</ins>, fechaSale?, matricula(fk), descripcionEstadia) *// fechaSale tiene valor null si la mascota está actualmente en el box*

# 1. Listar para cada veterinario cantidad de supervisiones realizadas con fecha de salida (fechaSale) durante enero de 2020. Indicar matricula, CUIT, nombre y apellido, dirección, teléfono y cantidad de supervisiones.

```sql
SELECT v.matrícula, v.cuit, v.nombYAp, v.dirección, v.teléfono, COUNT(*) as cantidad
FROM Veterinario v
INNER JOIN Supervisión s ON (s.matrícula = v.matrícula)
GROUP BY v.matrícula, v.cuit, v.nombYAp, v.dirección, v.teléfono
```

# 2. Listar CUIT, matricula, nombre, apellido,dirección y teléfono de veterinarios que no tengan mascotas bajo supervisión actualmente.

```sql
SELECT cuit, matrícula, nombYAp, dirección, teléfono
FROM Veterinario
WHERE matrícula NOT IN (
    SELECT matrícula
    FROM Supervisión
    WHERE fechaSale IS NULL
)
```

# 3. Listar nombre, edad, raza, peso y teléfono de contacto de mascotas fueron atendidas por el veterinario ‘Oscar Lopez’. Ordenar por nombre y raza de manera ascendente.

```sql
SELECT DISTINCT m.nombre, m.edad, m.raza, m.peso, m.teléfonoContacto
FROM Mascota m
INNER JOIN Supervisión s ON (s.codMascota = m.codMascota)
INNER JOIN Veterinario v ON (v.matrícula = s.matrícula)
WHERE v.nombYAp = 'Oscar López'
ORDER BY m.nombre, m.raza
```

# 4. Modificar nombre y apellido al veterinario con matricula: ‘MP 10000’, deberá llamarse: ‘Pablo Lopez’.

```sql
UPDATE Veterinario
SET (nombYAp = 'Pablo López')
WHERE matrícula = 'MP 10000'
```

# 5. Listar nombre, edad, raza, peso de mascotas que tengan supervisiones con el veterinario con matricula : ‘MP 1000’ y con el veterinario con matricula: ‘MN 4545’.

*// El INTERSECT elimina duplicados por defecto, así que no necesito usar DISTINCT en las consultas (CONSULTAR)*

```sql
(
    SELECT m.nombre, m.edad, m.raza, m.peso
    FROM Mascota m
    INNER JOIN Supervisión s ON (s.codMascota = m.codMascota)
    WHERE s.matrícula = 'MP 1000'
)
INTERSECT
(
    SELECT m.nombre, m.edad, m.raza, m.peso
    FROM Mascota m
    INNER JOIN Supervisión s ON (s.codMascota = m.codMascota)
    WHERE s.matrícula = 'MN 4545'
)
```

```sql
SELECT nombre, edad, raza, peso
FROM Mascota
WHERE codMascota IN (
    SELECT codMascota
    FROM Supervisión
    WHERE matrícula = 'MP 1000'
) AND codMascota IN (
    SELECT codMascota
    FROM Supervisión
    WHERE matrícula = 'MN 4545'
)
```

# 6. Listar nroBox, m2, ubicación, capacidad y nombre de mascota para supervisiones con fecha de entrada (fechaEntra) durante 2020.

```sql
SELECT DISTINCT b.nroBox, b.m2, b.ubicación, b.capacidad, m.nombre
FROM Box b
INNER JOIN Supervisión s ON (s.nroBox = b.nroBox)
INNER JOIN Mascota m ON (m.codMascota = s.codMascota)
WHERE s.fechaEntra BETWEEN '2020-01-01' AND '2020-12-31'
```