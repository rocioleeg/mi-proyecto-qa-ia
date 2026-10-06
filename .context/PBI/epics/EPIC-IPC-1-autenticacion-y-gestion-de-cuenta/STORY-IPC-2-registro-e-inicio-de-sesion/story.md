# Story: Como profesional, quiero registrarme e iniciar sesión con email y contraseña, para acceder al panel de administración
**ID:** IPC-2
**Epic:** IPC-1
**Implementación:** Sin verificar
**Refinamiento:** Borrador
**Inspección QA:** Sin inspeccionar
**Estado de sincronización:** Sincronizado con Jira

## Descripción
Como profesional, quiero registrarme e iniciar sesión con email y contraseña, para acceder al panel de administración.

## Criterios de Aceptación (Borrador)
- [ ] El registro requiere nombre completo, correo electrónico único y contraseña (mínimo 8 caracteres, al menos una mayúscula y un número).
- [ ] El sistema crea el usuario en Supabase Auth y dispara la creación del perfil en la tabla `professionals`.
- [ ] El sistema mantiene la sesión autenticada mediante cookies `httpOnly` seguras (expiración de JWT a 15 minutos, refresh token a 7 días).
- [ ] El profesional no autenticado es redirigido a `/login` al intentar acceder a rutas protegidas.

## Fuentes
| Dato / afirmación | De dónde sale |
| :--- | :--- |
| Requisitos de campos de registro y contraseña | `prd.md` · Feature 1 y Flujo 1 |
| Manejo de sesión con cookies httpOnly y Supabase Auth | `prd.md` · Feature 1 y sección 5 ("Seguridad") |
| Redirección por middleware ante sesión no válida | `prd.md` · sección 5 ("Seguridad") |
