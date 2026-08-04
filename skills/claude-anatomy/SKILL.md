---
name: claude-anatomy
description: "ES: Te dice si lo que vas a construir en Claude Code tiene que ser skill, plugin, subagente, comando, servidor MCP o hook, con el árbol de decisión y el esqueleto de archivos de la pieza elegida. Usala al arrancar cualquier proyecto, y cuando preguntes «esto lo hago como skill o como agente», «va como subagente o comando», «necesito un MCP o alcanza con una skill» o «qué va adentro de un plugin y qué va aparte», aunque no nombres esas palabras. EN: Tells you whether what you're building in Claude Code should be a skill, plugin, subagent, command, MCP server or hook, with the decision tree and file skeleton for the piece you pick. Use it at the start of any project, and on «skill or agent», «subagent or command», «do I need an MCP or does a skill cover it», or «what goes in a plugin versus on its own», even without those exact words."
metadata:
  version: 0.1.0
---

# Anatomía de Claude Code

## El dolor

Arrancás un proyecto con Claude Code y a la media hora ya tenés la duda: esto que
quiero armar, ¿es una skill? ¿Un agente? ¿Un comando? Probás una forma, no anda
como esperabas, la desarmás y probás otra. Cada vuelta cuesta tiempo, y al final
terminaste con un comando que debería haber sido una skill, o con un servidor MCP
para algo que un archivo de texto resolvía solo.

La pregunta se repite en cada proyecto nuevo porque nadie la responde una sola vez
y la deja escrita.

## Cuándo usar esto

Al arrancar cualquier proyecto de Claude Code, antes de escribir el primer
archivo. También cada vez que dudes entre dos formas para la misma idea, o que
alguien te pregunte «esto lo hago como skill o como agente» sin saber que la
pregunta tiene más de dos respuestas posibles.

## Las seis piezas, en una frase

**Skill.** Conocimiento que el modelo consulta cuando el tema aparece. No corre
sola: se carga en capas, y solo la primera se paga siempre.

**Plugin.** Un paquete que agrupa piezas —skills, subagentes, comandos, hooks,
hasta un servidor MCP— bajo un manifiesto único, para instalarlas y
desinstalarlas juntas. No es una pieza nueva: es el empaque de varias.

**Subagente.** Un trabajador que corre en un contexto aparte, con su propio
prompt de sistema, invocado por el hilo principal para delegar un pedazo de
trabajo sin llenar la conversación con los pasos intermedios. Devuelve un
informe al final.

**Comando.** Una instrucción guardada que la persona dispara escribiendo su
nombre con la barra. La decisión de cuándo correr no la toma el modelo: la toma
quien tipea.

**Servidor MCP.** Un proceso aparte que expone herramientas con argumentos y
respuesta ya definidos, para que el modelo las llame. Es la puerta hacia un
sistema externo: una base, una API, un servicio con su propia autenticación.

**Hook.** Un comando que corre solo, disparado por un evento del propio Claude
Code —antes de usar una herramienta, después de una respuesta, al cerrar la
sesión—, sin pasar por el criterio del modelo. Con el mismo evento, hace
siempre lo mismo.

## Qué carga contexto y qué no

No las seis cuestan igual, y esa diferencia es la que más se ignora al elegir.

La skill se carga en capas: el nombre y la descripción viven en cada
conversación aunque nadie la use, el cuerpo entra recién cuando dispara, y
`references/` o `assets/` solo si el trabajo los pide. El comando funciona
igual, pero nadie paga nada hasta que alguien escribe su nombre. El subagente no
cuesta nada hasta que se invoca, y cuando corre, lo hace en un contexto propio:
al hilo principal solo vuelve el resumen final. El servidor MCP es el que menos
se puede recortar: en cuanto se conecta, las herramientas que expone quedan
disponibles para el resto de la sesión, se usen o no. El hook no le cuesta nada
al modelo porque el modelo no lo lee: lo ejecuta el sistema cuando el evento
ocurre. El plugin no agrega costo propio; el costo es la suma de lo que traen
sus piezas.

`TODO(humano) S03`: esta sección describe el comportamiento documentado en el
momento de escribirla. Claude Code cambia de versión en versión y el detalle
fino puede correrse. Ver `SUPUESTOS.md`.

## El árbol de decisión

Siete preguntas, en orden. La primera que da «sí» —salvo la última— cierra la
decisión.

