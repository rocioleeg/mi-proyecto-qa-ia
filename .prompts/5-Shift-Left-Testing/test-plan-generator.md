# Prompt: Matriz de Riesgos y Plan de Pruebas

Este prompt te ayuda a identificar riesgos técnicos y de negocio asociados a una funcionalidad (Epic) y a definir la estrategia de pruebas adecuada para mitigarlos.

**Requisito previo:** Se debe haber completado `.context/PBI/epic-tree.md`. Si no existe, detén la ejecución de este prompt y sugiere al usuario la ejecución de `.prompts/4-Especificaciones (Backlog)/pbi-product-backlog.md`.

**Inputs necesarios:**
1.  Contenido de `.context/PBI/epic-tree.md` y del `epic.md` elegido
2.  Contenido de `.context/infrastructure/environments.md` (entornos reales del proyecto)
3.  Contenido de `.context/infrastructure/test-data-strategy.md` (datos disponibles)

---

### **INICIO DEL PROMPT**

**ROL: Test Manager**

Actúa como un Gerente de Pruebas responsable de la estrategia de calidad. Tu objetivo es identificar riesgos técnicos y de negocio, y diseñar un Plan de Pruebas que los mitigue eficientemente.

Primero, lee `.context/PBI/epic-tree.md` y muéstrame las Epics disponibles. **Déjame elegir por ID**: no me pidas un resumen a mano de algo que ya está escrito.

> ⚠️ **Si ya te di el alcance, no vuelvas a preguntarlo.** Un ID, una lista de IDs, el
> nombre de una Epic o la palabra "todas" **es el alcance: continúa directamente.**
>
> **Y prefiere siempre el modo de a una.** Una por sesión es una unidad de trabajo que entra
> en una rama, se revisa entera y se puede revertir sola. El modo masivo existe para poner
> un backlog al día, no para trabajar todos los días — y si te lo pido, decímelo y hacelo.

> **Si la historia que necesitas no aparece en el índice, o sabes que alguien la cambió en el tablero**, no me pidas que te la pegue ni la inventes: se trae con `.prompts/4-Especificaciones (Backlog)/pbi-sync-from-jira.md`, que la escribe en local y deja constancia de la fecha. Dilo y detente. Con la Epic elegida, lee su `epic.md` y los `story.md` que cuelgan de ella.

Lee también:
*   `.context/infrastructure/environments.md` — para saber **qué entornos existen de verdad y cómo se llaman en este proyecto**. Usa esos nombres. No asumas que hay un "Staging": puede no existir, o llamarse UAT, homologación o preproducción.
*   `.context/infrastructure/test-data-strategy.md` — para saber qué datos hay disponibles y bajo qué condiciones.

Si alguno de esos archivos no existe, sigue igual, pero anota en "Preguntas abiertas" que la estrategia se armó sin conocer el mapa de entornos o de datos.

### **1. Análisis de Riesgos (Risk-Based Testing)**
Identifica 3-5 riesgos potenciales (Técnicos, de Negocio o de Seguridad) si esta funcionalidad falla.
Clasifícalos por **Probabilidad** (1-5) e **Impacto** (1-5).

**El nivel sale del producto, con esta escala. No la improvises ni la cambies entre riesgos:**

| Probabilidad × Impacto | Nivel |
| :--- | :--- |
| **1 a 6** | Bajo |
| **7 a 14** | Medio |
| **15 a 25** | Alto |

**Los rangos son continuos a propósito:** no hay un solo valor entre 1 y 25 que quede sin
nivel, y así no tienes que razonar qué productos son alcanzables.

> ⚠️ **Con una excepción, y es la que evita el error clásico: un impacto de 5 nunca queda en
> `Bajo`.** Lo improbable y catastrófico no se descarta por aritmética — se mitiga distinto,
> pero se mira.

*   *Ejemplo:* "Fuga de datos en el login" (Prob 2 × Impacto 5 = 10 → **Medio**, y se anota que
    el impacto es máximo).
*   *Ejemplo:* "Turnos duplicados por concurrencia" (Prob 4 × Impacto 4 = 16 → **Alto**).

> Los riesgos no salen solo de tu criterio. **Revisa las "Preguntas abiertas" y las
> "Contradicciones detectadas" de las fases anteriores**: un requisito que nadie confirmó y
> una contradicción sin resolver son riesgos de producto, y suelen ser los que después
> explotan. Si un riesgo sale de ahí, cítalo.

### **2. Estrategia de Pruebas (Test Strategy)**
Define qué tipos de pruebas son necesarios para mitigar esos riesgos.
*   **Pruebas Unitarias:** ¿Qué lógica compleja tiene que quedar cubierta a ese nivel? Si el código ya existe, la pregunta no es qué escribirá el desarrollador sino qué hay hoy y qué falta.
*   **Pruebas de Integración:** ¿Qué APIs o bases de datos interactúan?
*   **Pruebas E2E (UI):** ¿Qué flujos críticos debe recorrer el usuario?
*   **Pruebas No Funcionales:** ¿Seguridad, Performance, Accesibilidad?

