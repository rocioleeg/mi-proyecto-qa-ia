# Epic: Autenticación y Gestión de Cuenta del Profesional
**ID:** IPC-1
**Estado de sincronización:** Sincronizado con Jira
**Estado del trabajo:** To Do

## Descripción
Módulo de registro, autenticación de profesionales con Supabase Auth, persistencia de sesión con cookies seguras y generación automática de URL pública única con slug incremental (`cita-ai.vercel.app/[slug]`).
Referencia: `.context/architecture/prd.md` · Feature 1 y Flujo 1.

## User Stories
- [ ] IPC-2: Como profesional, quiero registrarme e iniciar sesión con email y contraseña, para acceder al panel de administración
- [ ] IPC-3: Como profesional, quiero que se genere un slug público único automáticamente al registrarme, para tener mi URL personalizada
- [ ] IPC-4: Como profesional, quiero recuperar mi contraseña mediante correo electrónico, para restablecer mi acceso si lo olvido

## Fuentes
| Dato / afirmación | De dónde sale |
| :--- | :--- |
| Alcance del módulo de autenticación y registro | `prd.md` · Feature 1 ("Autenticación y Gestión de Cuenta del Profesional") |
| Sesión persistente con JWT en cookies httpOnly (15m/7d) | `prd.md` · Feature 1 y sección 5 ("Seguridad") |
| Generación automática de slug único | `prd.md` · Feature 1 y Flujo 1 |
| Recuperación de contraseña por token de un solo uso (1h) | `prd.md` · Feature 1 y sección 5 ("Seguridad") |
