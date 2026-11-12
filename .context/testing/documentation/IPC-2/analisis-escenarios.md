# Análisis de Escenarios: IPC-2 — Registro e inicio de sesión

**Historia:** IPC-2
**Fecha:** 2026-11-12
**Sesiones analizadas:**
- `.context/testing/exploratory/ui/session-2026-11-11-ipc-2.md`
- `.context/testing/exploratory/smoke/smoke-2026-11-11-produccion.md`
- `.context/testing/exploratory/ui/evidence/ipc-2-notes.md`
- `.context/testing/exploratory/handoffs/ui-to-api-db-2026-11-11-ipc-2.md`

> La historia declara `Implementación: Parcial`; los casos miden contra comportamiento existente, pero el backend de Supabase Auth no resuelve y el camino autenticado no pudo completarse.

## Candidatos para Regresión
| # | Tipo | Escenario | Origen | Estado en la sesión |
| :--- | :--- | :--- | :--- | :--- |
| 1 | E2E | Login con el usuario de prueba del `.env` | Sesión smoke | Ejecutado, FAIL: `net::ERR_NAME_NOT_RESOLVED` (NXDOMAIN) |
| 2 | Funcional/Seguridad | Login con credenciales inválidas y backend caído: mensaje y distinción de error de servicio | Sesión UI | Ejecutado, FAIL (defecto 3) |
| 3 | Funcional | Envío vacío del formulario de login | Sesión UI | Ejecutado, PASS |
| 4 | Funcional | Envío vacío del formulario de registro | Sesión UI | Ejecutado, PASS |
| 5 | Funcional | Registro con contraseña de 3 caracteres | Sesión UI | Ejecutado, FAIL (defecto 4) |
| 6 | Funcional | Registro con contraseña sin mayúscula | Sesión UI | Ejecutado, FAIL (defecto 4) |
| 7 | Funcional | Registro con contraseña sin número | Sesión UI | Ejecutado, FAIL (defecto 4) |
| 8 | Funcional | Registro con nombre de 101 caracteres | Sesión UI | Ejecutado, FAIL (defecto 5) |
| 9 | Funcional | Registro con correo de 255 caracteres | Sesión UI | Ejecutado, FAIL (defecto 5) |
| 10 | Funcional/Seguridad | Triple envío rápido del login | Sesión UI | Ejecutado, FAIL (defecto 6) |
| 11 | Seguridad | Acceso directo a `/dashboard` sin sesión | Sesión UI y smoke | Ejecutado, FAIL (defecto 2, ticket #69) |
| 12 | No funcional | Responsive 375x667 en login y registro, sin desborde horizontal | Sesión UI | Ejecutado, PASS |
| 13 | E2E | Registro exitoso de un profesional nuevo | `story.md` · CA-1 | Nunca se probó: no se autorizó crear una cuenta en Producción y el backend no responde |
| 14 | Funcional/Seguridad | Registro con un correo ya existente | `story.md` · CA-2 | Nunca se probó: no hay una cuenta accesible y el backend no responde |
| 15 | Integración | Expiración del JWT a los 15 minutos y renovación con refresh token | `story.md` · CA-7 | Nunca se probó: no hay sesión válida |
| 16 | Seguridad | Cookies `httpOnly`/`Secure`, refresh de 7 días y logout con invalidación de sesión | `story.md` · CA-7; ticket #69 | Nunca se probó: no hay sesión válida ni criterio de logout |
| 17 | Seguridad | Rate limiting y bloqueo por intentos fallidos de login | `story.md` · Preguntas abiertas; inspección IPC-2 · defecto 11 | Nunca se probó con backend operativo |
| 18 | Integración | Recepción del correo de bienvenida tras el registro | `story.md` · CA-1; plan IPC-2 · R5 | Nunca se probó: no se creó ninguna cuenta |
| 19 | Funcional | Destino posterior al registro: panel o asistente inicial | `story.md` · CA-1 (PENDIENTE) | Nunca se probó: no se completó el registro |
| 20 | Seguridad/API | Validación server-side de contraseña, nombre y correo | `story.md` · CA-3 y CA-4; plan IPC-2 · R4 | Nunca se probó: el backend no responde |
| 21 | No funcional | Matriz de navegadores: Safari, Firefox, Edge e in-app browsers | `environments.md` · 1 | Nunca se probó: solo Chrome headless |
| 22 | Funcional | Normalización del correo: espacios y mayúsculas | `story.md` · Preguntas abiertas | Nunca se probó: sin backend operativo |
| 23 | Integración | Registro parcial: usuario en Supabase Auth pero fallo en `professionals` o slug | Plan IPC-2 · R3 | Nunca se probó: no se creó ninguna cuenta |
| 24 | Seguridad | Anti-enumeración: mensaje y tiempo de respuesta con correo existente e inexistente | Plan IPC-2 · R2; `03-especificacion-funcional-v0.3.md` · 3.3 | Nunca se probó: sin backend operativo |

## Huecos de Cobertura
*   **CA-1 — Registro exitoso:** ninguna sesión completó un alta. Falta verificar la creación en Supabase Auth, el trigger de `professionals`, el slug (IPC-3), el correo de bienvenida (IPC-16), la sesión y el destino posterior.
*   **CA-2 — Correo ya existente:** no se probó el rechazo ni el mensaje. La política sigue abierta entre mensaje explícito y mensaje neutro anti-enumeración.
*   **CA-5 — Login exitoso:** se intentó con el usuario de prueba en el smoke, pero el host de Supabase Auth no resuelve. No se observó redirección, cookie ni sesión.
*   **CA-7 — Expiración y renovación:** no se pudo verificar el JWT de 15 minutos, el refresh de 7 días ni el redirect a `/login` con refresh vencido.
*   **Validación server-side:** las validaciones de contraseña, nombre y correo se observaron solo en el cliente; no se sabe si el servidor las rechaza.
*   **Cookies y logout:** no se verificaron `httpOnly`, `Secure`, `SameSite`, almacenamiento del token ni invalidación al cerrar sesión (ticket #69).
*   **Rate limiting:** el triple envío se probó solo con credenciales inválidas y backend caído; no se observó bloqueo ni cooldown.
*   **Correo de bienvenida:** no se creó ninguna cuenta, por lo que no se disparó el envío ni se pudo verificar el proveedor.
*   **Registro parcial:** no se probó qué ocurre si falla el perfil en `professionals`, el slug o el correo después de crear el usuario en Auth.
*   **Normalización del correo:** no hay criterio ni prueba para espacios, mayúsculas o duplicados con distinto casing.
*   **Matriz de navegadores:** solo se ejecutó Chrome headless; faltan Safari, Firefox, Edge y webviews de Instagram/WhatsApp.

## Descartados
*   **Título de la pestaña sin cambios en modo registro:** hallazgo cosmético; el título sigue siendo "Iniciar Sesión - CITA AI". No afecta el flujo ni un criterio de aceptación.
*   **Sin ayuda visible ni control de visibilidad de contraseña:** mejora de UX sin criterio de aceptación documentado; se retoma si producto define el requisito.
*   **Layout y textos del formulario de registro:** observación PASS sin un criterio de aceptación específico; se cubre de forma indirecta con los casos de registro.

## Trazabilidad
| Historia (US) | Caso / escenario | Bug | Evidencia |
| :--- | :--- | :--- | :--- |
| IPC-2 | Login con el usuario de prueba | — (defecto 1 de la sesión) | `smoke-2026-11-11-produccion.md` · verificación 3 |
| IPC-2 | Login con credenciales inválidas y backend caído | — (defecto 3 de la sesión) | `session-2026-11-11-ipc-2.md` · escenario 2 |
| IPC-2 | Envío vacío de login | — | `session-2026-11-11-ipc-2.md` · escenario 3 |
| IPC-2 | Envío vacío de registro | — | `session-2026-11-11-ipc-2.md` · escenario 3 |
| IPC-2 | Contraseña de 3 caracteres | — (defecto 4 de la sesión) | `session-2026-11-11-ipc-2.md` · escenario 4 |
| IPC-2 | Contraseña sin mayúscula | — (defecto 4 de la sesión) | `session-2026-11-11-ipc-2.md` · escenario 4 |
| IPC-2 | Contraseña sin número | — (defecto 4 de la sesión) | `session-2026-11-11-ipc-2.md` · escenario 4 |
| IPC-2 | Nombre de 101 caracteres | — (defecto 5 de la sesión) | `session-2026-11-11-ipc-2.md` · escenario 5 |
| IPC-2 | Correo de 255 caracteres | — (defecto 5 de la sesión) | `session-2026-11-11-ipc-2.md` · escenario 5 |
| IPC-2 | Triple envío rápido del login | — (defecto 6 de la sesión) | `session-2026-11-11-ipc-2.md` · escenario 6 |
| IPC-2 | Acceso directo a `/dashboard` sin sesión | — (defecto 2 de la sesión; ticket #69) | `session-2026-11-11-ipc-2.md` · escenario 7 |
| IPC-2 | Responsive 375x667 en login y registro | — | `session-2026-11-11-ipc-2.md` · escenario 8 |
| IPC-2 | Registro exitoso de un profesional nuevo | — | `story.md` · CA-1 |
| IPC-2 | Registro con un correo ya existente | — | `story.md` · CA-2 |
| IPC-2 | Expiración y renovación de sesión | — | `story.md` · CA-7 |
| IPC-2 | Cookies `httpOnly`/`Secure` y logout | — | `story.md` · CA-7; ticket #69 |
| IPC-2 | Rate limiting y bloqueo de intentos | — | `inspeccion-IPC-2.md` · defecto 11 |
| IPC-2 | Recepción del correo de bienvenida | — | `story.md` · CA-1 |
| IPC-2 | Destino posterior al registro | — | `story.md` · CA-1 (PENDIENTE) |
| IPC-2 | Validación server-side | — | `story.md` · CA-3 y CA-4 |
| IPC-2 | Matriz de navegadores e in-app | — | `environments.md` · 1 |
| IPC-2 | Normalización del correo | — | `story.md` · Preguntas abiertas |
| IPC-2 | Registro parcial y trigger de `professionals` | — | `test-plan-IPC-2-registro-e-inicio-de-sesion.md` · R3 |
| IPC-2 | Anti-enumeración de usuarios | — | `test-plan-IPC-2-registro-e-inicio-de-sesion.md` · R2 |

## Sin cobertura
*   **Backend de autenticación:** el host `uuhstroppfwbuxudziqe.supabase.co` no resuelve (NXDOMAIN); sin backend no se puede completar registro, login, refresh ni validación server-side. Hace falta restablecer el DNS o confirmar el host correcto.
*   **Cuenta de prueba:** la cuenta `qa-profesional-base@mailinator.com` está definida, pero la credencial vive en `.env` y el login no puede ejecutarse. Hace falta una sesión válida sin exponer secretos.
*   **Entorno aislado:** solo existe Producción; no se pueden crear cuentas, probar fuerza bruta, rate limiting ni cargas sin impacto real. Hace falta UAT/staging con Supabase separado.
*   **Acceso a base y logs:** sin `service_role` ni lectura de logs no se puede verificar el trigger de `professionals`, cuentas huérfanas, cookies o invalidación de tokens. Hace falta acceso controlado de QA.
*   **Token vencido:** no se puede forzar un JWT de 15 minutos o un refresh de 7 días sin acceso al token o un reloj de pruebas. Hace falta una estrategia de manipulación o espera controlada.
*   **Correo de bienvenida:** no se creó ninguna cuenta y el proveedor vigente está en contradicción (Resend vs Supabase). Hace falta una cuenta de prueba y un buzón controlado.
*   **Navegadores:** solo hay Chrome headless. Hace falta Safari, Firefox, Edge y dispositivos o emulación de webviews.
*   **Decisiones de producto:** destino posterior al registro, mensaje para correo existente, logout, rate limiting y normalización del correo siguen sin definir; sin oráculo no se pueden escribir resultados esperados definitivos.