```
1. ¿Corre sola, sin que nadie la dispare a mano, y al final entrega algo?
   Sí → no es ninguna de las seis piezas de acá: es un agente completo,
        con su propio disparo y su propia entrega.
   No → seguí.

2. ¿Necesita hablar con un sistema externo con su propia credencial
   (una API, una base, un servicio de terceros)?
   Sí → servidor MCP.
   No → seguí.

3. ¿La persona la dispara a mano, escribiendo su nombre?
   Sí → comando.
   No → seguí.

4. ¿Necesita su propio contexto aislado, para no llenar la conversación
   principal con pasos intermedios largos?
   Sí → subagente.
   No → seguí.

5. ¿Tiene que correr sola, disparada por un evento del propio Claude Code,
   sin pasar por el criterio del modelo?
   Sí → hook.
   No → seguí.

6. Lo que queda: el modelo tiene que saber hacer esto cuando el tema
   aparece, sin que nadie la dispare a mano y sin tocar nada externo.
   → skill.

7. ¿La respuesta juntó dos o tres piezas que siempre viajan juntas?
   Sí → empaquetalas en un plugin.
   No → la pieza sola alcanza.
```

Una copia de una página, para imprimir o pegar en un canal, está en
[assets/arbol-de-decision.md](assets/arbol-de-decision.md).

## Plugin o skill suelta

Un plugin aporta cuando dos o más piezas siempre viajan juntas: una skill que
necesita un comando propio, o un hook que solo tiene sentido si la skill que lo
acompaña también está instalada. El manifiesto las instala y las desinstala
como una unidad.

Una skill suelta alcanza cuando es una sola pieza de conocimiento sin
compañeras. Empaquetarla en un plugin de una sola skill no suma nada: agrega un
manifiesto para envolver algo que ya funcionaba solo.

Si dudás entre las dos, o querés ver el anti-patrón del plugin de una sola
pieza con más detalle, leé
[references/plugin-vs-skill.md](references/plugin-vs-skill.md).

## MCP o skill

La señal más confiable es la credencial. Si al escribir la skill aparece un
token, una clave o un usuario y contraseña, la decisión ya está tomada: eso es
un servidor MCP, con una skill de criterio arriba si hace falta decidir algo
antes de llamarlo.

Una skill no necesita saber nada del mundo externo: necesita saber qué hacer
con lo que ya tiene. Un MCP existe porque hay algo afuera —una base, un
servicio, una cuenta— que solo se puede tocar con su propia autenticación.

El detalle, con ejemplos de una skill que en realidad escondía un MCP, está en
[references/mcp-vs-skill.md](references/mcp-vs-skill.md). Leelo cuando la
skill que estás armando empiece a pedir una variable de entorno.

## Subagente o comando

Los dos se disparan explícitamente, pero por lados distintos. El comando lo
dispara la persona, escribiendo su nombre, y corre en la misma conversación:
comparte el contexto con quien lo llamó. El subagente lo dispara el hilo
principal cuando decide delegar un trabajo largo, y corre aparte: al terminar,
solo vuelve el informe, no los pasos que lo produjeron.

La pregunta que decide: si los pasos intermedios ensucian la conversación y
nadie necesita verlos, es subagente. Si el valor está en que la persona controle
cuándo corre, es comando.

Ejemplos de los dos casos, con el motivo de cada elección, están en
[references/subagente-vs-comando.md](references/subagente-vs-comando.md).

## El orden de carga, a grandes rasgos

Al arrancar una sesión, lo primero que entra es el archivo de instrucciones del
proyecto. Después, el nombre y la descripción de cada skill instalada —el nivel
más liviano de la revelación progresiva—. Los servidores MCP se conectan y
registran sus herramientas, y ese costo queda fijo para toda la sesión, se usen
o no. Los subagentes y los comandos quedan indexados por nombre, pero su
contenido no entra hasta que alguien los invoca. Los hooks se registran contra
sus eventos y no ocupan contexto en ningún momento: corren, no se leen.

El orden exacto y qué tan temprano se conecta cada MCP dependen de la versión de
Claude Code. `TODO(humano) S01`: esta sección da el orden general, no el
verificado contra una build puntual. El detalle y las fuentes están en
[references/orden-de-carga.md](references/orden-de-carga.md).

## Los anti-patrones más comunes

