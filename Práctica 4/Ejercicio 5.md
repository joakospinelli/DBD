Localidad(<ins>CodigoPostal</ins>, nombreL, descripcion, #habitantes)

Arbol(<ins>nroArbol</ins>, especie, años, calle, nro, codigoPostal(fk))

Podador(<ins>DNI</ins>, nombre, apellido, telefono,fnac,codigoPostalVive(fk))

Poda(<ins>codPoda</ins>,fecha, DNI(fk),nroArbol(fk))

# 1. Listar especie, años, calle, nro. y localidad de árboles podados por el podador ‘Juan Perez’ y por el podador ‘Jose Garcia’.

```sql
(
    SELECT a.especie, a.años, a.calle, a.nro, l.nombreL
    FROM Arbol a
    INNER JOIN Localidad l ON (l.códigoPostal = a.códigoPostal)
    INNER JOIN Poda ON (Poda.nroArbol = a.nro)
    INNER JOIN Podador p ON (p.dni = Poda.dni)
    WHERE (p.nombre = 'Juan') and (p.apellido = 'Pérez')
)
INTERSECT
(
    SELECT a.especie, a.años, a.calle, a.nro, l.nombreL
    FROM Arbol a
    INNER JOIN Localidad l ON (l.códigoPostal = a.códigoPostal)
    INNER JOIN Poda ON (Poda.nroArbol = a.nro)
    INNER JOIN Podador p ON (p.dni = Poda.dni)
    WHERE (p.nombre = 'José') and (p.apellido = 'García')
)
```

# 2. Reportar DNI, nombre, apellido, fnac y localidad donde viven podadores que tengan podas durante 2018.

```sql
SELECT DISTINCT p.dni, p.nombre, p.apellido, p.fnac
INNER JOIN Poda ON (Poda.dni = p.dni)
INNER JOIN Localidad l ON (l.codigoPostal = p.codigoPostalVive)
WHERE Poda.fecha BETWEEN '2018-01-01' AND '2018-12-31'
```

# 3. Listar especie, años, calle, nro y localidad de árboles que no fueron podados nunca.

```sql
SELECT a.especie, a.años, a.calle, a.nro, l.nombreL
FROM Arbol a
INNER JOIN Localidad l ON (l.códigoPostal = a.códigoPostal)
WHERE a.nroArbol NOT IN (
    SELECT DISTINCT nroArbol
    FROM Poda
)
```

# 4. Reportar especie, años,calle, nro y localidad de árboles que fueron podados durante 2017 y no fueron podados durante 2018.

```sql
(
    SELECT a.especie, a.años, a.calle, a.nro, l.nombreL
    FROM Arbol a
    INNER JOIN Localidad l ON (l.códigoPostal = a.códigoPostal)
    INNER JOIN Poda ON (Poda.nroArbol = a.nroArbol)
    WHERE fecha BETWEEN '2017-01-01' AND '2017-12-31'
)
EXCEPT
(
    SELECT a.especie, a.años, a.calle, a.nro, l.nombreL
    FROM Arbol a
    INNER JOIN Localidad l ON (l.códigoPostal = a.códigoPostal)
    INNER JOIN Poda ON (Poda.nroArbol = a.nroArbol)
    WHERE fecha BETWEEN '2019-01-01' AND '2019-12-31'
)
```

# 5. Reportar DNI, nombre, apellido, fnac y localidad donde viven podadores con apellido terminado con el string ‘ata’ y que el podador tenga al menos una poda durante 2018. Ordenar por apellido y nombre.

*// No entendí en el enunciado (no lo voy a hacer 👍)*

# 6. Listar DNI, apellido, nombre, teléfono y fecha de nacimiento de podadores que solo podaron árboles de especie ‘Coníferas’.

*// 2 soluciones posibles*

```sql
SELECT p.dni, p.apellido, p.nombre, p.teléfono, p.fnac
FROM Podador p
WHERE p.dni NOT IN (
    SELECT Poda.dni
    FROM Poda
    INNER JOIN Arbol a ON (a.nroArbol = Poda.nroArbol)
    WHERE NOT (a.especie = 'Coníferas')
)
```

```sql
(
    SELECT p.dni, p.apellido, p.nombre, p.teléfono, p.fnac
    FROM Podador p
    INNER JOIN Poda ON (Poda.dni = p.dni)
    INNER JOIN Arbol a ON (a.nroArbol = Poda.nroArbol)
    WHERE a.especie = 'Coníferas'
)
EXCEPT
(
    SELECT p.dni, p.apellido, p.nombre, p.teléfono, p.fnac
    FROM Podador p
    INNER JOIN Poda ON (Poda.dni = p.dni)
    INNER JOIN Arbol a ON (a.nroArbol = Poda.nroArbol)
    WHERE NOT (a.especie = 'Coníferas')
)
```

# 7. Listar especie de árboles que se encuentren en la localidad de ‘La Plata’ y también en la localidad de ‘Salta’.

```sql
(
    SELECT DISTINCT a.especie
    FROM Arbol a
    INNER JOIN Localidad l ON (l.códigoPostal = a.códigoPostal)
    WHERE l.nombre = 'La Plata'
)
INTERSECT
(
    SELECT DISTINCT a.especie
    FROM Arbol a
    INNER JOIN Localidad l ON (l.códigoPostal = a.códigoPostal)
    WHERE l.nombre = 'Salta'
)
```

# 8. Eliminar el podador con DNI: 22234566.

```sql
DELETE FROM Poda WHERE dni = '22234566'

DELETE FROM Podador WHERE dni = '22234566'
```

# 9. Reportar nombre, descripción y cantidad de habitantes de localidades que tengan menos de 100 árboles.

```sql
SELECT l.nombreL, l.descripción, l.habitantes
FROM Localidad l
INNER JOIN Arbol a ON (a.códigoPostal = l.códigoPostal)
GROUP BY a.nroArbol, l.nombreL, l.descripción, l.habitantes
HAVING COUNT(*) < 100
```