# Esqueleto: servidor MCP

Un servidor MCP casi nunca se escribe desde cero. Los servicios conocidos
—GitHub, Slack, Postgres, Sentry, Notion— ya tienen el suyo. Lo que se arma
primero es la **conexión**.

## Conectar uno que ya existe

```bash
claude mcp add --transport http {{nombre}} https://{{servidor}}/mcp
claude mcp add --env API_KEY=... --transport stdio {{nombre}} -- {{comando}}
```

Lo mismo escrito a mano, en `.mcp.json` en la raíz del proyecto:

```json
{
  "mcpServers": {
    "{{nombre}}": {
      "command": "{{comando}}",
      "args": ["{{argumento}}"],
      "env": { "{{VARIABLE}}": "${{{VARIABLE}}}" }
    }
  }
}
```

**El envoltorio `mcpServers` va siempre.** En el `.mcp.json` del proyecto y en el
de un plugin, los dos. Sin él, el servidor no carga.

Una entrada con `url` y sin `type` es un error de configuración: Claude Code la
lee como si fuera un servidor por stdio y la saltea.

## Los transportes

| Transporte | Cuándo |
|---|---|
| `stdio` | Corre como proceso local en tu máquina. Para lo que necesita tocar el disco |
| `http` | Servidores remotos. Es el recomendado. Acepta el alias `streamable-http` |
| `sse` | Obsoleto. Solo si el servicio no expone otra cosa |
| `ws` | Conexión persistente en las dos direcciones. Sin OAuth |

## Las credenciales

Nunca adentro del `.mcp.json` que se versiona. Van por variable de entorno, y el
repo lleva un `env.example` con los nombres y sin los valores:

```
{{SERVICIO}}_API_KEY=
{{SERVICIO}}_URL=
```

## Si lo vas a escribir vos

```
{{nombre}}-mcp/
├── servidor/
│   ├── servidor.py       ← el proceso que habla el protocolo
│   └── tools.py          ← una función por operación
├── .mcp.json
├── env.example
└── pruebas/
```

Dos reglas que ahorran disgustos:

**Exponé las operaciones que alguien pidió, no las que la API tiene.** Envolver
cuarenta endpoints «por las dudas» no cuesta tokens como costaba antes —los
esquemas quedan diferidos— pero sí empeora la elección: entre cuarenta nombres
parecidos el modelo acierta menos que entre seis.

**Separá lo que consulta de lo que escribe.** Y que lo que escribe pida
confirmación.

## Si el sistema de afuera tiene que empujar

Todo lo de arriba es Claude llamando hacia afuera. Existe la dirección contraria:
que el sistema externo meta un evento adentro de una sesión abierta —el CI que
falló, el mensaje que llegó— y Claude reaccione.

Eso es un canal, y está en vista previa. Es un servidor MCP por stdio que declara
la capacidad `claude/channel`. La pieza es la misma; lo que cambia es para qué
lado va la flecha.

**Declararla no alcanza.** El servidor tiene que estar nombrado en `--channels` al
arrancar la sesión, y mientras dure la vista previa un canal propio no está en la
lista aprobada: para probarlo local hace falta una bandera de desarrollo. Si
armaste el servidor y no llega ningún evento, es esto y no tu código.

## Adentro de un plugin

El `.mcp.json` va en la raíz del plugin, con el mismo envoltorio, y las rutas se
escriben con `${CLAUDE_PLUGIN_ROOT}`:

```json
{
  "mcpServers": {
    "{{nombre}}": {
      "command": "${CLAUDE_PLUGIN_ROOT}/servidor/servidor.py"
    }
  }
}
```

Una ruta relativa ahí adentro se resuelve contra la carpeta de trabajo del que
instala, que no es donde está el plugin. Anda en tu máquina y falla en la de al
lado.
