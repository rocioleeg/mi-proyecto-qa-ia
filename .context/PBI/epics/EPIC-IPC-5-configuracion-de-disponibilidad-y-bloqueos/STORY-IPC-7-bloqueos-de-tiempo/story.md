# Story: Como profesional, quiero registrar bloqueos de tiempo en mi agenda, para no recibir turnos durante ausencias o vacaciones
**ID:** IPC-7
**Epic:** IPC-5
**Implementación:** Sin verificar
**Refinamiento:** Refinado
**Inspección QA:** Sin inspeccionar
**Estado de sincronización:** Sincronizado con Jira

## Descripción
Como profesional, quiero registrar bloqueos de tiempo en mi agenda, para no recibir turnos durante ausencias o vacaciones.

## Análisis INVEST
| Criterio | Cumple | Observación |
| :--- | :--- | :--- |
| Independiente | Sí | Se prueba con reglas fijas y citas de ejemplo; solo escribe en `time_blocks` y lee la grilla pública. |
| Negociable | Sí | El tratamiento de solapamientos y el formato del motivo son parametrizables. |
| Valiosa | Sí | Evita recibir turnos durante ausencias o vacaciones sin tocar la configuración semanal. |
| Estimable | Sí | Alta, edición y baja de bloqueos más descuento en el cálculo de grilla ya definido. |
| Pequeña | Sí | Acotada a bloqueos puntuales; la disponibilidad recurrente es IPC-6. |
| Testeable | Sí | Tras este refinamiento: cada escenario tiene un resultado observable en la agenda o en la grilla pública. |

## Criterios de Aceptación (Gherkin)

### Escenario 1: Registrar un bloqueo con motivo
**Given** que inicié sesión como profesional en la sección de disponibilidad y bloqueos
**When** ingreso inicio el 2026-12-24 a las 09:00 y fin el 2026-12-26 a las 18:00
**And** ingreso el motivo "Vacaciones de fin de año"
**And** confirmo el bloqueo
**Then** el bloqueo queda persistido en `time_blocks` con inicio, fin y motivo
**And** el bloqueo aparece en el listado de bloqueos de mi agenda
**And** la grilla pública deja de ofrecer turnos en ese rango de inmediato

### Escenario 2: Registrar un bloqueo sin motivo
**Given** que inicié sesión como profesional en la sección de disponibilidad y bloqueos
**When** ingreso inicio y fin futuros sin informar motivo
**And** confirmo el bloqueo
**Then** el bloqueo queda persistido en `time_blocks` sin motivo
**And** la grilla pública deja de ofrecer turnos en ese rango de inmediato

### Escenario 3: Bloqueo con fin anterior o igual al inicio es rechazado
**Given** que inicié sesión como profesional en la sección de disponibilidad y bloqueos
**And** ingreso inicio el 2026-12-26 a las 18:00 y fin el 2026-12-24 a las 09:00
**When** intento confirmar el bloqueo
**Then** se muestra un mensaje de error en el rango inválido
**And** no se persiste ningún bloqueo

### Escenario 4: Bloqueo sobre turnos ya confirmados mantiene las citas
**Given** que existen turnos confirmados dentro del rango a bloquear
**When** confirmo un bloqueo que cubre ese rango
**Then** el bloqueo queda persistido en `time_blocks`
**And** los turnos confirmados se mantienen activos y visibles en mi agenda
**And** la grilla pública deja de ofrecer turnos libres en ese rango

### Escenario 5: Eliminar un bloqueo libera el rango
**Given** que tengo un bloqueo vigente registrado en mi agenda
**When** elimino ese bloqueo
**And** consulto la grilla pública
**Then** el bloqueo ya no aparece en el listado de mi agenda
**And** la grilla pública vuelve a ofrecer los turnos libres de ese rango

### Escenario 6: Bloqueos solapados entre sí se registran como independientes
**Given** que tengo un bloqueo registrado del 2026-12-24 al 2026-12-26
**When** confirmo otro bloqueo del 2026-12-25 al 2026-12-27
**Then** ambos bloqueos quedan persistidos en `time_blocks`
**And** la grilla pública descuenta la unión de ambos rangos

## Notas de QA
* Probar bloqueos de jornada completa y de rangos parciales que corten franjas por la mitad.
* Verificar el aislamiento por profesional: los bloqueos solo los modifica su titular (`auth.uid() = professional_id` en `time_blocks`).
* Validar el descuento en `GET /api/public/availability` y el alta/listado/baja en los endpoints de `/api/availability/blocks`.
* Revisar la interpretación de husos horarios: los bloqueos son `timestamptz` y el cálculo usa `date-fns` con locale `es`.
* Usar cuentas de prueba y correos descartables: las pruebas impactan la base única de producción.
* El motivo hoy no tiene uso en interfaz; si aparece en alguna pantalla, reportarlo como desvío.

## Fuentes
| Dato / afirmación | De dónde sale |
| :--- | :--- |
| Bloqueos con inicio y fin en `time_blocks`, motivo opcional | `prd.md` · Feature 2 y Flujo 3; `system-design.md` · Modelo (`time_blocks.reason` nullable) |
| Descuento inmediato de la grilla pública | `prd.md` · Feature 2 (criterios de éxito) y Flujo 3; `system-design.md` · ADR-02 |
| Los turnos confirmados se mantienen activos ante un bloqueo posterior | `prd.md` · Riesgos ("Persistencia de citas previas ante bloqueos de agenda") |
| Alta, listado y baja de bloqueos | `system-design.md` · Endpoints (`POST`, `GET` y `DELETE /api/availability/blocks`) |
| Modificación restringida al profesional titular | `system-design.md` · RLS (`time_blocks`) |
| El motivo no tiene uso actual en interfaz | `system-design.md` · Modelo (`reason` "sin uso actual en interfaz") |
| Rechazo de rangos con fin menor o igual al inicio | **Hipótesis** — no hay documento que defina las validaciones del formulario |
| Longitud máxima del motivo | **Hipótesis** — no hay documento que la fije |
| Bloqueos solapados entre sí aceptados como registros independientes | **Hipótesis** — no hay documento que defina el tratamiento del solapamiento entre bloqueos |
| Bloqueos en el pasado rechazados o sin efecto | **Hipótesis** — no hay documento que defina el tratamiento de fechas pasadas |

## Contradicciones detectadas
* Ninguna detectada

## Preguntas abiertas
* ¿Se rechazan los rangos con fin menor o igual al inicio, o se permite algún caso especial?
* ¿Se rechazan los bloqueos totalmente en el pasado, o se aceptan sin efecto?
* ¿Cuál es la longitud máxima del motivo y dónde se muestra, si es que se muestra?
* ¿Se aceptan bloqueos solapados entre sí, o el sistema debe rechazarlos?
* ¿En qué huso horario ingresa y visualiza el profesional los bloqueos?
* Decisión de producto abierta (registrada en `prd.md`): ante un bloqueo sobre turnos confirmados, ¿se mantendrán siempre las citas, o se ofrecerá cancelación masiva con notificación?
