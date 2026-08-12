# Los once anti-patrones

Todos salen de lo mismo: elegir la pieza por lo que suena importante en vez de
por quién la dispara y qué carga.

Convención de este archivo: **lo que falla va en bloque de código, lo que se
corrige va en cita.**

## 1. Armar un comando de barra para algo nuevo

```
.claude/commands/revisar-pr.md
```

Los comandos se fusionaron con las skills. El archivo sigue funcionando y no hay
que migrar nada, pero para algo nuevo no es la puerta de entrada.

> Una skill con `disable-model-invocation: true` hace exactamente lo mismo:
> la disparás vos con `/revisar-pr` y el modelo no la usa por su cuenta. Y encima
> te deja colgarle `references/` y `scripts/`, que un `.md` suelto no tiene.

## 2. Escribir un subagente aparte solo para aislar contexto

```
.claude/agents/revisor-de-archivos.md
```

Escrito porque la tarea lee cuarenta archivos y no queríamos ese ruido en la
conversación principal. Ese motivo ya no alcanza.

> El aislamiento hoy es un campo. `context: fork` en la skill que ya tenías la
> corre en un subagente sin que exista un segundo archivo que mantener.
>
> El subagente propio se justifica cuando es un trabajador reusable, con
> herramientas recortadas, al que llaman por su nombre desde varios lugares.

## 3. Un servidor MCP con cuarenta herramientas por las dudas

```
listar_usuarios, crear_usuario, editar_usuario, borrar_usuario,
listar_pedidos, crear_pedido, editar_pedido, borrar_pedido, ...
```

Envolver la API entera porque «alguna vez puede servir».

> El motivo por el que esto está mal cambió, así que vale aclararlo: antes se
> decía que los esquemas te comían el contexto en cada turno. Hoy la búsqueda de
> herramientas los difiere, así que ese costo ya casi no aplica.
>
> Lo que sí queda es que el modelo elige peor entre cuarenta nombres parecidos
> que entre seis. Expone las operaciones que alguien pidió. Y si querés el
> número real de tu máquina, `/mcp` lo mide.

## 4. Una skill que pide una clave de API

```markdown
Para consultar los tickets usá SOPORTE_API_KEY=sk_live_a1b2...
```

Publica la credencial, y además no anda: el modelo no hace un pedido HTTP porque
se lo diga un archivo de texto.

> Son dos piezas. El servidor MCP expone `listar_tickets_abiertos()` y guarda la
> credencial de su lado. La skill se queda con el criterio de priorización y no
> menciona ninguna clave.

## 5. Un plugin con una sola skill adentro que usás solo vos

```
mi-plugin/
├── .claude-plugin/plugin.json
└── skills/
    └── revisar-pr/SKILL.md
```

`agents/`, `commands/` y `hooks/` vacías.

> Dejala suelta en `.claude/skills/revisar-pr/`. El manifiesto no suma nada
> todavía.
>
> El plugin se gana el lugar cuando hay dos o más piezas que se instalan juntas,
> o cuando lo mismo tiene que andar en otra máquina.

## 6. Un hook que tiene que decidir con criterio

```json
{ "matcher": "Bash",
  "hooks": [{ "type": "command", "command": "./es-peligroso.sh" }] }
```

Con un script que intenta adivinar si el comando es riesgoso mirando la línea.

> El hook no pasa por el modelo, y eso es justo lo que lo hace útil. Pero si
> «peligroso» depende del contexto, el script fijo va a fallar en silencio en los
> casos raros, que son los que importan.
>
> La regla fija va en el hook —`rm -rf` se bloquea siempre— y el criterio va en
> una skill. Si de verdad necesitás juicio adentro del hook, existen los tipos
> `prompt` y `agent`, pero pensá primero si eso no era una skill.

## 7. Una regla importante escrita en el CLAUDE.md

```markdown
IMPORTANTE: nunca hagas commit sin correr los tests.
```

Se cumple casi siempre. «Casi siempre» es exactamente el problema cuando lo que
está en juego es publicar algo.

> Una instrucción escrita es un pedido. Si tiene que valer todas las veces, es un
> hook de `PreToolUse`, que es la única pieza que no depende del criterio del
> modelo.

## 8. Un CLAUDE.md de ochocientas líneas

Con la historia del proyecto, las convenciones de seis lenguajes y el detalle de
tres integraciones.

Entra entero en cada sesión, hable de lo que hable la conversación de hoy.

> Dejá en el `CLAUDE.md` lo que aplica siempre, y que sea corto.
>
> Lo que aplica a veces va en skills, que entran cuando el tema aparece. Y lo que
> aplica a ciertos archivos va en `.claude/rules/` con `paths:`, que entra
> solamente cuando Claude toca esos archivos.

## 9. Un SKILL.md de tres mil palabras con seis casos adentro

```markdown
## Proveedor A
...400 palabras...
## Proveedor B
...400 palabras...
## Proveedor C
```

> Una skill que cargó se queda cargada el resto de la sesión. Con seis casos
> adentro pagás los seis aunque el de hoy sea uno.
>
> El cuerpo lleva el procedimiento y la tabla de cuál es cuál. Cada caso se va a
> un archivo en `references/`, y el cuerpo dice cuándo leer cada uno.

## 10. `context: fork` en una skill que solo sabe cosas

```yaml
---
name: convenciones-de-tests
context: fork
---
Usá describe/it. Un assert por test. Nombres en español.
```

> El subagente arranca, recibe pautas, no tiene ninguna tarea que hacer con
> ellas, y vuelve sin nada.
>
> `context: fork` es para skills con instrucciones ejecutables. Para conocimiento
> va el default, o `user-invocable: false` si además no querés que salga en el
> menú de `/`.

## 11. Pedir un artefacto esperando una aplicación

```
Armá un artefacto donde el equipo cargue las horas y queden guardadas.
```

> El artefacto es una página sola y sin backend: no guarda lo que alguien
> escriba en un formulario, no tiene varias rutas y no autentica a nadie.
>
> Para mostrar, comparar o anotar, sirve. Para guardar datos, es una aplicación y
> la hospedás vos.

## Lo que tienen en común

En diez de los once, la pieza elegida era más grande que el problema.

Ese es el sesgo por defecto y conviene tenerlo presente: ante la duda, la pieza
más chica que resuelve el caso es casi siempre la correcta, y siempre es la más
fácil de tirar cuando te equivocaste.
