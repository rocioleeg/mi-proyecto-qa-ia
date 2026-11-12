# Caso de Prueba: Cambios sin guardar y botón "Atrás"

**ID:** CP-TEMP-09
**Historia:** IPC-6
**Estado de sincronización:** PENDIENTE DE SUBIR A JIRA
**Ruta:** A
**Tipo:** Manual
**Prioridad:** Media
**Automatizable:** No

## Precondiciones
*   La sección `/dashboard/availability` está abierta.
*   El usuario puede editar la configuración sin guardar.
*   No se requiere sesión válida para observar la navegación, pero el guardado final queda fuera de este caso.

## Datos de Prueba
*   Lunes activo con franja 09:00–18:00 modificada sin guardar.
*   Ningún dato real de profesionales.

## Pasos
| # | Acción | Resultado Esperado |
| :--- | :--- | :--- |
| 1 | Abrir `/dashboard/availability` | Se muestra la configuración semanal |
| 2 | Activar el lunes y definir 09:00–18:00 | La franja queda editada en la UI |
| 3 | Sin guardar, presionar el botón "Atrás" del navegador | La aplicación advierte sobre cambios sin guardar o conserva el estado editado |
| 4 | Volver a la sección de disponibilidad | No se pierden los cambios sin advertencia |
| 5 | Repetir con una modificación en la duración | El comportamiento es consistente entre horario y duración |

## Trazabilidad
| Historia (US) | Caso / escenario | Bug de origen | Evidencia |
| :--- | :--- | :--- | :--- |
| IPC-6 | Pérdida de cambios sin guardar al navegar con "Atrás" | BUG-06 | `session-2026-11-12-ipc-6.md` · escenario 10; `2026-11-12-ipc-6-back-button-fail.png` |

## Origen
*   **Priorización:** `priorizacion-roi.md` · escenario 9, score 1.8
*   **Criterio de aceptación cubierto:** `story.md` · Escenario 3 (modificación de disponibilidad); riesgo R2
