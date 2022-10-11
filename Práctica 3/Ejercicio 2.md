*// El modelo físico no tenía las IDs en el enunciado así que pongo las que me parecen a mí*

Banda(<ins>codigoB</ins>, nombreBanda, genero_musical, año_creacion)

Integrante (<ins>DNI</ins>, nombre, apellido,dirección, email, fecha_nacimiento, codigoB(fk))

Escenario(<ins>nroEscenario</ins>, nombre_escenario, ubicación,cubierto, m2, descripción)

Recital(<ins>fecha, hora, nroEscenario</ins>, codigoB(fk))

# 1. Listar datos personales de integrantes con apellido ‘Garcia’ o fecha de nacimiento anterior a 2005 que toquen en bandas de rock and roll.

π <sub>DNI, nombre, apellido, direccion, email, fecha_nacimiento</sub> (σ <sub>apellido = 'Garcia' or fecha_nacimiento < '2005/01/01'</sub> (Integrante) |x| (π <sub>codigoB [σ genero_musical = 'rock and roll'</sub> (Banda)) ])

# 2. Listar nombre de escenario, ubicación y descripción de escenarios que no tuvieron recitales durante 2019.

Escenarios2019 <= Escenario |x| [ π <sub>nroEscenario</sub> (σ <sub>fecha >= '2019/01/01' and fecha <= '2019/12/31'</sub> (Recital)) ]

π <sub>nombre_escenario,ubicación,descripción</sub> (Escenario - Escenarios2019)

# 3. Listar nombre de escenario, ubicación y descripción de escenarios que tuvieron recitales con género musical rock and roll o tuvieron recitales durante 2020.

EscenariosRock <= Escenario |x| (π <sub>nroEscenario</sub> (Recital |x| (π <sub>codigoB [σ genero_musical = 'rock and roll'</sub> (Banda)] )))

Escenarios2020 <= Escenario |x| (π <sub>nroEscenario</sub> [ σ <sub>fecha >= '2020/01/01' and fecha <= '2020/12/31'</sub> (Recital) ] )

π <sub>nombre_escenario, ubicación, descripción</sub> (EscenariosRock U Escenarios2020)

*// Otra solución usando el prod. cartesiano*

π <sub>nombre_escenario, ubicación, descripción</sub> (Escenario |x| (π <sub>nroEscenario</sub> [σ <sub>Recital.codigoB = Banda.codigoB or (Recital.fecha >= '2020/01/01' and Recital.fecha <= '2020/12/31')</sub> (Recital x (π <sub>codigoB</sub> [σ <sub>genero_musical = 'rock and roll'</sub> (Banda) ]) ]) )

# 4. Listar nombre, género musical y año de creación de bandas que hayan realizado recitales en escenarios cubiertos durante 2019 *(cubierto es true o false según corresponda)*

π <sub>nombreBanda, género_musical, año_creación</sub> (Banda |x| [ π <sub>Recital.codigoB</sub> (σ <sub>fecha >= '2019/01/01' and fecha <= '2019/12/31'</sub> (Recital) |x| [ π <sub>nroEscenario</sub> (σ <sub>cubierto = true</sub> (Recital)) ]) ])

# 5. Listar DNI, nombre, apellido,dirección y email de integrantes nacidos entre 2000 y 2005 y que toquen en bandas con género pop que hayan tenido recitales durante 2020.

*// Acá creo que no hace falta que busque por Banda  porque Integrante y Recital también comparten 'codigoB' (CONSULTAR)*

π <sub> DNI, nombre, apellido, dirección, email </sub>([ σ fecha_nacimiento >= '2020/01/01' and fecha_nacimiento <= '2020/12/31'(Integrante)] |x| π <sub>codigoB</sub> [ σ <sub>fecha >= '2020/01/01' and fecha <= '2020/12/31'</sub> (Recital) ])

# 6. Listar DNI, nombre, apellido,email de integrantes que hayan tocado en el escenario con nombre ‘Gustavo Cerati’ y no hayan tocado en el escenario con nombre ‘Carlos Gardel’.

IntegrantesCerati <= (Integrante |x| [ π <sub> codigoB </sub>(Recital |x| π<sub> nroEscenario </sub>[σ <sub>nombre_escenario = 'Gustavo Cerati'</sub> (Escenario) ]) ])

IntegrantesGardel <= (Integrante |x| [ π <sub> codigoB</sub>(Recital |x| π<sub> nroEscenario </sub>[σ <sub>nombre_escenario = 'Carlos Gardel'</sub> (Escenario) ]) ])

π <sub>DNI, nombre, apellido, email</sub> (IntegrantesCerati - IntegrantesGardel)

# 7. Modificar el año de creación de la banda de nombre ‘Ratones Paranoicos’ a: 1983.

*// No sé si está bien escrita la modificación (CONSULTAR)*

δ <sub>año_creacion</sub> <= año_creacion = 1983 (σ <sub>nombreBanda = ‘Ratones Paranoicos’</sub> (Banda))

*// Probar hacerlo con unión y diferencia*

# 8. Reportar nombre, género musical y año de creación de bandas que hayan realizado recitales durante 2019, y además hayan tocado durante 2020.

Bandas2019 <= Banda |x| π <sub>codigoB</sub> [σ <sub>fecha >= '2019/01/01' and fecha <= '2019/12/31'</sub> (Recital) ]

Bandas2020 <= Banda |x| π <sub>codigoB</sub> [σ <sub>fecha >= '2020/01/01' and fecha <= '2020/12/31'</sub> (Recital) ]

π <sub>nombreBanda, genero_musical, año_creacion</sub> (Bandas2019 ∩ Bandas2020)

# 9. Listar el cronograma de recitales del dia 04/12/2019. Se deberá listar: nombre de la banda que ejecutará el recital, fecha, hora, y el nombre y ubicación del escenario correspondiente.

π <sub>nombreBanda, fecha, hora, nombre_escenario, ubicación</sub> (σ <sub>Recital.nroEscenario = Escenario.nroEscenario</sub> [ Escenario x ( σ <sub>Banda.codigoB = Recital.codigoB</sub> [Banda x σ <sub>fecha = '2019/12/04'</sub> (Recital) ]) ])

*// Este en realidad no sé por qué usé el prod. cartesiano en vez del prod. natural (CONSULTAR SI LAS 2 ESTÁN BIEN)*

π <sub>nombreBanda, fecha, hora, nombre_escenario, ubicación</sub> (Banda |x| [ (π <sub>nroEscenario, nombre_escenario, ubicación</sub> (Escenario) ) |x| [π <sub>nroEscenario, codigoB, fecha, hora</sub> (σ <sub>fecha = '2019/12/04'</sub> (Recital)) ] ])
