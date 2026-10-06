# Epic: Portal Público de Auto-Reserva
**ID:** IPC-8
**Estado de sincronización:** Sincronizado con Jira
**Estado del trabajo:** To Do

## Descripción
Interfaz web pública accesible vía `cita-ai.vercel.app/[slug]` que calcula dinámicamente los intervalos disponibles en tiempo real (descontando citas confirmadas y bloqueos) y permite al cliente agendar en 30 segundos sólo con nombre y correo, sin registro previo ni contraseñas.
Referencia: `.context/architecture/prd.md` · Feature 3 y Flujo 2.

## User Stories
- [ ] IPC-9: Como cliente final, quiero visualizar la disponibilidad semanal en tiempo real, para elegir un horario que me convenga
- [ ] IPC-10: Como cliente final, quiero agendar un turno ingresando solo mi nombre y email, para confirmar la reserva sin crear cuenta

## Fuentes
| Dato / afirmación | De dónde sale |
| :--- | :--- |
| Cálculo dinámico de turnos disponibles en memoria | `prd.md` · Feature 3 y sección Fuentes |
| Regla mandataria de no requerir registro ni contraseña al cliente final | `prd.md` · Feature 3 y sección 1 ("Visión") |
| Registro de reserva con estado `confirmed` inmediato | `prd.md` · Feature 3 y Flujo 2 |
