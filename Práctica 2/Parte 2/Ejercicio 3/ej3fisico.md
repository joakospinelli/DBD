# Modelo físico

Empleado (<ins>DNI</ins>, CUIT, Nombre, Apellido)

No Docente (<ins>DNI(Fk)</ins>, Categoría)

Historial (<ins>DNI No Docente, Código área, Fecha inicio</ins>, Fecha fin?)

Área(<ins>Código</ins>, Nombre, Descripción)

Teléfonos Área (<ins>Código área, Nro. teléfono</ins>)

Teléfono (<ins>Número</ins>)

Docente (<ins>Legajo</ins>, DNI(Fk), Código Materia(Fk))

Materia (<ins>Código</ins>, Descripción)