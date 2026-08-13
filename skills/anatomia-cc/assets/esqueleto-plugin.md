# Esqueleto: plugin

```
{{nombre-del-plugin}}/
├── .claude-plugin/
│   ├── plugin.json           ← el manifiesto
│   └── marketplace.json      ← solo si el repo se reparte solo
├── skills/
│   └── {{nombre}}/SKILL.md
├── agents/
│   └── {{nombre}}.md
├── commands/                 ← formato viejo, sigue andando
├── hooks/
│   └── hooks.json
└── .mcp.json
```

Dos reglas de ubicación que rompen el plugin si se equivocan:

**El manifiesto va adentro de `.claude-plugin/`.** Un `plugin.json` suelto en la
raíz no lo lee nadie.

**Las piezas van en la raíz, no adentro de `.claude-plugin/`.** Esa carpeta lleva
manifiestos, nada más.

## El manifiesto mínimo

```json
{
  "name": "{{nombre-del-plugin}}",
  "version": "0.1.0",
  "description": "{{Qué trae adentro}}",
  "license": "MIT"
}
```

`name` es el único campo obligatorio, y de hecho el `plugin.json` entero es
opcional: sin él, Claude Code descubre las piezas por las carpetas. Igual
conviene tenerlo, porque es lo que te deja versionar.

## Si adentro va un servidor MCP

```json
{
  "mcpServers": {
    "{{nombre}}": {
      "command": "${CLAUDE_PLUGIN_ROOT}/servidor/servidor.py"
    }
  }
}
```

El envoltorio `mcpServers` va **siempre**, tanto en el `.mcp.json` de la raíz del
plugin como en el del proyecto. Y toda ruta se escribe con `${CLAUDE_PLUGIN_ROOT}`:
una ruta relativa se resuelve contra la carpeta de trabajo del que instala, que no
es donde quedó el plugin.

## Lo que un subagente pierde al entrar a un plugin

`hooks`, `mcpServers` y `permissionMode` se ignoran para subagentes que vienen
adentro de un plugin. Lo escribís suelto, anda, lo empaquetás, y deja de respetar
los permisos sin avisar.

## Repartirlo

Un plugin instalado a mano no necesita nada más. Para que otros lo instalen con
un comando, hace falta un marketplace: un `marketplace.json` en un repo de git.

Si el repo tiene un solo plugin y es el repo mismo:

```json
{
  "name": "{{nombre-del-repo}}",
  "owner": { "name": "{{usuario}}", "url": "https://github.com/{{usuario}}" },
  "plugins": [
    {
      "name": "{{nombre-del-plugin}}",
      "source": "./",
      "version": "0.1.0",
      "description": "{{Qué trae adentro}}",
      "license": "MIT"
    }
  ]
}
```

Y se instala así:

```
/plugin marketplace add {{usuario}}/{{nombre-del-repo}}
/plugin install {{nombre-del-plugin}}@{{nombre-del-repo}}
```

`source: "./"` quiere decir «el plugin es este mismo repo». Si en cambio el
marketplace es un índice que apunta a varios repos, cada entrada lleva la URL del
repo de destino.

## Cuándo el plugin todavía no hace falta

Si `skills/` tiene una sola entrada y `agents/`, `commands/` y `hooks/` están
vacías, el manifiesto no está sumando nada sobre dejar la skill suelta en
`.claude/skills/`.

El plugin se gana el lugar cuando hay dos o más piezas que se instalan juntas o
no sirven, o cuando lo mismo tiene que andar en otro repo o en la máquina de otra
persona.
