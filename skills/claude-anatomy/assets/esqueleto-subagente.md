# Esqueleto de un subagente

Copiá el archivo y completá los marcadores. Vive en `agents/{{nombre}}.md`,
suelto o dentro de un plugin.

```
agents/{{nombre-del-subagente}}.md
```

```
---
name: {{nombre-del-subagente}}
description: "{{cuando-el-hilo-principal-tiene-que-invocarlo}}"
model: {{inherit|sonnet|opus|haiku}}
color: {{blue|cyan|green|yellow|magenta|red}}
---

{{prompt-de-sistema-completo:
   - qué recibe
   - qué pasos sigue
   - qué formato tiene que tener el informe que devuelve}}
```

El subagente no puede preguntarle nada a la persona a mitad de camino: corre
aislado y devuelve un informe al final. Si el trabajo necesita ida y vuelta con
alguien, esa parte se queda en el hilo principal, y el subagente se invoca solo
para el tramo que no necesita preguntar nada.

El `description` es lo único que el hilo principal ve para decidir cuándo
delegar: tiene que decir con claridad qué tipo de trabajo recibe este
subagente y qué devuelve.
