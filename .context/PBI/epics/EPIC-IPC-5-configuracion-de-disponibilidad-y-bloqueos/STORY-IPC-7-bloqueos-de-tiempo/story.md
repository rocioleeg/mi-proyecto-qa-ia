# Story: Como profesional, quiero registrar bloqueos de tiempo en mi agenda, para no recibir turnos durante ausencias o vacaciones
**ID:** IPC-7
**Epic:** IPC-5
**Implementación:** Sin verificar
**Refinamiento:** Borrador
**Inspección QA:** Sin inspeccionar
**Estado de sincronización:** Sincronizado con Jira

## Descripción
Como profesional, quiero registrar bloqueos de tiempo en mi agenda, para no recibir turnos durante ausencias o vacaciones.

## Criterios de Aceptación (Borrador)
- [ ] El profesional puede ingresar fecha/hora de inicio, fecha/hora de fin y un motivo opcional para el bloqueo.
- [ ] El sistema persiste el registro en la tabla `time_blocks`.
- [ ] Los intervalos bloqueados se descuentan inmediatamente de la grilla de disponibilidad pública calculada para clientes.

## Fuentes
| Dato / afirmación | De dónde sale |
| :--- | :--- |
| Definición de bloqueos de tiempo (`time_blocks`) | `prd.md` · Feature 2 y Flujo 3 |
| Descuento inmediato de la disponibilidad pública | `prd.md` · Feature 2 y Flujo 3 |
