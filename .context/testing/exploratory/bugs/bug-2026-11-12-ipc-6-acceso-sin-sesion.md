# Bug: [Disponibilidad] La configuración de agenda se renderiza sin sesión

**ID:** BUG-01
**Estado de sincronización:** PENDIENTE DE SUBIR A JIRA
**Fecha:** 2026-11-12
**Entorno:** Producción
**Severidad:** Crítica
**Prioridad:** Alta

## Descripción
La ruta `/dashboard/availability` renderiza completa la configuración de agenda del profesional (duración de cita, bloqueos de tiempo y horario semanal recurrente) sin exigir una sesión. El header muestra "Iniciar Sesión" y "Registrarse Gratis", pero no hay redirección ni bloqueo de la vista. Esto contradice el supuesto de sesión iniciada de la historia y la separación entre panel privado y endpoints públicos del diseño técnico.

## Pasos para Reproducir
1.  Abrir una ventana privada o un contexto sin cookies de sesión.
2.  Navegar a `https://cita-ai.vercel.app/dashboard/availability`.
3.  Observar que se carga "Gestionar Disponibilidad" con "Configuración General", "Bloqueos de Tiempo" y "Horario Semanal Recurrente".
4.  Verificar que no hay redirección a `/login` y que el header muestra los botones "Iniciar Sesión" y "Registrarse Gratis".

## Resultados
*   **Esperado:** el panel de administración debe exigir una sesión válida. Las rutas privadas se protegen mediante JWT y los endpoints privados requieren sesión, mientras que solo `/api/public/*` queda sin autenticación — según `system-design.md` · 4 "Diseño de Interfaces (APIs)" y 3 "Políticas de Row Level Security"; `story.md` · Escenario 1 asume "que inicié sesión como profesional". La redirección exacta a `/login` no está documentada; se registra como pregunta abierta.
*   **Real:** la página responde `200` y muestra toda la configuración sin sesión; no hay redirección ni llamada a `/api/*`.

## Evidencia
*   `.context/testing/exploratory/ui/evidence/screenshots/2026-11-12-ipc-6-acceso-sin-sesion-fail.png`
*   `.context/testing/exploratory/ui/evidence/ipc-6-notes.md`
*   `.context/testing/exploratory/ui/session-2026-11-12-ipc-6.md`

## Trazabilidad
| Historia (US) | Caso / escenario | Bug | Evidencia |
| :--- | :--- | :--- | :--- |
| IPC-6 | Supuesto de sesión / Escenario 1 — guardar disponibilidad semanal | BUG-01 | `.context/testing/exploratory/ui/evidence/screenshots/2026-11-12-ipc-6-acceso-sin-sesion-fail.png` |

## Referencia Cruzada
*   **Sesión de origen:** `.context/testing/exploratory/ui/session-2026-11-12-ipc-6.md`
*   **Datos de prueba usados:** ninguno; acceso sin sesión. No se creó ni modificó ningún dato.
*   **IDs de traza:** requestId / correlationId no disponibles.
*   **Limitación:** el intento de login con el usuario de prueba de `.env` falló porque el `POST` a Supabase Auth terminó en `net::ERR_NAME_NOT_RESOLVED`; por eso la reproducción se hizo sin sesión.
