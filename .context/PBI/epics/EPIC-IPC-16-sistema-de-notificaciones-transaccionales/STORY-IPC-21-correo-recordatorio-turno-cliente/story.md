# Story: Como cliente final, quiero recibir un correo de recordatorio 24 horas antes de mi turno, para no olvidar mi cita y reducir ausencias
**ID:** IPC-21
**Epic:** IPC-16
**Implementación:** Sin verificar
**Refinamiento:** Refinado
**Inspección QA:** Sin inspeccionar
**Estado de sincronización:** Sincronizado con Jira

## Descripción
Como cliente final, quiero recibir un correo de recordatorio 24 horas antes de mi turno, para no olvidar mi cita y reducir ausencias.

## Análisis INVEST
| Criterio | Cumple | Observación |
| :--- | :--- | :--- |
| Independiente | Sí | Se prueba con turnos futuros sin depender de otras historias. |
| Negociable | Sí | La antelación exacta, el horario de envío y el mecanismo de programación están pendientes de definición. |
| Valiosa | Sí | Es el único mecanismo previsto contra no-shows; no entra en el lanzamiento inicial. |
| Estimable | No | Falta definir el scheduler (cron de Vercel o servicio externo) y el horario de envío. |
| Pequeña | Sí | Acotada al despacho programado del recordatorio. |
| Testeable | Sí | Tras este refinamiento: cada escenario tiene un resultado observable en la casilla del cliente. |

## Criterios de Aceptación (Gherkin)

### Escenario 1: Recordatorio el día previo al turno
**Given** que tengo un turno confirmado para mañana
**When** llega el momento programado del envío
**Then** recibo un correo de recordatorio en mi casilla
**And** el correo incluye fecha, hora, duración y nombre del profesional

### Escenario 2: Turno lejano no genera recordatorio aún
**Given** que tengo un turno confirmado para dentro de 7 días
**When** llega el momento programado del envío de hoy
**Then** no recibo ningún recordatorio para ese turno

### Escenario 3: Turno no vigente no genera recordatorio
**Given** que tengo un turno que ya no está vigente
**When** llega el momento programado del envío
**Then** no recibo ningún recordatorio para ese turno

### Escenario 4: Envío automático sin intervención
**Given** que tengo un turno confirmado para mañana
**When** llega el momento programado del envío
**Then** el recordatorio se despacha sin intervención del profesional
**And** el despacho queda registrado para auditoría

## Notas de QA
* Historia evolutiva fuera del lanzamiento inicial: no probar en producción actual hasta su implementación.
* Verificar la ventana exacta una vez definida (24 horas previas contra día anterior a hora fija).
* Probar cambios de fecha del turno tras el envío: no debe llegar un recordatorio con datos viejos.
* Revisar husos horarios: el turno es `timestamptz` y el cliente puede estar en otro país que el profesional.
* Usar correos descartables y revisar también spam por el dominio de prueba no verificado.

## Fuentes
| Dato / afirmación | De dónde sale |
| :--- | :--- |
| Recordatorio previo como requisito contra no-shows | `03-especificacion-funcional-v0.3.md` · sección 6 |
| Contenido con datos del turno y profesional | `03-especificacion-funcional-v0.3.md` · sección 6 (tabla) |
| Proveedor Resend para correos del producto | `05-hilo-mail-cambio-de-alcance.md` · Mail de Diego (28/02/2026); `prd.md` · Feature 6 |
| Exclusión del lanzamiento inicial por falta de cron | `05-hilo-mail-cambio-de-alcance.md` · Mail de Diego (28/02/2026); `04-notas-tecnicas.md` · "mails" |
| Deuda registrada como riesgo de producto prioritario | `prd.md` · Riesgos |
| Antelación exacta de 24 horas y horario de envío | **Hipótesis** — la especificación lo deja TBD y habla del día anterior |
| Estados de turno excluidos del envío | **Hipótesis** — hoy el único estado es confirmado y no existe cancelación |
| Mecanismo de programación (cron de Vercel o servicio externo) | **Hipótesis** — pendiente de definición técnica |
| Registro de auditoría del despacho | **Hipótesis** — ningún documento define trazabilidad de envíos |

## Contradicciones detectadas
* **Recordatorio previo al turno:** `03-especificacion-funcional-v0.3.md` · sección 6 lo cataloga como requisito no negociable, pero `05-hilo-mail-cambio-de-alcance.md` (Diego, 28/02/2026) y `04-notas-tecnicas.md` · "mails" constatan que no entra en el lanzamiento por falta de cron en el plan de Vercel. Se trata como historia evolutiva post-lanzamiento (ya registrada en `epic-tree.md`).

## Preguntas abiertas
* ¿La antelación es exactamente 24 horas antes o un envío diario a hora fija el día anterior?
* ¿A qué hora se ejecuta el envío programado?
* ¿Qué mecanismo de programación se usará (cron de Vercel con plan pago, servicio externo, otro)?
* ¿Qué estados del turno excluyen el recordatorio, y existirá un estado de cancelación o no-show?
* ¿Se reintenta el envío ante fallos de Resend?
