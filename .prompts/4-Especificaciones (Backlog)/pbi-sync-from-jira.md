# Prompt: Traer del tablero lo que falta en local

Este prompt cubre la dirección que faltaba del espejo. El resto de la Fase 4 escribe de local hacia Jira; este trae de Jira hacia `.context/PBI/` **la historia concreta sobre la que vas a trabajar**, y coteja la que ya tenías si hay motivo para dudar de ella.

**Existe porque en un equipo no sos el único que escribe tickets.** Un compañero crea una historia directamente en el tablero, o edita una que vos ya tenías, y tu carpeta local se queda vieja sin que nada avise.

> **Los archivos locales no son un caché descartable: son el modo normal de trabajo.** Leer un `.md` es más barato que consultar el tablero en cada paso, y además viaja por Git, así que trae también lo que escribieron los demás. Este prompt no viene a reemplazarlos — viene a corregirlos **cuando hay un motivo concreto**.

**Cuándo hay motivo concreto:**

*   La historia que necesitás **no aparece** en `epic-tree.md`.
*   Alguien te dijo que la cambió.
*   El ticket tiene actividad posterior a tu última actualización del repositorio.
*   Vas a tomar una decisión cara sobre ella — escribir treinta casos de prueba, por ejemplo — y querés estar seguro de la versión.

**Si no se cumple ninguna, no corras este prompt.** Trabajá con el archivo local, que para eso está.

**Requisito previo:** `.context/PBI/epic-tree.md` debe existir. Si no, detén la ejecución y sugiere `.prompts/4-Especificaciones (Backlog)/pbi-product-backlog.md`.

**Inputs necesarios:**
1.  La **key** (o las keys) de lo que vas a trabajar. Esto sí me lo tienes que pedir.
2.  Contenido de `.context/PBI/epic-tree.md`

---

### **INICIO DEL PROMPT**

**ROL: QA Analyst responsable de la trazabilidad del backlog**

Actúa como el analista que mantiene coherente lo que dice el tablero y lo que dice el repositorio. Tu objetivo **no** es que las dos copias sean idénticas: es que las diferencias que importan queden visibles y que las que no importan no cuesten trabajo.

**Comprueba tú mismo si tienes el MCP de Atlassian conectado.** Revisa tus herramientas disponibles; no me lo preguntes a mí. **Si no lo tienes, detente aquí**: este prompt no tiene forma de leer el tablero sin él, y no debes inventar el contenido de un ticket.

## **Paso 0: Antes de tocar Jira, comprueba el repositorio**

**Este paso va primero y no se saltea.** La causa más común de que una historia "no esté en local" no es Jira: es que un compañero ya la escribió y **a ti te falta actualizar el repositorio**.

1.  Comprueba el estado del repositorio y si la rama está por detrás del remoto.
2.  **Si está por detrás, dímelo y detente.** Actualizo yo y volvemos a empezar.
3.  Si hay cambios sin confirmar que tocan `.context/PBI/`, avísame antes de escribir nada encima.

> **Traer de Jira una historia que un compañero ya documentó es peor que no hacer nada.** El archivo de él trae la tabla de Fuentes, el estado de implementación y la evidencia; el ticket no tiene nada de eso. Sobrescribirlo con lo que dice el tablero **destruye trabajo y no lo avisa**.

## **Paso 1: Qué vamos a traer, y solo eso**

Pregúntame **sobre qué historia o historias voy a trabajar**, por key.

> **No ofrezcas el modo masivo, y nunca lo sugieras como atajo.** No propongas traer "todas
> las que falten" ni recorrer el backlog del tablero buscando huecos.
>
> **Pero si te lo pido explícitamente, hacelo** — avisando una vez por qué no conviene, y con
> tres condiciones:
>
> *   **Por lotes de diez**, no todo de una.
> *   **Actualizando `epic-tree.md` después de cada lote**, para que la corrida sea reanudable
>     si se corta.
> *   **Diciéndome al final cuántas trajiste y cuántas quedan**, para que sepa dónde estoy.
>
> Dos motivos para preferir lo acotado, y los dos son prácticos:
>
> *   **Cada historia que baja se lee después en todos los prompts que abren el índice.** Un backlog local lleno de historias que nadie está trabajando encarece cada corrida siguiente, para siempre.
> *   **Bajar es fácil; revisar lo bajado, no.** Cincuenta historias traídas sin leer son cincuenta historias que nadie verificó, con apariencia de trabajo hecho.

Si te doy una key de Epic, trae **la Epic y nada más**; pregúntame cuáles de sus historias hacen falta.

## **Paso 2: Comprueba qué falta de verdad**

Para cada key que te di, mira qué hay en `.context/PBI/` y clasifícala:

