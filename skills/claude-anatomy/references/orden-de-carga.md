# El orden de carga al arrancar una sesión

Se lee cuando el resumen del cuerpo no alcanza y necesitás el detalle de qué
entra primero y qué se paga siempre.

## El orden, a grandes rasgos

1. **El archivo de instrucciones del proyecto.** Entra completo, en cada
   sesión, sin excepción.
2. **El nombre y la descripción de cada skill instalada.** Es el nivel más
   liviano de la revelación progresiva: unas cien palabras por skill, y entra
   aunque esa skill no se use en toda la conversación.
3. **Los servidores MCP se conectan y registran sus herramientas.** Ese
   registro queda fijo para el resto de la sesión. A diferencia de una skill,
   un MCP no tiene un nivel «liviano»: en cuanto se conecta, sus herramientas
   están disponibles, se llamen o no.
4. **Subagentes y comandos quedan indexados por nombre.** El sistema sabe que
   existen y cómo se llaman, pero su contenido —el prompt del subagente, las
   instrucciones del comando— no entra al contexto hasta que alguien los
   invoca.
5. **Los hooks se registran contra sus eventos.** No ocupan contexto en ningún
   momento de la sesión: el modelo no los lee, el sistema los ejecuta cuando el
   evento correspondiente ocurre.
6. **El cuerpo de una skill entra recién cuando dispara.** Y dentro de una
   skill que disparó, `references/` y `assets/` entran solo si el trabajo
   concreto los pide.

## Por qué el MCP es distinto

Las otras cinco piezas tienen un nivel de entrada barato —el nombre, la
descripción, el índice por nombre— y un nivel caro que se paga solo cuando hace
falta. El servidor MCP no tiene ese primer nivel liviano: para que el modelo
sepa qué herramientas ofrece, esas herramientas tienen que estar registradas
apenas se conecta. Es la pieza que menos se beneficia de la revelación
progresiva, y por eso instalar muchos servidores MCP sin usarlos pesa más que
instalar muchas skills sin usarlas.

## `TODO(humano) S01`

Este orden describe el comportamiento general documentado en el momento de
escribir esta skill. No está verificado contra una versión puntual de Claude
Code, y el detalle fino —por ejemplo, si algún MCP se conecta de forma diferida
en vez de al arranque— puede cambiar entre versiones. Si tu caso depende de
este detalle exacto, confirmalo contra la documentación oficial vigente antes
de decidir. Ver `SUPUESTOS.md` del artefacto.
