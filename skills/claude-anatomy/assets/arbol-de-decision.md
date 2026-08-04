# Anatomía de Claude Code — árbol de decisión de una página

Copiá este archivo entero si querés la versión para imprimir o pegar en un
canal. No hace falta leer el resto de la skill para usarlo.

```
¿Qué pieza construyo?

1. ¿Corre sola, sin que nadie la dispare a mano, y al final entrega algo?
   SI -> Agente completo (no es ninguna de las seis piezas de abajo).
   NO -> seguir.

2. ¿Necesita hablar con un sistema externo con su propia credencial?
   SI -> Servidor MCP.
   NO -> seguir.

3. ¿La persona la dispara a mano, escribiendo su nombre?
   SI -> Comando.
   NO -> seguir.

4. ¿Necesita su propio contexto aislado, para no llenar la
   conversación principal con pasos intermedios largos?
   SI -> Subagente.
   NO -> seguir.

5. ¿Corre sola, disparada por un evento del propio Claude Code,
   sin pasar por el criterio del modelo?
   SI -> Hook.
   NO -> seguir.

6. El modelo sabe hacer esto cuando el tema aparece, sin disparo
   a mano y sin tocar nada externo.
   -> Skill.

7. ¿Salieron dos o tres piezas que siempre viajan juntas?
   SI -> Empaquetalas en un Plugin.
   NO -> la pieza sola alcanza.
```

## Las seis piezas, en una línea cada una

| Pieza | Se dispara por | Vive en |
|---|---|---|
| Skill | El tema aparece en la conversación | `skills/nombre/SKILL.md` |
| Plugin | Se instala como paquete | `.claude-plugin/plugin.json` + sus piezas |
| Subagente | El hilo principal delega | `agents/nombre.md` |
| Comando | La persona escribe `/nombre` | `commands/nombre.md` |
| Servidor MCP | El modelo llama una herramienta | `.mcp.json` o `plugin.json` |
| Hook | Un evento de Claude Code | `hooks/hooks.json` |

## Las dos preguntas que más rinden si no alcanza con el árbol

1. ¿Quién o qué la dispara: la persona, el hilo principal, un evento, o nadie
   porque el tema simplemente aparece?
2. ¿Toca algo de afuera que necesite su propia credencial?

Con esas dos respuestas, el árbol de arriba se completa solo.
