# Story: Como profesional, quiero definir mi disponibilidad semanal por día y la duración del turno, para estructurar mi oferta horaria
**ID:** IPC-6
**Epic:** IPC-5
**Implementación:** Sin verificar
**Refinamiento:** Refinado
**Inspección QA:** Sin inspeccionar
**Estado de sincronización:** Sincronizado con Jira

## Descripción
Como profesional, quiero definir mi disponibilidad semanal por día y la duración del turno, para estructurar mi oferta horaria.

## Análisis INVEST
| Criterio | Cumple | Observación |
| :--- | :--- | :--- |
| Independiente | Sí | Se puede implementar y probar sin los bloqueos de IPC-7; solo necesita reglas en `availability_rules` y duración en `professionals`. |
| Negociable | Sí | El formato de franjas, la granularidad y el rango de duración son parametrizables. |
| Valiosa | Sí | Estructura la oferta horaria sin abrir y cerrar turnos manualmente cada día. |
| Estimable | Sí | Reglas CRUD acotadas más recálculo de grilla ya definido en la arquitectura. |
| Pequeña | Sí | Alcance limitado a reglas semanales y duración estándar; los bloqueos puntuales son IPC-7. |
| Testeable | Sí | Tras este refinamiento: cada escenario tiene un resultado observable en el panel o en la grilla pública. |

## Criterios de Aceptación (Gherkin)

### Escenario 1: Guardar disponibilidad semanal con duración estándar
**Given** que inicié sesión como profesional en la sección de disponibilidad de la agenda
**And** definí franjas de lunes a viernes de 09:00 a 18:00
**When** fijo la duración del turno en 60 minutos
**And** guardo los cambios
**Then** las reglas semanales quedan persistidas para los días 0 a 6 según lo definido
**And** la grilla pública ofrece turnos de 60 minutos dentro de esas franjas

### Escenario 2: Día sin franjas no ofrece turnos
**Given** que inicié sesión como profesional en la sección de disponibilidad de la agenda
**And** dejé el sábado y el domingo sin franjas horarias
**When** guardo los cambios
**Then** la grilla pública no ofrece turnos el sábado ni el domingo
**And** la grilla pública sigue ofreciendo turnos los días con franjas definidas

### Escenario 3: Modificar la disponibilidad reemplaza la configuración previa
**Given** que tengo reglas guardadas de lunes a viernes de 09:00 a 18:00
**When** cambio las franjas a lunes a jueves de 10:00 a 16:00
**And** guardo los cambios
**Then** la grilla pública deja de ofrecer turnos los viernes
**And** la grilla pública ofrece turnos de lunes a jueves en el nuevo horario
**And** no quedan reglas remanentes del horario anterior

### Escenario 4: Franja con fin anterior o igual al inicio es rechazada
**Given** que inicié sesión como profesional en la sección de disponibilidad de la agenda
**And** definí el lunes de 18:00 a 09:00
**When** intento guardar los cambios
**Then** se muestra un mensaje de error en la franja inválida
**And** no se guarda ningún cambio en la configuración

### Escenario 5: Duración de turno inválida es rechazada
**Given** que inicié sesión como profesional en la sección de disponibilidad de la agenda
**When** fijo la duración del turno en 0 minutos
**And** intento guardar los cambios
**Then** se muestra un mensaje de error en el campo de duración
**And** no se guarda ningún cambio en la configuración

### Escenario 6: Franjas solapadas el mismo día son rechazadas
**Given** que inicié sesión como profesional en la sección de disponibilidad de la agenda
**And** definí el lunes de 09:00 a 12:00 y de 11:00 a 14:00
**When** intento guardar los cambios
**Then** se muestra un mensaje de error en las franjas solapadas
**And** no se guarda ningún cambio en la configuración

## Notas de QA
* Probar con las duraciones de ejemplo documentadas (45, 50 y 60 minutos) y con los extremos del rango una vez definido.
* Verificar el aislamiento por profesional: las reglas solo las modifica su titular (`auth.uid() = professional_id` en `availability_rules`).
* Validar el recálculo en `GET /api/public/availability` tras cada guardado, incluyendo el descuento de citas confirmadas y bloqueos.
* Usar cuentas de prueba y correos descartables: las pruebas impactan la base única de producción.
* Revisar la interpretación de husos horarios: las franjas son tipo `time` sin zona y el cálculo usa `date-fns` con locale `es`.

## Fuentes
| Dato / afirmación | De dónde sale |
| :--- | :--- |
| Días 0 = Domingo a 6 = Sábado, con inicio y fin por día | `prd.md` · Feature 2; `system-design.md` · Modelo (`availability_rules`) |
| Duración estándar en minutos, ejemplos 45, 50 y 60 | `prd.md` · Feature 2 y Flujo 1; `system-design.md` · Modelo (`professionals.appointment_duration_minutes`) |
| Reemplazo atómico al guardar (elimina previas e inserta el conjunto nuevo) | `prd.md` · Feature 2 (criterios de éxito); `system-design.md` · Endpoints (`POST /api/availability/rules`) |
| Recálculo inmediato de la grilla pública ante cambios | `system-design.md` · ADR-02 |
| Modificación restringida al profesional titular | `system-design.md` · RLS (`availability_rules`) |
| Día sin franjas equivale a día no laborable | **Hipótesis** — no hay documento que defina el tratamiento exacto del día vacío |
| Granularidad de 15 minutos para las franjas | **Hipótesis** — no hay documento que la fije |
| Rechazo de franjas con fin menor o igual al inicio | `03-especificacion-funcional-v0.3.md` · sección 4.1 (validaciones) |
| Rechazo de franjas solapadas el mismo día | `03-especificacion-funcional-v0.3.md` · sección 4.1 (validaciones) |
| Duración mayor a cero; restricción a múltiplos pendiente | `03-especificacion-funcional-v0.3.md` · sección 4.2 (TBD múltiplos de 5 o 15) |

## Contradicciones detectadas
* Ninguna detectada

## Preguntas abiertas
* ¿Un día sin franjas es un día no laborable, o existe una marca explícita de día activo/inactivo?
* ¿Se permite más de un intervalo por día, o solo uno de inicio a fin?
* ¿Cuál es la granularidad mínima de las franjas (15, 30 minutos)?
* ¿Se restringirá la duración a múltiplos de 5 o 15 minutos, o se acepta cualquier entero mayor a cero?
* ¿Qué mensajes de error exactos se muestran ante franjas inválidas o duración inválida?
* ¿En qué huso horario se interpretan las franjas al calcular la grilla pública?
* ¿Qué ocurre con los turnos ya confirmados si el profesional reduce su disponibilidad?
