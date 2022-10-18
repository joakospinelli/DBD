Vehiculo = (patente, modelo, marca, peso, km)

Camion = (patente, largo, max_toneladas, cant_ruedas, tiene_acoplado)

Auto = (patente, es_electrico, tipo_motor)

Service = (fecha, patente, km_service, observaciones, monto)

Parte = (cod_parte, nombre, precio_parte)

Service_Parte = (fecha, patente, cod_parte, precio)

# 1. Listar todos los datos de aquellos camiones que tengan entre 8 y 12 ruedas, y que hayan realizado algún service antes de los 10000 km.

π <sub>patente,largo,max_tonelades,cant_ruedas,tiene_acoplado</sub> ([ σ <sub>(cant_ruedas>=8) and (cant_ruedas<=12)</sub> (Camión) ] |x| [ π <sub>patente</sub> ( σ <sub>km_service < 10000</sub> (Service)) ])

# 2. Listar los autos que hayan realizado el service “cambio de aceite” antes de los 13.000 km o hayan realizado el service “inspección general” que incluya la parte “filtro de combustible”.

*// No sé bien cómo buscar por las partes de un service, supongo que por fecha + patente (CONSULTAR)*

AutosAceite <= (Auto |x| [ π <sub>patente</sub> ( σ <sub>(km_service < 13000) and (observaciones="cambio de aceite")</sub> (Service)) ])

SPCombustible <= [ π <sub>cod_parte</sub> ( σ <sub>nombre="filtro de combustible"</sub> (Parte)) ] |x| ( π <sub>patente, fecha, cod_parte</sub> (Service_Parte))

AutosCombustible <= Auto |x| ( π <sub>patente</sub> [ SPCombustible |x| (σ <sub>nombre="inspección general"</sub> (Service)) ])

AutosAceite U AutosCombustible

# 3. Dar de baja todos los camiones con más de 350.000 km.

Camiones350000 <= [ π <sub>patente</sub> ( σ <sub>km > 350000</sub> (Vehiculo)) ] |x| Camion

Camion <= Camion - Camiones350000

# 4. Listar el nombre y precio de aquellas partes que figuren en todos los service realizados durante 2019.

*// Puede ser que según el orden de los conjuntos la división me de mal el resultado (CONSULTAR)*

SP2019 <= ( σ <sub>fecha >= '2019/01/01' and fecha <= '2019/12/31'</sub> (Service)) |x| ( π <sub>patente, fecha, cod_parte</sub> (Service_Parte))

π <sub>nombre, precio_parte</sub> (Parte % ( π <sub>cod_parte</sub> SP2019))

# 5. Listar todos los autos cuyo tipo de motor sea eléctrico. Mostrar información de patente, modelo, marca y peso.

π <sub>patente, modelo, marca, peso</sub> (Vehiculo |x| [ π <sub>patente</sub> ( σ <sub>es_electrico = True</sub> (Auto)) ])

# 6. Dar de alta una parte, cuyo nombre sea “Aleron” y precio $3400.

Parte <= Parte U ({*1, "Alerón", 3400*})

# 7. Dar de baja todos los services que se realizaron al auto con patente ‘AAA564’.

Service <= Service - ( σ <sub>patente = "AAA564"</sub> (Service))

# 8. Modificar el precio de las partes incrementando un 15% dicho valor.

δ precio_parte <= precio_parte * 1.15 (Parte)

# 9. Listar todos los vehículos que hayan tenido services durante el 2019.

Vehiculo % [ π <sub>patente</sub> ( σ <sub>fecha >= '2019/01/01' and fecha <= '2019/12/31'</sub> (Service)) ]