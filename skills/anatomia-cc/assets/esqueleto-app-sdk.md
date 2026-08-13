# Esqueleto: app con el Agent SDK

Esto es para cuando lo que querés construir lo va a usar gente que nunca va a
abrir Claude Code. Un botón en tu producto, un endpoint de tu backend, un bot.

El Agent SDK es Claude Code como librería adentro de tu programa: las mismas
herramientas, el mismo bucle, y vos manejás los permisos y la orquestación.

## En Python

```
{{mi-app}}/
├── app.py
├── requirements.txt
├── .env                  ← ANTHROPIC_API_KEY, y no se versiona.
│                            El SDK NO lo lee solo: exportá la variable,
│                            o cargalo vos con python-dotenv
└── .claude/
    ├── skills/           ← el SDK las carga igual que Claude Code
    └── agents/
```

```python
import asyncio
from claude_agent_sdk import query

async def main():
    async for mensaje in query(prompt="{{lo que tiene que hacer}}"):
        print(mensaje)

asyncio.run(main())
```

## En TypeScript

```
{{mi-app}}/
├── src/index.ts
├── package.json
├── .env
└── .claude/
```

```typescript
import { query } from "@anthropic-ai/claude-agent-sdk";

for await (const mensaje of query({ prompt: "{{lo que tiene que hacer}}" })) {
  console.log(mensaje);
}
```

## Desde otro lenguaje

El SDK es Python y TypeScript. Desde Go, Ruby, PHP o lo que uses, se llama la
CLI y se lee la salida como JSON:

```bash
claude -p "{{la tarea}}" --output-format json
```

Es menos cómodo y alcanza para la mayoría de los casos.

## Lo que hay que saber antes de arrancar

**Se paga con una clave de API, no con tu suscripción.** Anthropic no permite que
un producto de terceros ofrezca iniciar sesión con una cuenta de claude.ai. Tu
app usa su propia clave y vos pagás el consumo.

**Carga tu `.claude/`.** Las skills y los subagentes que ya escribiste andan
adentro de la app, sin reescribir nada. Eso hace que valga la pena probar la idea
primero como skill y recién después envolverla.

**Los permisos los definís vos.** No hay una persona mirando y aprobando. Lo que
en Claude Code te pregunta, acá lo decide tu código. Es la diferencia más
importante y es donde se cometen los errores caros.

## Antes de escribir la primera línea

Preguntate si de verdad hace falta la app.

Si el que lo va a usar sos vos, o tu equipo, y todos tienen Claude Code, una
skill hace lo mismo y no hay que mantener un servidor, ni rotar una clave, ni
cobrar el consumo.

La app se justifica cuando el que lo usa **no** tiene Claude Code y no lo va a
tener.
