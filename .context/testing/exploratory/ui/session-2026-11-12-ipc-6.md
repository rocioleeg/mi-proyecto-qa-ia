# Sesión Exploratoria UI: IPC-6 — Disponibilidad semanal y duración de turno

**Fecha:** 2026-11-12
**Entorno:** Producción
**Duración:** ~35 minutos (automatizada)
**Modo de ejecución:** Navegador automatizado (Playwright CLI, Chrome headless; sin Playwright MCP)
**Misión:** Encontrar inconsistencias en la configuración de disponibilidad semanal y duración de turno (IPC-6), con foco en validaciones, manejo de sesión, persistencia esperada y comportamiento responsive.

## Escenarios Probados
| # | Escenario | Resultado | Evidencia |
| :--- | :--- | :--- | :--- |
| 1 | Acceso directo a `/dashboard/availability` sin sesión | FAIL — la página se renderiza sin autenticación | `evidence/screenshots/2026-11-12-ipc-6-acceso-sin-sesion-fail.png` |
| 2 | Inicio de sesión con el usuario de prueba de `.env` | FAIL — el `POST` a Supabase Auth termina en `net::ERR_NAME_NOT_RESOLVED`; la UI permanece en `/login` con "Email o contraseña inválidos" | `evidence/screenshots/2026-11-12-ipc-6-login-fail.png` |
| 3 | Camino feliz: lunes a viernes 09:00-18:00 + "Guardar Cambios" | FAIL — muestra "No autenticado"; no envía petición | `evidence/screenshots/2026-11-12-ipc-6-guardado-semanal-fail.png` |
| 4 | Franja inválida 18:00 → 09:00 | PASS — rechaza con mensaje y no envía petición | `evidence/screenshots/2026-11-12-ipc-6-franja-invalida-pass.png` |
| 5 | Franjas solapadas 09:00-12:00 + 11:00-14:00 | PASS — rechaza con mensaje y no envía petición | `evidence/screenshots/2026-11-12-ipc-6-franjas-solapadas-pass.png` |
| 6 | Duración 0 y duración 50 | FAIL — 0 no es seleccionable y 50 no existe en el `select` | `evidence/screenshots/2026-11-12-ipc-6-duracion-opciones-fail.png` |
| 7 | Guardado de duración sin sesión | FAIL — `PUT /api/professionals/settings` responde 401 y la UI muestra "Error al guardar" | `evidence/screenshots/2026-11-12-ipc-6-guardado-duracion-fail.png` |
| 8 | Super User: múltiples intervalos y todos los días activos | PASS (observación) — 4 intervalos en lunes y 7 días activos sin límite visible | `evidence/screenshots/2026-11-12-ipc-6-multiples-intervalos-pass.png` |
| 9 | Responsive 375x667 | FAIL — desborde horizontal: `scrollWidth` 389 vs viewport 375 | `evidence/screenshots/2026-11-12-ipc-6-responsive-mobile-fail.png` |
| 10 | Back Button con cambios sin guardar | FAIL — el estado se pierde sin advertencia (1 día activo → 0) | `evidence/screenshots/2026-11-12-ipc-6-back-button-fail.png` |
| 11 | Persistencia de reglas y recálculo de la grilla pública | No ejecutado — sin sesión; login bloqueado por DNS; sin slug público | — |

## Defectos Encontrados
1. **Crítico — `/dashboard/availability` accesible sin sesión.** La página renderiza toda la configuración (duración, bloqueos y horario semanal) sin autenticación y sin redirección a login. Contradice el supuesto de la historia ("Given que inicié sesión como profesional...") y expone la UI de configuración. Está relacionado con el acceso sin sesión a `/dashboard` reportado en IPC-2 y con el ticket #69.
2. **Alto — El guardado semanal no se puede completar y el mensaje no guía a iniciar sesión.** Con un horario válido (lunes a viernes 09:00-18:00), "Guardar Cambios" muestra "No autenticado" y no envía ninguna petición; no hay redirección ni explicación. La persistencia queda sin verificar.
3. **Alto — El guardado de duración sin sesión envía una petición y muestra un error genérico.** La UI envía `PUT /api/professionals/settings` con `{"appointment_duration":45}`; la API responde `401` con `{"error":"Unauthorized"}` y la UI muestra "Error al guardar". No distingue sesión ausente/expirada de un error de servidor.
4. **Medio — La duración 0 no es seleccionable y la opción 50 no existe.** El `select` solo ofrece 15/30/45/60/90/120 minutos. El Escenario 5 (0 minutos) no se puede ejecutar y el PRD menciona 50 minutos como ejemplo.
5. **Medio — Desborde horizontal en 375x667.** `document.documentElement.scrollWidth` es 389 px con un viewport de 375 px. El botón "Registrarse Gratis" del header se extiende hasta 389 px; los `input[type=time]` también tienen un desborde interno menor (133 vs 130 px).
6. **Bajo — Pérdida silenciosa de cambios sin guardar.** Al activar un día y cambiar una hora, navegar al dashboard y volver con "Atrás", el día queda desactivado (1 activo → 0) sin advertencia de cambios sin guardar.
7. **Bajo — Sin límite visible de intervalos por día.** Se agregaron 4 intervalos al lunes y se activaron los 7 días sin error ni mensaje; no hay un límite visible. La historia no define el máximo.
8. **Bajo — Mensajes de autenticación inconsistentes.** El guardado semanal muestra "No autenticado" y el de duración "Error al guardar" para el mismo problema de sesión.

