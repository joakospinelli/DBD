Vehiculo = (<ins>patente</ins>, modelo, marca, peso, km)

Camion = (<ins>patente</ins>, largo, max_toneladas, cant_ruedas, tiene_acoplado)

Auto = (<ins>patente</ins>, es_electrico, tipo_motor)

Service = (<ins>fecha, patente</ins>, km_service, observaciones, monto)

Parte = (<ins>cod_parte</ins>, nombre, precio_parte)

Service_Parte = (<ins>fecha, patente, cod_parte</ins>, precio)

# 1. Listar todos los datos de aquellos camiones que tengan entre 4 y 8 ruedas, y que hayan realizado algún service en los últimos 365 días. Ordenar por patente, modelo y marca.

```sql
SELECT c.patente, c.largo, c.max_toneladas, c.cant_ruedas, c.tiene_acoplado, v.modelo, v.marca, v.peso, v.km
FROM Camión c
INNER JOIN Vehículo v ON (v.patente = c.patente)
INNER JOIN Service s ON (s.patente = c.patente)
WHERE (c.cant_ruedas BETWEEN 4 AND 8) AND (s.fecha BETWEEN '2022-10-23' AND '2021-10-23')
ORDER BY c.patente, v.modelo, v.marca
```

# 2. Listar los autos que hayan realizado el service “cambio de aceite” antes de los 13.000 km o hayan realizado el service “inspección general” que incluya la parte “filtro de combustible”.

```sql
(
    SELECT a.*
    FROM Auto a
    INNER JOIN Service s ON (s.patente = a.patente)
    WHERE (s.observaciones = 'cambio de aceite') AND (s.km_service < 13000)
)
UNION
(
    SELECT a.*
    FROM Auto a
    INNER JOIN Service s ON (s.patente = a.patente)
    INNER JOIN Service_Parte sp ON (sp.patente = a.patente) AND (sp.fecha = s.fecha)
    INNER JOIN Parte p ON (p.codParte = sp.codParte)
    WHERE (s.observaciones = 'inspección general') AND (p.nombre = 'filtro de combustible')
)
```

# 3. Listar nombre y precio de todas las partes que aparezcan en más de 30 service que hayan salido (partes) más de $4.000.

```sql
SELECT p.nombre, p.precio
FROM Parte p
INNER JOIN Service_Parte sp ON (sp.codParte = p.codParte)
WHERE precio > 4000
GROUP BY p.codParte, p.nombre, p.precio
HAVING COUNT(*) > 30
```

# 4. Dar de baja todos los camiones con más de 250.000 km.

```sql
DELETE FROM Service_Parte WHERE patente IN (
    SELECT patente
    FROM Vehículo
    WHERE km > 250000
)

DELETE FROM Service WHERE patente IN (
    SELECT patente
    FROM Vehículo
    WHERE km > 250000
)

DELETE FROM Camión WHERE patente IN (
    SELECT patente
    FROM Vehículo
    WHERE km > 250000
)

DELETE FROM Vehículo WHERE km > 250000
```

# 5. Listar el nombre y precio de aquellas partes que figuren en todos los service realizados en el corriente año.

*// La consulta por el año siempre va en el último NOT EXISTS*

```sql
SELECT p.nombre, p.precio
FROM Parte p
WHERE NOT EXISTS (
    SELECT *
    FROM Service s
    WHERE AND NOT EXISTS (
        SELECT * FROM Service_Parte sp
        WHERE (sp.codParte = p.codParte)
        AND ((s.patente = sp.patente) AND (s.fecha = sp.fecha))
        AND (s.fecha BETWEEN '2022-01-01' AND '2022-12-31') 
    )
)
```

# 6. Listar todos los autos cuyo tipo de motor sea eléctrico. Mostrar información de patente, modelo , marca y peso.

```sql
SELECT a.patente, v.modelo, v.marca, v.peso
FROM Auto a
INNER JOIN Vehículo v ON (v.patente = a.patente)
WHERE a.es_eléctrico = True
```

# 7. Dar de alta una parte, cuyo nombre sea “Aleron” y precio $6400.

```sql
INSERT INTO Parte VALUES (
    nombre: 'Alerón',
    precio: 6400.00
)
```

# 8. Dar de baja todos los services que se realizaron al auto con patente ‘AWA564’.

```sql
DELETE FROM Service_Parte WHERE patente = 'AWA564'

DELETE FROM Service WHERE patente = 'AWA564'
```

# 9. Listar todos los vehículos que hayan tenido services durante el 2018.
 
*// DISTINCT por si hubiese hecho más de un Service en 2018*

```sql
SELECT DISTINCT v.*
FROM Vehículo v
INNER JOIN Service s ON (s.patente = v.patente)
WHERE s.fecha BETWEEN '2018-01-01' AND '2018-12-31'
```