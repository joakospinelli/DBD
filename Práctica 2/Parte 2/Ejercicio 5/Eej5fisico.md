# Modelo físico

Persona (<ins>DNI</ins>, Nombre, Apellido, Email?)

Docente (<ins>DNI(Fk)</ins>, Año desde que ocupa cargo)

Alumno (<ins>Legajo</ins>, DNI(Fk))

Título (<ins>Código</ins>, Nombre)

Títulos docente (<ins>DNI Docente, Código título</ins>, Fecha)

Carrera (<ins>Código</ins>, Nombre)

Inscriptos (<ins>DNI Alumno, Código carrera</ins>)