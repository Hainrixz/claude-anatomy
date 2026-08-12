# Esqueleto: skill

```
{{nombre-de-la-skill}}/
├── SKILL.md              ← lo único obligatorio
├── references/           ← se lee solo si el cuerpo manda a leerlo
│   └── {{tema}}.md
├── assets/               ← se copia, no se lee
│   └── {{plantilla}}.md
└── scripts/              ← se ejecuta, no se lee
    └── {{tarea}}.py
```

La carpeta va en `.claude/skills/` del proyecto, o en la carpeta de skills de tu
configuración de usuario si la querés en todas partes.

El nombre de la carpeta es el comando. Si la carpeta se llama `revisar-pr`, se
invoca con `/revisar-pr`.

## El mínimo que anda

```markdown
---
name: {{nombre-de-la-skill}}
description: "ES: {{qué hace}}. Usala cuando {{frase real 1}}, {{frase real 2}} o {{frase real 3}}. EN: {{the same}}."
metadata:
  version: 0.1.0
---

# {{Título}}

## El dolor

{{Qué se rompe hoy, en dos o tres frases.}}

## Cuándo usar esto

{{En qué momento entra, con las señales concretas.}}

## Procedimiento

1. {{Paso}}
2. {{Paso}}

## Qué entregás

{{La salida, en una lista corta.}}
```

## Las cuatro variantes

Cambia una línea del frontmatter, cambia quién la dispara y dónde corre.

**Que la dispare solo el modelo, cuando aparece el tema.** No agregues nada. Es
el default.

**Que la dispares solo vos.** Para cosas con consecuencias: desplegar, commitear,
mandar mensajes.

```yaml
disable-model-invocation: true
```

**Que la use solo el modelo, sin salir en el menú de `/`.** Para conocimiento de
fondo que no es una acción.

```yaml
user-invocable: false
```

**Que corra en un subagente, para no llenarte el contexto.** Solo sirve si la
skill trae una tarea concreta.

```yaml
context: fork
agent: Explore
```

Ojo: estos tres campos son extensiones de Claude Code. Si la skill tiene que
viajar a claude.ai o a la API, el estándar admite seis campos y ninguno de estos
está adentro. Está explicado en `references/modos-de-skill.md`.

## Si venís de un comando de barra

Un archivo en `.claude/commands/desplegar.md` sigue funcionando y no hay que
tocarlo. Si lo querés pasar a skill:

```
.claude/commands/desplegar.md      →   .claude/skills/desplegar/SKILL.md
```

Agregale `disable-model-invocation: true` para que se comporte igual que antes, y
listo. Lo que ganás es la carpeta: ahora podés colgarle `references/` y `scripts/`.

## Si lo que querías era cambiar el tono, no el conocimiento

Eso no es una skill: es un estilo de salida. Es un `.md` con frontmatter que va
en `.claude/output-styles/` del proyecto, o en la carpeta equivalente de tu
configuración de usuario.

```markdown
---
name: {{Nombre visible}}
description: {{Qué cambia en la respuesta}}
keep-coding-instructions: true
---

{{Las instrucciones que se suman al prompt de sistema.}}
```

Se elige con `/config`. Y hay un detalle que hace perder tiempo: el estilo es
parte del prompt de sistema, que se lee una sola vez al arrancar. El cambio
aplica después de `/clear` o en la sesión siguiente, no en el momento.

## Para escribir la skill completa

Este archivo da el árbol, no el contenido. Para que alguien la escriba entera,
con la descripción que dispara y las pruebas, está la skill `skill-smith`.
