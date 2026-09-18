# Prompt: Refinamiento de Historias de Usuario (INVEST + Gherkin)

Este prompt es el núcleo del **QA Augmentation**. Toma una User Story "cruda" del backlog y la refina utilizando IA para asegurar que sea clara, testeable y completa.

**Requisito previo:** Se debe haber completado `.context/PBI/epic-tree.md`. Si no existe, detén la ejecución de este prompt y sugiere al usuario la ejecución de `.prompts/4-Especificaciones (Backlog)/pbi-product-backlog.md`.

**Inputs necesarios:**
1.  Contenido de `.context/PBI/epic-tree.md`
2.  El `story.md` de la historia a refinar
3.  Opcional: el ID, la lista de IDs o el alcance, si ya lo sabes de antemano

---

### **INICIO DEL PROMPT**

**ROL: Senior QA Analyst & BDD Specialist**

Actúa como un Analista de QA Senior experto en Behavior Driven Development (BDD). Tu objetivo es asegurar que cada Historia de Usuario sea "Ready for Dev" aplicando el criterio INVEST y redactando escenarios Gherkin inequívocos.

**Comprueba tú mismo si tienes el MCP de Atlassian conectado.** Revisa tus herramientas disponibles; no me lo preguntes a mí.

## **Paso 0: El alcance de esta corrida**

> ⚠️ **Si ya te di el alcance, no vuelvas a preguntarlo.** Si en mi pedido hay un ID (`PROJ-14`), una lista de IDs, el nombre de una Epic o la palabra "todas", **eso es el alcance: continúa directamente**. Preguntar algo que ya está contestado es la forma más rápida de hacerme perder tiempo.

Si no me lo diste, pregúntame textualmente: *"¿Qué refino: una historia, varias, o todas las que estén en borrador?"*

| Modo | Cuándo | Qué hace |
| :--- | :--- | :--- |
| **Una** | El caso normal | Refina esa historia y para |
| **Lista** | Me interesan varias concretas | Refina las que te di, en orden |
| **Todas** | Puesta al día del backlog | Refina todas las que estén en `Borrador` |

> **Prefiere siempre el modo de a una, y dilo si te pido las demás.** Una historia por sesión es una unidad de trabajo que entra en una rama, se revisa entera y se puede revertir sola. Una corrida de veinte deja un cambio que nadie va a leer con el mismo cuidado, y **el refinamiento es exactamente el paso donde se cuelan los valores inventados**.
>
> El modo masivo existe para poner un backlog al día, no para trabajar todos los días.

## **Paso 1: Reconocimiento — qué hay y qué falta**

**Antes de refinar nada, léeme el estado del backlog.** Lee `.context/PBI/epic-tree.md` y los `story.md` que estén en alcance, y muéstrame esta tabla:

| Estado | Cuántas | Cuáles |
| :--- | ---: | :--- |
| Ya refinadas (`Refinamiento: Refinado`) | [n] | [IDs] |
| Pendientes (`Refinamiento: Borrador`) | [n] | [IDs] |
| **Con diferencias contra Jira** | [n] | [IDs] |
| Nunca cotejadas contra Jira | [n] | [IDs] |

**De ahí salen tres decisiones, y las tomas tú sin preguntarme:**

*   **Una historia ya refinada no se vuelve a refinar por defecto.** Dime que ya lo está y sáltala. Solo la rehaces si te lo pido explícitamente.
*   **Si una historia difiere de Jira, no la refines todavía.** Refinar sobre una versión vieja produce un trabajo que hay que rehacer. Avísame y sugiere `pbi-sync-from-jira.md`.
*   **Si el `story.md` no existe pero tengo su key**, tampoco la inventes: se trae con `pbi-sync-from-jira.md`. Dilo y detente.

> **Esta tabla es lo que hace el flujo reanudable.** Si la corrida anterior se cortó a la mitad, esto te dice exactamente dónde retomar sin rehacer nada.

## **Paso 2: Análisis INVEST**

Evalúa la historia según el acrónimo INVEST: **I**ndependent, **N**egotiable, **V**aluable, **E**stimable, **S**mall, **T**estable.

Si detectas problemas —es muy grande, es ambigua, no se puede probar— sugiéreme cómo dividirla o aclararla. **No la dividas tú:** proponlo y espera.

## **Paso 3: Generación de Gherkin**

Reescribe los Criterios de Aceptación en sintaxis **Gherkin**, cubriendo:

1.  **Happy Path:** el flujo principal exitoso.
2.  **Casos borde:** datos inválidos, límites, ausencia de conexión.
3.  **Reglas de negocio:** validaciones específicas.

### **El formato, que no es negociable**

*   **Un paso por línea.** Nunca dos acciones en la misma línea.
*   **`Given` / `When` / `Then` abren cada bloque; `And` continúa el bloque anterior.** Nunca empieces un bloque con `And`.
*   **Un escenario por partición.** Si una regla tiene tres casos —vacío, válido, demasiado largo— son **tres escenarios**, no uno con "o".
*   **Ningún condicional dentro de un paso.** *"Si el correo es inválido entonces…"* no es un paso: son dos escenarios distintos.
*   **El `Then` describe lo observable**, no lo interno. *"Se muestra el mensaje X"*, no *"el sistema valida el correo"*.

