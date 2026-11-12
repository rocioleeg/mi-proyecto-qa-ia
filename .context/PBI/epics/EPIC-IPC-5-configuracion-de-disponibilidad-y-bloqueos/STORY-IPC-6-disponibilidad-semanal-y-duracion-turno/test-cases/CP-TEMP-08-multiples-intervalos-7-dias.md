# Caso de Prueba: Múltiples intervalos por día y 7 días activos

**ID:** CP-TEMP-08
**Historia:** IPC-6
**Estado de sincronización:** PENDIENTE DE SUBIR A JIRA
**Ruta:** A
**Tipo:** Manual
**Prioridad:** Media
**Automatizable:** No

## Precondiciones
*   La sección `/dashboard/availability` está abierta.
*   Para verificar persistencia se necesita una sesión válida; el login de prueba está bloqueado por DNS de Supabase Auth.
*   Si no hay sesión, se ejecuta solo la parte visual y se registra el bloqueo.

## Datos de Prueba
*   Siete días activos.
*   Lunes con dos intervalos no solapados: 09:00–12:00 y 14:00–18:00.
*   Otro día con un intervalo válido.
*   Ningún dato real de profesionales.

## Pasos
| # | Acción | Resultado Esperado |
| :--- | :--- | :--- |
| 1 | Abrir `/dashboard/availability` | Se muestra la configuración semanal |
| 2 | Activar los siete días | Cada día mantiene su propio estado activo |
| 3 | Definir una franja válida en cada día | Cada día conserva sus horarios de forma independiente |
| 4 | Añadir un segundo intervalo al lunes (09:00–12:00 y 14:00–18:00) | La UI acepta ambos intervalos sin error |
| 5 | Añadir un segundo intervalo a otro día | La UI mantiene los intervalos separados por día |
| 6 | Revisar mensajes y estabilidad | No hay error visible, no se pierde la configuración ni se bloquea la vista |
| 7 | Si hay sesión válida, guardar y recargar | Las reglas de los siete días y sus intervalos persisten |

## Trazabilidad
| Historia (US) | Caso / escenario | Bug de origen | Evidencia |
| :--- | :--- | :--- | :--- |
| IPC-6 | Múltiples intervalos por día y 7 días activos | BUG-07 (pregunta abierta) | `session-2026-11-12-ipc-6.md` · escenario 8; `2026-11-12-ipc-6-multiples-intervalos-pass.png` |

## Origen
*   **Priorización:** `priorizacion-roi.md` · escenario 7, score 1.8
*   **Criterio de aceptación cubierto:** `story.md` · Escenario 1 (reglas de días 0 a 6) y Escenario 6 (más de un intervalo por día); riesgo R2
