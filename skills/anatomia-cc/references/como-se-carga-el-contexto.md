# Qué entra al contexto, y cuándo

## Por qué importa para elegir la pieza

Casi todas las piezas pueden hacer casi lo mismo. Lo que las separa de verdad es
qué te cobran y cuándo te lo cobran.

Una sesión que arranca con medio contexto ocupado no falla: se pone tonta más
temprano. Elegís mal la pieza el día uno y lo pagás al mes, cuando ya nadie se
acuerda de por qué esa carpeta está ahí.

## El turno cero, en orden

Esto es lo que ya está adentro antes de que escribas la primera palabra.

| Orden | Qué entra | Cuánto pesa |
|---|---|---|
| 1 | El prompt de sistema | Fijo, no lo ves |
| 2 | La memoria automática, `MEMORY.md` | Las primeras 200 líneas o 25 KB, lo que llegue antes |
| 3 | Datos del entorno: carpeta, sistema, shell, si es un repo | Poco |
| 4 | Los **nombres** de las herramientas MCP | Poco: los esquemas quedan diferidos |
| 5 | Una línea de descripción por cada skill instalada | Poco por skill, y se suma |
| 6 | Tu `CLAUDE.md` de usuario | Entero |
| 7 | El `CLAUDE.md` del proyecto | Entero |

Recién después entra lo que escribiste vos.

## Lo que no entra hasta que hace falta

- **El cuerpo de una skill.** Entra cuando la skill dispara, no antes. Sus
  `references/` y `assets/` entran solo si el cuerpo manda a leerlos.
- **Los esquemas de las herramientas MCP.** Por defecto, la búsqueda de
  herramientas los deja diferidos y trae solo los que la tarea pide.
- **El prompt de un subagente.** Entra en la ventana del subagente, no en la tuya.
- **Un hook.** Su definición no entra nunca. Los tipos `command`, `http` y
  `mcp_tool` no pasan por el modelo en absoluto; `prompt` y `agent` sí, porque son
  una llamada al modelo disfrazada de hook.

## Las tres consecuencias que deciden casi todo

**Una skill que ya cargó se queda cargada.** No se descarga al terminar el tema.
Por eso un `SKILL.md` de tres mil palabras que cubre seis casos te cobra los seis
aunque el de hoy sea uno. La salida no es escribir menos: es partir el cuerpo del
material de apoyo.

**El subagente es la única pieza que trabaja sin gastarte contexto.** Corre en su
ventana, lee cuarenta archivos si hace falta, y te devuelve el resumen. Los
cuarenta archivos no entran a tu conversación. A cambio no te puede preguntar
nada a mitad de camino.

**Un hook cuesta cero mientras no diga nada.** Y acá hay una trampa que hace
perder una tarde: lo que el hook imprima por salida estándar **no entra al
contexto**, va al log de depuración. Las excepciones son `UserPromptSubmit`,
`UserPromptExpansion` y `SessionStart`.

Si querés que Claude lea algo que produjo el hook, el hook tiene que devolverlo
en `hookSpecificOutput.additionalContext`. Un `PostToolUse` con un `echo` adentro,
esperando que el modelo lo lea, no le llega a nadie.

## Lo que cambió con el MCP, y que conviene desaprender

Durante un tiempo el consejo fue: no instales muchos servidores MCP porque los
esquemas de sus herramientas te comen el contexto en cada turno.

Hoy eso ya no describe el comportamiento por defecto. La búsqueda de herramientas
viene prendida y difiere los esquemas: al arranque entran los nombres, y el
esquema completo llega cuando alguna herramienta se va a usar de verdad.

El costo que sí sigue existiendo es otro, y es de calidad, no de tokens. Entre
cuarenta nombres parecidos el modelo elige peor que entre seis. Un servidor con
herramientas «por las dudas» sigue siendo mala idea, pero por esto y no por lo
otro.

Y no hace falta que nadie te lo estime: `/context` te muestra en qué se está
yendo la ventana, desglosado, y `/mcp` te muestra el estado y las herramientas de
cada servidor. Medilo en tu máquina antes de discutirlo.

## Después de `/compact`

La lista de descripciones de skills no se vuelve a inyectar. Se conservan las
skills que efectivamente se usaron.

Una skill marcada con `disable-model-invocation: true` no aparece en esa lista
ni siquiera al arrancar: se queda afuera del contexto hasta que vos escribís
`/nombre`. Es la forma más barata que hay de tener una skill instalada.

## Cómo verificar todo esto vos mismo

- `/context` te muestra en qué se está yendo la ventana ahora mismo.
- `/mcp` te muestra el estado y las herramientas de cada servidor.
- La documentación tiene una simulación paso a paso del arranque de una sesión,
  en la página «Explore the context window».

Estos números cambian entre versiones. Lo que no cambia es el orden de las
preguntas: quién lo dispara, qué carga, y cuándo.
