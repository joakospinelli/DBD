PERSONA = (<ins>DNI</ins>, Apellido, Nombre, Fecha_Nacimiento, Estado_Civil, Genero)

ALUMNO = (<ins>DNI</ins>, Legajo, Año_Ingreso)

PROFESOR = (<ins>DNI</ins>, Matricula, Nro_Expediente)

TITULO = (<ins>Cod_Titulo</ins>, Nombre, Descripción)

TITULO-PROFESOR = (<ins>Cod_Titulo, DNI</ins>, Fecha)

CURSO = (<ins>Cod_Curso</ins>, Nombre, Descripción, Fecha_Creacion, Duracion)

ALUMNO-CURSO = (<ins>DNI, Cod_Curso</ins>, Año, Desempeño, Calificación)

PROFESOR-CURSO = (<ins>DNI, Cod_Curso</ins>, Fecha_Desde, Fecha_Hasta)

# 1. Listar DNI, legajo y apellido y nombre de todos los alumnos que tegan año ingreso inferior a 2014.

```sql
SELECT a.dni, a.legajo, p.apellido, p.nombre
FROM Alumno a
INNER JOIN Persona p ON (p.dni = a.dni)
WHERE a.año_ingreso < 2014
```

# 2. Listar DNI, matricula, apellido y nombre de los profesores que dictan cursos que tengan más 100 horas de duración. Ordenar por DNI

```sql
SELECT prof.dni, prof.matrícula, p.apellido, p.nombre
FROM Profesor prof
INNER JOIN Persona p ON (p.dni = prof.dni)
INNER JOIN Profesor-Curso pc ON (pc.dni = prof.dni)
INNER JOIN Curso c ON (c.cod_curso = pc.cod_curso)
WHERE c.duración > 100
ORDER BY prof.dni
```

# 3. Listar el DNI, Apellido, Nombre, Género y Fecha de nacimiento de los alumnos inscriptos al curso con nombre “Diseño de Bases de Datos” en 2019.

```sql
SELECT a.dni, p.apellido, p.nombre, p.género
FROM Alumno a
INNER JOIN Persona p ON (p.dni = a.dni)
INNER JOIN Alumno-Curso ac ON (ac.dni = a.dni)
INNER JOIN Curso c ON (c.cod_curso = ac.cod_curso)
WHERE (ac.año = 2019) AND (c.nombre = 'Diseño de Bases de Datos')
```

# 4. Listar el DNI, Apellido, Nombre y Calificación de aquellos alumnos que obtuvieron una calificación superior a 9 en los cursos que dicta el profesor “Juan Garcia”. Dicho listado deberá estar ordenado por Apellido.

*// Uso DISTINCT porque un alumno se podría haber sacado 9 en más de una materia de Juan García*

*// Acá me trabé con tantos joins. No sé si está bien hacer 2 INNER JOIN con Persona; uno para los alumnos y otro para el profesor (CONSULTAR)*

```sql
SELECT DISTINCT a.dni, p.apellido, p.nombre, ac.calificación
FROM Alumno a
INNER JOIN Persona p ON (p.dni = a.dni)
INNER JOIN Alumno-Curso ac ON (ac.dni = a.dni)
INNER JOIN Profesor-Curso pc ON (pc.cod_curso = ac.cod_curso)
INNER JOIN Profesor prof ON (prof.dni = pc.dni)
INNER JOIN Persona p2 ON (p2.dni = prof.dni)
WHERE (p2.nombre = 'Juan' AND p2.apellido = 'García') AND (ac.calificación > 9)
ORDER BY p.apellido
```

# 5. Listar el DNI, Apellido, Nombre y Matrícula de aquellos profesores que posean más de 3 títulos. Dicho listado deberá estar ordenado por Apellido y Nombre.

*// LEFT JOIN con Título-Profesor porque podría haber profesores sin títulos*

*// 2 soluciones posibles*

```sql
SELECT prof.dni, p.apellido, p.nombre, prof.matrícula
FROM Profesor prof
INNER JOIN Persona p ON (p.dni = prof.dni)
LEFT JOIN Título-Profesor tp ON (tp.dni = prof.dni)
GROUP BY prof.dni
HAVING COUNT(*) > 3
ORDER BY p.apellido, p.nombre
```

```sql
SELECT prof.dni, p.apellido, p.nombre, prof.matrícula
FROM Profesor prof
INNER JOIN Persona p ON (p.dni = prof.dni)
WHERE prof.dni IN (
    SELECT tp.dni
    FROM Título-Profesor tp
    GROUP BY tp.dni
    HAVING COUNT(*) > 3
)
ORDER BY p.apellido, p.nombre
```


# 6. Listar el DNI, Apellido, Nombre, Cantidad de horas y Promedio de horas que dicta cada profesor. La cantidad de horas se calcula como la suma de la duración de todos los cursos que dicta.

