# Esqueleto: artefacto

Acá no hay carpeta que armar. Un artefacto no es un archivo que escribís: es una
página que Claude publica desde la sesión, en un link de claude.ai que se abre en
el navegador.

Lo que se arma es el pedido.

## Cómo se pide

```
Armá un artefacto que {{muestre esto}}, con {{esta forma}}.
```

Ejemplos que funcionan:

```
Armá un artefacto que recorra este PR con el diff anotado al lado de cada
hallazgo, y que marque la severidad con color.
```

```
Hacé un tablero de los deploys que fallaron la semana pasada, por servicio,
y actualizalo mientras investigás.
```

Claude escribe un `.html` o un `.md` en tu proyecto, pide permiso una vez, y lo
publica. Te imprime la URL.

Para cambiarlo, se lo pedís y republica **en el mismo link**. Quien lo tenga
abierto ve el cambio. Cada publicación queda como una versión.

Desde otra sesión, le pasás la URL. Sin la URL, crea uno nuevo en vez de
actualizar el que ya existe.

## Los límites, antes de que te ilusiones

Es una página sola y sin backend. En concreto:

| Límite | Qué significa |
|---|---|
| Una sola página | Los links relativos no resuelven. Las secciones van con anclas |
| Sin backend | No guarda lo que alguien escriba en un formulario, ni autentica a nadie |
| Sin pedidos a otros servidores | Todo va embebido: el CSS, el JavaScript, las imágenes |
| Tipos de archivo | `.html`, `.htm` o `.md` |
| Tamaño | Hasta 16 MiB ya renderizado |

Si necesitás guardar datos, tener varias rutas o que alguien inicie sesión, eso
no es un artefacto: es una aplicación y la hospedás vos.

## Datos que se actualizan

Un artefacto puede llamar conectores MCP cada vez que alguien lo abre, así la
página muestra datos de hoy y no una foto del día que se armó.

```
Armá un tablero de los PR abiertos que traiga la lista en vivo por mi
conector de GitHub cuando se carga la página.
```

Tres cosas que conviene saber antes de prometerle esto a alguien:

- Cada persona que lo abre usa **sus** conectores, no los tuyos. Dos personas
  pueden ver datos distintos en la misma página.
- A cada visitante se le pide permiso la primera vez.
- Los servidores MCP locales tuyos —los de `.mcp.json`— sirven para armar la
  página, pero la página publicada no los puede llamar.

Y una que sorprende: un artefacto que llama conectores **no** se puede compartir
por link público.

## Qué hace falta para que ande

Plan Pro, Max, Team o Enterprise, y la sesión iniciada con `/login`. Una sesión
que corre con una clave de API no puede publicar artefactos.

Si Claude escribe el `.html` local y no te da ningún link, es esto: la sesión no
cumple alguna de esas condiciones.

## Cuándo conviene de verdad

Cuando lo que salió es más fácil de mirar que de leer. Un diff anotado, un
gráfico, cuatro maquetas para comparar, una línea de tiempo que se va llenando
mientras corre algo largo.

Si la salida es texto que se lee bien en la terminal, el artefacto es una vuelta
de más.
