---
name: anatomia-cc
description: "ES: Te dice qué construir en Claude Code y te da el esqueleto de archivos: skill y en qué modo, subagente, hook, servidor MCP, plugin, rutina, artefacto, app con el Agent SDK, una línea en CLAUDE.md, o nada. Corré esto antes del primer archivo. Se dispara con «esto lo hago como skill o como agente», «necesito un MCP o alcanza con una skill», «va como comando o como skill», «quiero que esto salga como una pantalla para mandarle a alguien y no como texto en la terminal», «quiero meter Claude adentro de mi app», «quiero que corra solo todas las noches» o «por dónde empiezo». EN: Tells you what to build in Claude Code and hands you the file skeleton: skill and mode, subagent, hook, MCP server, plugin, routine, artifact, Agent SDK app, a CLAUDE.md line, or nothing. Triggers on «skill or agent», «do I need an MCP», «slash command or skill», «I want this as a page I can send someone, not terminal text», «I want Claude inside my app», «run it nightly on its own», «where do I start»."
license: MIT
metadata:
  version: 0.3.1
---

# Anatomía de Claude Code

## El dolor

Abrís Claude Code y hay skills, subagentes, comandos, plugins, servidores MCP,
hooks, canales, rutinas y artefactos. Nueve palabras, ninguna explicada, y una
decisión que hay que tomar en el minuto uno.

Lo más común es armar un subagente para algo que era un archivo de texto, o un
servidor MCP para algo que resolvía un script de veinte líneas.

El costo no se ve el primer día. Se ve al mes, cuando la sesión arranca pesada,
el modelo elige mal seguido, y nadie se acuerda de por qué esa carpeta está ahí.

Este archivo no enseña a escribir cada pieza. Enseña a elegir cuál.

## Cuándo usar esto

Al empezar cualquier proyecto, antes del primer archivo. Y cada vez que dudás
entre dos formas para la misma idea.

También sirve para el caso que casi nadie contempla: que la respuesta correcta
sea que no hay que construir nada.

## Las tres preguntas que más rinden

Con estas tres alcanza para la mayoría de los casos, sin recorrer el árbol
entero.

```
1. ¿Quién aprieta el botón?
   Vos, escribiendo /algo · el modelo, cuando aparece el tema · un evento de
   Claude Code · el reloj · un sistema de afuera · nadie, es una regla que
   Claude tiene que saber siempre.

2. ¿Toca algo de afuera que pida su propia credencial?
   Una base, una API, la cuenta de un servicio.

3. Cuando termina, ¿qué queda y quién lo mira?
   Texto en tu terminal · una pantalla que alguien abre en el navegador ·
   archivos cambiados en el repo · una respuesta que consume otro programa.
```

La tercera es la que más se olvida, y es la que decide si esto vive adentro de
una sesión o no.

## Paso 0 · ¿Hace falta construir algo?

Cuatro preguntas. La primera que da sí termina acá.

```
0.1 ¿Lo que falta es que Claude se acuerde de una regla tuya?
    «usá pnpm, no npm» · «corré los tests antes de commitear»
    SÍ → No construyas ninguna pieza: es una línea en CLAUDE.md.
         Si la regla aplica solo a ciertos archivos, va en .claude/rules/
         con `paths:`, y entra nada más cuando Claude toca esos archivos.

0.2 ¿Ya lo hizo otro?
    GitHub, Slack, Postgres, Notion y Sentry ya tienen servidor MCP. Y hay
    plugins publicados para buena parte del resto.
    SÍ → No construyas nada. Instalalo.

0.3 ¿Lo que te molesta es CÓMO te contesta, no lo que sabe?
    Querés otro tono, más explicación, que siempre arranque con un diagrama.
    SÍ → No es una skill: es un estilo de salida, en .claude/output-styles/.
         Ojo, se lee una vez al arrancar: aplica después de /clear.

0.4 ¿Lo resuelve un script, sin modelo adentro?
    Si siempre se hace igual y no hay nada que decidir, es un script.
    SÍ → Escribilo. Claude lo corre con Bash cuando haga falta.
    NO → seguí al Paso 1.
```

Buena parte de los casos que llegan hasta acá se resuelven en este paso. No es
una broma: la pieza más chica que resuelve el problema es casi siempre la
correcta, y siempre es la más fácil de tirar cuando te equivocaste.

## Paso 1 · ¿Adentro de una sesión, o afuera?

Tres preguntas. La primera que da sí termina acá.

