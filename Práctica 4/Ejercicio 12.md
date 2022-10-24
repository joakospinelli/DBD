Barberia = (<ins>codBarberia</ins>, razon_social, direccion, telefono)

Cliente = (<ins>nroCliente</ins>, DNI, nombYAp, direccionC, fechaNacimiento, celular)

Barbero = (<ins>codEmpleado<ins>, DNIB, nombYApB, direccionB, telefonoContacto, mail)

Atencion = (<ins>codEmpleado,Fecha,hora</ins>, codBarberia(fk), nroCliente(fk),descTratamiento, valor)

# 1. Listar DNI, nombYAp, direccionC, fechaNacimiento y celular de clientes que no tengan atención durante 2020.

```sql
SELECT DNI, nombYAp, direcciónC, fechaNacimiento, celular
FROM Cliente
WHERE nroCliente NOT IN (
    SELECT nroCliente
    FROM Atención
    WHERE fecha BETWEEN '2020-01-01' AND '2020-12-31'
)
```

# 2. Listar para cada barbero cantidad de atenciones que realizaron durante 2018. Listar DNIB, nombYApB, direccionB, telefonoContacto, mail y cantidad de atenciones.

```sql
SELECT b.dniB, b.nombYApB, b.direcciónB, b.teléfonoContacto, b.mail, COUNT(*) as cantidad
FROM Barbero b
LEFT JOIN Atención a ON (a.codEmpleado = b.codEmpleado)
GROUP BY b.codEmpleado, b.dniB, b.nombYAp, b.direcciónB, b.teléfonoContacto, b.mail
```

# 3. Listar razón social, dirección y teléfono de barberias que tengan atenciones para el cliente con DNI: 22283566. Ordenar por razón social y dirección ascendente.

```sql
SELECT bb.razón_social, bb.dirección, bb.teléfono
FROM Barbería bb
INNER JOIN Atención a ON (a.codBarbería = bb.codBarbería)
INNER JOIN Cliente c ON (c.nroCliente = a.nroCliente)
WHERE c.dni = '22283566'
ORDER BY bb.razón_social, bb.dirección
```

# 4. Listar DNIB, nombYApB, direccionB, telefonoContacto y mail de barberos que tengan atenciones con valor superior a 5000.

```sql
SELECT b.dniB, b.nombYApB, b.direcciónB, b.teléfonoContacto, b.mail
FROM Barbero b
INNER JOIN Atención a ON (a.codEmpleado = b.codEmpleado)
GROUP BY b.codEmpleado, b.nombYAp, b.direcciónB, b.teléfonoContacto, b.mail
HAVING SUM(a.valor) > 5000
```

# 5. Listar DNI, nombYAp, direccionC, fechaNacimiento y celular de clientes que tengan atenciones en la barbería con razón social: ‘Corta barba’ y también se hayan atendido en la barbería con razón social: ‘Barberia Barbara’.

```sql
(
    SELECT c.dni, c.nombYAp, c.direcciónC, c.fechaNacimiento, c.celular
    FROM Cliente c
    INNER JOIN Atención a ON (a.nroCliente = c.nroCliente)
    INNER JOIN Barbería bb ON (bb.codBarberia = a.codBarbería)
    WHERE bb.razon_social = 'Corta Barba'
)
INTERSECT
(
    SELECT c.dni, c.nombYAp, c.direcciónC, c.fechaNacimiento, c.celular
    FROM Cliente c
    INNER JOIN Atención a ON (a.nroCliente = c.nroCliente)
    INNER JOIN Barbería bb ON (bb.codBarberia = a.codBarbería)
    WHERE bb.razon_social = 'Barbería Bárbara'
)
```

# 6. Eliminar el cliente con DNI: 22222222.

```sql
DELETE FROM Atención
WHERE nroCliente = (
    SELECT nroCliente
    FROM Cliente
    WHERE dni = '22222222'
)

DELETE FROM Cliente
WHERE dni = '22222222'
```
