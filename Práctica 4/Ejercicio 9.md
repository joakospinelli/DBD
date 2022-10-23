Proyecto (<ins>codProyecto</ins>, nombreP,descripcion, fechaInicioP, fechaFinP, fechaFinEstimada,DNIResponsable, equipoBackend, equipoFrontend) *// DNIResponsable corresponde a un empleado, equipoBackend y equipoFrontend corresponden a un equipo*

Equipo (<ins>codEquipo</ins>, nombreE, descripcionTecnologias,DNILider) *//DNILider corresponde a un empleado*

Empleado (<ins>DNI</ins>,nombre, apellido, telefono, direccion, fechaIngreso)

Empleado_Equipo (<ins>codEquipo, DNI</ins>, fechaInicio, fechaFin,descripcionRol)

# 1. Listar nombre, descripción, fecha de inicio y fecha de fin de proyectos ya finalizados que no fueron terminados antes de la fecha de fin estimada.

```sql
SELECT nombreP, descripción, fechaInicioP, fechaFinP
FROM Proyecto
WHERE fechaFinP > fechaFinEstimada
```

# 2. Listar DNI, nombre, apellido, telefono, dirección y fecha de ingreso de empleados que no son, ni fueron responsables de proyectos. Ordenar por apellido y nombre.

```sql
SELECT dni, nombre, apellido, teléfono, dirección
FROM Empleado
WHERE dni NOT IN (
    SELECT DNIResponsable
    FROM Proyecto
)
ORDER BY apellido, nombre
```

# 3. Listar DNI, nombre, apellido, teléfono y dirección de líderes de equipo que tenga más de un equipo a cargo.

```sql
SELECT emp.dni, emp.nombre, emp.apellido, emp.teléfono, emp.dirección
FROM Empleado emp
INNER JOIN Equipo e ON (e.dniLider= emp.dni)
GROUP BY emp.dni, emp.nombre, emp.apellido, emp.teléfono, emp.dirección
HAVING COUNT(*) > 1
```

# 4. Listar DNI, nombre, apellido, teléfono y dirección de todos los empleados que trabajan en el proyecto con nombre ‘Proyecto X’. No es necesario informar responsable y líderes.

*// Si puedo poner un OR en la condición ON del INNER JOIN no tengo que hacer la unión (CONSULTAR)*

```sql
(
    SELECT emp.dni, emp.nombre, emp.apellido, emp.teléfono, emp.dirección
    FROM Empleado emp
    INNER JOIN Empleado_Equipo ee ON (ee.dni = emp.dni)
    INNER JOIN Proyecto p ON (p.equipoFrontend = ee.codEquipo)
    WHERE p.nombreP = 'Proyecto X'
)
UNION
(
    SELECT emp.dni, emp.nombre, emp.apellido, emp.teléfono, emp.dirección
    FROM Empleado emp
    INNER JOIN Empleado_Equipo ee ON (ee.dni = emp.dni)
    INNER JOIN Proyecto p ON (p.equipoBackend = ee.codEquipo)
    WHERE p.nombreP = 'Proyecto X' 
)
```

# 5. Listar nombre de equipo y datos personales de líderes de equipos que no tengan empleados asignados y trabajen con tecnología ‘Java’.

*// 2 soluciones posibles (CONSULTAR)*

```sql
SELECT e.nombreE, emp.nombre, emp.apellido, emp.dni, emp.dirección
FROM Empleado emp
INNER JOIN Equipo e ON (e.dniLider = emp.dni)
WHERE (e.descripciónTecnologias LIKE '%Java%') AND NOT EXISTS (
    SELECT *
    FROM Empleado_Equipo ee
    WHERE (ee.codEquipo = e.codEquipo)
)
```

```sql
SELECT e.nombreE, emp.nombre, emp.apellido, emp.dni, emp.dirección
FROM Empleado emp
INNER JOIN Equipo e ON (e.dniLider = emp.dni)
WHERE (e.descripciónTecnologias LIKE '%Java%') AND e.codEquipo NOT IN (
    SELECT codEquipo
    FROM Empleado_Equipo
```

# 6. Modificar nombre, apellido y dirección del empleado con DNI: 40568965 con los datos que desee.

```sql
UPDATE Cliente
SET (
    nombre = 'AAA',
    apellido = 'BBB',
    dirección = '12 3456'
)
WHERE dni = '40568965'
```

# 7. Listar DNI, nombre, apellido, teléfono y dirección de empleados que son responsables de proyectos pero no han sido líderes de equipo.

*// DISTINCT por si fue responsable de varios proyectos*

```sql
SELECT DISTINCT emp.dni, emp.nombre, emp.apellido, emp.teléfono, emp.dirección
FROM Empleado emp
INNER JOIN Proyecto p ON (p.dniResponsable = emp.dni)
WHERE p.dni NOT IN (
    SELECT dniLider
    FROM Equipo
)
```

# 8. Listar nombre de equipo y descripción de tecnologías de equipos que hayan sido asignados como equipos frontend y backend.

*// Misma duda que en EJ4 pero con INTERSECT*

```sql
(
    SELECT e.nombreE, e.descripciónTecnologias
    FROM Equipo e
    INNER JOIN Proyecto p ON (p.equipoFrontend = e.codEquipo)
)
INTERSECT
(
    SELECT e.nombreE, e.descripciónTecnologias
    FROM Equipo e
    INNER JOIN Proyecto p ON (p.equipoBackend = e.codEquipo)
)
```

# 9. Listar nombre, descripción, fecha de inicio, nombre y apellido de responsables de proyectos a finalizar durante 2019.

*// No sé si 'proyectos a finalizar' sería con la fechaFin o con la fechaEstimada (CONSULTAR)*

```sql
SELECT p.nombreP, p.descripción, p.fechaInicioP, emp.nombre, emp.apellido
FROM Proyecto p
INNER JOIN Empleado emp ON (emp.dni = p.dniResponsable)
WHERE p.fechaFinEstimada BETWEEN '2019-01-01' AND '2019-12-31'
```