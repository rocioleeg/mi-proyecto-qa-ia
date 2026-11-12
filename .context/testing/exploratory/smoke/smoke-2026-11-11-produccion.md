# Reporte de Smoke Test

**Fecha:** 2026-11-11
**Entorno:** Producción
**URL:** https://cita-ai.vercel.app/
**Modo de ejecución:** Automatizado con Playwright (CLI, Chrome del sistema) — sin Playwright MCP
**Estado:** FAILED

## Verificaciones

| # | Verificación | Resultado | Evidencia |
| :--- | :--- | :--- | :--- |
| 1 | Carga de la portada | PASS | `https://cita-ai.vercel.app/` responde `308` y redirige a `/login`, que responde `200`. Captura: `.context/testing/exploratory/smoke/evidence/smoke-2026-11-11-produccion-login.png` |
| 2 | Título de la página | PASS | Título observado: `Iniciar Sesión - CITA AI`. No hay un valor esperado documentado; es coherente con la página. |
| 3 | Inicio de sesión | FAIL | Se cargaron `TEST_USER_EMAIL` y `TEST_USER_PASSWORD` desde `.env` y se envió el formulario. La aplicación permaneció en `/login` y mostró "Email o contraseña inválidos." No hubo redirección al panel. Captura: `.context/testing/exploratory/smoke/evidence/smoke-2026-11-11-produccion-post-login.png` |
| 4 | Acceso a `/dashboard` sin sesión | FAIL | `/dashboard` responde `200` y renderiza el panel ("Bienvenido a tu panel de control", navegación y métricas) en lugar de redirigir a `/login`. Captura: `.context/testing/exploratory/smoke/evidence/smoke-2026-11-11-produccion-dashboard-sin-sesion.png` |
| 5 | Protección de APIs del panel | PASS | `GET /api/appointments` → `401`; `GET /api/clients` → `401`; `GET /api/professionals/settings` → `405` (solo admite `PUT`). |
| 6 | Ruta de registro | PASS (observación) | `/register`, `/signup`, `/registro` y variantes responden `404`. El registro no tiene ruta propia: se abre con el botón "Regístrate gratis" dentro de `/login`. |

**Nota sobre el login:** el mensaje de error es genérico, por lo que no se puede determinar si la cuenta no existe, si la contraseña es incorrecta o si falta confirmar el correo. La contraseña provista tampoco cumple la política documentada de al menos una mayúscula, lo que impide descartar un problema en el alta de la cuenta.

## Trazabilidad

| Historia (US) | Caso / escenario | Bug | Evidencia |
| :--- | :--- | :--- | :--- |
| N/A — sanidad general | Carga de la portada y título | — | `.../evidence/smoke-2026-11-11-produccion-login.png` |
| IPC-2 | Escenario 5 — inicio de sesión exitoso | — | `.../evidence/smoke-2026-11-11-produccion-post-login.png` |
| IPC-2 | Escenario 8 — acceso a `/dashboard` sin sesión | — (relacionado con el ticket de soporte #69) | `.../evidence/smoke-2026-11-11-produccion-dashboard-sin-sesion.png` |
| IPC-2 | Escenarios 1 a 4 — registro y validaciones | — | No ejecutados: requieren interacción adicional con el formulario |
| IPC-2 | Registro: ruta de la página | — | No existe ruta dedicada; el registro es un modo de `/login` |

## Sin cobertura

*   **Registro E2E:** no se recorrieron los escenarios 1 a 4 del alta ni sus validaciones.
*   **Errores de consola del navegador:** no se capturaron.
*   **Expiración y renovación de sesión:** no verificable porque el login no prosperó.
*   **Logout e invalidación de token:** no se encontró un botón de cierre de sesión en el panel; el comportamiento esperado sigue sin definirse en la historia.
*   **Matriz de navegadores y viewports:** solo se ejecutó Chrome a 1366x768; no se cubrieron móviles ni navegadores in-app.
*   **Datos reales:** no se creó ningún registro ni se usó la cuenta comercial de Fernando. Las credenciales de prueba quedaron en `.env` (ignorado por Git) y no se transcriben en este reporte.