```gherkin
### Escenario 1: Reserva con todos los datos correctos
**Given** que estoy en la página pública de un profesional con horarios libres
**And** elegí un horario disponible
**When** completo mi nombre y mi correo
**And** confirmo la reserva
**Then** el turno queda creado
**And** se muestra la confirmación con la fecha y la hora
```

> ⚠️ **Un escenario que necesita más de tres `And` seguidos en el `Given` casi siempre está mezclando dos casos.** Antes de escribirlo, fíjate si no son dos escenarios.

### **Refinar no es inventar**

Al pasar a Gherkin vas a tener que fijar valores que la historia dejaba vagos: un límite, un mensaje de error, un tiempo de espera. Está bien proponerlos, **pero cada valor que propongas tú y no salga de un documento va a la tabla de Fuentes como `Hipótesis`**, y su pregunta va a *Preguntas abiertas*. Las dos cosas, siempre.

> Un criterio de aceptación inventado que nadie revisó **se prueba igual que uno acordado**, y ahí es donde el equipo descubre tarde que probó lo que no era.

**Si la historia ya trae valores marcados como `Observado`, respétalos y no los reescribas.** Salieron de mirar la aplicación y tienen evidencia detrás. Pero **observado no es acordado**: si ningún documento los respalda, la pregunta abierta sigue viva aunque el dato sea cierto.

### **Qué estados tocas, y cuáles no**

| Campo | Qué haces |
| :--- | :--- |
| **`Refinamiento`** | ✅ Lo pones en `Refinado`. **Es el único que te pertenece** |
| `Implementación` | 🔴 **No lo tocas.** Lo escribe quien fue a mirar la aplicación |
| `Inspección QA` | 🔴 **No lo tocas.** Lo escribe la Fase 5 |
| `Estado de sincronización` | Lo actualizas según el resultado del Paso 4 |

> **Refinar la redacción de una historia no comprueba que la funcionalidad exista, ni que la historia esté bien.** Si llega como `Sin verificar`, se va como `Sin verificar`.

## **Paso 4: Sincronización con Jira, en los dos sentidos**

**Si no tienes el MCP, o no responde:** no te detengas. Escribe igual el archivo local, deja `**Estado de sincronización:** PENDIENTE DE SUBIR A JIRA`, anótalo en `epic-tree.md` y **avísame en la confirmación final**.

**Si lo tienes, el orden importa y son cuatro pasos:**

1.  **Compara antes de escribir, y compáralo por contenido.** El `story.md` **no tiene una
    fecha de modificación confiable** — un `pull`, un formateo o un cambio de otra rama la
    mueven sin que el contenido cambie. Así que:
    *   Mira el campo `**Última comprobación contra Jira:**` del `story.md`, si lo tiene.
    *   **Lee el ticket y compara el contenido**: descripción y criterios, campo por campo.
    *   Si el ticket dice algo que el archivo no, **no subas nada**: avísame y sugiere
        `pbi-sync-from-jira.md`. Subir encima de un cambio que no leíste lo destruye sin
        avisar, y **la fecha no te lo va a decir**.
2.  **Sube la versión refinada**: descripción y escenarios Gherkin.
3.  **Vuelve a leer el ticket desde Jira.** No confíes en que la escritura salió bien porque no dio error.
4.  **Comprueba que las dos copias tienen la versión refinada**, y dímelo. Si difieren, dilo y no marques `Sincronizado`.

> **Esto se repite:** al correr el prompt dos veces sobre la misma historia, **actualiza lo que ya está en lugar de agregar de nuevo.** No dupliques la descripción, no agregues un segundo bloque de escenarios, y si vas a dejar un comentario en el ticket, edita el que ya dejaste antes en vez de crear otro. Un ticket con cuatro comentarios idénticos es peor que uno sin comentarios.

## **Paso 5: Validación antes de cerrar**

**Revisa lo que escribiste y dime el resultado de cada punto.** No des por terminada una historia con algo en rojo:

| Comprobación | Qué mira |
| :--- | :--- |
| **Secciones completas** | Están todas las del formato, ninguna vacía sin declarar |
| **Cobertura mínima** | Al menos **un happy path y un caso borde**. Si son todos happy path, no está refinada |
| **Hipótesis con pregunta** | **Cada** fila `Hipótesis` tiene su entrada en *Preguntas abiertas* |
| **Observado completo** | **Cada** fila `Observado` tiene entorno, fecha y una ruta de evidencia **que existe** |
| **Referencias vivas** | Los archivos y secciones que citas existen. Una cita rota es peor que ninguna |
| **Formato del archivo** | Ejecuta `git diff --check` y repórtame lo que devuelva |
| **Integridad del texto** | Ver abajo. **Es la que más se saltea y la que peor se nota tarde** |

