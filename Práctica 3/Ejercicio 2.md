// El modelo físico no tenía las IDs en el enunciado así que pongo las que me parecen a mí

Banda(<ins>codigoB</ins>, nombreBanda, genero_musical, año_creacion)

Integrante (<ins>DNI</ins>, nombre, apellido,dirección, email, fecha_nacimiento, codigoB(fk))

Escenario(<ins>nroEscenario</ins>, nombre_escenario, ubicación,cubierto, m2, descripción)

Recital(<ins>fecha, hora, nroEscenario</ins>, codigoB(fk))

# 1. Listar datos personales de integrantes con apellido ‘Garcia’ o fecha de nacimiento anterior a 2005 que toquen en bandas de rock and roll.

π DNI, nombre, apellido, direccion, email, fecha_nacimiento (δ apellido = 'Garcia' or fecha_nacimiento < '2005/01/01' (Integrante) |x| (π codigoB (δ genero_musical = 'rock and roll' (Banda))))

# 2. Listar nombre de escenario, ubicación y descripción de escenarios que no tuvieron recitales durante 2019.

Escenarios2019 <= Escenario |x| [ π nroEscenario (δ fecha >= '2019/01/01' and fecha <= '2019/12/31' (Recital)) ]

π nombre_escenario,ubicación,descripción (Escenario - Escenarios2019)

# 3. Listar nombre de escenario, ubicación y descripción de escenarios que tuvieron recitales con género musical rock and roll o tuvieron recitales durante 2020.

EscenariosRock <= Escenario |x| (π nroEscenario (Recital |x| (π codigoB [δ genero_musical = 'rock and roll' (Banda)] )))

Escenarios2020 <= Escenario |x| (π nroEscenario [ δ fecha >= '2020/01/01' and fecha <= '2020/12/31'(Recital) ] )

π nombre_escenario, ubicación, descripción (EscenariosRock U Escenarios2020)

*// Otra solución usando el prod. cartesiano*

π <sub>nombre_escenario, ubicación, descripción</sub> (Escenario |x| (π <sub>nroEscenario</sub> [δ <sub>Recital.codigoB = Banda.codigoB or (Recital.fecha >= '2020/01/01' and Recital.fecha <= '2020/12/31')</sub> (Recital x (π <sub>codigoB</sub> [δ <sub>genero_musical = 'rock and roll'</sub> (Banda) ]) ]) )

# 4. Listar nombre, género musical y año de creación de bandas que hayan realizado recitales en escenarios cubiertos durante 2019 *(cubierto es true o false según corresponda)*

π <sub>nombreBanda, género_musical, año_creación</sub> (Banda |x| [ π <sub>Recital.codigoB</sub> (δ <sub>fecha >= '2019/01/01' and fecha <= '2019/12/31'</sub> (Recital) |x| [ π <sub>nroEscenario</sub> (δ <sub>cubierto = true</sub> (Recital)) ]) ])

# 5. Listar DNI, nombre, apellido,dirección y email de integrantes nacidos entre 2000 y 2005 y que toquen en bandas con género pop que hayan tenido recitales durante 2020.

# 6. Listar DNI, nombre, apellido,email de integrantes que hayan tocado en el escenario con nombre ‘Gustavo Cerati’ y no hayan tocado en el escenario con nombre ‘Carlos Gardel’.

# 7. Modificar el año de creación de la banda de nombre ‘Ratones Paranoicos’ a: 1983.

# 8. Reportar nombre, género musical y año de creación de bandas que hayan realizado recitales durante 2019, y además hayan tocado durante 2020.

# 9. Listar el cronograma de recitales del dia 04/12/2019. Se deberá listar: nombre de la banda que ejecutará el recital, fecha, hora, y el nombre y ubicación del escenario correspondiente.