```sql
SELECT prof.dni, p.apellido, p.nombre, SUM(c.duración) AS cantHoras, AVG (c.duración) AS promHoras
FROM Profesor prof
INNER JOIN Persona p ON (p.dni = prof.dni)
LEFT JOIN Profesor-Curso pc ON (pc.dni = prof.dni)
LEFT JOIN Curso c ON (c.cod_curso = pc.cod_curso)
GROUP BY prof.dni, p.apellido, p.nombre
```

# 7. Listar Nombre, Descripción del curso que posea más alumnos inscriptos y del que posea menos alumnos inscriptos durante 2019.

*// Se me ocurrió usar un UNION ALL por si fuesen el mismo*

```sql
(
    SELECT c.nombre, c.descripción
    FROM Curso c
    INNER JOIN Alumno-Curso ac ON (ac.cod_curso = c.cod_curso)
    HAVING COUNT(*) >= ALL (
        SELECT COUNT(*)
        FROM Alumno-Curso ac
        WHERE (ac.año = 2019)
        GROUP BY (ac.cod_curso)
    )
) UNION ALL (
    SELECT c.nombre, c.descripción
    FROM Curso c
    INNER JOIN Alumno-Curso ac ON (ac.cod_curso = c.cod_curso)
    HAVING COUNT(*) <= ALL (
        SELECT COUNT(*)
        FROM Alumno-Curso ac
        WHERE (ac.año = 2019)
        GROUP BY (ac.cod_curso)
    )
)
```

# 8. Listar el DNI, Apellido, Nombre, Legajo de alumnos que realizaron cursos con nombre conteniendo el string ‘BD’ durante 2018 pero no realizaron ningún curso durante 2019.

*// Misma duda que en el ej6*

```sql
SELECT a.dni, p.apellido, p.nombre, a.legajo
FROM Alumno a
INNER JOIN Persona p ON (p.dni = a.dni)
LEFT JOIN Alumno-Curso ac ON (ac.dni = a.dni)
LEFT JOIN Curso c ON (c.cod_curso = ac.cod_curso)
WHERE (ac.nombre LIKE '%BD%' and ac.año = 2018) AND a.dni NOT IN (
    SELECT ac.dni
    FROM Alumno-Curso ac
    WHERE ac.año = 2019
)
```

```sql
SELECT a.dni, p.apellido, p.nombre, a.legajo
FROM Alumno a
INNER JOIN Persona p ON (p.dni = a.dni)
LEFT JOIN Alumno-Curso ac ON (ac.dni = a.dni)
LEFT JOIN Curso c ON (ac.dni = a.dni)
WHERE (ac.nombre LIKE '%BD%' and ac.año = 2018) AND NOT EXISTS (
    SELECT *
    FROM Alumno-Curso ac2
    WHERE (ac2.año = 2019) AND (ac.dni = a.dni)
)
```

# 9. Agregar un profesor con los datos que prefiera y agregarle el título con código: 25.

```sql
INSERT INTO Persona(
    dni,
    apellido,
    nombre,
    fecha_nacimiento,
    estado_civil,
    genero
) VALUES (
    '12345678',
    'AAA',
    'BBB',
    '2001-03-15',
    'Soltero',
    'M'
)

INSERT INTO Profesor(
    dni,
    matrícula,
    nro_expediente
) VALUES (
    '12345678',
    12345,
    98765
)

INSERT INTO Título(
    '25',
    'Licenciatura en Informática',
    'Plan de estudios 2015'
)

INSERT INTO Título-Profesor(
    cod_título,
    dni,
    fecha
) VALUES (
    '25',
    '12345678',
    '2022-10-20'
)
```

# 10. Modificar el estado civil del alumno cuyo legajo es ‘2020/09’, el nuevo estado civil es divorciado.

*// No sé cuál sería la mejor manera de hacer el UPDATE WHERE (CONSULTAR)*

```sql
UPDATE Persona
SET estado_civil = 'Divorciado'
WHERE dni = (
    SELECT dni
    FROM Alumno
    WHERE legajo = '2020/09'
)
```

```sql
UPDATE Persona p
INNER JOIN Alumno a ON (p.dni = a.dni)
SET p.estado_civil = 'Divorciado'
WHERE a.legajo = '2020/09'
```

# 11. Dar de baja el alumno con DNI 30568989. Realizar todas las bajas necesarias para no dejar el conjunto de relaciones en estado inconsistente.

```sql
DELETE FROM Alumno-Curso
WHERE dni = '30568989'

DELETE FROM Alumno
WHERE dni = '30568989'

-- Bajas a realizar suponiendo que también hay que darle de baja en Persona

DELETE FROM Profesor-Curso
WHERE dni = '30568989'

DELETE FROM Título-Profesor
WHERE dni = '30568989'

DELETE FROM Profesor
WHERE dni = '30568989'

DELETE FROM Persona
WHERE dni = '30568989'
```