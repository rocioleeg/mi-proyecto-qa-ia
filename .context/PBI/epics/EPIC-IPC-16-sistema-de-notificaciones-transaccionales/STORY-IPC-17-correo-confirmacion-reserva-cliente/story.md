# Story: Como cliente final, quiero recibir un correo de confirmación con los detalles de mi turno, para tener un comprobante de la cita agendada
**ID:** IPC-17
**Epic:** IPC-16
**Implementación:** Sin verificar
**Refinamiento:** Refinado
**Inspección QA:** Sin inspeccionar
**Estado de sincronización:** Sincronizado con Jira

## Descripción
Como cliente final, quiero recibir un correo de confirmación con los detalles de mi turno, para tener un comprobante de la cita agendada.

## Análisis INVEST
| Criterio | Cumple | Observación |
| :--- | :--- | :--- |
| Independiente | Sí | Se prueba con una reserva de ejemplo sin depender de otras historias. |
| Negociable | Sí | La plantilla y el asunto del correo son ajustables. |
| Valiosa | Sí | Es el comprobante digital que evita reconfirmas por chat. |
| Estimable | Sí | Un disparo síncrono vía Resend con contenido ya definido. |
| Pequeña | Sí | Acotada al correo de confirmación al cliente. |
| Testeable | Sí | Tras este refinamiento: cada escenario tiene un resultado observable en la casilla de correo. |

## Criterios de Aceptación (Gherkin)

### Escenario 1: Confirmación con detalle del turno
**Given** que confirmé una reserva como cliente final con un correo válido
**When** el turno queda creado con estado confirmado
**Then** recibo un correo de confirmación en esa casilla
**And** el correo incluye fecha, hora, duración y nombre del profesional

### Escenario 2: Despacho automático sin aprobación previa
**Given** que confirmé una reserva como cliente final
**When** el turno queda creado
**Then** el correo se despacha sin intervención del profesional
**And** el turno nace confirmado en el mismo momento

### Escenario 3: Envío por el proveedor vigente
**Given** que confirmé una reserva como cliente final
**When** el turno queda creado
**Then** el correo se despacha a través de Resend

## Notas de QA
* Usar correos descartables (Mailinator) y revisar también spam: el remitente sale desde dominio de prueba sin SPF, DKIM ni DMARC verificados.
* Medir el impacto del envío síncrono en el tiempo de respuesta de la reserva.
* Verificar que fecha, hora y duración del correo coincidan con el turno creado y con la pantalla de confirmación.
* Cada prueba crea citas reales en producción: coordinar limpieza de turnos de prueba.

## Fuentes
| Dato / afirmación | De dónde sale |
| :--- | :--- |
| Correo al cliente al reservar con detalle del turno | `03-especificacion-funcional-v0.3.md` · sección 6 (tabla) y 5.1 |
| Turno confirmado automático sin aprobación | `03-especificacion-funcional-v0.3.md` · sección 8; `prd.md` · Feature 3 |
| Proveedor Resend para correos del producto | `05-hilo-mail-cambio-de-alcance.md` · Mail de Diego (28/02/2026); `prd.md` · Feature 6 |
| Envío síncrono dentro del request de reserva | `04-notas-tecnicas.md` · "mails"; `system-design.md` · ADR-04 |
| Remitente desde dominio de prueba sin verificar | `04-notas-tecnicas.md` · "mails"; `05-hilo-mail-cambio-de-alcance.md` · Mail de Diego (28/02/2026) |
| Asunto y plantilla exactos del correo | **Hipótesis** — ningún documento fija los textos |
| Comportamiento si Resend falla o demora | **Hipótesis** — ningún documento define si la reserva se crea igual |

## Contradicciones detectadas
* Ninguna detectada

## Preguntas abiertas
* ¿Cuáles son el asunto y la plantilla exactos del correo de confirmación?
* Si Resend falla o demora, ¿la reserva se crea igual y el correo se reintenta, o falla todo el request?
* ¿Existe reintento o cola ante fallos de envío, o el despacho es único e inmediato?
