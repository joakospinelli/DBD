le tengo el re miedo a desaprobar así que me hice este apunte para leerlo todas las noches antes de irme a dormir y soñar con álgebra relacional hasta aprenderlo

<img src="https://media.tenor.com/Yv2ty8bP3kgAAAAM/noooo-no.gif">

# Modelo conceptual
- Si una entidad necesita registrar a otra, con una relación (1,1) es suficiente; no hace falta ID externo (EJ: registrar el cliente de una venta; con una relación `Venta (1,1) - (0,N) Cliente` es suficiente)
- El ID externo sólo se usa si un valor varía para cada entidad de la relación anterior (EJ: si en el caso anterior la venta no tuviese un ID propio, necesita un ID externo para identificar cada venta como `Cliente, Fecha, Hora`)
- Para usar un ID externo la entidad débil (la que recibe el ID) tiene que tener cardinalidad *(1,1)* hacia la entidad que le envía el ID.
- Los ID compuestos se usan cuando no hay un ID único, pero hay una combinación de atributos no-únicos que sí es única (EJ: un número de vuelo se puede usar en días distintos; el día y el nro. no van a ser únicos, pero la combinación `Nro. vuelo, Fecha` sí va a ser)

**SIEMPRE ME OLVIDO DE ESCRIBIR LAS CARDINALIDADES. REVISAR ANTES DE ENTREGAR**

### Jerarquías:

| Nombre | Explicación | Ejemplo |
| ------ | ----------- | ------- |
| Parcial | Las especializaciones no representan a todas las entidades posibles para el problema, y se podría instancias una entidad de la superclase. | Una jerarquía de periodistas; podríamos tener como especializaciones a *Periodistas deportivos, Divulgadores*, etc pero aún así necesitamos poder representar a un Periodista normal. |
| Total | Hay una especialización para representar a todas las entidades necesarias para resolver el problema | Una jerarquía de personas en la facultad; con 3 especializaciones *(Profesor, Alumno, No Docente)* representamos a todos, por lo que todas las entidades van a pertenecer a alguno de los elementos. Nunca va a ser necesario instanciar una entidad de *"Persona"*. |
| Superpuesta | Una entidad puede funcionar como cualquier elemento de las especializaciones a la vez. | Un *Empleado* y un *Cliente* en un comercio; es superpuesta si el empleado también puede comprar. |
| Exclusiva | Una entidad sólo puede formar parte de una de las especializaciones a la vez. | Una *Moto* y un *Auto*; ambos heredan de *"Vehículo"*, pero es exclusiva porque un vehículo no puede ser ambas a la vez. |
| Subconjunto | Caso especial de jerarquía *(Parcial-Exclusiva)* en la que una jerarquía tiene una sola especialización. | Una jerarquía de calculadoras; podemos tener una única especialización que sea *"Calculadora científica"*, aunque debería poder representar entidades de Calculadora porque es una jerarquía parcial. |

**ESTO TAMBIÉN ME OLVIDO DE ESCRIBIR LAS COBERTURAS. REVISAR ANTES DE ENTREGAR**

# Modelo lógico

### Sacar atributos compuestos:

