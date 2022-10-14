Box = (nroBox,m2, ubicación, capacidad, ocupacion) //ocupación es un numérico indicando cantidad de mascotas en el box actualmente, capacidad es una descripción.

Mascota = (codMascota,nombre, edad, raza, peso, telefonoContacto)

Veterinario = (matricula, CUIT, nombYAp, direccion, telefono)

Supervision = (codMascota,nroBox, fechaEntra, fechaSale?, matricula(fk), descripcionEstadia) //fechaSale tiene valor null si la mascota está actualmente en el box

# 1. Modificar nombre y apellido al veterinario con matricula: ‘MP 10000’, deberá llamarse: ‘Pablo Lopez’.

δ nombYAp <= nombYAp = "Pablo Lopez" ( σ <sub>matricula = 'MP 10000'</sub> (Veterinario))

# 2. Listar nombre, edad, raza, peso de mascotas que tengan supervisiones con el veterinario con matricula : ‘MP 1000’ y con el veterinario con matricula: ‘MN 4545’.

MascotasMP1000 <= Mascota |x| [ π <sub>codMascota</sub> ( σ <sub>matricula = 'MP 1000'</sub> (Supervision)) ]

MascotasMN4545 <= Mascota |x| [ π <sub>codMascota</sub> ( σ <sub>matricula = 'MN 4545'</sub> (Supervision)) ]

π <sub>nombre, edad, raza, peso</sub> (MascotasMP1000 ∩ MascotasMN4545)

# 3. Listar nroBox, m2, ubicación, capacidad y nombre de mascota para supervisiones con fecha de entrada (fechaEntra) durante 2020.

*// No sé si está bien hecho el |x| entre los 3 conjuntos (CONSULTAR)*

Supervisiones2020 <= σ <sub>fechaEntra >= '2020/01/01' and fechaEntra <= '2020/12/31'</sub> (Supervisión)

π <sub>Box.nroBox, Box.m2, Box.ubicacion, Box.capacidad, Mascota.nombre</sub> [ ( π <sub>codMascota, nombre</sub> (Mascota)) |x| ( π <sub>nroBox, codMascota</sub> (Supervisiones2020)) |x| (π <sub>nroBox, ubicacion, capacidad, m2</sub> (Box)) ]