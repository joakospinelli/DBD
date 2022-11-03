Club(IdClub,nombreClub,ciudad)

Complejo(IdComplejo,nombreComplejo, IdClub(fk))

Cancha(IdCancha,nombreCancha,IdComplejo(fk))

Entrenador(IdEntrenador, nombreEntrenador,fechaNacimiento, direccion)

Entrenamiento(IdEntrenamiento, fecha, IdEntrenador(fk), IdCancha(fk))

# 1- Listar nombre, fecha de nacimiento y dirección de entrenadores que hayan entrenado en la cancha “Cancha 1” y en la Cancha “Cancha 2”.

EntrenadorC1 <= Entrenador |x| [ π <sub>IdEntrenador</sub> (Entrenamiento |x| [ π <sub>IdCancha</sub> (σ <sub>nombreCancha = "Cancha 1"</sub> (Cancha)) ]) ]

EntrenadorC2 <= Entrenador |x| [ π <sub>IdEntrenador</sub> (Entrenamiento |x| [ π <sub>IdCancha</sub> (σ <sub>nombreCancha = "Cancha 2"</sub> (Cancha)) ]) ]

π <sub>nombre, fechaNacimiento, dirección</sub> (EntrenadorC1 ∩ EntrenadorC2)

# 2- Listar todos los clubes en los que entrena el entrenador “Marcos Perez”. Informar nombre del club y ciudad.

π <sub>nombreClub, ciudad</sub> (Club |x| [ π <sub>IdClub</sub> (Complejo |x| [ π <sub>IdComplejo</sub> (Cancha |x| [ π <sub>idCancha</sub> (Entrenamiento |x| [ π <sub>IdEntrenador</sub> ( σ <sub>nombreEntrenador = 'Marcos Perez'</sub> (Entrenador)) ]) ]) ]) ])

# 3- Eliminar los entrenamientos del entrenador ‘Juan Perez’.

EntrenamientosJP <= Entrenamiento |x| [ π <sub>IdEntrenador</sub> ( σ <sub>nombreEntrenador = 'Juan Perez'</sub> (Entrenador)) ]

Entrenamiento <= Entrenamiento - EntrenamientosJP