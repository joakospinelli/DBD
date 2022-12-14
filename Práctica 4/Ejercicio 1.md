Cliente(<ins>idCliente</ins>, nombre, apellido, DNI, telefono, direccion)

Factura (<ins>nroTicket</ins>, total, fecha, hora,idCliente (fk))

Detalle(<ins>nroTicket, idProducto</ins>, cantidad, preciounitario)

Producto(<ins>idProducto</ins>, descripcion, precio, nombreP, stock)

# 1. Listar datos personales de clientes cuyo apellido comience con el string ‘Pe’. Ordenar por DNI

```sql
SELECT nombre,apellido,dni,teléfono,dirección
FROM Cliente
WHERE apellido LIKE 'Pe%'
ORDER BY dni
```

# 2. Listar nombre, apellido, DNI, teléfono y dirección de clientes que realizaron compras solamente durante 2017.

```sql
SELECT cli.nombre,cli.apellido,cli.dni,cli.teléfono,cli.dirección
FROM Cliente cli
WHERE fecha BETWEEN '2017-01-01' AND '2017-12-31'
EXCEPT (
    SELECT cli.nombre,cli.apellido,cli.dni,cli.teléfono,cli.dirección
    FROM Cliente cli
    WHERE fecha NOT BETWEEN '2017-01-01' AND '2017-12-31'
)
```

# 3. Listar nombre, descripción, precio y stock de productos vendidos al cliente con DNI:45789456, pero que no fueron vendidos a clientes de apellido ‘Garcia’.

```sql
SELECT p.nombreP,p.descripción,p.precio,p.stock
FROM Producto p
NATURAL JOIN Detalle d
NATURAL JOIN Factura f
INNER JOIN Cliente c ON (c.idCliente = f.idCliente)
WHERE c.dni = 45789456
EXCEPT (
    SELECT p.nombreP,p.descripción,p.precio,p.stock
    FROM Producto p
    NATURAL JOIN Detalle d
    NATURAL JOIN Factura f
    INNER JOIN Cliente c ON (c.idCliente = f.idCliente)
    WHERE c.apellido = 'García'
)
```

# 4. Listar nombre, descripción, precio y stock de productos no vendidos a clientes que tengan teléfono con característica: 221 (La característica está al comienzo del teléfono).Ordenar por nombre.

```sql
SELECT nombreP,descripción,precio,stock
FROM Producto
WHERE idProducto NOT IN (
    SELECT idProducto
    FROM Producto p
    INNER JOIN Detalle d ON (d.idCliente=p.idCliente)
    INNER JOIN Factura f ON (f.nroTicker=d.nroTicket)
    INNER JOIN Cliente c ON (c.idCliente = f.idCliente)
    WHERE Cliente.teléfono LIKE ('221%')
)
ORDER BY nombreP
```

# 5. Listar para cada producto: nombre, descripción, precio y cuantas veces fué vendido. Tenga en cuenta que puede no haberse vendido nunca el producto.

*// Tengo que hacer un LEFT JOIN en vez de un INNER/NATURAL JOIN porque el producto puede no haberse vendido nunca*

```sql
SELECT nombreP,descripción,precio, SUM(d.cantidad)
FROM Producto p
LEFT JOIN Detalle d ON (p.idProducto=d.idProducto)
GROUP BY idProducto,nombreP,descripción,precio
```

# 6. Listar nombre, apellido, DNI, teléfono y dirección de clientes que compraron los productos con nombre ‘prod1’ y ‘prod2’ pero nunca compraron el producto con nombre ‘prod3’.

```sql
SELECT c.nombre,c.apellido,c.dni,c.teléfono,c.dirección
FROM Cliente c
INNER JOIN Detalle d ON (d.idCliente=c.idCliente)
INNER JOIN Producto p ON (p.idProducto=d.idProducto)
WHERE (p.nombreP='prod1') AND c.idCliente IN (
    SELECT c.idCliente
    FROM Cliente c
    INNER JOIN Factura f ON (f.idCliente=c.idCliente)
    INNER JOIN Detalle d ON (d.nroTicket=f.nroTicket)
    INNER JOIN Producto p ON (p.idProducto=d.idProducto)
    WHERE (p.nombreP='prod2')
) EXCEPT (
    SELECT nombre,apellido,dni,teléfono,dirección
    FROM Cliente c
    INNER JOIN Factura f ON (f.idCliente=c.idCliente)
    INNER JOIN Detalle d ON (d.nroTicket=f.nroTicket)
    INNER JOIN Producto p ON (p.idProducto=d.idProducto)
    WHERE (p.nombreP='prod3')
)
```

# 7. Listar nroTicket, total, fecha, hora y DNI del cliente, de aquellas facturas donde se haya comprado el producto ‘prod38’ o la factura tenga fecha de 2019.

```sql
SELECT f.nroTicket,f.total,f.fecha,f.hora,c.dni
FROM Factura f
INNER JOIN Cliente c ON (c.idCliente=f.idCLiente)
INNER JOIN Detalle d ON (d.nroTicket=f.nroTicket)
INNER JOIN Producto p ON (p.idProducto=d.idProducto)
WHERE (p.nombre='prod38') OR (f.fecha BETWEEN '2019-01-01' AND '2019-12-31')
```

# 8. Agregar un cliente con los siguientes datos: nombre:’Jorge Luis’, apellido:’Castor’, DNI:40578999, teléfono:221-4400789, dirección:’11 entre 500 y 501 nro:2587’ y el id de cliente: 500002. Se supone que el idCliente 500002 no existe.

```sql
INSERT INTO Cliente(
    nombre,
    apellido,
    dni,
    teléfono,
    dirección,
    idCliente)
VALUES (
    'Jorge Luis',
    'Castor',
    '40578999',
    '221-4400789',
    '11 entre 500 y 501 nro: 2587',
    500002)
```

# 9. Listar nroTicket, total, fecha, hora para las facturas del cliente ´Jorge Pérez´ donde no haya comprado el producto ´Z´.

```sql
(
    SELECT f.nroTicket, f.total, f.fecha, f.hora
    FROM Factura f
    INNER JOIN Detalle d ON (d.nroTicket=f.nroTicket)
    INNER JOIN Producto p ON (p.idProducto=f.idProducto)
    INNER JOIN Cliente c ON (c.idCliente=f.idCliente)
    WHERE (c.nombre='Jorge' AND c.apellido='Pérez')
)
EXCEPT
(
    SELECT f.nroTicket, f.total, f.fecha, f.hora
    FROM Factura f
    INNER JOIN Detalle d ON (d.nroTicket=f.nroTicket)
    INNER JOIN Producto p ON (p.idProducto=f.idProducto)
    INNER JOIN Cliente c ON (c.idCliente=f.idCliente)
    WHERE p.nombreP='Z'
)
```

*// Estaba mal poner el AND NOT (p.nombreP = 'Z') porque eso consulta por los detalles, pero las facturas tienen más de un detalle*


# 10. Listar DNI, apellido y nombre de clientes donde el monto total comprado, teniendo en cuenta todas sus facturas, supere $10.000.000.

```sql
SELECT c.dni,c.apellido,c.nombre
FROM Cliente c
INNER JOIN Factura ON (f.idCliente=c.idCliente)
GROUP BY c.dni,c.apellido,c.nombre
HAVING SUM(f.total) > 10000000 
```