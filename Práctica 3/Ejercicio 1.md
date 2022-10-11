Cliente(<ins>idCliente</ins>, nombre, apellido, DNI, telefono, direccion)

Factura (<ins>nroTicket</ins>, total, fecha, hora,idCliente(Fk))

Detalle(<ins>nroTicket, idProducto</ins>, cantidad, preciounitario)

Producto(<ins>idProducto</ins>, descripcion, precio, nombreP, stock)

# 1. Listar nombre, apellido, DNI, teléfono y dirección de clientes con DNI superior a 22222222.

π <sub>nombre,apellido,dni,teléfono,dirección</sub> (σ <sub>dni > 22222222</sub> (Cliente) )

# 2. Listar nombre, apellido, DNI, teléfono y dirección de clientes con DNI superior a 22222222 y que tengan facturas cuyo total no supere los $100000.

π <sub>nombre,apellido,dni,teléfono,dirección</sub> ([ π <sub>idCliente</sub> (σ <sub>total <= 100000</sub> (Factura)) ] |x| [ σ (<sub>dni > 22222222</sub> (Cliente)) ])

# 3. Listar nombre, apellido, DNI, teléfono y dirección de clientes que realizaron compras durante 2020.

π <sub>nombre,apellido,dni,teléfono,dirección</sub> [ π <sub>idCliente</sub> (σ <sub>fecha >= '2020/01/01' and fecha =< '2020/12/31'</sub> (Factura)) |x| Cliente ]

# 4. Listar nombre, apellido, DNI, teléfono y dirección de clientes que no realizaron compras durante 2020.

Clientes2020 <= Cliente |x| π <sub>idCliente</sub> (σ <sub>fecha >= '2020/01/01' and fecha =< '2020/12/31'</sub> (Factura) )

π <sub>nombre,apellido,DNI,teléfono,dirección</sub> ([Cliente |x| π <sub>idCliente</sub>(Factura)] - Clientes2020)

*// Tengo que hacer lo de "Cliente |x| π <sub>idCliente</sub>(Factura)" antes de la diferencia porque podría tener clientes que no compraron nunca*

# 5. Listar nombre, apellido, DNI, teléfono y dirección de clientes que solo tengan compras durante 2020.

π <sub>DNI, nombre, apellido, teléfono, dirección</sub> (Cliente - [NoCompraron2020 |x| π <sub>idCliente</sub> (Factura)])


# 6. Listar nombre, descripción, precio y stock de productos no vendidos.

productosVendidos <= Producto |x| [π <sub>idProducto</sub> (Detalle) ]

π <sub>nombreP,descripcion,precio,stock</sub> (Producto - productosVendidos)

# 7. Listar nombre, apellido, DNI, teléfono y dirección de clientes que no compraron el producto con nombre ‘ProductoX’ durante 2020.

VentasProductoX <= Detalle |X| [𝝅 <sub>idProducto</sub> (𝛔 <sub>nombreP = ‘ProductoX’</sub> (Producto)]

FacturasProdX2020 <= [𝛔 <sub>fecha >= '2020/01/01' and fecha <= '2020/12/31'</sub> (Factura) ] |x| [𝝅 <sub>nroTicket</sub> (VentasProductoX) ]

ClientesProdX2020 <= Cliente |x| [ 𝝅 <sub>idCliente</sub> (FacturasProdX2020) ]

𝝅 <sub>nombre, apellido, DNI, teléfono, dirección</sub> (Cliente - ClientesProdX2020)

# 8. Listar nombre, apellido, DNI, teléfono y dirección de clientes que compraron el producto con nombre ‘Producto A’’ y no compraron el producto con nombre ‘Producto B’.

FacturasProductoA <= π <sub>nroTicket</sub> ([ Detalle |x| ( π <sub>idProducto</sub> [σ <sub>nombreP = 'Producto A'</sub> (Producto)] ) ])

FacturasProductoB <= π <sub>nroTicket</sub> ([ Detalle |x| ( π <sub>idProducto</sub> [σ <sub>nombreP = 'Producto B'</sub> (Producto)] ) ])

CompraronProdA <= Cliente |x| (π <sub>idCliente</sub> (FacturasProductoA) )

CompraronProdB <= Cliente |x| (π <sub>idCliente</sub> (FacturasProductoB) )

π <sub>nombre, apellido, DNI, teléfono, dirección</sub> (CompraronProdA - CompraronProdB)

# 9. Listar nroTicket, total, fecha, hora y DNI del cliente, de aquellas facturas donde se haya comprado el producto ‘Producto C’.

FacturasProductoC <= Factura |x| ( [ π <sub>nroTicket, idProducto</sub> (Detalle) ] |x| [ π <sub>idProducto</sub> ([ σ <sub>nombreP = 'Producto C'</sub> (Producto) ]) ])

π <sub>nroTicket, total, fecha, hora, DNI</sub> ([ π <sub>idCliente, DNI</sub> (Cliente) ] |x| FacturasProductoC)


# 10. Agregar un producto con id de producto 1000, descripción “mi producto”, precio $10000, nombreP “producto Z” y stock 1000. Se supone que el idProducto 1000 no existe.

Producto <= Producto U ( *{1000, "mi producto", 10000, "producto Z", 1000}* )