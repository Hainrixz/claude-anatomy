# Subagente o comando

Se lee cuando ya sabés que la pieza se dispara a mano —no es una skill ni un
hook— y falta decidir si va como subagente o como comando.

## La diferencia que importa

Los dos arrancan porque alguien o algo los llama de forma explícita, pero el
contexto se comporta distinto.

Un comando corre en la misma conversación que lo llamó. Todo lo que hace y
todo lo que produce queda a la vista de quien lo tipeó, paso por paso.

Un subagente corre aparte. El hilo principal lo invoca, el subagente hace su
trabajo en un contexto propio, y lo único que vuelve a la conversación
original es el informe final. Los pasos intermedios no ensucian nada.

## Tabla de casos

| Situación | Conviene |
|---|---|
| La persona quiere ver cada paso mientras corre | Comando |
| El trabajo tiene pasos intermedios largos que no aportan nada verlos | Subagente |
| Hace falta que corran muchos en paralelo sin saturar la conversación principal | Subagente |
| Es una instrucción corta que se ejecuta una vez y se termina | Comando |
| El resultado es lo único que importa, no el camino | Subagente |
| La persona necesita poder interrumpir o corregir a mitad de camino | Comando |

## Ejemplo, con el motivo

Pedido: «revisá los 40 archivos de este directorio y decime cuáles tienen un
problema de seguridad».

Como comando: la persona ve cada archivo que se revisa, en orden, en la misma
conversación. Sirve si quiere intervenir si algo se ve raro a mitad de camino.

Como subagente: se lanza uno por archivo o por lote, cada uno revisa en su
propio contexto, y al hilo principal solo llegan los hallazgos. Sirve cuando
son 40 archivos y nadie quiere leer 40 revisiones completas para llegar a la
lista de problemas.

La regla corta: si el volumen de pasos intermedios es alto y el valor está en
el resultado agregado, subagente. Si el valor está en poder mirar y cortar a
mitad de camino, comando.

## Un subagente no puede entrevistar

Un subagente corre aislado y devuelve un informe al final: no puede hacer una
pregunta de seguimiento según lo que contestaste en la anterior, porque no hay
una conversación de ida y vuelta con la persona. Si el trabajo necesita
preguntar y esperar respuesta varias veces, la parte conversacional se queda en
el hilo principal, y el subagente se usa solo para el tramo que no necesita
preguntar nada.
