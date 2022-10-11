*// En estos no sé si las mayúsculas son porque son las ID o si es una trampa para que el prod. natural no funcione porque es case-sensisite (CONSULTAR)*

Agencia (RAZON_SOCIAL, dirección, telef, e-mail)

Ciudad (CODIGOPOSTAL, nombreCiudad, añoCreación)

Cliente (DNI, nombre, apellido, teléfono, dirección)

Viaje(FECHA,HORA,DNI, cpOrigen (Fk), cpDestino (Fk), razon_social(Fk), descripcion) *// cpOrigen y cpDestino corresponden a la ciudades origen y destino del viaje*

# 1. Eliminar el cliente con DNI:25326992.

Cliente <= Cliente - ( σ <sub>DNI=25326992</sub> (Cliente))

# 2. Listar datos personales de clientes que solo realizaron viajes locales.(En cada viaje realizado coincide la localidad origen con la destino, cpOrigen y cpDestino).

Cliente |x| [ π<sub>DNI</sub> ( σ <sub>cpOrigen=cpDestino</sub> (Viaje)) ]

# 3. Listar información de agencias que no tengan viajes para el cliente DNI:22222222 durante el primer semestre de 2020.

*// CONSULTAR*

AgenciasCliente <= σ <sub>Agencia.RAZON_SOCIAL = Viaje.razon_social</sub> (Agencia x [π<sub>RAZON_SOCIAL</sub> (σ <sub>fecha >= '2020/01/01' and fecha <= '2020/07/31' and DNI = 22222222</sub> (Viaje)) ])

Agencia - AgenciasCliente

# 4. Listar información de agencias que realizaron viajes durante 2019 y no realizaron viajes durante 2020.

Agencia2019 <= σ <sub>Agencia.RAZON_SOCIAL = Viaje.razon_social</sub> (Agencia x [ π <sub>RAZON_SOCIAL</sub> (σ <sub>fecha >= '2019/01/01' and fecha <= '2019/12/31'</sub> (Viaje)) ])

Agencia2020 <= σ <sub>Agencia.RAZON_SOCIAL = Viaje.razon_social</sub> (Agencia x [ π <sub>RAZON_SOCIAL</sub> (σ <sub>fecha >= '2020/01/01' and fecha <= '2020/12/31'</sub> (Viaje)) ])

π <sub>Agencia.RAZON_SOCIAL, Agencia.dirección, Agencia.telef, Agencia.e-mail</sub> (Agencia2019 - Agencia2020)

# 5. Agregar una agencia de viajes con los datos que desee.

Agencia <= Agencia U ({*12345, "50 y 120", "221 123-4567", "viajes@agencia.com"*})

# 6. Listar datos personales de clientes que viajaron con ciudad destino ‘Lincoln’ pero no realizaron viajes con origen ‘La Plata’.

ClientesDestLincoln <= Cliente |x| (π <sub>DNI</sub> [ σ <sub>Viaje.cpDestino = Ciudad.CODIGOPOSTAL</sub> (Viaje x [ π <sub>CODIGOPOSTAL</sub> (σ <sub>nombreCiudad = 'Lincoln'</sub> (Ciudad)) ]) ])

ClientesOrgLaPlata <= Cliente |x| (π <sub>DNI</sub> [ σ <sub>Viaje.cpOrigen = Ciudad.CODIGOPOSTAL</sub> (Viaje x [ π <sub>CODIGOPOSTAL</sub> (σ <sub>nombreCiudad = 'La Plata'</sub> (Ciudad)) ]) ])

ClientesDestLincoln - ClientesOrgLaPlata

# 7. Listar nombre, apellido, dirección y teléfono de clientes que viajaron con todas las agencias.

*// No sé si está bien el |x| anidado (CONSULTAR)*

π <sub>nombre, apellido, dirección, teléfono</sub> (Cliente |x| Viaje |x| Agencia) % (π <sub>RAZON_SOCIAL</sub> (Agencia))

# 8. Listar código postal, nombre Ciudad y año creación de ciudades que no recibieron viajes durante 2020.

*// No sé qué tan bien está el prod. cartesiano (CONSULTAR)*

Ciudades2020 <= σ <sub>Viaje.cpDestino = Ciudad.CODIGOPOSTAL</sub> (Ciudad x [ π <sub>cpDestino</sub> ( σ <sub>fecha>='2020/01/01' and fecha<='2020/12/31'</sub> (Viaje)) ])

*// Falta proyección*
Ciudad - Ciudades2020

# 9. Reportar información de agencias que realizaron viajes durante 2019 o que tengan dirección igual a ‘General Pinto’.

Agencias2019 <=  Agencia |x| ( π <sub>razon_social</sub> [ σ <sub>fecha>='2019/01/01' and fecha<='2019/12/31'</sub> (Viaje)) ])

(Agencia - Agencias2019) U ( σ <sub>dirección = 'General Pinto'</sub> (Agencia))

# 10. Actualizar el teléfono del cliente con DNI: 2789655 a: 221-4400345.

δ teléfono <= teléfono='221-4400345'(σ <sub>fecha>='dni=2789655'</sub> (Cliente))