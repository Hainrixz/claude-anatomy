# Esqueleto de un comando

Copiá el archivo y completá los marcadores. Vive en `commands/{{nombre}}.md`,
suelto o dentro de un plugin. Se dispara escribiendo `/{{nombre}}`.

```
commands/{{nombre-del-comando}}.md
```

```
---
description: "{{que-hace-en-una-frase}}"
---

{{instrucciones-del-comando:
   - qué pasos sigue, en orden
   - qué le pide a la persona si falta algo
   - qué entrega al terminar}}
```

A diferencia del subagente, el comando corre en la misma conversación que lo
llamó: todo lo que hace queda a la vista de quien lo tipeó, y la persona puede
interrumpirlo o corregirlo a mitad de camino.
