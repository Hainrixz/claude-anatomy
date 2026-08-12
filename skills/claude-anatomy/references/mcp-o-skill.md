# Servidor MCP o skill

## La señal es la credencial

No hace falta pensarlo mucho. Si mientras escribís el `SKILL.md` aparece un
token, una clave de API o un usuario y contraseña, la decisión ya está tomada:
eso es un servidor MCP.

Una skill es un archivo de texto que el modelo lee. Meter una credencial adentro
es publicarla: viaja en el repo, entra al contexto y queda en la transcripción.

## El caso típico, en las dos versiones

Alguien quiere que Claude priorice los tickets de soporte abiertos.

Así **no**:

```markdown
---
name: triaje-de-soporte
---
Para ver los tickets, llamá a la API con este token:
SOPORTE_API_KEY=sk_live_a1b2c3...
GET https://api.soporte.com/v1/tickets?estado=abierto
```

Además de publicar la clave, no funciona: el modelo no puede hacer un pedido HTTP
porque se lo diga un archivo de texto.

Así **sí**, en dos piezas:

> **El servidor MCP** expone dos operaciones: `listar_tickets_abiertos()` y
> `ver_ticket(id)`. La credencial vive en una variable de entorno, del lado del
> servidor. El modelo nunca la ve.
>
> **La skill** no menciona ninguna clave. Trae el criterio: qué hace que un
> ticket sea urgente, cómo se agrupan los repetidos, qué se responde primero y
> por qué.

Es el reparto que más se repite en la práctica. El MCP trae los datos, la skill
trae el juicio.

## Cuándo alcanza con la skill sola

Cuando lo que hay que procesar ya está en la conversación.

Si pegaste el texto, si el archivo está en el repo, si los datos ya los trajo
otra cosa: no hay sistema externo, no hay credencial, no hay MCP. Es una skill y
listo.

## Cuándo alcanza con el MCP solo

Cuando la operación es mecánica y no hay nada que decidir.

«Traeme el estado del pedido 4821» no necesita criterio. Una herramienta que
consulta y devuelve alcanza. Escribir una skill encima para explicar cómo leer un
número de pedido es trabajo que nadie va a leer.

## Antes de escribir un servidor, fijate si ya existe

GitHub, Slack, Postgres, Notion, Sentry, Linear, Figma y varios más ya tienen su
servidor MCP, mantenido por alguien que no sos vos.

Escribir el propio se justifica cuando el sistema es tuyo, o cuando es interno, o
cuando el que existe expone cuarenta operaciones y vos necesitás tres.

## Lo que ya no es cierto sobre el costo

Antes se decía que cada servidor MCP conectado te cobraba los esquemas de todas
sus herramientas en cada turno, y de ahí salía el consejo de instalar pocos.

Hoy la búsqueda de herramientas viene prendida por defecto y difiere los
esquemas: al arranque entran los nombres y el esquema llega cuando la herramienta
se va a usar.

El costo que sí queda es de precisión. Entre cuarenta nombres parecidos el modelo
elige peor que entre seis. Así que la regla práctica sobrevive —exponé lo que
alguien pidió, no lo que la API tiene— pero el motivo cambió.

Si querés el número de tu máquina en vez de una opinión, `/mcp` te muestra el
costo por servidor.

## La pregunta que resuelve el noventa por ciento

¿Esto necesita entrar a algún lado con una credencial propia?

Sí, es MCP. No, es skill. Y si además hay criterio que aplicar sobre lo que el
MCP trae, son las dos, y el MCP se construye primero: la skill sin él no tiene
qué llamar.
