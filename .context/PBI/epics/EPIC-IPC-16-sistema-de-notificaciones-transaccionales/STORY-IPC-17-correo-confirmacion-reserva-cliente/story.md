# Story: Como cliente final, quiero recibir un correo de confirmación con los detalles de mi turno, para tener un comprobante de la cita agendada
**ID:** IPC-17
**Epic:** IPC-16
**Implementación:** Sin verificar
**Refinamiento:** Borrador
**Inspección QA:** Sin inspeccionar
**Estado de sincronización:** Sincronizado con Jira

## Descripción
Como cliente final, quiero recibir un correo de confirmación con los detalles de mi turno, para tener un comprobante de la cita agendada.

## Criterios de Aceptación (Borrador)
- [ ] Envío automático del correo transaccional vía Resend al confirmarse el turno.
- [ ] Inclusión de fecha, hora, duración y nombre del profesional en el cuerpo del correo.
- [ ] Despacho sin requerir confirmación manual previa por parte del profesional.

## Fuentes
| Dato / afirmación | De dónde sale |
| :--- | :--- |
| Disparo del correo tras insertar el turno en `appointments` | `04-notas-tecnicas.md` · "la reserva" y "mails"; `prd.md` · Flujo 2 |
| Inclusión de datos clave de la cita en el contenido | `03-especificacion-funcional-v0.3.md` · sección 6 |
| Estado `confirmed` automático e inmediato sin aprobación previa | `03-especificacion-funcional-v0.3.md` · sección 8 |
| Proveedor de correo Resend | `05-hilo-mail-cambio-de-alcance.md` · Mail de Diego (28/02/2026) |