- Lo más fácil es unificarlos todos en un atributo simple, pero hay que tener cuidado por si dice que debe ser detallado o que algún campo es importante para resolver el problema (EJ: podés unir toda la dirección compuesta en un solo campo, pero si pide *"Dirección **detallada**"* no)
- La otra opción más zzz es pasar todos los atributos a la entidad, pero es útil cuando pide detalles o que los campos compuestos sirven para resolver el problema (EJ: si tenemos el atributo compuesto de *Dirección* de una persona y pide que esté detallada, en el lógico tendríamos que pasar todos los campos *"Calle, número, piso, depto"* a la entidad *"Persona"*.

**EN LA TEORÍA VIMOS OTRA OPCIÓN QUE ES CREAR UNA ENTIDAD A PARTIR DEL ATRIBUTO COMPUESTO, PERO EN LA PRÁCTICA NOS DIJERON QUE NO LO HAGAMOS**

### Sacar jerarquías:

- `Dejar todo`: todas las entidades de especialización pasan a tener una relación *"Padre (0,1) - (1,1) Hijo"* con la entidad raiz; se reemplaza la jerarquía por relaciones
- `Dejar al padre`: se eliminan las especializaciones y la entidad raiz pasa a tener los atributos y relaciones de todos; los que no se repetían van a ser opcionales. Se puede agregar un atributo de *"tipo"* que indique la especialización
- `Dejar a los hijos`: **SÓLO SIRVE PARA COBERTURAS (T,E)**. Se elimina la entidad raiz y cada hijo va a tener sus atributos y relaciones.
- Lo más fácil es dejar al padre o dejar todo, porque se puede usar en todas las combinaciones de cobertura posibles
- Es importante que **todas** las entidades que antes eran especializaciones tengan un ID; puede ser un ID externo (si se deja todo) o una clave unívoca que ya tenían antes, **pero hay que tener en cuenta esto para elegir cómo sacar la jerarquía**

| Cobertura | Soluciones posibles |
| --------- | ------------------- |
| (T, E) | Dejar todo, dejar al padre, dejar a los hijos |
| (T, S) | Dejar todo, dejar al padre |
| (P, E) | Dejar todo, dejar al padre | 
| (P, S) | Dejar todo, dejar al padre |

### Sacar atributos polivalentes:

- La única posibilidad es crear una entidad nueva que represente al atributo y que tenga una relación polivalente con la entidad vieja
- Hay que prestar atención que desde el atributo polivalente podría haber distintas cardinalidades; la cardinalidad no va a ser siempre muchos a muchos *"Entidad (0/1,N) - (0/1,N) AtributoPolivalente"* (EJ: los teléfonos de una persona; si queremos que en el modelo un teléfono pueda pertenecer a una sola persona entonces la cardinalidad va a ser *"Persona (1,N) - (1,1) Teléfono"*)

# Modelo físico

### Convertir tablas:

- Se puede elegir cualquiera de las claves unívocas del modelo lógico como clave primaria
- Los atributos opcionales se escriben como *"{nombre}?"*
- Si se usa una clave foránea, tiene que representar **siempre** a la clave primaria de la otra tabla

### Convertir relaciones:

- Si la relación tiene cardinalidad *"(1,1) - (1,1)"* entonces una de las dos tablas puede tener una clave foránea que apunte a la otra (**sólo una de las dos**). También se puede hacer esto si una de las dos tiene cardinalidad *(0,1)*, pero en ese caso la clave foránea siempre va a estar en la que tenga *(1,1)*
- Si la relación tiene cardinalidad *"(0,1) - (0,1)"*, entonces se tiene que crear una tabla nueva con dos claves foráneas apuntando a las dos tablas y **sólo una** como clave primaria
- Si la relación es *"A (0/1,N) - (1,1) B"*, entonces B va a tener una clave foránea que apunte hacia A
- Si la relación es *" A (0/1,N) - B(0/1,N"*, entonces se crea una tabla con dos claves foráneas apuntando a las dos tablas; **la clave primaria se forma por las dos claves foráneas**
- Los atributos de la relación se quedan en la tabla (si se crea una nueva) y podría llegar a formar la clave primaria, o se van a la tabla que tenga la clave foránea hacia la otra

# Álgebra relacional

- Selección (σ): devuelve las tuplas de la tabla que cumplan con la condición.
- Proyección (π): devuelve sólo las columnas deseadas de cada tupla de la tabla; elimina duplicados
- Renombre (ρ): le cambia el nombre a una tabla para seguir usándola en la consulta
- Unión (U): devuelve los elementos de la tabla A + los de B; elimina duplicados
- Intersección (∩): devuelve los elementos que están en la tabla A y también están en B
- Diferencia (-): devuelve los elementos que están en la tabla A y que no están en B
- Prod. cartesiano (x): devuelve una nueva tabla combinando todas las tuplas de la tabla A con las de la tabla B
- Prod. natural (|x|): devuelve una nueva tabla combinando las tuplas de la tabla A con las de la tabla B, combinándolas según los valores de las columnas que tengan el mismo nombre. Queda uno solo de los atributos que coincidan
- División (%): devuelve una tabla seleccionando las tuplas de tabla A que correspondan a **todas** las tuplas de tabla B. El esquema de B debe ser menor que el de A, y el resultado elimina todas las columnas de B

##

- El producto natural junta las tablas según las columnas con el mismo nombre, así que si coincide el nombre de alguna que **no** queremos juntar hay que usar un cartesiano con una selección después
- Para hacer unión, intersección y diferencia las tablas tienen que tener esquemas iguales (misma cantidad de columnas, con el mismo tipo de atributo en cada posición; pueden tener nombres distintos)
- Conviene proyectar siempre después de una operación para reducir la cantidad de campos de c/tupla lo antes posible para no trollear con un prod. natural después
- Cuando el enunciado diga algo de ***todos*** (como *"clientes que hayan comprado todos los productos"*), hay que usar el %

# SQL

- Decidí no usar ***nunca*** el NATURAL JOIN porque no sé cómo funciona si hago varios anidados
- Las subconsultas de un EXISTS pueden usar los campos de las tablas de afuera (no sé si eso funciona con las demás subconsultas)
- Cuando el enunciado diga algo de ***todos*** (como *"clientes que hayan comprado todos los productos"*), hay que usar 2 NOT EXISTS, y adentro del último se hacen las consultas de coincidencias y/o condiciones
- Si en el SELECT mostramos algo además de una función de agregación, en el GROUP BY tenemos que escribir todos los elementos que queremos
- Si hay que buscar algo que cumpla con una condición y/o otra, conviene usar siempre UNION/INTERSECT en vez de AND/OR en el WHERE
- Las funciones de agregación no siempre van con un GROUP BY (EJ: cuando se usa una sola tabla)
- Los GROUP BY hay que agruparlos siempre por clave primaria
- Cuando el enunciado diga algo de encontrar el mayor, hay veces que es más efectivo usar un `>= all` antes que un `MAX()` (EJ: si tenemos que contar algo, podemos hacer un COUNT(*) y en el HAVING poner una subconsulta `>= all`)
- Si tenemos que buscar algo que sea *mayor* que todos y usamos un `>= all` como el anterior, porque su propio valor se va a encontrar en la subconsulta entonces nunca va a ser mayor que todos
- La función para obtener la fecha de hoy es `NOW()`
- Cuando hay que hacer un **INSERT/UPDATE/DELETE** usando valores de otra tabla, conviene siempre usar una subconsulta en vez de un INNER JOIN (EJ: usar un IN en el WHERE de una actualización o una eliminación, en vez de hacer un join para encontrar el valor que coincide con la otra tabla)
- Si hay que eliminar una tupla de una tabla, primero hay que eliminarla de todas las tablas que la estén referenciando con una clave foránea

**SIEMPRE ME OLVIDO DEL SOME Y ALL PARA EL WHERE, ACORDARSE DE USARLOS**