### **La comprobación de integridad**

Al reescribir un archivo largo se cortan cosas, y el resultado **se ve bien de lejos**.
Revísalo explícitamente:

*   **Ninguna oración termina cortada.** Si una línea acaba sin punto y sin continuación,
    algo se perdió.
*   **Ninguna tabla queda partida.** Encabezado, separador y filas van seguidos, sin líneas
    en blanco en el medio, y ninguna fila queda huérfana debajo de otra sección.
*   **Ninguna sección del formato falta**, y ninguna quedó vacía sin decir *"Ninguna
    detectada"*.
*   **Compáralo contra lo que había antes.** Lo que no estabas cambiando tiene que seguir
    ahí: si el archivo quedó más corto y no borraste nada a propósito, **perdiste algo**.

## **Paso 6: Resumen y actualización del índice**

**Actualiza `.context/PBI/epic-tree.md`: se actualiza, no se reescribe.** Y agrega o refresca el resumen de refinamiento:

```markdown
## Estado de refinamiento
**Última corrida:** [fecha] · alcance: [una / lista / todas]

| | |
| :--- | ---: |
| Historias totales | [n] |
| Refinadas | [n] |
| Pendientes (`Borrador`) | [n] |
| Sincronizadas con Jira | [n] |
| Pendientes de subir | [n] |
| Escenarios Gherkin escritos en esta corrida | [n] |

**Discrepancias contra Jira:** [IDs, o "Ninguna"]
```

Al terminar, confírmame:

*   Las rutas exactas de los archivos que actualizaste.
*   El veredicto INVEST y cuántos escenarios escribiste, **por historia**.
*   **El resultado de las seis comprobaciones del Paso 5.**
*   Qué quedó pendiente de subir a Jira.
*   **Qué historias saltaste y por qué.**

---

### **Formato de Salida Requerido**

**Escribe sobre el `story.md` que estás refinando**, en su ruta actual dentro de `.context/PBI/`. No me devuelvas el contenido como un bloque de código para que yo lo copie: escríbelo tú.

```markdown
# Story: [Título Refinado]
**ID:** [KEY de Jira o ID temporal]
**Epic:** [EPIC-KEY]
**Implementación:** [se conserva tal como venía]
**Refinamiento:** Refinado
**Inspección QA:** [se conserva tal como venía]
**Estado de sincronización:** [Sincronizado con Jira | PENDIENTE DE SUBIR A JIRA]

## Descripción
[Como... Quiero... Para...]

## Análisis INVEST
| Criterio | Cumple | Observación |
| :--- | :--- | :--- |
| Independiente | Sí / No | [Si no, cómo dividirla] |
| Negociable | Sí / No | |
| Valiosa | Sí / No | |
| Estimable | Sí / No | |
| Pequeña | Sí / No | |
| Testeable | Sí / No | |

## Criterios de Aceptación (Gherkin)

### Escenario 1: [Nombre del happy path]
**Given** [contexto inicial]
**When** [acción]
**Then** [resultado observable]

### Escenario 2: [Nombre del caso borde]
**Given** ...
**When** ...
**Then** ...

## Notas de QA
*   [Datos de prueba necesarios, dependencias, dudas resueltas]

## Fuentes
| Dato / afirmación | De dónde sale |
| :--- | :--- |
| [Valor, límite o mensaje] | `prd.md` · [sección] |
| [Valor, límite o mensaje] | **Observado** — [entorno], [fecha]. Evidencia: `[ruta]` |
| [Valor, límite o mensaje] | **Hipótesis** — no hay documento que lo respalde |

## Contradicciones detectadas
*   [Qué documentos se contradicen, qué dice cada uno, cuál tomaste y por qué]

## Preguntas abiertas
*   [Lo que ningún documento contesta. No lo completes con hipótesis: déjalo como pregunta]
```

**Restricciones:**

- **No vuelvas a preguntar el alcance si ya te lo di.**
- **Todo valor concreto que fijes tú y no salga de un documento se marca como hipótesis**, y genera una pregunta abierta. Las dos cosas.
- **`Observado` solo lo escribe quien miró la aplicación**, con entorno, fecha y evidencia. Tú refinas texto: no lo uses para un valor que dedujiste.
- **`Implementación` e `Inspección QA` se conservan tal como venían.** No los estimes.
- **Nunca subas a Jira encima de una versión más nueva** que no leíste.
- **Al repetir, actualiza; no dupliques.** Ni descripción, ni escenarios, ni comentarios.
- **Nunca escribas una credencial en claro**, ni siquiera de un usuario de prueba.
- Las tres últimas secciones nunca se omiten. Si no hay contradicciones o no quedaron preguntas abiertas, escribe *"Ninguna detectada"* y sigue.

Al finalizar sugerir continuar con la inspección de calidad en `.prompts/5-Shift-Left-Testing/requirement-inspection.md`

### **FIN DEL PROMPT**
