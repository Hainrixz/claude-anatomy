# Esqueleto de un hook

Copiá el bloque y completá los marcadores. Vive en `hooks/hooks.json`, suelto
o dentro de un plugin.

```
hooks/hooks.json
```

```json
{
  "hooks": {
    "{{PreToolUse|PostToolUse|Stop|SessionStart}}": [
      {
        "matcher": "{{patron-opcional-de-que-herramienta-dispara-esto}}",
        "hooks": [
          {
            "type": "command",
            "command": "{{comando-que-corre}}"
          }
        ]
      }
    ]
  }
}
```

Antes de escribir el hook, confirmá con `references/anti-patrones.md#5` que la
regla que va adentro es fija, siempre igual para el mismo evento. Si depende
de juicio o de contexto que cambia caso a caso, esa parte va en una skill, no
en el hook.

El comando corre por fuera del modelo: no se beneficia de que un modelo lo lea
ni lo interprete, así que tiene que ser determinista y devolver un código de
salida claro.
