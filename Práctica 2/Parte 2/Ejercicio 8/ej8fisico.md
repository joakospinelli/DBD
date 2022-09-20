# Modelo físico

Persona (<ins>DNI</ins>, Nombre y apellido, Dirección, Email?)

Teléfono (<ins>Número</ins>, DNI Persona(Fk))

Fisiatra (<ins>Matrícula</ins>, DNI(Fk))

Paciente (<ins>DNI(Fk)</ins>, Peso, Estatura, Fecha nacimiento, Matrícula fisiatra(Fk))

Historia clínica (<ins>DNI Paciente</ins>, Resumen)

Lesión (<ins>Nombre</ins>)

Historial lesiones (<ins>Nombre lesión, DNI Paciente</ins>, Fecha)