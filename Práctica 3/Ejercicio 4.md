Equipo(codigoE, nombreE, descripcionE)
Integrante (DNI, nombre, apellido,ciudad,email, telefono,codigoE(fk))
Laguna(nroLaguna, nombreL, ubicación,extension, descripción)
TorneoPesca(codTorneo, fecha,hora,nroLaguna(fk), descripcion)
Inscripcion(codTorneo,codigoE,asistio, gano) *// asistio y gano son true o false según corresponda*

# 1. Listar DNI, nombre, apellido y email de integrantes que sean de la ciudad ‘La Plata’ y estén inscriptos en torneos que se disputaron durante 2019.

π <sub>DNI, nombre, apellido, email</sub> ( σ<sub>ciudad = 'La Plata'</sub> (Integrante) |x| [ π <sub>Inscripcion.codigoE</sub> (Inscripción |x| [ π <sub>codTorneo</sub> ( σ <sub>fecha>='2019/01/01' and fecha<='2019/12/31'</sub> (TorneoPesca)) ]) ])

# 2. Reportar nombre y descripción de equipos que solo se hayan inscripto en torneos de 2019.

NoInscriptos2019 <= Inscripción |x| [ π <sub>codTorneo</sub> ( σ <sub>fecha<='2019/01/01' and fecha>='2019/12/31'</sub> (TorneoPesca)) ]) ])

π <sub>nombre, descripción</sub> (Equipo - NoInscriptos2019)

# 3. Listar nombre, ubicación, extensión y descripción de lagunas que hayan tenido torneos durante 2019 y no hayan tenido torneos durante 2020.

Lagunas2019 <= Laguna |x| [ π <sub>nroLaguna</sub> ( σ <sub>fecha>='2019/01/01' and fecha<='2019/12/31'</sub> (TorneoPesca)) ]

Lagunas2020 <= Laguna |x| [ π <sub>nroLaguna</sub> ( σ <sub>fecha>='2020/01/01' and fecha<='2020/12/31'</sub> (TorneoPesca)) ]

π <sub>nombreL, ubicación, extensión, descripción</sub> (Lagunas2019 - Lagunas2020)

# 4. Listar para la laguna con nombre ‘laguna x’, nombre y descripción de equipos ganadores de torneos que se disputaron durante 2019 en la mencionada laguna.

TorneosLX2019 <= σ <sub>fecha>='2019/01/01' and fecha<='2019/12/31'</sub> (TorneoPesca) |x| [ π <sub>nroLaguna</sub> (σ <sub>nombreL='laguna x'</sub> (Laguna)) ]

π <sub>nombre, descripcion</sub> [ Equipo |x| (π <sub>codigoE</sub> [ ( σ <sub>gano = true</sub> (Inscripcion)) |x| ( π <sub>codTorneo</sub> (TorneosLX2019)) ]) ]

# 5. Reportar nombre, y descripción de equipos que tengan inscripciones en todas las lagunas.



# 6. Eliminar el equipo con código: 10000.



# 7. Listar nombreL, ubicación,extensión y descripción de lagunas que no tuvieron torneos.



# 8. Reportar nombre, y descripción de equipos que tengan inscripciones a torneos a disputarse durante 2019, pero no tienen inscripciones a torneos de 2020.



# 9. Listar DNI, nombre, apellido, ciudad y email de integrantes que asistieron o ganaron algún torneo que se disputó en la laguna con nombre: ‘Laguna Brava’.
