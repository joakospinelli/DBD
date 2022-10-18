Proyecto(codProyecto, nombrP,descripcion, fechaInicioP, fechaFinP,
fechaFinEstimada,DNIResponsable(fk), equipoBackend(fk), equipoFrontend(fk)) *// DNIResponsable corresponde a un empleado, equipoBackend y equipoFrontend corresponden a un equipo*

Equipo(codEquipo, nombreE, descripcionTecnologias,DNILider(fk)) *// DNILider corresponde a un empleado*

Empleado(DNI,nombre, apellido, telefono, direccion, fechaIngreso)

Empleado_Equipo(codEquipo,DNI, fechaInicio, fechaFin,descripcionRol)

# 1. Listar nombre, descripción, fecha de inicio y fecha de fin de proyectos ya finalizados que no fueron terminados antes de la fecha de fin estimada.

*// No sé cómo ver si el proyecto está terminado (CONSULTAR)*

σ <sub>(fechaFinP < fechaFinEstimada)</sub> (Proyecto)

# 2. Listar DNI, nombre, apellido, teléfono, dirección y fecha de ingreso de empleados que no hayan sido responsables de proyectos.

EmpleadosLideres <= σ <sub>Empleado.DNI=DNILider</sub> [ Empleado x ( π <sub>DNILider</sub> (Equipo)) ]

π <sub>DNI, nombre, apellido, teléfono, direccion, fechaIngreso</sub> (Empleado - EmpleadosLideres)

# 3. Listar DNI, nombre, apellido, teléfono y dirección de todos los empleados que trabajan en el proyecto con nombre ‘Proyecto X’. No es necesario informar responsable y líderes.

EquipoFrontend <= σ <sub>Empleado_Equipo.codEquipo=equipoFrontend</sub> ( Empleado_Equipo x [ π <sub>equipoFrontend</sub> ( σ <sub>nombrP='Proyecto X'</sub> (Proyecto)) ])

EquipoBackend< = σ <sub>Empleado_Equipo.codEquipo=equipoBackend</sub> ( Empleado_Equipo x [ π <sub>equipoBackend</sub> ( σ <sub>nombrP='Proyecto X'</sub> (Proyecto)) ])

π <sub>DNI, nombre, apellido, teléfono, dirección</sub> ( Empleado |x| [ ( π <sub>DNI</sub> (EquipoFrontend)) U ( π <sub>DNI</sub> (EquipoBackend)) ])

# 4. Listar nombre de equipo y datos personales de líderes de equipos que no tengan empleados asignados y trabajen con tecnología ‘Java’.

EquiposJava <= σ <sub>descripcionTecnologias='Java'</sub> (Equipo)

EquiposConEmpleados <= Equipo |x| [( π <sub>codEquipo</sub> [ Empleado_Equipo |x| ( π <sub>dni</sub> (Empleado)) ] )

π <sub>EquiposVacios.nombreE, Empleado.DNI, Empleado.nombre, Empleado.apellido, Empleado.direccion</sub> [ σ <sub>Empleado.DNI=EquiposVacios.DNILider</sub> (Empleado x ρ <sub>EquiposVacios</sub> [ EquiposJava U (Equipo - EquiposConEmpleados) ]) ]

# 5. Modificar nombre, apellido y dirección del empleado con DNI: 40568965 con los datos que desee.

*// No sé si está bien la modificación de varios campos (CONSULTAR)*

δ <sub>nombre,apellido,dirección</sub> <= nombre='AAA',apellido='BBB',direccion='1 1234' (σ <sub>dni=40568965</sub> (Empleado))

# 6. Listar DNI, nombre, apellido, teléfono y dirección de empleados que son responsables de proyectos pero no han sido líderes de equipo.

Responsables <= π <sub>DNI,nombre,apellido, telefono,direccion, echaIngreso</sub> (σ <sub>Proyecto.DNIResponsable = Empleado.DNI</sub> [ Empleado x (π <sub>DNIResponsable</sub> (Proyecto)) ])

Líderes <= π <sub>DNI,nombre,apellido,telefono,direccion,fechaIngreso</sub> (σ <sub>Equipo.DNILider = Empleado.DNI</sub> [ Empleado x (π <sub>DNILider</sub> (Equipo)) ])

π <sub>DNI,nombre,apellido,telefono,direccion</sub> (Responsables - Líderes)

# 7. Listar nombre de equipo y descripción de tecnologías de equipos que hayan sido asignados como equipos frontend y backend.

EquiposFrontend <= π <sub>codEquipo,nombre,descripcionTecnologías</sub> ( σ <sub>Equipo.codEquipo = Proyecto.equipoFrontend</sub> [ Equipo x ( π <sub>equipoFrontend</sub> (Proyecto)) ])

EquiposBackend <= π <sub>codEquipo,nombre,descripcionTecnologías</sub> ( σ <sub>Equipo.codEquipo = Proyecto.equipoBackend</sub> [ Equipo x ( π <sub>equipoBackend</sub> (Proyecto)) ])

π <sub>nombre,descripcionTecnologías</sub> (EquiposFrontend U EquiposBackend)

# 8. Listar nombre, descripción, fecha de inicio, nombre y apellido de responsables de proyectos a finalizar durante 2019.

π <sub>EP.nombreP,EP.descripción,EP.fechaInicioP,EP.nombre,EP.apellido</sub> ( ρ EP [ σ <sub>Empleado.DNI = Proyecto.DNIResponsable</sub> ( Empleado x [ π <sub>DNIResponsable</sub> ( σ <sub>(fechaFinP>='2019/01/01') and (fechaFinP<='2019/12/31')</sub> (Proyecto)) ]) ])

# 9. Listar nombre de equipo, descripción de tecnología y la información personal del líder, de equipos que no estén asignados a ningún proyecto aun.

EquiposAsignados <= π <sub>codEquipo, nombreE, descripcionTecnologias, DNILider</sub> ( σ <sub>(codEquipo=equipoFrontend) or (codEquipo=equipoBackend)</sub> [ Equipo x ( π <sub>equipoFrontend,equipoBackend</sub> (Proyecto)) ])

π <sub>nombreE, descripcionTecnologías, nombre, apellido, telefono, dirección</sub> ( σ <sub>DNI=DNILider</sub> [ Empleado x (Equipo - EquiposAsignados) ])