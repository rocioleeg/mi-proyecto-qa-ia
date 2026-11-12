# Notas de evidencia — Sesión exploratoria UI IPC-2

**Fecha:** 2026-11-11
**Entorno:** Producción (`https://cita-ai.vercel.app`)
**Método:** Playwright CLI con Chrome headless, viewport 1366x768 y 375x667.
**Sesión:** `../session-2026-11-11-ipc-2.md`

## Fallo de backend observado
*   La petición de login se dirige a `POST https://uuhstroppfwbuxudziqe.supabase.co/auth/v1/token`.
*   Resultado: `net::ERR_NAME_NOT_RESOLVED`; `nslookup` del host devuelve NXDOMAIN.
*   La UI traduce el fallo a "Email o contraseña inválidos." y no distingue un error de servicio de unas credenciales incorrectas.
*   Cada intento de login produce un `TypeError: Failed to fetch` en la consola del navegador.

## Validaciones observadas
| Caso | Valor probado | Resultado del navegador | Mensaje |
| :--- | :--- | :--- | :--- |
| Login vacío | email y contraseña vacíos | Inválido (`valueMissing: true`) | "Completa este campo" |
| Registro vacío | nombre, correo y contraseña vacíos | Inválido (`valueMissing: true`) | "Completa este campo" |
| Contraseña corta | `Ab1` (3 caracteres) | Válido | Sin mensaje |
| Contraseña sin mayúscula | `abcdefgh` | Válido | Sin mensaje |
| Contraseña sin número | `Abcdefgh` | Válido | Sin mensaje |
| Nombre largo | 101 caracteres | Válido; sin `maxlength` | Sin mensaje |
| Correo largo | 255 caracteres | Válido; sin `maxlength` | Sin mensaje |

## Envío repetido
*   Tres clics rápidos (100 ms entre cada uno) generaron **3 solicitudes** a `/auth/v1/token`.
*   El botón se deshabilita tras el primer clic, pero se rehabilita cuando la petición falla y acepta los siguientes.
*   No se observó debounce, cooldown ni bloqueo persistente.

## Responsive
*   Login a 375x667: `scrollWidth = 375`, `innerWidth = 375`; sin desborde horizontal.
*   Registro a 375x667: `scrollWidth = 375`, `innerWidth = 375`; sin desborde horizontal.

## Inventario de capturas
| Archivo | Qué muestra |
| :--- | :--- |
| `2026-11-11-ipc-2-login-invalid-credentials-fail.png` | Login con correo sintético no registrado y mensaje "Email o contraseña inválidos." |
| `2026-11-11-ipc-2-login-empty-submit-pass.png` | Login tras envío vacío; el navegador marca los campos requeridos. |
| `2026-11-11-ipc-2-registration-form-pass.png` | Formulario "Crea tu cuenta profesional" en `/login`. |
| `2026-11-11-ipc-2-registration-empty-submit-pass.png` | Registro tras envío vacío; el navegador marca los campos requeridos. |
| `2026-11-11-ipc-2-password-policy-short-fail.png` | Contraseña de 3 caracteres aceptada por la UI. |
| `2026-11-11-ipc-2-password-policy-no-uppercase-fail.png` | Contraseña sin mayúscula aceptada por la UI. |
| `2026-11-11-ipc-2-password-policy-no-number-fail.png` | Contraseña sin número aceptada por la UI. |
| `2026-11-11-ipc-2-long-inputs-fail.png` | Nombre de 101 caracteres y correo de 255 aceptados por la UI. |
| `2026-11-11-ipc-2-double-submit-fail.png` | Estado tras tres envíos rápidos; se contaron 3 solicitudes de autenticación. |
| `2026-11-11-ipc-2-dashboard-no-session-fail.png` | `/dashboard` renderizado sin sesión. |
| `2026-11-11-ipc-2-mobile-login-pass.png` | Login a 375x667 sin desborde horizontal. |
| `2026-11-11-ipc-2-mobile-registration-pass.png` | Registro a 375x667 sin desborde horizontal. |

## Privacidad
*   No se usó la cuenta de Fernando ni se transcribieron sus credenciales.
*   Los correos que aparecen en las capturas son sintéticos y no están registrados (`qa-no-existe-2026@mailinator.com`, `qa-ui-2026@mailinator.com`).
*   No se creó ningún usuario, cita, cliente ni registro en la base de datos.