### **3. Estado del plan**

**Un plan que necesita algo que no existe no es un plan optimista: es un plan que va a fallar
el día de la ejecución.** Decláralo con uno de estos tres estados, y no lo dejes implícito:

| Estado | Cuándo |
| :--- | :--- |
| **Ejecutable** | Todo lo que el plan pide existe hoy |
| **Ejecutable con limitaciones** | Se puede correr una parte. **Lo que no, va listado como bloqueante** |
| **Bloqueado** | Falta algo sin lo cual el plan no arranca — un entorno, un acceso, un dato imprescindible |

Cada bloqueante se escribe con **qué impide** y **quién lo tiene que dar**. Un bloqueante sin
dueño no se resuelve solo.

### **4. Herramientas y Datos**
*   Sugiere qué herramientas usar (ej: Jest, Playwright, K6, OWASP ZAP).
*   Define qué datos de prueba específicos necesitamos (ej: "Usuarios con tarjetas caducadas"), **y contrástalo con lo que `test-data-strategy.md` dice que existe**. Si el dato que hace falta no está disponible, eso es un bloqueante del plan, no un detalle: regístralo.

---

### **Formato de Salida Requerido**

**Escribe el archivo en `.context/testing/test-plan-[nombre-epic].md`.** Si la carpeta no existe, créala. No me devuelvas el contenido como un bloque de código para que yo lo copie: escríbelo tú.

Al terminar, confírmame:
*   La ruta exacta del archivo que escribiste.
*   El riesgo de mayor nivel y qué lo mitiga.
*   Qué secciones quedaron incompletas y por qué.

El contenido debe seguir esta estructura:

```markdown
# Plan de Pruebas: [Nombre Epic]

**Epic:** [ID]
**Fecha:** [fecha]

## 1. Matriz de Riesgos del Producto
| ID | Riesgo | Probabilidad (1-5) | Impacto (1-5) | Nivel | Mitigación |
| :--- | :--- | :--- | :--- | :--- | :--- |
| R1 | Fuga de datos en el login | 2 | 5 | **10 · Medio** — impacto máximo | Pruebas de autenticación y revisión de mensajes de error |
| R2 | Turnos duplicados por concurrencia | 4 | 4 | **16 · Alto** | Prueba de reserva simultánea sobre el mismo horario |

## 2. Niveles de Prueba (Pyramid)
*   **Unitarias:** [Lógica de negocio a cubrir]
*   **Integración:** [APIs y servicios]
*   **E2E:** [Flujos críticos de usuario]

## 3. Pruebas No Funcionales
*   **Seguridad:** [Escenarios de inyección, auth, etc.]
*   **Performance:** [Carga esperada]

## 4. Estado del plan

**Estado:** [Ejecutable | Ejecutable con limitaciones | Bloqueado]

| Bloqueante | Qué impide | Qué hace falta para levantarlo |
| :--- | :--- | :--- |
| [Acceso, dato o entorno que falta] | [Qué parte del plan no se puede correr] | [Quién lo tiene que dar] |

*Si no hay ninguno, escribir "Ninguno: el plan se puede ejecutar entero".*

## 5. Necesidades de Entorno y Datos
> Usa los nombres de entorno reales de `environments.md`. Si un entorno que hace falta no
> existe, dilo: es un riesgo del plan.

| Necesidad | Entorno | ¿Disponible hoy? | Referencia |
| :--- | :--- | :--- | :--- |
| [Dato o acceso requerido] | [Entorno real] | Sí / No / Parcial | `test-data-strategy.md` · [sección] |

## Fuentes
| Dato / afirmación | De dónde sale |
| :--- | :--- |
| [Riesgo identificado] | `story.md` · [sección] |
| [Entorno o dato] | `environments.md` · [sección] |
| [Riesgo identificado] | **Hipótesis** — no hay documento que lo respalde |

## Contradicciones detectadas
*   [Qué documentos se contradicen, qué dice cada uno, cuál tomaste y por qué]

## Preguntas abiertas
*   [Lo que ningún documento contesta. No lo completes con hipótesis: déjalo como pregunta]
```

**Restricciones:**

- **Todo riesgo o necesidad de datos que no salga de un documento se marca como hipótesis** en la tabla de Fuentes.
- **Nunca escribas una credencial en claro**, ni siquiera de un usuario de prueba. Referencia dónde está guardada.
- **No inventes entornos.** Si el plan necesita uno que `environments.md` no lista, va a "Preguntas abiertas" como bloqueante.
- Las tres últimas secciones nunca se omiten. Si no hay contradicciones o no quedaron preguntas abiertas, escribe *"Ninguna detectada"* y sigue.

Una vez aprobado el plan, y con el entorno disponible —desplegado ahora o funcionando desde antes—, sugerir iniciar la ejecución con `.prompts/6-Testing Exploratorio/smoke-test.md`

### **FIN DEL PROMPT**
