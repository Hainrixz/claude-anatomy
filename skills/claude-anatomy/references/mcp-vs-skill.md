# MCP o skill

Se lee cuando la skill que estás pensando empieza a pedir una credencial, o
cuando no estás seguro de si el trabajo es criterio o es una llamada a algo de
afuera.

## La señal más confiable

Si al escribir el `SKILL.md` aparece un token, una clave de API o un usuario y
contraseña, la decisión ya está tomada: eso es un servidor MCP, con una skill
de criterio arriba si hace falta decidir algo antes de llamarlo.

Una skill no necesita saber nada del mundo externo. Necesita saber qué hacer
con lo que ya tiene delante. Si para «saber qué hacer» hay que autenticarse
contra algo, esa parte no es skill.

## Ejemplo de una skill que en realidad escondía un MCP

Pedido: «una skill que revise mis tickets de soporte abiertos y me diga cuáles
priorizar».

Versión mal armada: una skill con instrucciones para «consultar la API de
soporte con la clave configurada» y después aplicar el criterio de prioridad.
La clave vive en el cuerpo de la skill o en una variable que el `SKILL.md`
asume que ya existe.

Versión correcta: un servidor MCP que expone `listar_tickets_abiertos` y
`ver_ticket(id)`, con la autenticación resuelta en su propia configuración. La
skill se queda con el criterio: qué hace que un ticket sea urgente, qué orden
seguir, cuándo escalar. La skill llama las herramientas del MCP; no guarda
ninguna credencial.

## Cuándo sí es solo skill

Cuando todo el insumo ya está en la conversación o en un archivo que la
persona pegó o adjuntó. Ahí no hace falta ir a buscar nada afuera: alcanza con
criterio sobre lo que ya está.

## Cuándo sí es solo MCP, sin skill arriba

Cuando la llamada es mecánica y no hace falta decidir nada antes de hacerla:
«traeme el estado del pedido número tal». No hay criterio que aplicar, solo una
consulta. Ahí el MCP alcanza solo.

## La combinación más común

MCP más skill es el par más frecuente cuando el pedido mezcla «traé datos de
afuera» con «decidí algo con esos datos». Se declaran como dos piezas, y la
skill depende del MCP para funcionar.
