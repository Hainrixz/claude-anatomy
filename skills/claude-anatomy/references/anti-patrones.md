# Los anti-patrones, con antes y después

Se lee cuando ya identificaste el anti-patrón en la tabla del cuerpo y querés
el ejemplo concreto de cómo se corrige.

Convención de este archivo: **lo que falla va en bloque de código**, lo que se
corrige va en cita.

## 1 · La skill que pide una API key

Antes:

```
## Cómo configurarla
Necesitás una cuenta en el servicio y pegar tu clave en la variable
SOPORTE_API_KEY antes de que esta skill funcione.
```

Después: la clave se va a un servidor MCP, que la maneja en su propia
configuración. La skill se queda con el criterio.

> La skill decide qué ticket priorizar. Para leer los tickets, llama a las
> herramientas del servidor MCP de soporte. No necesita ni ve ninguna clave.

## 2 · El comando para algo que se repite solo

Antes:

```
## Uso
Escribí /revisar-commits cada vez que termines de programar, para chequear
que no se cuele una credencial.
```

El problema: depende de que la persona se acuerde de tipearlo, y nadie se
acuerda siempre.

Después: si el disparo es un evento del propio Claude Code —por ejemplo,
antes de correr `git commit`— va como hook. Si el disparo es que el tema
apareció en la conversación, va como skill.

> Un hook de `PreToolUse` revisa el diff antes de cada commit y bloquea el que
> tenga una credencial adentro. Nadie tiene que acordarse de nada.

## 3 · El plugin de una sola skill

Antes:

```
mi-plugin/
├── .claude-plugin/
│   └── plugin.json
└── skills/
    └── unica-skill/
        └── SKILL.md
```

Después:

```
unica-skill/
└── SKILL.md
```

> Un plugin de una sola pieza es una skill con un manifiesto de más. El plugin
> aporta recién cuando aparece la segunda pieza que viaja con la primera.

## 4 · El subagente para una decisión de una frase

Antes:

```
agents/elegir-color.md
---
name: elegir-color
description: Decide qué color usar para un botón según la marca.
---
Elegí el color según la guía de marca adjunta. Devolvé el hex.
```

El costo de aislar un contexto aparte no se paga con un solo paso de criterio.

Después:

> La decisión de color va en el cuerpo de la skill que ya está armando el
> botón. No hace falta delegarla a nada aparte.

## 5 · El hook que necesita criterio

Antes:

```json
{
  "hooks": {
    "PreToolUse": [{
      "matcher": "Bash",
      "hooks": [{"type": "command", "command": "revisar-si-es-peligroso.sh"}]
    }]
  }
}
```

Si «peligroso» depende de contexto —qué proyecto es, qué se venía haciendo—,
un script fijo no lo va a resolver bien, y va a fallar en silencio en los
casos borde.

Después:

> Si la regla es fija —«bloqueá cualquier `rm -rf` sobre `/`»—, el hook
> alcanza. Si depende de juicio, la decisión va en una skill, y el hook se
> queda solo con lo que siempre se resuelve igual.

## 6 · El SKILL.md que explica seis casos en un solo cuerpo

Antes: un `SKILL.md` de 2800 palabras que cubre seis proveedores distintos de
envío, con una sección larga por cada uno.

Después:

```
cotizar-envios/
├── SKILL.md              ← el procedimiento y cómo elegir el proveedor
└── references/
    ├── proveedor-a.md
    ├── proveedor-b.md
    └── proveedor-c.md
```

> El cuerpo dice cómo elegir el proveedor y qué hacer una vez elegido. El
> detalle de cada proveedor se lee solo si el caso de hoy es ese.
