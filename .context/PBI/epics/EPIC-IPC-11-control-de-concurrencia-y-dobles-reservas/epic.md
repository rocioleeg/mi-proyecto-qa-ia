# Epic: Control de Concurrencia y Prevención de Dobles Reservas
**ID:** IPC-11
**Estado de sincronización:** Sincronizado con Jira
**Estado del trabajo:** To Do

## Descripción
Validación estricta que impide solapamientos y dobles reservas de turnos para un mismo profesional. El sistema ejecuta una comprobación de disponibilidad tanto al consultar la grilla como en una segunda verificación inmediata previa a la inserción en base de datos.
Referencia: `.context/architecture/prd.md` · Feature 4 y sección 6 ("Riesgos y Mitigaciones").

## User Stories
- [ ] IPC-12: Como sistema, quiero validar la disponibilidad antes de insertar la cita, para prevenir dobles reservas coincidentes

## Fuentes
| Dato / afirmación | De dónde sale |
| :--- | :--- |
| Doble verificación de disponibilidad previa al insert | `prd.md` · Feature 4 y sección Fuentes |
| Rechazo con mensaje específico y preservación de datos en pantalla | `prd.md` · Feature 4 |
| Riesgo de condición de carrera en concurrencia | `prd.md` · sección 6 ("Riesgos y Mitigaciones") |