```
1.1 ¿Lo que se entrega es una pantalla que alguien mira o comparte —un
    tablero, un informe con gráficos, un diff anotado— y no texto en la
    terminal?
    SÍ → Artefacto: una página que Claude publica desde la sesión y
         actualiza en el mismo link.
         El límite, antes de que te ilusiones: es UNA página sin backend.
         No guarda formularios y no tiene rutas. Si necesitás eso, es una
         app y la hospedás vos.

1.2 ¿Lo va a usar gente que nunca abre Claude Code, desde tu web o tu
    producto?
    SÍ → App con el Agent SDK, en Python o TypeScript.

1.3 ¿Tiene que correr con tu computadora apagada? Cada noche, cada lunes.
    SÍ → Rutina en la nube, con /schedule. Corre sobre un clon limpio y no
         lee tus skills locales: habilitalas en tu cuenta de claude.ai, o
         que viajen en el repo o en un plugin.
    NO → seguí al Paso 2.
```

Si alguna dio sí, el detalle está en `references/afuera-de-la-sesion.md`.

## Paso 2 · Qué pieza

Cuatro preguntas. La primera que da sí fija la pieza.

```
2.1 ¿Tiene que pasar SIEMPRE, sin que el modelo decida?
    Formatear después de cada edición. Frenar un comando peligroso.
    SÍ → Hook. Una instrucción escrita es un pedido; el hook es la única
         forma de garantizarlo.

2.2 ¿Necesita entrar a un sistema de afuera con su propia credencial?
    SÍ → Servidor MCP. Si además hay criterio que aplicar sobre lo que
         trae, arriba va una skill: son dos piezas, y el MCP va primero.
         Si el sistema de afuera tiene que EMPUJAR el evento hacia adentro
         de una sesión abierta, eso es un canal.

2.3 ¿Es un trabajador reusable, con herramientas recortadas, al que vas a
    llamar por su nombre desde varios lugares?
    SÍ → Subagente propio, en .claude/agents/nombre.md.
         Dato que decide muchos casos: un subagente no te puede preguntar
         nada a mitad de camino.

2.4 Lo que queda es una SKILL. Falta el modo, que está acá abajo.
```

## Los cuatro modos de una skill

Acá está el cambio que más desactualiza al material viejo: **el comando dejó de
ser una pieza aparte y pasó a ser un campo del frontmatter de la skill.**

Un archivo en `.claude/commands/deploy.md` y una skill en
`.claude/skills/deploy/SKILL.md` producen los dos el mismo `/deploy`.

El subagente **sigue siendo una pieza aparte**, con su archivo y su carpeta. Lo
que cambió es otra cosa y conviene no confundirla: una skill ahora puede correr
adentro de un subagente con `context: fork`, sin que tengas que escribirle uno
propio. El subagente propio se justifica cuando además es reusable.

| | La disparás vos | La dispara el modelo | Las dos |
|---|---|---|---|
| **Corre en tu conversación** | `disable-model-invocation: true` | `user-invocable: false` | el default |
| **Corre aparte** | `disable-model-invocation: true` + `context: fork` | `user-invocable: false` + `context: fork` | `context: fork` |

`context: fork` decide **dónde** corre, no **quién** la dispara. Sola no la saca
del menú de `/`: para eso hace falta `user-invocable: false`. Son dos ejes
distintos y se combinan.

- **`disable-model-invocation: true`** es lo que antes era un comando. Va para
  cosas con consecuencias: desplegar, commitear, mandar mensajes. Regalo extra:
  no ocupa contexto hasta que la invocás.
- **`user-invocable: false`** es conocimiento de fondo. Claude debería saberlo
  cuando el tema aparece, pero no es nada que alguien quiera tipear.
- **`context: fork`** corre la skill en un subagente. Solo sirve si la skill trae
  una tarea; si es solo conocimiento, el subagente vuelve con las manos vacías.

El detalle, y el precio de usar campos que no están en el estándar, en
`references/modos-de-skill.md`.

## Paso 3 · ¿Se empaqueta?

Esto se contesta siempre, al final, después de saber qué pieza es.

```
3.1 ¿Te salieron dos o más piezas que se instalan juntas o no sirven?
    ¿O lo mismo tiene que andar en otro repo, o en la máquina de otra
    persona?
    SÍ → Plugin. Y si además vas a repartir varios, arriba va un
         marketplace: un marketplace.json en un repo de git.
    NO → Dejalo suelto en .claude/. Un plugin de una sola pieza que usás
         solo vos es un manifiesto de más.
```

## Qué carga contexto y qué no

Casi todas las piezas pueden hacer casi lo mismo. Lo que las separa es qué te
cobran y cuándo.

