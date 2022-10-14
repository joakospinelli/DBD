Barberia = (codBarberia, razon_social, direccion, telefono)

Cliente = (nroCliente,DNI, nombYAp, direccionC, fechaNacimiento, celular)

Barbero = (codEmpleado,DNIB, nombYApB, direccionB, telefonoContacto, mail)

Atencion = (codEmpleado,Fecha,hora,codBarberia(fk), nroCliente(fk),descTratamiento, valor)

# 1. Listar DNIB, nombYApB, direccionB, telefonoContacto y mail de barberos que tengan atenciones con valor superior a 5000.

π <sub>DNIB, NombYApB, direccionB, telefonoContacto, mail</sub> ( Barbero |x| [ π <sub>codEmpleado</sub> ( σ <sub>valor > 5000</sub> (Atencion)) ])

# 2. Listar DNI, nombYAp, direccionC, fechaNacimiento y celular de clientes que tengan atenciones en la barbería con razón social: ‘Corta barba’ y también se hayan atendido en la barbería con razón social: ‘Barberia Barbara’.

ClientesCortaBarba <= Cliente |x| ([ π <sub>codBarberia, nroCliente</sub> (Atención) ] |x| [ π <sub>codBarberia</sub> ( σ <sub>razon_social = 'Corta barba'</sub> (Barberia)) ])

ClientesBarbara <= Cliente |x| ([ π <sub>codBarberia, nroCliente</sub> (Atención) ] |x| [ π <sub>codBarberia</sub> ( σ <sub>razon_social = 'Barbería Bárbara'</sub> (Barberia)) ])

π <sub>DNI, nombYAp, direccionC, fechaNacimiento, celular</sub> (ClientesCortaBarba ∩ ClientesBarbara)

# 3. Eliminar el cliente con DNI: 22222222.

Cliente <= Cliente - (σ <sub>DNI = 22222222</sub> (Cliente))