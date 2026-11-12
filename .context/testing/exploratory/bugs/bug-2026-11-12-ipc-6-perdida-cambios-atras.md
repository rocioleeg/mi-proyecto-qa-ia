# Bug: [Disponibilidad] Los cambios sin guardar se pierden al volver con "Atrás"

**ID:** BUG-06
**Estado de sincronización:** PENDIENTE DE SUBIR A JIRA
**Fecha:** 2026-11-12
**Entorno:** Producción
**Severidad:** Baja
**Prioridad:** Baja

## Descripción
Al editar la disponibilidad sin guardar, navegar al dashboard y volver con el botón "Atrás" del navegador, el estado editado se pierde sin advertencia. Un día activado con una hora modificada vuelve a quedar desactivado.

## Pasos para Reproducir
1.  Abrir `https://cita-ai.vercel.app/dashboard/availability`.
2.  Activar Lunes y cambiar la hora de inicio a 10:00.
3.  Navegar al dashboard.
4.  Pulsar "Atrás" en el navegador.
5.  Observar que Lunes queda desactivado y que no hubo advertencia de cambios sin guardar.

## Resultados
*   **Esperado:** no hay documento que defina el comportamiento esperado para cambios sin guardar ni una advertencia al abandonar la vista. Se registra como pregunta abierta: ¿debe advertirse, conservarse el estado o descartarse explícitamente? La historia de IPC-6 no menciona persistencia local ni confirmación de salida.
*   **Real:** al volver con "Atrás", el día activo se pierde (1 día activo → 0) y no se muestra ninguna advertencia.

## Evidencia
*   `.context/testing/exploratory/ui/evidence/screenshots/2026-11-12-ipc-6-back-button-fail.png`
*   `.context/testing/exploratory/ui/evidence/ipc-6-notes.md` · sección "Back Button"
*   `.context/testing/exploratory/ui/session-2026-11-12-ipc-6.md`

## Trazabilidad
| Historia (US) | Caso / escenario | Bug | Evidencia |
| :--- | :--- | :--- | :--- |
| IPC-6 | Navegación con cambios sin guardar — no cubierto por los escenarios Gherkin | BUG-06 | `.context/testing/exploratory/ui/evidence/screenshots/2026-11-12-ipc-6-back-button-fail.png` |

## Referencia Cruzada
*   **Sesión de origen:** `.context/testing/exploratory/ui/session-2026-11-12-ipc-6.md`
*   **Datos de prueba usados:** Lunes activo con hora 10:00; no se guardó ningún cambio.
*   **IDs de traza:** requestId / correlationId no disponibles.
