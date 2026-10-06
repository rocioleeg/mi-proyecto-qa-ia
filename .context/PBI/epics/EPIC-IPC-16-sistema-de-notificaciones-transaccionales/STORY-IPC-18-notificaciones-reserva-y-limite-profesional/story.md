# Story: Como profesional, quiero recibir notificaciones por correo de nuevas reservas y límite alcanzado, para dar seguimiento a mi agenda y cupo comercial
**ID:** IPC-18
**Epic:** IPC-16
**Implementación:** Sin verificar
**Refinamiento:** Borrador
**Inspección QA:** Sin inspeccionar
**Estado de sincronización:** Sincronizado con Jira

## Descripción
Como profesional, quiero recibir notificaciones por correo de nuevas reservas y límite alcanzado, para dar seguimiento a mi agenda y cupo comercial.

## Criterios de Aceptación (Borrador)
- [ ] Notificación por correo al profesional ante cada nueva reserva con los datos del cliente (nombre, email y horario).
- [ ] Envío de correo al profesional en el momento en que se alcanza el límite de 10 clientes únicos con mensaje de felicitaciones y aviso de cupo.
- [ ] Despacho de emails a través del proveedor Resend.

## Fuentes
| Dato / afirmación | De dónde sale |
| :--- | :--- |
| Aviso de nueva reserva con datos del cliente | `03-especificacion-funcional-v0.3.md` · sección 6 |
| Correo con tono de celebración por límite freemium alcanzado | `03-especificacion-funcional-v0.3.md` · sección 7.2; `prd.md` · Flujo 4 |
| Proveedor Resend en lugar de Supabase/SendGrid | `05-hilo-mail-cambio-de-alcance.md` |
| Exclusión del recordatorio del día anterior por restricciones de cron en Vercel | `05-hilo-mail-cambio-de-alcance.md`; `04-notas-tecnicas.md` · "mails" |
