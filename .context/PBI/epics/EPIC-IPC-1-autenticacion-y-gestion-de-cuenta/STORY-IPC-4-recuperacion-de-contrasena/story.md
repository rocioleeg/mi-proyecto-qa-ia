# Story: Como profesional, quiero recuperar mi contraseña mediante correo electrónico, para restablecer mi acceso si lo olvido
**ID:** IPC-4
**Epic:** IPC-1
**Implementación:** Sin verificar
**Refinamiento:** Borrador
**Inspección QA:** Sin inspeccionar
**Estado de sincronización:** Sincronizado con Jira

## Descripción
Como profesional, quiero recuperar mi contraseña mediante correo electrónico, para restablecer mi acceso si lo olvido.

## Criterios de Aceptación (Borrador)
- [ ] La solicitud de recuperación envía un correo con token de un solo uso válido estrictamente por 1 hora (60 minutos).
- [ ] La interfaz responde con un mensaje unificado tanto para correos existentes como inexistentes, impidiendo la enumeración de usuarios.
- [ ] Al acceder al enlace válido, el profesional puede definir una nueva contraseña cumpliendo las reglas de complejidad.

## Fuentes
| Dato / afirmación | De dónde sale |
| :--- | :--- |
| Recuperación con token de 60 minutos | `prd.md` · Feature 1 y sección 5 ("Seguridad") |
| Mensaje unificado para prevención de enumeración | `prd.md` · sección 5 ("Seguridad") |
