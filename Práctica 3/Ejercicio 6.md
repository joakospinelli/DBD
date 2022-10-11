Proyecto(codProyecto, nombrP,descripcion, fechaInicioP, fechaFinP,
fechaFinEstimada,DNIResponsable(fk), equipoBackend(fk), equipoFrontend(fk)) *// DNIResponsablecorresponde a un empleado, equipoBackend y equipoFrontend corresponden a un equipo*

Equipo(codEquipo, nombreE, descripcionTecnologias,DNILider(fk)) *// DNILider corresponde a un empleado*

Empleado(DNI,nombre, apellido, telefono, direccion, fechaIngreso)

Empleado_Equipo(codEquipo,DNI, fechaInicio, fechaFin,descripcionRol)

# 1. Listar nombre, descripción, fecha de inicio y fecha de fin de proyectos ya finalizados que no fueron terminados antes de la fecha de fin estimada.

*// No sé cómo ver si el proyecto está terminado (CONSULTAR)*

σ <sub>(fechaFinP<=Date.now) and (fechaFinP != fechaFinEstimada)</sub> (Proyecto)

# 2. Listar DNI, nombre, apellido, teléfono, dirección y fecha de ingreso de empleados que no hayan sido responsables de proyectos.

( π <sub>DNILider</sub> (Equipo))

# 3. Listar DNI, nombre, apellido, teléfono y dirección de todos los empleados que trabajan en el proyecto con nombre ‘Proyecto X’. No es necesario informar responsable y líderes.



# 4. Listar nombre de equipo y datos personales de líderes de equipos que no tengan empleados asignados y trabajen con tecnología ‘Java’.



# 5. Modificar nombre, apellido y dirección del empleado con DNI: 40568965 con los datos que desee.

δ <sub>nombre,apellido,dirección</sub> <= nombre='AAA',apellido='BBB',direccion='1 1234' (σ <sub>dni=40568965</sub> (Empleado))

# 6. Listar DNI, nombre, apellido, teléfono y dirección de empleados que son responsables de proyectos pero no han sido líderes de equipo.



# 7. Listar nombre de equipo y descripción de tecnologías de equipos que hayan sido asignados como equipos frontend y backend.



# 8. Listar nombre, descripción, fecha de inicio, nombre y apellido de responsables de proyectos a finalizar durante 2019.



# 9. Listar nombre de equipo, descripción de tecnología y la información personal del líder, de equipos que no estén asignados a ningún proyecto aun.


