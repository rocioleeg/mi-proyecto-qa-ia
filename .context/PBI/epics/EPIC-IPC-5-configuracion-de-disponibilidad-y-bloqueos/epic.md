# Epic: Configuración de Disponibilidad y Bloqueos de Agenda
**ID:** IPC-5
**Estado de sincronización:** Sincronizado con Jira
**Estado del trabajo:** To Do

## Descripción
Módulo de administración donde el profesional define sus franjas horarias semanales recurrentes por día de la semana, establece una duración estándar de turno en minutos y gestiona bloqueos de tiempo (`time_blocks`) para ausencias o vacaciones.
Referencia: `.context/architecture/prd.md` · Feature 2 y Flujo 3.

## User Stories
- [ ] IPC-6: Como profesional, quiero definir mi disponibilidad semanal por día y la duración del turno, para estructurar mi oferta horaria
- [ ] IPC-7: Como profesional, quiero registrar bloqueos de tiempo en mi agenda, para no recibir turnos durante ausencias o vacaciones

## Fuentes
| Dato / afirmación | De dónde sale |
| :--- | :--- |
| Configuración de reglas semanales por día (0 = Domingo a 6 = Sábado) | `prd.md` · Feature 2 |
| Duración estándar fija de turno en minutos | `prd.md` · Feature 2 |
| Reemplazo atómico de disponibilidad al guardar | `prd.md` · Feature 2 y sección Fuentes |
| Registro de bloqueos de tiempo (`time_blocks`) | `prd.md` · Feature 2 y Flujo 3 |
