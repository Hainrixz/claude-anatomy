# Esqueleto de una skill

Copiá el árbol y completá los marcadores entre llaves dobles. Para construirla
entera —descripción que dispara, evals de disparo y verificación— usá
`skill-smith`; esto es solo el punto de partida.

```
{{nombre-de-la-skill}}/
├── SKILL.md
├── references/
│   └── {{tema-que-solo-se-lee-a-veces}}.md
├── assets/
│   └── {{plantilla-que-se-copia}}.md
└── evals/
    └── evals.json
```

## `SKILL.md` mínimo

```
---
name: {{nombre-de-la-skill}}
description: "ES: {{que-hace}}. Usala cuando {{cuando-dispara}}. EN: {{the same in English}}."
metadata:
  version: 0.1.0
---

# {{Nombre legible}}

## El dolor
{{que-le-pasa-hoy-a-alguien-sin-esto}}

## Cuándo usar esto
{{situaciones-concretas}}

## {{Procedimiento o criterio}}
{{el-cuerpo-docente}}

## Qué entregás
{{la-salida-concreta}}
```

`references/` y `assets/` son opcionales: agregalos solo si el cuerpo pasa las
400 palabras en algún tema, o si hay algo para copiar y completar.
