# Story: Como sistema, quiero validar la disponibilidad antes de insertar la cita, para prevenir dobles reservas coincidentes
**ID:** IPC-12
**Epic:** IPC-11
**Implementación:** Sin verificar
**Refinamiento:** Borrador
**Inspección QA:** Sin inspeccionar
**Estado de sincronización:** Sincronizado con Jira

## Descripción
Como sistema, quiero validar la disponibilidad antes de insertar la cita, para prevenir dobles reservas coincidentes.

## Criterios de Aceptación (Borrador)
- [ ] La API de reservas ejecuta una verificación de disponibilidad inmediata antes de persistir la cita en base de datos.
- [ ] Si dos usuarios intentan reservar el mismo slot simultáneamente, el segundo intento es rechazado con el mensaje: *"¡Casi! Parece que alguien más reservó este horario. Por favor, elegí otro."*.
- [ ] Ante el rechazo por concurrencia, los datos personales ingresados por el cliente se preservan en pantalla para que solo deba elegir otro horario.

## Fuentes
| Dato / afirmación | De dónde sale |
| :--- | :--- |
| Doble verificación antes de insertar | `prd.md` · Feature 4 y sección Fuentes |
| Mensaje exacto de rechazo por concurrencia | `prd.md` · Feature 4 |
| Preservación de datos en formulario | `prd.md` · Feature 4 |
