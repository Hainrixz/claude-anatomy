# Cuando lo que querés no vive adentro de una sesión

## De qué se trata este archivo

Las piezas clásicas —skill, subagente, hook, MCP— comparten un supuesto: hay una
persona con Claude Code abierto, escribiendo.

A veces no. A veces lo que hace falta es una pantalla que alguien abre en el
navegador, o Claude adentro de un producto que usa gente que no tiene Claude
Code, o algo que corre a las tres de la mañana.

Eso es lo que hay acá.

## Una pantalla para que alguien mire

Es un artefacto: una página que Claude publica desde la sesión, con su link, que
se actualiza en el mismo lugar cada vez que se republica.

Sirve cuando el resultado se entiende mejor mirándolo que leyéndolo. Un diff
anotado, un tablero, cuatro maquetas al lado de la otra.

Es **una página sola y sin backend**. No guarda formularios, no tiene rutas, y
para traer datos de afuera solo puede llamar conectores MCP. Si necesitás más que
eso, no es un artefacto: es una aplicación.

El detalle en `assets/esqueleto-artefacto.md`.

## Claude adentro de tu producto

Es el Agent SDK: Claude Code como librería, en Python o TypeScript. Desde otro
lenguaje se corre la CLI con `-p` y `--output-format json`.

Carga tu `.claude/` igual que Claude Code, así que las skills y los subagentes
que ya escribiste andan adentro sin reescribirlos. Por eso conviene probar la
idea como skill primero.

Dos cosas que cambian todo: se paga con una clave de API, y los permisos los
decide tu código porque no hay nadie mirando.

El detalle en `assets/esqueleto-app-sdk.md`.

## Que corra solo

Tres formas, y la elección sale de una pregunta: ¿tiene que correr con tu máquina
apagada?

Sí, es una rutina en la nube. No, y la sesión puede estar cerrada, es una tarea
del escritorio. No, y alcanza con que corra mientras trabajás, es `/loop`.

La trampa de la rutina: corre sobre un clon limpio y **no lee la carpeta de
skills de tu máquina**. Hay tres salidas: habilitar la skill en tu cuenta de
claude.ai, commitearla en el `.claude/skills/` del repo, o mandarla en un plugin
declarado en el `.claude/settings.json` del repo.

El detalle en `assets/esqueleto-rutina.md`.

## Cuando son demasiados subagentes

Un subagente resuelve una tarea aparte. Tres subagentes también.

Cuando son cincuenta, y tienen que revisarse entre ellos, y el plan de quién hace
qué no entra en una ventana de contexto, eso es un workflow: un script que
orquesta subagentes, con el plan escrito afuera del contexto en vez de adentro.

No hay carpeta que armar para correrlo la primera vez: se le pide a Claude que
escriba el script, que es JavaScript. Si el resultado sirve, se guarda en
`.claude/workflows/` y queda para reusar.

## Cuando el evento viene de afuera

Todo lo anterior asume que la sesión pregunta. Existe la dirección contraria: que
el CI que falló, o el mensaje que llegó, entren solos a una sesión que ya está
abierta.

Eso es un canal. Por debajo es un servidor MCP que declara la capacidad
`claude/channel`; lo que cambia es para qué lado va la flecha.

Vale la pena saber que existe, sobre todo para no escribir un `/loop` que
consulta cada cinco minutos algo que podría avisar solo.

## Tres que conviene mirar antes de apoyarte en ellas

**Los equipos de agentes** —varias sesiones coordinadas— son experimentales y
vienen apagados: se prenden con `CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1`.
Construir algo que dependa de eso hoy es construir sobre algo que puede cambiar.

**Los mensajes entre sesiones** son otra cosa, y conviene no meterlos en la misma
bolsa: ya vienen **prendidos** en macOS y Linux, sin configurar nada. O sea que
la pregunta no es cómo activarlos sino si querés apagarlos.

**Los canales** están en vista previa. Y hay un detalle que hace perder una tarde:
que tu servidor declare la capacidad **no alcanza**. Tiene que estar nombrado en
`--channels` al arrancar la sesión, y mientras dure la vista previa un canal
propio ni siquiera entra por ahí sin una bandera de desarrollo. Si armás el MCP y
no llega ningún evento, es esto.

## La pregunta que ordena todo esto

Cuando termina, ¿qué queda y quién lo mira?

- Texto en tu terminal, para vos → una pieza de las clásicas.
- Una pantalla que alguien abre → artefacto.
- Una respuesta que consume otro programa → app con el SDK.
- Nada, porque nadie estaba mirando → rutina.