| Anti-patrón | Por qué falla | Qué hacer en su lugar |
|---|---|---|
| Una skill que pide una API key | Esconde un MCP con un instructivo encima | Partir en dos: servidor MCP más skill de criterio |
| Un comando para algo que se repite solo, sin que nadie lo tipee | Nadie va a acordarse de escribirlo cada vez | Hook, si el disparo es un evento; skill, si el disparo es un tema de conversación |
| Un plugin con una sola skill adentro | El manifiesto no suma nada sobre la skill sola | Dejar la skill suelta hasta que aparezca una segunda pieza |
| Un subagente para una decisión de una frase | El costo de aislar contexto no se paga con un solo paso | Resolverlo en el cuerpo de la skill que lo dispara |
| Un hook que decide con criterio, no con una regla fija | El hook no pasa por el modelo: si necesita juicio, va a fallar en silencio | Skill, si hace falta criterio; hook, solo para lo que siempre se resuelve igual |
| Un SKILL.md de 3000 palabras que explica seis casos distintos | Se carga entero cada vez que dispara, aunque el caso de hoy sea uno solo | Cuerpo con el procedimiento general y un archivo en `references/` por caso |

Cada fila con su ejemplo de antes y después está en
[references/anti-patrones.md](references/anti-patrones.md). Leelo cuando ya
identificaste el anti-patrón y quieras el ejemplo concreto de cómo se corrige.

## Cómo se arma la recomendación y el esqueleto

El procedimiento es el mismo siempre: correr las siete preguntas del árbol de
decisión contra el caso que trajiste, en orden, sin saltear ninguna. La primera
que contesta «sí» fija la pieza, salvo la última, que decide si además hace
falta un plugin alrededor.

Si el caso no da para contestar alguna pregunta con lo que dijiste, la
respuesta pide lo que falta: quién o qué la dispara, si toca algo externo, y
qué se entrega al final. Son las tres preguntas que más rinden, y con eso casi
siempre alcanza para completar el árbol.

Una vez que la pieza está decidida, el paso siguiente es copiar su esqueleto
desde `assets/` y completarlo con el caso concreto. Cada archivo de esqueleto
trae la estructura de carpetas y los campos mínimos del frontmatter o el JSON
que le corresponde, con marcadores entre llaves dobles donde va lo específico
del caso.

`TODO(humano) S02`: la ficha no especifica si el esqueleto tiene que ser un
árbol para copiar a mano —lo que hay en `assets/`— o un generador que arme los
archivos solo. Se eligió lo primero, porque no inventa una herramienta que la
ficha no pidió. Ver `SUPUESTOS.md`.

Si la pieza recomendada es una skill, el esqueleto de acá alcanza para
arrancar, pero construirla entera —con descripción que dispara, evals y
verificación— es otro trabajo. Eso lo hace `skill-smith`, no esta skill.

## Qué entregás

Tres cosas, siempre las tres:

```
1. La pieza recomendada (skill, plugin, subagente, comando, MCP o hook),
   con el motivo en una frase.
2. El árbol de archivos de esa pieza, copiado desde assets/ y ya
   nombrado con el caso concreto.
3. Si el caso reparte en dos piezas, cuál se construye primero y por qué.
```

La tercera no es un adorno: la mitad de los casos reales no resuelven en una
sola pieza, y construir en el orden equivocado deja la primera pieza sin nada
que la sostenga.

## Material de apoyo

- `references/plugin-vs-skill.md` — leelo cuando dudes si empaquetar en un
  plugin o dejar la skill suelta.
- `references/mcp-vs-skill.md` — leelo cuando la skill que estás pensando
  empiece a pedir una credencial.
- `references/subagente-vs-comando.md` — leelo cuando dudes entre delegar en
  un subagente o dejarlo como comando.
- `references/orden-de-carga.md` — leelo cuando necesites el detalle del
  orden de carga, no solo el resumen.
- `references/anti-patrones.md` — leelo cuando ya viste el anti-patrón en la
  tabla y querés el ejemplo de antes y después.
- `assets/arbol-de-decision.md` — copialo entero si querés la versión de una
  página para imprimir o pegar en un canal.
- `assets/esqueleto-skill.md`, `assets/esqueleto-plugin.md`,
  `assets/esqueleto-subagente.md`, `assets/esqueleto-comando.md`,
  `assets/esqueleto-mcp.md`, `assets/esqueleto-hook.md` — copiá el que
  corresponda a la pieza recomendada y completalo. No hace falta leer los
  otros cinco.

## Lo que esto no hace

No escribe la skill, el plugin o el subagente completos. Entrega el esqueleto
y la recomendación; completarlo con el criterio real de tu caso es tu trabajo,
o el de otra forja si la pieza es una skill.

No reemplaza la documentación oficial de Claude Code. Esta guía da un punto de
partida rápido; si el detalle técnico de una versión puntual importa para tu
caso, verificalo contra la documentación vigente, no solo contra esto.

No decide si conviene automatizar algo. Esa es una pregunta anterior a ésta:
acá se asume que ya se decidió construir algo y falta elegir la forma.
