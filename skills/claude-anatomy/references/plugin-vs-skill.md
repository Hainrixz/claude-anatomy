# Plugin o skill suelta

Este archivo se lee cuando dudás si empaquetar en un plugin o dejar la skill
sola. Para la mayoría de los casos, la sección del cuerpo alcanza.

## La pregunta que decide

No es «¿esto es lo bastante importante para un plugin?». Es «¿hay dos o más
piezas que siempre viajan juntas?».

Una skill sola, sin comando ni hook que la acompañe, no gana nada adentro de un
plugin. El manifiesto agrega un archivo más para envolver algo que ya
funcionaba sin él.

## Cuándo un plugin aporta de verdad

**Una skill con su comando.** La skill enseña el criterio, el comando dispara
el flujo completo a mano. Si el comando no tiene sentido sin la skill instalada
—y viceversa—, van juntos.

**Una skill con su hook.** La skill decide, el hook aplica esa decisión sin
pasar por el modelo en cada paso. Ejemplo: una skill que define qué archivos
nunca se commitean, y un hook de `PreToolUse` que lo hace cumplir.

**Varias skills del mismo dominio, con un ciclo de vida compartido.** Si tres
skills siempre se instalan y se desinstalan juntas porque forman un mismo
producto, un plugin las agrupa en una instalación.

**Un servidor MCP con la skill que sabe usarlo.** El MCP expone las
herramientas; la skill trae el criterio de cuándo llamarlas. Separados, la
persona tiene que enterarse de que existen los dos por su cuenta.

## El anti-patrón

Un plugin con una sola skill adentro y nada más. Se ve así:

```
mi-plugin/
├── .claude-plugin/
│   └── plugin.json
└── skills/
    └── mi-skill/
        └── SKILL.md
```

Compará contra la skill sola:

```
mi-skill/
└── SKILL.md
```

La segunda hace exactamente lo mismo, con un archivo menos y sin manifiesto que
mantener. El plugin no suma nada acá: solo aparece cuando hay una segunda pieza
real.

## Señal rápida

Si al escribir el plugin la carpeta `skills/` tiene una sola entrada y
`agents/`, `commands/` y `hooks/` están vacías, todavía no hace falta el
plugin. Esperá a la segunda pieza.