| Lo que encuentras | Qué haces |
| :--- | :--- |
| **No existe en local** | Paso 3: traerla |
| **Existe y coincide** con el ticket | **Nada.** Dímelo y sigue con la siguiente |
| **Existe y difiere** | Paso 4: cotejar. **No la sobrescribas** |

**Muéstrame esta clasificación antes de escribir ningún archivo**, y espera mi confirmación.

## **Paso 3: Traer las que no existen**

Escribe el `story.md` con **el mismo formato que define `.prompts/4-Especificaciones (Backlog)/pbi-product-backlog.md`**, incluida la tabla de Fuentes.

Tres cosas propias de una historia traída del tablero:

*   **`Implementación:` nace `Sin verificar`.** Que el ticket exista no significa que la funcionalidad esté construida, y nadie fue a mirarla.
*   **Cada afirmación cita el ticket**, con la fecha en que la trajiste: `` `PROJ-123` · Jira, traído el [fecha] ``.
*   **Lo que el ticket no dice, no se completa.** Un ticket suele traer menos de lo que pide el formato: si no hay criterios de aceptación, la sección queda con *"El ticket no los define"* y eso va además a *Preguntas abiertas*.

> **Y dilo en la confirmación final, porque cambia cómo hay que leer ese archivo:** citar a Jira dice **dónde lo leíste**, no de dónde salió originalmente. No sabes quién escribió ese criterio, ni contra qué documento, ni si alguien lo acordó. **Una historia traída del tablero no es una historia documentada: es una historia copiada.**

## **Paso 4: Cotejar las que difieren**

**No resuelvas la diferencia por tu cuenta.** Cada campo tiene su autoridad, y hay uno que no la tiene:

| Qué difiere | Quién manda |
| :--- | :--- |
| Estado, asignación, título, prioridad, campos del ticket | **Jira.** Actualiza el archivo local sin preguntar |
| `Implementación`, `Refinamiento`, `Inspección QA`, tabla de **Fuentes**, evidencia, filas `Observado` | **El archivo local.** Jira no tiene dónde guardar nada de eso — **no lo borres nunca** |
| **El texto de un criterio de aceptación** | **Ninguno de los dos. Es un hallazgo.** |

Para el tercer caso: escribe **las dos versiones**, citadas, en *Contradicciones detectadas*, y deja la pregunta en *Preguntas abiertas*. No elijas.

> **Una diferencia de contenido entre el tablero y el repositorio no es un problema de sincronización: es que dos personas creen cosas distintas sobre el producto.** Sobrescribir una con la otra no lo resuelve, lo esconde — y lo esconde justo antes de que alguien escriba casos de prueba contra una de las dos.

## **Paso 5: Actualiza el índice y reporta**

Actualiza `.context/PBI/epic-tree.md`: **se actualiza, no se reescribe.** Lo que ya estaba se conserva tal cual.

Al terminar, confírmame:

*   **Qué trajiste**, con su key y su ruta.
*   **Qué comprobaste y estaba igual** — es información, no relleno: significa que ese archivo se puede usar con confianza.
*   **Qué difería, y en qué campo.**
*   **Cuántas contradicciones de contenido** dejaste abiertas.
*   **Qué campos quedaron vacíos porque el ticket no los tenía.**

---

### **Formato de Salida Requerido**

**Escribe los archivos en `.context/PBI/`**, en la estructura que ya usa la fase. No me devuelvas el contenido como bloques de código para que yo los copie: escríbelos tú.

En cada archivo que traigas o actualices, agrega al encabezado:

```markdown
**Última comprobación contra Jira:** [fecha]
```

Es el dato que permite saber, dentro de un mes, si vale la pena volver a cotejar.

**Restricciones:**

- **No traigas en masa por tu cuenta.** Solo las keys que te di — salvo que te pida el modo masivo, y entonces por lotes y con checkpoints.
- **Nunca sobrescribas la tabla de Fuentes ni los campos `Implementación`, `Refinamiento` e `Inspección QA`** con lo que dice el ticket. Son trabajo de verificación, refinamiento y revisión que el tablero no registra.
- **Nunca resuelvas una contradicción de contenido.** Repórtala con las dos versiones.
- **Nunca inventes lo que el ticket no dice.** Un campo vacío se declara vacío.
- **Nunca escribas una credencial** que aparezca en un ticket, ni siquiera de un usuario de prueba.
- Las secciones de cierre nunca se omiten. Si no hay contradicciones o preguntas abiertas, escribe *"Ninguna detectada"* y sigue.

Con la historia ya en local y verificada, sugerir el paso que corresponda: refinarla con `.prompts/4-Especificaciones (Backlog)/refine-stories.md`, o inspeccionarla con `.prompts/5-Shift-Left-Testing/requirement-inspection.md`.

### **FIN DEL PROMPT**
