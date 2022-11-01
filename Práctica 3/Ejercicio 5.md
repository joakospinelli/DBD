Club(codigoClub, nombre, anioFundacion, codigoCiudad(FK))

Ciudad(codigoCiudad, nombre)

Estadio(codigoEstadio, codigoClub(FK), nombre, direccion)

Jugador(dni, nombre, apellido, edad, codigoCiudad(FK))

ClubJugador(codigoClub, dni, desde, hasta)

# 1. Reportar nombre y año de fundación de clubes de la ciudad de La Plata, además del nombre y dirección del estadio del mismo.

ClubesLaPlata <= Club |x| [ π <sub>codigoCiudad</sub> ( σ <sub>nombre='La Plata'</sub> (Ciudad)) ]

π <sub>ClubesLaPlata.nombre, anioFundacion, Estadio.nombre, descripcion</sub> (ClubesLaPlata |x| Estadio)

# 2. Listar datos personales de jugadores actuales del club River Plate que hayan jugado en el club Boca Juniors.

JugadoresRiver <= Jugador |x| [ π <sub>dni</sub> ( ClubJugador |x| [ π<sub>codigoClub</sub> ( σ <sub>nombre='River Plate'</sub> (Club)) ]) ]

JugadoresBoca <= Jugador |x| [ π <sub>dni</sub> ( ClubJugador |x| [ π<sub>codigoClub</sub> ( σ <sub>nombre='Boca Juniors'</sub> (Club)) ]) ]

JugadoresRiver ∩ JugadoresBoca

# 3. Listar información de todos los clubes donde se desempeñó el jugador: Marcelo Gallardo. Indicar nombre, año de fundación y localidad del club.

JugoGallardo <= ClubJugador |x| [ π <sub>dni</sub> ( σ <sub>nombre='Marcelo Gallardo'</sub> (Jugador)) ]

*// Acá tendría que hacer otro prod. con la ciudad para sacar el nombre pero no tengo ganas zzz*

π <sub>nombre, anioFundacion, codigoCiudad</sub> [ Club |x| ( π <sub>codigoClub</sub> (JugoGallardo)) ]

# 4. Reportar dni, nombre y apellido de aquellos jugadores que no tengan más de 25 años y jueguen en algún club de la ciudad de Junín.

JugadoresMenor25 <= π <sub>dni</sub> (σ <sub>edad<=25</sub> (Jugador))

JugadoresJunin <= Jugador |x| π <sub>dni</sub> (ClubJugador |x| [ π<sub>codigoCiudad</sub> (σ <sub>nombre='Junin'</sub> (Ciudad)) ])

π <sub>dni, nombre, apellido</sub> (JugadoresMenor25 ∩ JugadoresJunin)

*// Otra opción*

ClubesJunin <= Club |x| π<sub>codigoCiudad</sub> (σ <sub>nombre = 'Junin'</sub> (Ciudad))

π<sub>dni, nombre, apellido</sub> [ σ <sub>edad <= 25</sub> (Jugador) |x| π <sub>dni</sub> (ClubJugador |x| [ π<sub>codigoClub</sub> (ClubesJunin)) ]) ]

# 5. Mostrar el nombre de los clubes que tengan jugadores de la ciudad de Chivilcoy mayores de 25 años.

JugadoresMayor25 <= σ <sub>edad>25</sub> (Jugador) |x| [ π<sub>codigoCiudad</sub> ( σ <sub>nombre='Chivilcoy'</sub> (Ciudad)) ]

π<sub>Club.nombre</sub> ( Club |x| π<sub>codigoClub</sub> [ ClubJugador |x| ( π <sub>dni</sub> (JugadoresMayor25)) ])

# 6. Reportar el nombre y apellido de aquellos jugadores que hayan jugado en todos los clubes.

π<sub>nombre,apellido</sub> [ ( π <sub>nombre,apellido,dni</sub> (Jugador) |x| π <sub>dni,codigoClub</sub> (ClubJugador)) % π<sub>codigoClub</sub> (Club) ]

# 7. Listar nombre de los clubes que no hayan tenido ni tengan jugadores de la ciudad de La Plata.

JugadoresLaPlata <= Jugador |x| [ π <sub>codigoCiudad</sub> ( σ <sub>nombre='La Plata'</sub> (Ciudad)) ]

ClubesJugadoresLP <= Club |x| ( π <sub>codigoClub</sub> [ ClubJugador |x| ( π <sub>dni</sub> (JugadoresLaPlata)) ])

Club - ClubesJugadoresLP

# 8. Mostrar dni, nombre y apellido de aquellos jugadores que jugaron o juegan en el club: Club Atlético Rosario Central.

π <sub>dni, nombre, apellido</sub> (Jugador |x| [π <sub>dni</sub> (ClubJugador |x| [ π <sub>codigoClub</sub> ( σ <sub>nombre='Club Atlético Rosario Central'</sub> (Club)) ]) ])

# 9. Eliminar al jugador cuyo dni es: 24242424

Jugador <= Jugador - ( σ <sub>dni = '24242424'</sub> (Jugador))