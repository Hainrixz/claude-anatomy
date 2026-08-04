# Anatomía de Claude Code

Te dice si lo que vas a construir tiene que ser skill, plugin, subagente,
comando, servidor MCP o hook, con el árbol de decisión y el esqueleto de
archivos de la pieza elegida.

## El problema

Arrancás un proyecto con Claude Code y a la media hora ya tenés la duda: esto
que quiero armar, ¿es una skill? ¿Un agente? ¿Un comando? Probás una forma, no
anda como esperabas, la desarmás y probás otra.

La pregunta se repite en cada proyecto nuevo porque nadie la responde una sola
vez y la deja escrita en algún lado.

## Qué hace

Corre siete preguntas en cascada sobre el caso que trajiste y devuelve una
pieza concreta —no un menú de seis opciones—, con el motivo en una frase y el
árbol de archivos de esa pieza, listo para completar. Si el caso reparte en
dos piezas, dice cuál se construye primero.

## Cómo se instala

```bash
mkdir -p .claude/skills
cp -R skills/claude-anatomy .claude/skills/
```

La carpeta se llama igual que el `name` del frontmatter. Si la renombrás, la
skill no carga.

Para instalarla a nivel de usuario en vez de proyecto, copiala a la carpeta de
skills de tu configuración de Claude Code. La ruta de proyecto es reversible y
la de usuario no: por eso el default es proyecto.

## Cómo se usa

Se invoca sola. Escribí lo que escribirías igual:

- «esto lo hago como skill o como agente?»
- «no sé si esto va como subagente o comando»
- «necesito un MCP o alcanza con una skill»

También dispara en inglés, con «should I build this as a skill or an agent».

## Lo que NO hace y por qué

No escribe la skill, el plugin o el subagente completos: entrega el esqueleto
y la recomendación, no el artefacto terminado. Completarlo es otro trabajo, o
el de `skill-smith` si la pieza elegida es una skill.

No reemplaza la documentación oficial de Claude Code, que cambia con cada
versión. Da un punto de partida rápido, no la referencia final.

No decide si conviene automatizar algo. Esa pregunta es anterior a ésta: acá
se asume que ya se decidió construir algo y falta elegir la forma.

## De dónde salió

Lo pidieron 14 personas. Los identificadores están en [CITAS.md](CITAS.md).

## Cómo se verificó

La compuerta `validar_artefacto.py` no se pudo correr en esta sesión: el
entorno de la forja bloqueó la ejecución de `python3` sin que hubiera alguien
para aprobarla. El detalle completo, con la verificación manual que se hizo en
su lugar, está en `EVIDENCIA/run-record.json`.

Tiene 3 supuestos marcados en [SUPUESTOS.md](SUPUESTOS.md), los tres con cómo
se corrigen.

El set de disparo tiene 20 casos en `skills/claude-anatomy/evals/evals.json`:
10 que tienen que activar la skill y 10 que no. Están escritos, no ejecutados.

Estado: pendiente de que la compuerta corra en una máquina donde `python3` no
esté bloqueado.

## Licencia

MIT.