| Pieza | En el turno cero | Después |
|---|---|---|
| `CLAUDE.md` | Entero, siempre | — |
| `.claude/rules/` con `paths:` | Nada | Cuando Claude toca esos archivos |
| Skill | Una línea de descripción | El cuerpo al disparar, y ahí se queda |
| Skill con `disable-model-invocation` | Nada | Todo, al invocarla vos |
| Servidor MCP | Los nombres de las herramientas | El esquema, cuando se usa |
| Subagente | Nada en tu ventana | Corre en la suya y te devuelve el resumen |
| Hook | Nada | Solo el `additionalContext` que devuelva |

Tres cosas que casi nadie sabe. **Una skill que cargó se queda cargada** el resto
de la sesión, así que un `SKILL.md` con seis casos adentro te cobra los seis
aunque hoy uses uno. **La búsqueda de herramientas difiere los esquemas de MCP**,
así que el viejo consejo de no instalar servidores porque comen contexto hoy se
dice distinto: lo que empeora con cuarenta herramientas no es el gasto, es la
elección. Y **el `stdout` suelto de un hook no entra al contexto**: va al log de
depuración. Lo que Claude lee es lo que el hook devuelva en
`hookSpecificOutput.additionalContext`.

Todo esto se mide, no se estima: `/context` te muestra en qué se está yendo la
ventana ahora mismo.

Está desarrollado en `references/como-se-carga-el-contexto.md`.

## Los anti-patrones más comunes

| Anti-patrón | Qué hacer en su lugar |
|---|---|
| Un comando de barra para algo nuevo | Skill con `disable-model-invocation` |
| Un subagente aparte solo para aislar contexto | `context: fork` en la skill que ya tenías |
| Un MCP con cuarenta herramientas por las dudas | Las operaciones que alguien pidió |
| Una skill que pide una clave de API | MCP abajo, skill de criterio arriba |
| Un plugin con una sola skill que usás solo vos | Dejala suelta |
| Un hook que decide con criterio | Hook para la regla fija, skill para el juicio |
| Una regla importante escrita en el `CLAUDE.md` | Si tiene que valer siempre, es un hook |
| Un `CLAUDE.md` de ochocientas líneas | Lo que aplica a veces va en skills |
| Un `SKILL.md` de tres mil palabras con seis casos | Cuerpo corto y un archivo por caso |
| `context: fork` en una skill que solo sabe cosas | El default, o `user-invocable: false` |
| Pedir un artefacto esperando una app | Si guarda datos, la hospedás vos |

Los once con el antes y el después están en `references/anti-patrones.md`.

## Qué entregás

```
1. La pieza recomendada, una sola, con el motivo en una frase y el número
   de la pregunta que la decidió. Si es una skill, también el modo.
2. El árbol de archivos de esa pieza, copiado del assets/ que corresponde
   y ya nombrado con el caso concreto.
3. Si el caso reparte en dos piezas, cuál se construye primero y por qué.
4. Lo que NO hace falta construir. Si alguien vino a pedir un plugin y se
   va con una línea en CLAUDE.md, eso se dice.
```

## Material de apoyo

Leé el que corresponda al caso, no todos.

| Archivo | Cuándo |
|---|---|
| `references/como-se-carga-el-contexto.md` | Hay que comparar el costo de dos formas |
| `references/modos-de-skill.md` | La respuesta fue skill y falta el modo |
| `references/mcp-o-skill.md` | La skill que pensabas empezó a pedir una credencial |
| `references/afuera-de-la-sesion.md` | El Paso 1 dio sí |
| `references/anti-patrones.md` | Querés el antes y el después de un caso |
| `assets/arbol-de-decision.md` | El árbol entero en una página, para copiar |
| `assets/esqueleto-skill.md` | La respuesta fue skill o estilo de salida |
| `assets/esqueleto-subagente.md` | La respuesta fue subagente |
| `assets/esqueleto-hook.md` | La respuesta fue hook |
| `assets/esqueleto-mcp.md` | La respuesta fue servidor MCP o canal |
| `assets/esqueleto-plugin.md` | Hay que empaquetar, o repartir |
| `assets/esqueleto-artefacto.md` | La respuesta fue artefacto |
| `assets/esqueleto-app-sdk.md` | La respuesta fue app con el SDK |
| `assets/esqueleto-rutina.md` | Tiene que correr solo, cada tanto |

## Lo que esto no hace

No escribe la pieza entera. Entrega la recomendación y el esqueleto. Si la pieza
elegida es una skill, la escribe `skill-smith`.

No reemplaza la documentación oficial, que cambia seguido. Esta guía se escribió
contra la documentación de agosto de 2026 y conviene verificar contra la de hoy
lo que vayas a apoyar en un número.

No decide si conviene automatizar algo. Esa pregunta es anterior: acá se asume
que ya decidiste construir, y falta la forma.
