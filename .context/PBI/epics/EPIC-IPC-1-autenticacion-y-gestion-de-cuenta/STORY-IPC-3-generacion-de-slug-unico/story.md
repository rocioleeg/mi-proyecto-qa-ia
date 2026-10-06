# Story: Como profesional, quiero que se genere un slug público único automáticamente al registrarme, para tener mi URL personalizada
**ID:** IPC-3
**Epic:** IPC-1
**Implementación:** Sin verificar
**Refinamiento:** Borrador
**Inspección QA:** Sin inspeccionar
**Estado de sincronización:** Sincronizado con Jira

## Descripción
Como profesional, quiero que se genere un slug público único automáticamente al registrarme, para tener mi URL personalizada.

## Criterios de Aceptación (Borrador)
- [ ] El sistema genera automáticamente el slug público tomando como base el nombre completo del profesional.
- [ ] En caso de coincidencia con un slug preexistente, el sistema resuelve la colisión de forma incremental y única.
- [ ] La URL pública generada (`cita-ai.vercel.app/[slug]`) queda asignada y disponible para consultas externas.

## Fuentes
| Dato / afirmación | De dónde sale |
| :--- | :--- |
| Generación automática e incremental de slug único | `prd.md` · Feature 1 y Flujo 1 |
| Formato de URL pública (`cita-ai.vercel.app/[slug]`) | `prd.md` · sección 1 ("Visión") y Feature 1 |
