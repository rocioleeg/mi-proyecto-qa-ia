# Sesión Exploratoria UI: IPC-2 — Registro e inicio de sesión

**Fecha:** 2026-11-11
**Entorno:** Producción
**Duración:** ~25 minutos (automatizada)
**Modo de ejecución:** Navegador automatizado (Playwright CLI, Chrome headless; sin Playwright MCP)
**Misión:** Encontrar inconsistencias en el flujo de registro e inicio de sesión del profesional (IPC-2), con foco en validaciones, manejo de errores, navegación y comportamiento responsive.

## Escenarios Probados
| # | Escenario | Resultado | Evidencia |
| :--- | :--- | :--- | :--- |
| 1 | Login con usuario de prueba (camino feliz) | No ejecutado | El backend de Supabase Auth no resuelve (NXDOMAIN). El intento con el usuario de prueba está en `../smoke/smoke-2026-11-11-produccion.md` y falló por la misma causa. |
| 2 | Login con credenciales inválidas y backend caído | FAIL | `evidence/screenshots/2026-11-11-ipc-2-login-invalid-credentials-fail.png` |
| 3 | Envío vacío en login y registro | PASS | `evidence/screenshots/2026-11-11-ipc-2-login-empty-submit-pass.png`, `evidence/screenshots/2026-11-11-ipc-2-registration-empty-submit-pass.png` |
| 4 | Política de contraseña: 3 caracteres, sin mayúscula, sin número | FAIL | `evidence/screenshots/2026-11-11-ipc-2-password-policy-short-fail.png`, `evidence/screenshots/2026-11-11-ipc-2-password-policy-no-uppercase-fail.png`, `evidence/screenshots/2026-11-11-ipc-2-password-policy-no-number-fail.png` |
| 5 | Entradas largas: nombre de 101 caracteres y correo de 255 | FAIL | `evidence/screenshots/2026-11-11-ipc-2-long-inputs-fail.png` |
| 6 | Triple envío rápido del login | FAIL | `evidence/screenshots/2026-11-11-ipc-2-double-submit-fail.png` |
| 7 | Acceso directo a `/dashboard` sin sesión | FAIL | `evidence/screenshots/2026-11-11-ipc-2-dashboard-no-session-fail.png` |
| 8 | Responsive 375x667 en login y registro | PASS | `evidence/screenshots/2026-11-11-ipc-2-mobile-login-pass.png`, `evidence/screenshots/2026-11-11-ipc-2-mobile-registration-pass.png` |
| 9 | Layout y textos del formulario de registro | PASS (observación) | `evidence/screenshots/2026-11-11-ipc-2-registration-form-pass.png` |

## Defectos Encontrados
1. **Crítico — Autenticación inoperante por backend inaccesible.** La petición de login se dirige a `POST https://uuhstroppfwbuxudziqe.supabase.co/auth/v1/token` y falla con `net::ERR_NAME_NOT_RESOLVED`; el dominio no existe (NXDOMAIN). Login y registro no pueden completarse. La interfaz muestra "Email o contraseña inválidos." en lugar de un error de servicio.
2. **Crítico — `/dashboard` accesible sin sesión.** La ruta renderiza "Bienvenido a tu panel de control" y la navegación privada sin autenticación; contradice el Escenario 8 y el PRD §5. Está relacionado con el ticket de soporte #69.
3. **Alto — Mensaje de error de login engañoso.** "Email o contraseña inválidos." se usa tanto para credenciales incorrectas como para un fallo de red. Además, el texto difiere de "Email o contraseña incorrectos" de la especificación 3.2.
4. **Medio — Sin validación client-side de la política de contraseña.** La interfaz considera válidas contraseñas de 3 caracteres, sin mayúscula o sin número; no muestra ayuda ni mensaje de regla incumplida. El rechazo del servidor no se pudo verificar.
5. **Medio — Sin límites de longitud en nombre y correo.** El nombre de 101 caracteres y el correo de 255 se consideran válidos; no hay `maxlength`. El rechazo del servidor no se pudo verificar.
6. **Bajo — Envío repetido.** Tres clics rápidos generan 3 solicitudes de autenticación. El botón se deshabilita tras el primer clic, pero se rehabilita cuando la petición falla y admite nuevos envíos.
7. **Bajo — Título de la pestaña sin cambios en registro.** El modo registro sigue mostrando el título "Iniciar Sesión - CITA AI"; solo cambia el encabezado a "Crea tu cuenta profesional".
8. **Bajo — Sin ayuda de contraseña ni control de visibilidad.** No se observó texto con los requisitos de contraseña ni un botón para mostrar u ocultar el valor ingresado.

