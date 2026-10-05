# Story: Como cliente final, quiero visualizar la disponibilidad semanal en tiempo real, para elegir un horario que me convenga
**ID:** IPC-9
**Epic:** IPC-8
**Implementación:** Sin verificar
**Refinamiento:** Borrador
**Inspección QA:** Sin inspeccionar
**Estado de sincronización:** Sincronizado con Jira

## Descripción
Como cliente final, quiero visualizar la disponibilidad semanal en tiempo real, para elegir un horario que me convenga.

## Criterios de Aceptación (Borrador)
- [ ] La aplicación web consulta el endpoint público de disponibilidad y renderiza los turnos disponibles para la semana seleccionada.
- [ ] Los turnos confirmados y los rangos bloqueados por el profesional no se muestran como opciones disponibles.
- [ ] La interfaz se adapta correctamente a dispositivos móviles y muestra las fechas/horas localizadas en idioma español.

## Fuentes
| Dato / afirmación | De dónde sale |
| :--- | :--- |
| Consulta a endpoint público de disponibilidad | `prd.md` · Feature 3 y Flujo 2 |
| Filtrado de citas confirmadas y bloqueos | `prd.md` · Feature 3 |
| Localización en español y diseño responsivo | `prd.md` · sección 5 ("Compatibilidad y Usabilidad") |
