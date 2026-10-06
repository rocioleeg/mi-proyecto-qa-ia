# Story: Como cliente final, quiero recibir un correo de recordatorio 24 horas antes de mi turno, para no olvidar mi cita y reducir ausencias
**ID:** IPC-21
**Epic:** IPC-16
**Implementación:** Sin verificar
**Refinamiento:** Borrador
**Inspección QA:** Sin inspeccionar
**Estado de sincronización:** Sincronizado con Jira

## Descripción
Como cliente final, quiero recibir un correo de recordatorio 24 horas antes de mi turno, para no olvidar mi cita y reducir ausencias.

## Criterios de Aceptación (Borrador)
- [ ] Despacho automático de correo transaccional vía Resend programado aproximadamente 24 horas antes del inicio del turno agendado.
- [ ] Inclusión de fecha, hora, duración y nombre del profesional en el cuerpo del correo.
- [ ] El envío debe procesarse mediante tarea programada (cron job/scheduler) sin intervención manual del profesional.
- [ ] Exclusión de citas canceladas o finalizadas del despacho de recordatorios.

## Fuentes
| Dato / afirmación | De dónde sale |
| :--- | :--- |
| Necesidad del recordatorio previo al turno para reducir no-shows | `03-especificacion-funcional-v0.3.md` · sección 6 |
| Envío transaccional vía Resend | `05-hilo-mail-cambio-de-alcance.md` · Mail de Diego (28/02/2026); `prd.md` · Feature 6 |
| Ejecución mediante scheduler / cron job diario | `05-hilo-mail-cambio-de-alcance.md` · Mail de Diego (28/02/2026) |
| Inclusión de datos clave del turno y profesional | `03-especificacion-funcional-v0.3.md` · sección 6 |
| Exclusión de citas canceladas o no vigentes | Hipótesis (regla estándar de negocio para evitar recordatorios indebidos) |
