Cliente(<ins>idCliente</ins>, nombre, apellido, DNI, telefono, direccion)

Factura (<ins>nroTicket</ins>, total, fecha, hora,idCliente(Fk))

Detalle(<ins>nroTicket, idProducto</ins>, cantidad, preciounitario)

Producto(<ins>idProducto</ins>, descripcion, precio, nombreP, stock)

# 1. Listar nombre, apellido, DNI, teléfono y dirección de clientes con DNI superior a 22222222.

π nombre,apellido,dni,teléfono,dirección (σ dni > 22222222 (Cliente) )

# 2. Listar nombre, apellido, DNI, teléfono y dirección de clientes con DNI superior a 22222222 y que tengan facturas cuyo total no supere los $100000.

π nombre,apellido,dni,teléfono,dirección ( (π idCliente (σ total <= 100000 (Factura)) |x| (σ dni > 22222222 (Cliente) )

# 3. Listar nombre, apellido, DNI, teléfono y dirección de clientes que realizaron compras durante 2020.

π nombre,apellido,dni,teléfono,dirección (π idCliente (σ fecha >= '2020/01/01' and fecha =< '2020/12/31' (Factura)) |x| Cliente)

# 4. Listar nombre, apellido, DNI, teléfono y dirección de clientes que no realizaron compras durante 2020.

π nombre,apellido,dni,teléfono,dirección ((π idCliente (σ fecha < 01/01/2020 and fecha > 31/12/2020  (Factura))) |x| Cliente)

# 5. Listar nombre, apellido, DNI, teléfono y dirección de clientes que solo tengan compras durante 2020.

compraron2020 <= π nombre,apellido,dni,teléfono,dirección (π idCliente (σ fecha >= '2020/01/01' and fecha =< '2020/12/31' (Factura)) |x| Cliente)

noCompraron2020 <= π nombre,apellido,dni,teléfono,dirección ((π idCliente (σ fecha < '01/01/2020' and fecha > '31/12/2020'  (Factura))) |x| Cliente)

compraron2020 - noCompraron2020

# 6. Listar nombre, descripción, precio y stock de productos no vendidos.

productosVendidos <= Producto |x| (π idProducto(Detalle) )

π nombreP,descripcion,precio,stock (Producto - productosVendidos)

# 7. Listar nombre, apellido, DNI, teléfono y dirección de clientes que no compraron el producto con nombre ‘ProductoX’ durante 2020.

*// En estos creo que podría usar el %*

facturasConProdX2020 <= (σ fecha >= '2020/01/01' and fecha <= '2020/12/31' (Factura)) |x| ( π nroTicket ((Detalle) |x| (π idProducto [σ nombreP = 'ProductoX'(Producto)]) ) )

π nombre,apellido,DNI,telefono,direccion (Cliente - (Cliente |x| [π idCliente (facturasConProdX2020)] ) )

# 8. Listar nombre, apellido, DNI, teléfono y dirección de clientes que compraron el producto con nombre ‘Producto A’’ y no compraron el producto con nombre ‘Producto B’.

facturasConProdA <= (Factura)) |x| ( π nroTicket ((Detalle) |x| (π idProducto [σ nombreP = 'Producto A'(Producto)]) ) )

facturasConProdB <= (Factura)) |x| ( π nroTicket ((Detalle) |x| (π idProducto [σ nombreP = 'Producto B'(Producto)]) ) )

compraronProdA <= Cliente |x| (π idCliente (facturasConProdA) )

compraronProdB <= Cliente |x| (π idCliente (facturasConProdA) )

facturasConProdA - facturasConProdB

# 9. Listar nroTicket, total, fecha, hora y DNI del cliente, de aquellas facturas donde se haya comprado el producto ‘Producto C’.

facturasConProdC <= (Factura)) |x| ( π nroTicket ((Detalle) |x| (π idProducto [σ nombreP = 'Producto C'(Producto)]) ) )

π P.nroTicket, P.total, P.fecha, P.hora, C.dni (σ C.idCliente = P.idCliente (ρ C [π idCliente,dni (Cliente)] x ρ F (facturasConProdC) ) )

# 10. Agregar un producto con id de producto 1000, descripción “mi producto”, precio $10000, nombreP “producto Z” y stock 1000. Se supone que el idProducto 1000 no existe.

Producto <= Producto U ( *{1000, "mi producto", 10000, "producto Z", 1000}* )