# Modelo físico

Persona (<ins>DNI</ins>, Nombre y apellido, Dirección, Email?)

Teléfono (<ins>Número</ins>, DNI Persona(Fk))

Fisiatra (<ins>DNI</ins>, Matrícula)

Paciente (<ins>DNI</ins>, Peso, Estatura, Fecha nacimiento, DNI Fisiatra(Fk))

Historia clínica (<ins>DNI Paciente</ins>, Resumen)

Lesión (<ins>Nombre</ins>)

Historia lesiones (<ins>Nombre lesión, DNI Paciente</ins>, Fecha)