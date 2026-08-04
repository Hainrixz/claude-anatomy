# Esqueleto de un servidor MCP

Un servidor MCP no se escribe desde cero acá: casi siempre ya existe uno para
el servicio que necesitás, y lo que se arma es la conexión. Este esqueleto es
esa conexión, no el servidor.

Suelto, en la raíz del proyecto:

```
.mcp.json
```

```json
{
  "{{nombre-del-servidor}}": {
    "command": "{{comando-para-arrancarlo}}",
    "args": ["{{argumento-1}}", "{{argumento-2}}"],
    "env": {
      "{{NOMBRE-DE-LA-VARIABLE}}": "{{de-donde-sale-el-valor}}"
    }
  }
}
```

Dentro de un plugin, el mismo bloque va incrustado en `plugin.json` bajo la
clave `mcpServers`, y cualquier ruta a un archivo del propio plugin usa
`${CLAUDE_PLUGIN_ROOT}` en vez de una ruta relativa suelta.

Cualquier variable de entorno que el servidor necesite se declara en
`env.example`, con el nombre nada más, nunca con el valor real. La skill de
criterio que llama a este servidor —si hace falta una— no ve ni guarda esa
variable: la maneja el servidor.
