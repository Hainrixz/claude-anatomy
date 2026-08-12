# Esqueleto: subagente

```
.claude/
└── agents/
    └── {{nombre}}.md
```

Un archivo, nada más. Va en `agents/` del proyecto, o en la carpeta equivalente
de tu configuración de usuario si lo querés en todas partes.

## El mínimo que anda

```markdown
---
name: {{nombre}}
description: {{Cuándo el hilo principal tiene que delegarle a este}}
tools: Read, Grep, Glob
model: inherit
color: cyan
---

Sos {{el rol}}.

{{Las instrucciones. Es un prompt de sistema entero: el subagente no ve tu
conversación, así que acá va todo lo que necesita saber.}}

Devolvé {{la forma exacta de la salida}}.
```

## Los campos, y sus valores válidos

| Campo | Valores |
|---|---|
| `name` | Obligatorio. Minúsculas y guiones. No puede llevar `:` |
| `description` | Obligatorio. Es lo que decide cuándo se le delega |
| `tools` | Las que puede usar. Si lo omitís, hereda todas |
| `disallowedTools` | Las que se le sacan |
| `model` | `sonnet`, `opus`, `haiku`, `fable`, un id completo, o `inherit` |
| `color` | `red`, `blue`, `green`, `yellow`, `purple`, `orange`, `pink`, `cyan` |
| `permissionMode` | `default`, `acceptEdits`, `auto`, `dontAsk`, `bypassPermissions`, `plan` |
| `maxTurns` | Cuántos turnos antes de frenar |
| `skills` | Skills que se precargan enteras al arrancar |
| `memory` | `user`, `project` o `local`, para que aprenda entre sesiones |
| `background` | En `true`, siempre corre en segundo plano |
| `isolation` | En `worktree`, corre sobre una copia aislada del repo |

Dos que se equivocan seguido: `color` no acepta `magenta`, y `model` por defecto
es `inherit`, no el modelo más caro.

## Lo que un subagente no puede hacer

**No te puede preguntar nada.** La herramienta de hacer preguntas no existe
adentro de un subagente. Si el trabajo necesita ida y vuelta con una persona, esa
parte se queda en el hilo principal.

**No ve tu conversación.** Arranca limpio. Todo lo que necesite saber va en su
prompt.

**Corriendo en segundo plano tiene menos herramientas.** Y ese es el default. Si
tu subagente necesita una herramienta que no está en el conjunto reducido,
declaralo con `background: false`.

## Si el subagente viaja adentro de un plugin

Tres campos se ignoran: `hooks`, `mcpServers` y `permissionMode`.

Esto muerde así: lo escribís suelto, anda, lo metés en un plugin, y deja de
respetar los permisos sin decir nada. Si dependés de esos tres, el subagente se
queda suelto.

## Cuando no alcanza con uno

Si en vez de tres subagentes necesitás cincuenta, que se revisen entre ellos y
que el plan sobreviva a la ventana de contexto, eso ya no es un subagente suelto:
es un workflow, que es un script que orquesta subagentes y guarda el plan afuera
del contexto.

## Antes de escribir esto, una pregunta

Si lo único que querés es que una tarea corra aparte para no llenarte el
contexto, no hace falta un subagente propio. Le ponés `context: fork` a la skill
que ya tenías. El subagente propio se justifica cuando es un trabajador reusable,
con herramientas recortadas, al que van a llamar por su nombre desde varios
lugares.
