# Bug: [Disponibilidad] El guardado del horario semanal no se completa y muestra "No autenticado"

**ID:** BUG-02
**Estado de sincronización:** PENDIENTE DE SUBIR A JIRA
**Fecha:** 2026-11-12
**Entorno:** Producción
**Severidad:** Alta
**Prioridad:** Alta

## Descripción
Con un horario semanal válido, el botón "Guardar Cambios" no completa la operación: muestra "No autenticado" y no envía ninguna petición. El camino feliz de la historia queda bloqueado y el usuario no recibe una redirección ni una explicación que lo lleve a iniciar sesión.

## Pasos para Reproducir
1.  Abrir `https://cita-ai.vercel.app/dashboard/availability` sin sesión.
2.  Activar los días Lunes, Martes, Miércoles, Jueves y Viernes.
3.  Definir en cada día la franja 09:00–18:00.
4.  Hacer clic en "Guardar Cambios".
5.  Observar el mensaje "No autenticado" y verificar en la pestaña de red que no se envía ninguna petición.

## Resultados
*   **Esperado:** con una sesión válida, el guardado debe reemplazar atómicamente las reglas semanales y persistirlas — según `story.md` · Escenarios 1 y 3; `prd.md` · Feature 2, "Criterios de éxito"; `system-design.md` · Endpoints privados, `POST /api/availability/rules`.
*   **Real:** la UI muestra "No autenticado" y no envía ninguna petición. No se puede verificar la persistencia ni el recálculo de la grilla pública.

## Evidencia
*   `.context/testing/exploratory/ui/evidence/screenshots/2026-11-12-ipc-6-guardado-semanal-fail.png`
*   `.context/testing/exploratory/ui/evidence/ipc-6-notes.md`
*   `.context/testing/exploratory/ui/session-2026-11-12-ipc-6.md`

## Trazabilidad
| Historia (US) | Caso / escenario | Bug | Evidencia |
| :--- | :--- | :--- | :--- |
| IPC-6 | Escenario 1 — guardar disponibilidad semanal | BUG-02 | `.context/testing/exploratory/ui/evidence/screenshots/2026-11-12-ipc-6-guardado-semanal-fail.png` |
| IPC-6 | Escenario 3 — modificar la disponibilidad reemplaza la configuración previa | BUG-02 | `.context/testing/exploratory/ui/evidence/screenshots/2026-11-12-ipc-6-guardado-semanal-fail.png` |

## Referencia Cruzada
*   **Sesión de origen:** `.context/testing/exploratory/ui/session-2026-11-12-ipc-6.md`
*   **Datos de prueba usados:** horario lunes a viernes 09:00–18:00; no se usó una cuenta real ni se creó ningún dato.
*   **IDs de traza:** requestId / correlationId no disponibles.
*   **Relación:** la ausencia de sesión es la causa inmediata; el login de prueba no pudo completarse por `net::ERR_NAME_NOT_RESOLVED` contra Supabase Auth. Ver BUG-01 y BUG-03.
