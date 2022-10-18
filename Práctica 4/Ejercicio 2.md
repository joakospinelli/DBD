AGENCIA (<u>RAZON_SOCIAL</u>, dirección, telef, e-mail)

CIUDAD (<u>CODIGOPOSTAL</u>, nombreCiudad, añoCreación)

CLIENTE (<u>DNI</u>, nombre, apellido, teléfono, dirección)

VIAJE(<u>FECHA,HORA, DNI</u>, cpOrigen(fk), cpDestino(fk), razon_social(fk), descripcion)
*//cpOrigen y cpDestino corresponden a la ciudades origen y destino del viaje*

# 1. Listar razón social, dirección y teléfono de agencias que realizaron viajes desde la ciudad de ‘La Plata’ (ciudad origen) y que el cliente tenga apellido ‘Roma’. Ordenar por razón social y luego por teléfono.

```sql
SELECT a.razon_social,a.direccion,a.telef
FROM Agencia a
INNER JOIN Viaje v ON (v.razon_social=a.razon_social)
INNER JOIN Cliente c ON (c.dni=v.dni)
INNER JOIN Ciudad ci ON (ci.codigoPostal=c.cpOrigen)
WHERE (ci.nombre='La Plata') AND (c.apellido='Roma')
```

# 2. Listar fecha, hora, datos personales del cliente, ciudad origen y destino de viajes realizados en enero de 2019 donde la descripción del viaje contenga el String ‘demorado’.

```sql
SELECT v.fecha,v.hora,cli.nombre,cli.apellido,cli.dni,co.nombreCiudad,cd.nombreCiudad
FROM Viaje v
INNER JOIN Ciudad co ON (v.cpOrigen=co.codigo_postal)
INNER JOIN Ciudad cd ON (v.cpDestino=cd.cpDestino)
INNER JOIN Cliente cli ON (v.dni=cli.dni)
WHERE (v.fecha BETWEEN '2019-01-01' AND '2019-12-31') AND (v.descripción LIKE '%demorado%')
```

# 3. Reportar información de agencias que realizaron viajes durante 2019 o que tengan dirección de mail que termine con ‘@jmail.com’.

```sql
SELECT *
FROM Agencia a
INNER JOIN Viaje v ON (a.razon_social=v.razon_social)
WHERE (v.fecha BETWEEN '2019-01-01' AND '2019-12-31') OR (a.email LIKE '%@jmail.com')
```

# 4. Listar datos personales de clientes que viajaron solo con destino a la ciudad de ‘Coronel Brandsen’

```sql
SELECT cli.nombre,cli.apellido,cli.dni,cli.dirección,cli.teléfono
FROM Cliente cli
INNER JOIN Viaje v ON (v.dni = cli.dni)
INNER JOIN Ciudad c ON (c.codigo_postal = v.cpDestino)
WHERE c.nombreCiudad = 'Coronel Brandsen'
EXCEPT (
    SELECT cli.nombre,cli.apellido,cli.dni,cli.dirección,cli.teléfono
    FROM Cliente cli
    INNER JOIN Viaje v ON (v.dni = cli.dni)
    INNER JOIN Ciudad c ON (c.codigo_postal = v.cpDestino)
    WHERE NOT (c.nombreCiudad = 'Coronel Brandsen')
) 
```

# 5. Informar cantidad de viajes de la agencia con razón social ‘TAXI Y’ realizados a ‘Villa Elisa’.

```sql
SELECT COUNT(*)
FROM Agencia a
INNER JOIN Viaje v WHERE (v.razon_social=a.razon_social)
INNER JOIN Ciudad c WHERE (c.codigopostal=v.cpDestino)
WHERE (c.nombreCiudad='Villa Elisa') AND (a.razon_social='TAXI Y')
```

# 6. Listar nombre, apellido, dirección y teléfono de clientes que viajaron con todas las agencias.

# 7. Modificar el cliente con DNI: 38495444 actualizando el teléfono a: 221-4400897.

# 8. Listar razon_social, dirección y teléfono de la/s agencias que tengan mayor cantidad de viajes realizados.

# 9. Reportar nombre, apellido, dirección y teléfono de clientes con al menos 10 viajes.

# 10. Borrar al cliente con DNI 40325692.
