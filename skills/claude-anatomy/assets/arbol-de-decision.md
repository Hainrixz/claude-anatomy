# El árbol de decisión, en una página

Para imprimir, pegar en un canal, o tener al lado mientras arrancás un proyecto.

Cuatro pasos. Adentro de cada uno, la primera pregunta que da sí cierra ese paso.
El Paso 3 se contesta siempre, al final.

## Las tres preguntas que más rinden

```
1. ¿Quién aprieta el botón?
   Vos, escribiendo /algo · el modelo, cuando aparece el tema · un evento de
   Claude Code · el reloj · un sistema de afuera · nadie, es una regla que
   Claude tiene que saber siempre.

2. ¿Toca algo de afuera que pida su propia credencial?
   Una base, una API, la cuenta de un servicio.

3. Cuando termina, ¿qué queda y quién lo mira?
   Texto en tu terminal · una pantalla que alguien abre en el navegador ·
   archivos cambiados en el repo · una respuesta que consume otro programa.
```

## El árbol

```
PASO 0 · ¿HACE FALTA CONSTRUIR ALGO?

  0.1 ¿Es que Claude se acuerde de una regla tuya?      SI -> CLAUDE.md
                                                        NO -> sigue
  0.2 ¿Ya lo hizo otro?                                 SI -> instalalo
                                                        NO -> sigue
  0.3 ¿Te molesta CÓMO contesta, no lo que sabe?        SI -> estilo de salida
                                                        NO -> sigue
  0.4 ¿Lo resuelve un script, sin modelo adentro?       SI -> script
                                                        NO -> PASO 1

PASO 1 · ¿ADENTRO DE UNA SESIÓN, O AFUERA?

  1.1 ¿Se entrega una pantalla que alguien mira?        SI -> artefacto
                                                        NO -> sigue
  1.2 ¿Lo usa gente que no abre Claude Code?            SI -> app, Agent SDK
                                                        NO -> sigue
  1.3 ¿Corre con tu computadora apagada?                SI -> rutina en la nube
                                                        NO -> PASO 2

PASO 2 · QUÉ PIEZA

  2.1 ¿Tiene que pasar SIEMPRE, sin criterio?           SI -> hook
                                                        NO -> sigue
  2.2 ¿Entra a un sistema con credencial propia?        SI -> servidor MCP
                                                        NO -> sigue
  2.3 ¿Es un trabajador reusable, con nombre?           SI -> subagente
                                                        NO -> sigue
  2.4 Lo que queda                                            -> SKILL
                                                               (elegí el modo)

PASO 3 · ¿SE EMPAQUETA?  (siempre, al final)

  3.1 ¿Dos o más piezas que viajan juntas?              SI -> plugin
      ¿O tiene que andar en otra máquina?                     (+ marketplace
                                                               si lo repartís)
                                                        NO -> dejalo suelto
```

## Los cuatro modos de una skill

| | La disparás vos | La dispara el modelo | Las dos |
|---|---|---|---|
| **En tu conversación** | `disable-model-invocation: true` | `user-invocable: false` | el default |
| **Aparte** | `disable-model-invocation` + `context: fork` | `context: fork` | `context: fork` |

## Dónde vive cada pieza

| Pieza | Se dispara por | Vive en |
|---|---|---|
| `CLAUDE.md` | Siempre está | `CLAUDE.md` en la raíz |
| Regla con `paths:` | Claude toca esos archivos | `.claude/rules/nombre.md` |
| Estilo de salida | Lo elegís con `/config` | `.claude/output-styles/nombre.md` |
| Skill | El tema aparece, o `/nombre` | `.claude/skills/nombre/SKILL.md` |
| Subagente | El hilo principal delega | `.claude/agents/nombre.md` |
| Hook | Un evento de Claude Code | `.claude/settings.json` |
| Servidor MCP | El modelo llama una herramienta | `.mcp.json` |
| Canal | Un sistema de afuera empuja | Un MCP con `claude/channel` |
| Plugin | Se instala como paquete | `.claude-plugin/plugin.json` |
| Marketplace | Se agrega una vez | `.claude-plugin/marketplace.json` |
| Artefacto | Lo publica la sesión | Un link de claude.ai |
| Rutina | El reloj | Se arma con `/schedule` |
| App | Tu programa lo llama | Tu repo, con el Agent SDK |

## Lo que no está en el árbol y conviene saber

**Workflow.** Cuando en vez de tres subagentes necesitás cincuenta y que se
revisen entre ellos. Es un script que orquesta y guarda el plan afuera del
contexto.

**Equipos de agentes y mensajes entre sesiones.** Existen, están marcados como
experimentales y vienen apagados. No conviene apoyar nada en ellos todavía.
