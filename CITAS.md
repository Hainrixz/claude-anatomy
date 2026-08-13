# De dónde salió este producto

`claude-anatomy` no se le ocurrió a nadie de este proyecto. Salió de una caja de
preguntas en Instagram que contestaron 712 personas.

## Los dos números, que son distintos

La ficha de este producto en el catálogo lleva dos datos separados, y conviene
no confundirlos:

- **Demanda: 14.** Es la medida de cuánto se pidió esto, calculada sobre el
  análisis completo de las respuestas.
- **Cinco identificadores de origen.** Son las respuestas concretas que quedaron
  registradas como el origen de este producto, y son las cinco de la tabla de
  abajo.

O sea que la tabla tiene cinco filas y no catorce a propósito: no es una lista
recortada de los que pidieron, es la lista de las respuestas que la ficha cita
como procedencia. Son dos cosas y por eso los números no coinciden.

## Los identificadores

| ID | Qué aporta |
|---|---|
| R0143 | La duda inicial, al empezar cualquier proyecto de Claude Code, sobre si algo se arma como skill o como agente |
| R0161 | El pedido de una guía única para no perder tiempo probando la pieza equivocada antes de escribir el primer archivo |
| R0204 | La confusión sobre cuándo conviene empaquetar en un plugin en vez de dejar piezas sueltas |
| R0232 | La duda sobre si algo necesita un servidor MCP propio o alcanza con una skill de criterio |
| R0242 | La pregunta sobre cuándo conviene un subagente en vez de un comando de barra |

Cada identificador resuelve contra `CORPUS.md` del análisis privado. Lo
verifica `citas_resuelven`, que además falla si alguno no está entre las citas
de esta ficha: un producto no puede apropiarse de la procedencia de otro para
parecer más pedido de lo que fue.

## Qué se publica y qué no

Se publica el identificador y el número agregado de personas que pidieron
esto. Un número agregado no identifica a nadie.

No se publica el texto del comentario ni el usuario de Instagram. Esa gente
contestó una story, no consintió aparecer citada para siempre en un
repositorio público. Hay un verificador que falla si aparece un usuario o el
texto literal de un comentario.

La columna «Qué aporta» es una paráfrasis escrita por la forja. Dice qué
necesidad aportó cada pedido, con otras palabras.

## Si pediste esto

Si reconocés tu pedido acá y querés que tu nombre aparezca junto al producto,
abrí una discusión en el repositorio y se agrega. La decisión por defecto es
no publicarlo.
