# Los modos de una skill

## Lo que cambió, y por qué importa

Durante mucho tiempo esto eran tres piezas distintas: la skill que el modelo
usaba solo, el comando de barra que escribías vos, y el subagente que corría
aparte para no llenarte el contexto.

Hoy son una pieza con tres perillas.

Los comandos se fusionaron con las skills. Un archivo en `.claude/commands/deploy.md`
y una skill en `.claude/skills/deploy/SKILL.md` producen los dos el mismo
`/deploy` y funcionan igual. Los comandos que ya tenías escritos siguen andando y
no hay que migrar nada. Para algo nuevo conviene la skill, porque además admite
archivos de apoyo.

Y el aislamiento de contexto —lo único que antes obligaba a escribir un subagente
aparte— ahora es un campo del frontmatter.

Si venís de material escrito hace unos meses, esto es lo primero que hay que
corregir.

## Las dos preguntas que fijan el modo

**¿Quién la dispara?** Vos escribiendo `/nombre`, o el modelo cuando aparece el
tema.

**¿Dónde corre?** En tu conversación, o en una ventana aparte.

| | La disparás vos | La dispara el modelo | Las dos |
|---|---|---|---|
| **Corre en tu conversación** | `disable-model-invocation: true` | `user-invocable: false` | el default: no escribís nada |
| **Corre aparte** | `disable-model-invocation: true` + `context: fork` | `context: fork` | `context: fork` |

Los cuatro casos, dichos en criollo:

- **El default.** Ni escribís nada ni pasa nada raro. Vos la podés invocar y el
  modelo también.
- **`disable-model-invocation: true`.** Solo vos. Es lo que antes era un comando.
  Va para cosas con consecuencias: desplegar, commitear, mandar un mensaje. No
  querés que el modelo decida que hoy es buen día para desplegar. Regalo extra: no
  ocupa contexto hasta que la invocás.
- **`user-invocable: false`.** Solo el modelo. Va para conocimiento de fondo que
  no es una acción. «Cómo funciona el sistema viejo de facturación» es algo que
  Claude debería saber cuando el tema aparece, pero `/sistema-viejo` no es nada
  que alguien quiera tipear.
- **`context: fork`.** Corre en un subagente. Sirve cuando la tarea genera mucho
  ruido intermedio que a vos no te sirve: leer cuarenta archivos y traer tres
  hallazgos.

## Cuándo `context: fork` no sirve

Solo sirve si la skill trae una tarea que ejecutar.

Si la skill es «seguí estas convenciones al escribir tests», el subagente recibe
las pautas, no tiene nada que hacer con ellas, y vuelve con las manos vacías.
Para conocimiento va el default, o `user-invocable: false`.

La regla corta: `context: fork` para skills con instrucciones ejecutables, nunca
para skills que solo saben algo.

## El campo `agent`, y la dirección inversa

Con `context: fork` podés elegir en qué tipo de subagente corre, con `agent:`.

Y existe el camino inverso: un subagente puede declarar `skills:` para precargar
skills al arrancar. Ojo con esto, porque es al revés de lo que pasa en una sesión
normal: ahí se precarga el **contenido completo** de la skill, no la descripción.

## Todos los campos

Claude Code acepta todos estos en el frontmatter de un `SKILL.md`:

| Campo | Para qué |
|---|---|
| `name` | Obligatorio |
| `description` | Obligatorio. Es lo que hace que dispare |
| `argument-hint` | Qué argumentos espera, para el autocompletado |
| `arguments` | Nombres de los argumentos, para usarlos como `$nombre` |
| `disable-model-invocation` | Solo la disparás vos |
| `user-invocable` | En `false`, solo la dispara el modelo |
| `allowed-tools` | Herramientas que no piden permiso durante ese turno |
| `disallowed-tools` | Herramientas que se sacan mientras la skill está activa |
| `model` | Con qué modelo corre |
| `context` | En `fork`, corre en un subagente |
| `agent` | Qué tipo de subagente, si hay `context: fork` |
| `background` | En `false`, espera el resultado en el mismo turno |
| `metadata` | Datos tuyos. Claude Code no los mira |
| `license` | La licencia |
| `compatibility` | Requisitos del entorno. Hasta 500 caracteres |

Sustituciones que podés usar en el cuerpo: `$ARGUMENTS`, `$0` para el primer
argumento, `$1` para el segundo, `$nombre` para los declarados en `arguments`,
más `${CLAUDE_SKILL_DIR}` y `${CLAUDE_PROJECT_DIR}`.

## El precio de usar los campos extra

Acá hay un cruce que conviene saber antes y no después.

Las skills siguen un estándar abierto que admite **seis** campos: `name`,
`description`, `license`, `compatibility`, `metadata` y `allowed-tools`. Todo lo
demás es una extensión de Claude Code.

Mientras la skill viva en Claude Code, usá lo que quieras. Pero si la vas a
subir a claude.ai, usarla por la API de Skills o empaquetarla con las
herramientas del estándar, cualquier campo de más falla con un mensaje de este
tipo:

```
Unexpected key(s) in SKILL.md frontmatter: argument-hint.
Allowed properties are: allowed-tools, compatibility, description, license,
metadata, name
```

La decisión, en una línea: si la skill tiene que viajar, seis campos; si es tuya
y vive en Claude Code, usá todo.

Un detalle que sorprende: `version` **no** es uno de los seis. Va adentro de
`metadata`, así:

```yaml
metadata:
  version: 0.2.0
```

## De dónde sale el nombre del comando

En una skill tuya o de proyecto, el comando sale del **nombre de la carpeta**. El
campo `name` es solo la etiqueta que se muestra en los listados.

En una skill que viene adentro de un plugin es distinto: ahí `name` fija el
último tramo, y el prefijo del plugin se mantiene. `mi-plugin/skills/revisar/SKILL.md`
con `name: fancy` se invoca como `/mi-plugin:fancy`.

Por eso conviene que la carpeta y el `name` digan lo mismo. Cuando no coinciden,
el que se equivoca sos vos dentro de tres meses.
