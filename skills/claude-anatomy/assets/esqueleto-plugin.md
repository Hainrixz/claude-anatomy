# Esqueleto de un plugin

Copiá el árbol. Las carpetas `skills/`, `agents/`, `commands/` y `hooks/` van
en la raíz del plugin, nunca dentro de `.claude-plugin/`.

```
{{nombre-del-plugin}}/
├── .claude-plugin/
│   └── plugin.json
├── skills/
│   └── {{nombre-skill}}/
│       └── SKILL.md
├── agents/
│   └── {{nombre-subagente}}.md
├── commands/
│   └── {{nombre-comando}}.md
└── hooks/
    └── hooks.json
```

Las cuatro carpetas de piezas son opcionales: un plugin lleva solo las que
necesita. Si al completarlo `skills/` queda con una sola entrada y las otras
tres vacías, releé `references/plugin-vs-skill.md`: puede que todavía no haga
falta el plugin.

## `.claude-plugin/plugin.json` mínimo

```json
{
  "name": "{{nombre-del-plugin}}",
  "description": "{{que-agrupa-y-por-que-viajan-juntas-estas-piezas}}",
  "version": "0.1.0"
}
```

Si el plugin incluye un servidor MCP, el `.mcp.json` va en la raíz del plugin
como un mapa pelado (sin el envoltorio `mcpServers`), y cualquier ruta adentro
usa `${CLAUDE_PLUGIN_ROOT}` en vez de una ruta relativa suelta.
