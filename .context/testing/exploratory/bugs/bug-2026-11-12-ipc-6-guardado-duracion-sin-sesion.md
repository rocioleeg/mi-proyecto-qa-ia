# Bug: [Disponibilidad] El guardado de duración sin sesión envía una petición privada y muestra un error genérico

**ID:** BUG-03
**Estado de sincronización:** PENDIENTE DE SUBIR A JIRA
**Fecha:** 2026-11-12
**Entorno:** Producción
**Severidad:** Alta
**Prioridad:** Alta

## Descripción
Al cambiar la duración de la cita y pulsar "Guardar" sin sesión, la interfaz envía `PUT /api/professionals/settings`, recibe `401 Unauthorized` y muestra "Error al guardar". El mensaje no distingue entre sesión ausente o expirada y un fallo del servidor, y el usuario no es dirigido a iniciar sesión.

## Pasos para Reproducir
1.  Abrir `https://cita-ai.vercel.app/dashboard/availability` sin sesión.
2.  En "Duración de la Cita (minutos)", seleccionar `45 minutos`.
3.  Hacer clic en "Guardar".
4.  Observar la petición `PUT https://cita-ai.vercel.app/api/professionals/settings` con `{"appointment_duration":45}`.
5.  Verificar la respuesta `401 {"error":"Unauthorized"}` y el mensaje "Error al guardar".

## Resultados
*   **Esperado:** los endpoints privados, incluido `PUT /api/professionals/settings`, deben validar la sesión JWT y responder de forma coherente ante su ausencia; el flujo autenticado debe actualizar `appointment_duration_minutes` — según `system-design.md` · 4 "Diseño de Interfaces (APIs)" y Endpoints privados; `story.md` · Escenario 1. El mensaje exacto para sesión ausente no está documentado; se registra como pregunta abierta.
*   **Real:** la UI envía la petición sin sesión, la API responde `401` con `{"error":"Unauthorized"}` y la UI muestra el mensaje genérico "Error al guardar".

## Evidencia
*   `.context/testing/exploratory/ui/evidence/screenshots/2026-11-12-ipc-6-guardado-duracion-fail.png`
*   `.context/testing/exploratory/ui/evidence/ipc-6-notes.md`
*   `.context/testing/exploratory/ui/session-2026-11-12-ipc-6.md`

## Trazabilidad
| Historia (US) | Caso / escenario | Bug | Evidencia |
| :--- | :--- | :--- | :--- |
| IPC-6 | Escenario 1 — guardar disponibilidad y duración estándar | BUG-03 | `.context/testing/exploratory/ui/evidence/screenshots/2026-11-12-ipc-6-guardado-duracion-fail.png` |

## Referencia Cruzada
*   **Sesión de origen:** `.context/testing/exploratory/ui/session-2026-11-12-ipc-6.md`
*   **Datos de prueba usados:** duración 45 minutos; no se persistió ningún cambio ni se usó una cuenta real.
*   **IDs de traza:** requestId / correlationId no disponibles. Evidencia de red: `PUT /api/professionals/settings` → `401`.
*   **Relación:** el intento de login con el usuario de prueba de `.env` falló por `net::ERR_NAME_NOT_RESOLVED` contra Supabase Auth; la reproducción se hizo sin sesión.