## Trazabilidad
| Historia (US) | Caso / escenario | Bug | Evidencia |
| :--- | :--- | :--- | :--- |
| IPC-2 | Escenario 1 — registro exitoso | pendiente | No ejecutado: no se autorizó crear una cuenta en Producción y el backend no responde. |
| IPC-2 | Escenario 2 — correo ya existente | pendiente | No ejecutado: no hay una cuenta accesible y el backend no responde. |
| IPC-2 | Escenario 3 — contraseña fuera de política | pendiente | `evidence/screenshots/2026-11-11-ipc-2-password-policy-short-fail.png`, `evidence/screenshots/2026-11-11-ipc-2-password-policy-no-uppercase-fail.png`, `evidence/screenshots/2026-11-11-ipc-2-password-policy-no-number-fail.png` |
| IPC-2 | Escenario 4 — datos inválidos | pendiente | `evidence/screenshots/2026-11-11-ipc-2-registration-empty-submit-pass.png`, `evidence/screenshots/2026-11-11-ipc-2-long-inputs-fail.png` |
| IPC-2 | Escenario 5 — login exitoso | pendiente | No ejecutado: backend NXDOMAIN. |
| IPC-2 | Escenario 6 — login con credenciales incorrectas | pendiente | `evidence/screenshots/2026-11-11-ipc-2-login-invalid-credentials-fail.png` |
| IPC-2 | Escenario 7 — expiración y refresh | pendiente | No ejecutado: sin sesión válida. |
| IPC-2 | Escenario 8 — ruta protegida sin sesión | pendiente (relacionado con #69) | `evidence/screenshots/2026-11-11-ipc-2-dashboard-no-session-fail.png` |

## Sin cobertura
*   **Registro exitoso y login exitoso:** el backend de Supabase Auth no resuelve (NXDOMAIN) y no se autorizó crear una cuenta en Producción.
*   **Correo ya existente:** no hay una cuenta accesible para probar el rechazo; el backend no responde.
*   **Expiración y refresh de sesión, cookies `httpOnly`/`Secure` y logout:** no verificables sin una sesión iniciada.
*   **Rate limiting:** no se observó un mecanismo; el envío repetido se probó solo con credenciales inválidas.
*   **Correo de bienvenida:** no se creó ninguna cuenta, por lo que no se disparó el envío.
*   **Destino posterior al registro:** no se completó el registro.
*   **Validación del servidor:** no verificable por el fallo de red.
*   **Matriz de navegadores:** solo Chrome headless; se cubrió 375x667 y 1366x768. No se probaron Safari, Firefox, Edge ni navegadores in-app.
*   **Datos reales:** no se creó ningún dato, no se usó la cuenta de Fernando y no se transcribieron credenciales. El correo usado en la UI es sintético y no está registrado.

## Transferencia UI -> API/DB
### Acciones Disparadoras
*   Clic en "Iniciar Sesión" → `POST https://uuhstroppfwbuxudziqe.supabase.co/auth/v1/token` (falla con `net::ERR_NAME_NOT_RESOLVED`).
*   Clic en "Registrarse" con datos válidos → no ejecutado; se espera `POST https://uuhstroppfwbuxudziqe.supabase.co/auth/v1/signup` y luego la inserción en `professionals` por trigger.
*   Acceso directo a `/dashboard` → no hay redirección ni llamada observable a `/api/*`; el panel se renderiza sin sesión.
*   Clic en "Regístrate gratis" → cambio de modo dentro de `/login`; no hay navegación ni endpoint.

### Datos de Prueba Utilizados
*   email: `qa-no-existe-2026@mailinator.com` (sintético, no registrado) y `qa-ui-2026@mailinator.com` (sintético, solo para validaciones; no se envió).
*   userId: no disponible.
*   monto: no aplica.

### Trazas Observadas
*   requestId: no disponible.
*   correlationId: no disponible.
*   Evidencia de red: `POST .../auth/v1/token` → `net::ERR_NAME_NOT_RESOLVED`; DNS NXDOMAIN. La captura `evidence/screenshots/2026-11-11-ipc-2-login-invalid-credentials-fail.png` muestra el mensaje de la UI; el detalle de red está registrado en esta sesión y en el `story.md` de IPC-2.
