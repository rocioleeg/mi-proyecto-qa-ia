# Story: Como cliente final, quiero agendar un turno ingresando solo mi nombre y email, para confirmar la reserva sin crear cuenta
**ID:** IPC-10
**Epic:** IPC-8
**Implementación:** Sin verificar
**Refinamiento:** Refinado
**Inspección QA:** Sin inspeccionar
**Estado de sincronización:** Sincronizado con Jira

## Descripción
Como cliente final, quiero agendar un turno ingresando solo mi nombre y email, para confirmar la reserva sin crear cuenta.

## Análisis INVEST
| Criterio | Cumple | Observación |
| :--- | :--- | :--- |
| Independiente | Sí | Se prueba sobre slots visibles sin depender de otras historias. |
| Negociable | Sí | Los textos del formulario y la confirmación son ajustables. |
| Valiosa | Sí | Concreta la promesa de agendar en 30 segundos sin fricción. |
| Estimable | Sí | Un endpoint de reserva con reglas RN-01 a RN-04 ya definidas. |
| Pequeña | Sí | Acotada a elegir slot, ingresar dos datos y confirmar. |
| Testeable | Sí | Tras este refinamiento: cada escenario tiene un resultado observable en la página pública. |

## Criterios de Aceptación (Gherkin)

### Escenario 1: Reserva con nombre y correo queda confirmada
**Given** que estoy en la página pública de un profesional con horarios libres
**And** elegí un horario disponible
**When** completo mi nombre y mi correo electrónico
**And** confirmo la reserva
**Then** el turno queda creado con estado confirmado sin aprobación previa
**And** se muestra la confirmación con la fecha y la hora del turno

### Escenario 2: Cliente existente reutiliza su registro
**Given** que estoy en la página pública de un profesional con horarios libres
**And** mi correo ya tiene reservas anteriores con ese profesional
**When** completo mi nombre y mi correo
**And** confirmo la reserva
**Then** el turno queda creado con estado confirmado
**And** no se duplica mi registro de cliente

### Escenario 3: Slot reservado deja de ofrecerse
**Given** que confirmé una reserva en un horario
**When** otro cliente abre la misma URL pública
**Then** ese horario ya no aparece como opción disponible

### Escenario 4: Correo con formato inválido es rechazado
**Given** que estoy en la página pública de un profesional con horarios libres
**And** elegí un horario disponible
**When** completo mi nombre y el correo "no-es-un-correo"
**And** intento confirmar la reserva
**Then** se muestra un mensaje de error en el campo de correo
**And** no se crea ningún turno

### Escenario 5: Horario pasado no se puede reservar
**Given** que estoy en la página pública de un profesional
**When** intento reservar un horario anterior al momento actual
**Then** ese horario no aparece como opción disponible
**And** no se crea ningún turno en el pasado

## Notas de QA
* Verificar el alta en `POST /api/public/appointments` con respuesta 201 y el vínculo con `clients` por correo.
* Probar correos con mayúsculas, espacios y alias para validar la normalización antes del alta o reutilización.
* Validar que el insert público no permita modificar ni leer datos de otros profesionales (RLS como única barrera).
* Revisar el huso horario de `start_time` y `end_time` ante clientes en países distintos al profesional.
* Medir P95 del endpoint menor a 500 ms (valor medido, no objetivo acordado).
* Usar correos descartables: cada reserva de prueba crea clientes reales en producción.

## Fuentes
| Dato / afirmación | De dónde sale |
| :--- | :--- |
| Formulario solo con nombre y correo, sin cuenta | `prd.md` · Feature 3 y Flujo 2; `03-especificacion-funcional-v0.3.md` · sección 2.2 y 5.1 |
| Turno creado con estado confirmado sin aprobación | `prd.md` · Feature 3; `03-especificacion-funcional-v0.3.md` · sección 8 |
| Búsqueda o alta del cliente por correo | `system-design.md` · Endpoints (`POST /api/public/appointments`) |
| Slot reservado deja de ofrecerse de inmediato | `prd.md` · Feature 3; `03-especificacion-funcional-v0.3.md` · sección 5.2 (RN-01) |
| Horarios pasados no reservables | `03-especificacion-funcional-v0.3.md` · sección 5.2 (RN-03) |
| Mensaje exacto ante correo inválido | **Hipótesis** — no hay documento que fije el texto |
| Normalización del correo antes de comparar (mayúsculas, espacios) | **Hipótesis** — no hay documento que la defina |

## Contradicciones detectadas
* Ninguna detectada

## Preguntas abiertas
* ¿Qué mensaje exacto se muestra ante un correo con formato inválido?
* ¿Se normaliza el correo (minúsculas, sin espacios) antes de buscar o crear el cliente?
* ¿Cuál es la ventana máxima a futuro para reservar (hoy sin límite documentado)?
* ¿Qué ve el cliente si el slot se ocupa entre la visualización y la confirmación (cubierto por IPC-12)?
