# Notas de evidencia — Sesión exploratoria UI IPC-6

**Fecha:** 2026-11-12
**Entorno:** Producción (`https://cita-ai.vercel.app`)
**Método:** Playwright CLI con Chrome headless, viewports 1366x768 y 375x667.
**Sesión:** `../session-2026-11-12-ipc-6.md`

## Estructura observada
*   Ruta: `/dashboard/availability`; título "CITA AI - Gestión de Citas".
*   Encabezado "Gestionar Disponibilidad"; secciones "Configuración General", "Bloqueos de Tiempo" y "Horario Semanal Recurrente".
*   7 switches (Domingo a Sábado), todos con `aria-checked="false"` y texto "No disponible" al cargar.
*   Duración: `select#duration` con opciones 15, 30, 45, 60, 90 y 120; valor inicial 60.
*   Botones: "Guardar" (duración), "Crear Bloqueo" (IPC-7) y "Guardar Cambios" (semanal).
*   Al activar un día aparecen 2 `input[type=time]` con 09:00 y 17:00, y el botón "Añadir intervalo".
*   La página se renderiza sin sesión; el header muestra "Iniciar Sesión" y "Registrarse Gratis".

## Autenticación
*   Se intentó iniciar sesión con `TEST_USER_EMAIL` y `TEST_USER_PASSWORD` cargados desde `.env`; los valores no se transcribieron.
*   El formulario de `/login` disparó `POST https://uuhstroppfwbuxudziqe.supabase.co/auth/v1/token?grant_type=password`.
*   La petición falló con `net::ERR_NAME_NOT_RESOLVED`; no hubo respuesta HTTP.
*   La UI permaneció en `/login` y mostró "Email o contraseña inválidos." No se obtuvo sesión.

## Validaciones observadas
| Caso | Valores | Resultado | Mensaje | Petición |
| :--- | :--- | :--- | :--- | :--- |
| Franja inválida | Lunes 18:00 → 09:00 | Rechazado | "La hora de inicio debe ser anterior a la de fin en Lunes" | Ninguna |
| Franjas solapadas | Lunes 09:00-12:00 + 11:00-14:00 | Rechazado | "Horarios superpuestos en Lunes" | Ninguna |
| Guardado semanal sin sesión | Lunes a viernes 09:00-18:00 | Bloqueado | "No autenticado" | Ninguna |
| Guardado de duración sin sesión | 45 minutos | 401 | "Error al guardar" | `PUT /api/professionals/settings` |

## Autenticación y red
*   `PUT https://cita-ai.vercel.app/api/professionals/settings` con `{"appointment_duration":45}` → `401 {"error":"Unauthorized"}`.
*   Consola: "Failed to load resource: the server responded with a status of 401 ()".
*   Consola: "Failed to load resource: net::ERR_NAME_NOT_RESOLVED" al intentar autenticar contra Supabase.
*   El guardado semanal no envió ninguna petición; el de duración sí.
*   No se observaron `requestId` ni `correlationId`.

## Duración
*   Opciones: 15, 30, 45, 60, 90 y 120 minutos.
*   `selectOption('0')` y `selectOption('50')` fallan; el valor permanece en 60.
*   El Escenario 5 (0 minutos) no es ejecutable desde la UI.

## Super User
*   4 intervalos en lunes → 8 `input[type=time]`; sin error ni límite visible.
*   7 días activos; sin error.
*   No se guardó nada.

## Responsive
*   375x667: `innerWidth = 375`, `scrollWidth = 389`, `bodyScrollWidth = 389`; desborde horizontal de 14 px.
*   Elemento principal: `div.ml-auto.flex.items-center.space-x-4` y el botón "Registrarse Gratis" llegan a 389 px.
*   Los `input[type=time]` tienen `scrollWidth = 133` y `clientWidth = 130`.

## Back Button
*   Lunes activo con hora 10:00; navegación al dashboard y `goBack()` → lunes desactivado (1 → 0); sin advertencia.

## Inventario de capturas
| Archivo | Qué muestra |
| :--- | :--- |
| `2026-11-12-ipc-6-acceso-sin-sesion-fail.png` | `/dashboard/availability` renderizado sin sesión, con la configuración visible. |
| `2026-11-12-ipc-6-login-fail.png` | `/login` tras el intento fallido; mensaje "Email o contraseña inválidos." |
| `2026-11-12-ipc-6-guardado-semanal-fail.png` | Lunes a viernes 09:00-18:00 y mensaje "No autenticado" al guardar. |
| `2026-11-12-ipc-6-franja-invalida-pass.png` | Mensaje "La hora de inicio debe ser anterior a la de fin en Lunes". |
| `2026-11-12-ipc-6-franjas-solapadas-pass.png` | Mensaje "Horarios superpuestos en Lunes". |
| `2026-11-12-ipc-6-duracion-opciones-fail.png` | Selector de duración con las opciones disponibles; sin 0 ni 50. |
| `2026-11-12-ipc-6-guardado-duracion-fail.png` | Estado tras el 401 al guardar 45 minutos; mensaje "Error al guardar". |
| `2026-11-12-ipc-6-multiples-intervalos-pass.png` | Lunes con 4 intervalos y los 7 días activos. |
| `2026-11-12-ipc-6-responsive-mobile-fail.png` | Vista 375x667 con desborde horizontal del header. |
| `2026-11-12-ipc-6-back-button-fail.png` | Disponibilidad tras volver con "Atrás"; el día activo se perdió. |

## Privacidad
*   No se creó ningún dato, no se usó una cuenta real y no se transcribieron credenciales.
*   No se envió ningún correo ni se disparó ninguna notificación.
*   Las capturas se validaron por existencia y tamaño; los hallazgos se sustentan en datos de DOM, red y texto observados.
