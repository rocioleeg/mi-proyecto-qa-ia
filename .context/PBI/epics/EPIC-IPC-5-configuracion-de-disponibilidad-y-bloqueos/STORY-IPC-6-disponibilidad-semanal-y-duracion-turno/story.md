# Story: Como profesional, quiero definir mi disponibilidad semanal por día y la duración del turno, para estructurar mi oferta horaria
**ID:** IPC-6
**Epic:** IPC-5
**Implementación:** Sin verificar
**Refinamiento:** Borrador
**Inspección QA:** Sin inspeccionar
**Estado de sincronización:** Sincronizado con Jira

## Descripción
Como profesional, quiero definir mi disponibilidad semanal por día y la duración del turno, para estructurar mi oferta horaria.

## Criterios de Aceptación (Borrador)
- [ ] El profesional puede seleccionar los días de la semana activos (0 = Domingo a 6 = Sábado) y sus franjas horarias de inicio y fin.
- [ ] El profesional puede configurar una duración fija de turno en minutos (ej. 45 o 60 minutos) aplicable a todas sus sesiones.
- [ ] Al guardar los cambios, el sistema reemplaza de forma atómica la configuración previa de reglas de disponibilidad.

## Fuentes
| Dato / afirmación | De dónde sale |
| :--- | :--- |
| Configuración de días y franjas horarias | `prd.md` · Feature 2 |
| Duración fija estándar de turno | `prd.md` · Feature 2 |
| Reemplazo atómico de reglas de disponibilidad | `prd.md` · Feature 2 y sección Fuentes |
