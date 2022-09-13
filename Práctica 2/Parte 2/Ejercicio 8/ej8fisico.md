# Modelo físico

Persona (<u>DNI</u>, Nombre y apellido, Dirección, Email?)

Teléfono (<u>Número</u>, DNI Persona(Fk))

Fisiatra (<u>DNI</u>, Matrícula)

Paciente (<u>DNI</u>, Peso, Estatura, Fecha nacimiento, DNI Fisiatra(Fk))

Historia clínica (<u>DNI Paciente</u>, Resumen)

Lesión (<u>Nombre</u>)

Historia lesiones (<u>Nombre lesión, DNI Paciente</u>, Fecha)