# Esqueleto: hook

```
.claude/
├── settings.json         ← acá se declara
└── hooks/
    └── {{tarea}}.sh      ← acá vive el script
```

## El mínimo que anda

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Edit",
        "hooks": [
          {
            "type": "command",
            "command": "${CLAUDE_PROJECT_DIR}/.claude/hooks/formatear.sh"
          }
        ]
      }
    ]
  }
}
```

`matcher` filtra por nombre de herramienta. Si lo omitís, engancha con todo.

## Los eventos que más se usan

| Evento | Cuándo dispara |
|---|---|
| `PreToolUse` | Antes de una herramienta. El único que puede bloquearla |
| `PostToolUse` | Después. Para formatear, para revisar lo que quedó |
| `UserPromptSubmit` | Cuando mandás un mensaje |
| `SessionStart` | Al arrancar la sesión |
| `SessionEnd` | Al cerrarla |
| `Stop` | Cuando Claude termina de responder |
| `SubagentStop` | Cuando termina un subagente |
| `PreCompact` | Antes de compactar el contexto |
| `FileChanged` | Cuando cambia un archivo |
| `Notification` | Cuando Claude Code te avisa algo |

Hay más de treinta en total. Estos son los que cubren la mayoría de los casos
reales; el resto está en la referencia de hooks de la documentación.

## Los tipos de handler

No todo hook es un script de shell. Son cinco:

| Tipo | Qué hace |
|---|---|
| `command` | Corre un comando |
| `http` | Pega a una URL |
| `mcp_tool` | Llama una herramienta de un servidor MCP |
| `prompt` | Le pregunta a un modelo |
| `agent` | Despacha un subagente |

Los últimos dos existen justamente para el caso en que hace falta criterio. Pero
si el criterio es el corazón del asunto y no una excepción, mirá primero si eso
no era una skill.

## Bloquear algo, de verdad

Un `PreToolUse` puede negar la operación. El script decide y contesta por salida
estándar:

```bash
#!/bin/bash
COMANDO=$(jq -r '.tool_input.command')

if echo "$COMANDO" | grep -q 'rm -rf'; then
  jq -n '{
    hookSpecificOutput: {
      hookEventName: "PreToolUse",
      permissionDecision: "deny",
      permissionDecisionReason: "Comando destructivo frenado por un hook"
    }
  }'
else
  exit 0
fi
```

## La razón de fondo para elegir un hook

Una instrucción escrita en un `CLAUDE.md` o en una skill es un **pedido**. Se
cumple casi siempre, y «casi siempre» es exactamente el problema cuando lo que
está en juego es borrar algo o publicar algo.

Un hook no pasa por el criterio del modelo. Es la única forma de garantizar que
algo pase todas las veces.

El costo de esa garantía es que no razona. Si lo que define «peligroso» depende
del caso, un script fijo va a fallar en silencio en los casos raros. Ahí la regla
fija va en el hook y el criterio va en una skill.

## Lo que cuesta

Nada, mientras no diga nada. El hook corre afuera del modelo. Si imprime algo
para que Claude lo lea, eso sí entra al contexto.
