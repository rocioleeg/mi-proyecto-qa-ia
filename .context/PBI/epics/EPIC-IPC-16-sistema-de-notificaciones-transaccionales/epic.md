# Epic: [Epic] Sistema de Notificaciones Transaccionales
**ID:** IPC-16
**Estado de sincronización:** Sincronizado con Jira
**Estado del trabajo:** To Do

## Descripción
Despacho automatizado de correos electrónicos transaccionales a través del proveedor Resend para notificar eventos operativos y comerciales críticos: confirmación inmediata de reserva al cliente final, aviso de nuevo turno agendado al profesional y notificación festiva de cupo freemium alcanzado (10 clientes). Referencia: `prd.md` · Feature 6, `03-especificacion-funcional-v0.3.md` · sección 6 y `05-hilo-mail-cambio-de-alcance.md`.

## User Stories
- [ ] IPC-17: Como cliente final, quiero recibir un correo de confirmación con los detalles de mi turno, para tener un comprobante de la cita agendada
- [ ] IPC-18: Como profesional, quiero recibir notificaciones por correo de nuevas reservas y límite alcanzado, para dar seguimiento a mi agenda y cupo comercial
- [ ] IPC-21: Como cliente final, quiero recibir un correo de recordatorio 24 horas antes de mi turno, para no olvidar mi cita y reducir ausencias

## Fuentes
| Dato / afirmación | De dónde sale |
| :--- | :--- |
| Integración y despacho de correos a través de Resend | `05-hilo-mail-cambio-de-alcance.md` · Mail de Diego (28/02/2026); `prd.md` · Feature 6 |
| Confirmación de reserva al cliente final con detalle del turno | `03-especificacion-funcional-v0.3.md` · sección 6; `prd.md` · Flujo 2 |
| Aviso de nueva reserva al profesional con datos del cliente | `03-especificacion-funcional-v0.3.md` · sección 6; `04-notas-tecnicas.md` · "mails" |
| Notificación de límite alcanzado al profesional con tono festivo | `03-especificacion-funcional-v0.3.md` · sección 7.2; `prd.md` · Flujo 4 |
| Incorporación del recordatorio 24 horas antes del turno | `03-especificacion-funcional-v0.3.md` · sección 6; `05-hilo-mail-cambio-de-alcance.md` (reincorporado como evolutivo post-lanzamiento) |