## Trazabilidad
| Historia (US) | Caso / escenario | Bug | Evidencia |
| :--- | :--- | :--- | :--- |
| IPC-6 | Escenario 1 — guardar reglas y verificar la grilla pública | pendiente (bloqueado) | `evidence/screenshots/2026-11-12-ipc-6-guardado-semanal-fail.png` |
| IPC-6 | Escenario 2 — día sin franjas no laborable | pendiente | `evidence/screenshots/2026-11-12-ipc-6-acceso-sin-sesion-fail.png` |
| IPC-6 | Escenario 3 — lunes a viernes 09:00-18:00 | pendiente (bloqueado) | `evidence/screenshots/2026-11-12-ipc-6-guardado-semanal-fail.png` |
| IPC-6 | Escenario 4 — franja inválida | — (PASS) | `evidence/screenshots/2026-11-12-ipc-6-franja-invalida-pass.png` |
| IPC-6 | Escenario 5 — duración 0 | pendiente (no ejecutable) | `evidence/screenshots/2026-11-12-ipc-6-duracion-opciones-fail.png` |
| IPC-6 | Escenario 6 — franjas solapadas | — (PASS) | `evidence/screenshots/2026-11-12-ipc-6-franjas-solapadas-pass.png` |
| IPC-6 | Escenario 7 — duración 45/50/60 | pendiente (50 ausente) | `evidence/screenshots/2026-11-12-ipc-6-duracion-opciones-fail.png` |
| IPC-6 | Supuesto de sesión — ruta protegida | pendiente | `evidence/screenshots/2026-11-12-ipc-6-acceso-sin-sesion-fail.png`, `evidence/screenshots/2026-11-12-ipc-6-guardado-duracion-fail.png` |
| IPC-6 | Responsive 375x667 | pendiente | `evidence/screenshots/2026-11-12-ipc-6-responsive-mobile-fail.png` |
| IPC-6 | Navegación — cambios sin guardar | pendiente | `evidence/screenshots/2026-11-12-ipc-6-back-button-fail.png` |

## Sin cobertura
*   **Camino feliz de guardado y persistencia:** no se pudo completar; no hay sesión y el `POST` a `https://uuhstroppfwbuxudziqe.supabase.co/auth/v1/token?grant_type=password` termina en `net::ERR_NAME_NOT_RESOLVED`. No se creó ningún dato.
*   **Grilla pública y recálculo:** no verificable; el dashboard muestra "Perfil público no disponible... No pudimos encontrar tu slug" y no hay sesión.
*   **Aislamiento por profesional, RLS y `auth.uid()`:** no verificable sin sesión ni `service_role`.
*   **Validación server-side de reglas y duración:** solo se observó el 401 de duración; no se pudo probar con una sesión válida.
*   **Duración 0 y 50:** la opción 0 no existe en el `select` y la 50 tampoco; el escenario de 0 minutos no se pudo ejecutar.
*   **Bloqueos de Tiempo (IPC-7):** la sección está visible en la página, pero queda fuera del alcance de IPC-6 y no se probó.
*   **Correos y notificaciones:** no se disparó ninguno; no se creó ninguna cuenta ni dato.
*   **Matriz de navegadores:** solo Chrome headless; se cubrieron 375x667 y 1366x768. No se probaron Safari, Firefox, Edge ni navegadores in-app.
*   **Inspección visual de las capturas:** se validó la existencia y el tamaño de los archivos; los hallazgos se sustentan en datos de DOM, red y texto observados.
*   **Datos reales:** no se creó ningún dato, no se usó la cuenta de Fernando y no se transcribieron credenciales. En esta sesión no se envió ningún correo.

## Transferencia UI -> API/DB
### Acciones Disparadoras
*   Abrir `/dashboard/availability` directamente → no hay redirección ni llamada a `/api/*`; la página se renderiza sin sesión.
*   Enviar el formulario de `/login` → `POST https://uuhstroppfwbuxudziqe.supabase.co/auth/v1/token?grant_type=password`; la resolución DNS falla con `net::ERR_NAME_NOT_RESOLVED` y no se obtiene respuesta HTTP.
*   Activar días y completar franjas → estado local; no hay petición hasta guardar.
*   Clic en "Guardar Cambios" con franja inválida o solapada → validación client-side; no hay petición.
*   Clic en "Guardar Cambios" con horario válido sin sesión → muestra "No autenticado"; no se observó petición.
*   Clic en "Guardar" de duración sin sesión → `PUT https://cita-ai.vercel.app/api/professionals/settings` con `{"appointment_duration":45}` → `401 {"error":"Unauthorized"}`; UI "Error al guardar".
*   Clic en "Añadir intervalo" → estado local; no hay petición.
*   Navegación al dashboard y "Atrás" → se pierde el estado local; no hay petición ni advertencia.

### Datos de Prueba Utilizados
*   email: `TEST_USER_EMAIL` — definido en `.env`, no transcrito.
*   userId: no disponible.
*   monto: no aplica.
*   Duración seleccionada: 45 minutos (no persistida).
*   Días y horarios: lunes a viernes 09:00-18:00; lunes 09:00-12:00 + 11:00-14:00; lunes 18:00-09:00; lunes con 4 intervalos; 7 días activos. Todo quedó en memoria y no se guardó.

### Trazas Observadas
*   requestId: no disponible.
*   correlationId: no disponible.
*   Evidencia de red: `PUT /api/professionals/settings` → 401; `POST` a Supabase Auth sin respuesta por fallo de DNS. Consola: "Failed to load resource: net::ERR_NAME_NOT_RESOLVED" y "Failed to load resource: the server responded with a status of 401 ()". No hubo errores de página.
