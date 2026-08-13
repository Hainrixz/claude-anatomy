# Esqueleto: que corra solo, cada tanto

Tampoco hay carpeta acá. Lo que hay es elegir cuál de las tres formas es la tuya,
y esa elección la define una sola pregunta: **¿tiene que correr con tu
computadora apagada?**

## Las tres formas

| | Rutina en la nube | Tarea del escritorio | `/loop` |
|---|---|---|---|
| Dónde corre | En la nube | En tu máquina | En tu máquina |
| ¿La compu prendida? | No hace falta | Sí | Sí |
| ¿La sesión abierta? | No hace falta | No hace falta | Sí |
| ¿Ve tus archivos? | No: clona el repo de nuevo | Sí | Sí |
| Intervalo mínimo | 1 hora | 1 minuto | 1 minuto |

## Rutina en la nube

Se arma con `/schedule`. Sirve para el resumen de los lunes, la revisión de los
PR de la noche, la auditoría semanal de dependencias.

El detalle que hace perder una tarde: **la rutina no ve tus skills personales.**
Arranca como una sesión nueva sobre un clon limpio del repo. Lo que tengas en la
carpeta de skills de tu configuración de usuario no existe ahí.

Si la rutina necesita una skill, la skill tiene que viajar:

- adentro del repo, en `.claude/skills/`, o
- adentro de un plugin declarado en `.claude/settings.json`

Los servidores MCP también se configuran por rutina, no se heredan.

## Tarea del escritorio

Se programa desde la app de escritorio. Corre en tu máquina, así que ve tus
archivos y tus herramientas locales, y baja hasta un minuto de intervalo.

El precio es que la máquina tiene que estar prendida.

## `/loop`

Para mirar algo mientras estás trabajando. Un deploy que está corriendo, un PR
que espera revisión.

```
/loop 5m fijate si terminó el deploy y contame qué pasó
```

Sin intervalo, Claude elige cuánto esperar según lo que vio: menos si hay
movimiento, más si está todo quieto.

```
/loop fijate si pasó el CI y respondé los comentarios de la revisión
```

Vive adentro de la sesión. Si cerrás la conversación, se termina. Y vence a los
siete días.

## Antes de programar nada

Dos preguntas que ahorran la pieza entera.

**¿Es un intervalo, o es un evento?** Si lo que querés es reaccionar cuando pasa
algo —el CI falló, llegó un mensaje— consultar cada cinco minutos es la forma
cara de enterarse. Eso es un canal: el sistema de afuera empuja el evento adentro
de la sesión.

**¿Hace falta un modelo?** Si la tarea siempre se hace igual y no hay nada que
decidir, un cron con un script cuesta menos y falla menos.
