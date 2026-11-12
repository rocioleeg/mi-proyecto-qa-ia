# Bug: [Disponibilidad] No hay límite visible de intervalos por día

**ID:** BUG-07
**Estado de sincronización:** PENDIENTE DE SUBIR A JIRA
**Fecha:** 2026-11-12
**Entorno:** Producción
**Severidad:** Baja
**Prioridad:** Baja

## Descripción
La interfaz permite añadir intervalos sin mostrar un máximo. Se agregaron 4 intervalos al lunes y se activaron los 7 días sin error ni mensaje. La historia asume múltiples intervalos por día, pero no define un límite; tampoco hay validación visible.

## Pasos para Reproducir
1.  Abrir `https://cita-ai.vercel.app/dashboard/availability`.
2.  Activar Lunes.
3.  Pulsar "Añadir intervalo" tres veces hasta tener 4 intervalos.
4.  Activar también Martes, Miércoles, Jueves, Viernes, Sábado y Domingo.
5.  Observar que no aparece ningún límite ni mensaje de error.

## Resultados
*   **Esperado:** no hay documento que fije un máximo de intervalos por día. El Escenario 6 de la historia asume más de un intervalo, pero no define un límite. Se registra como pregunta abierta: ¿cuántos intervalos se permiten y qué mensaje debe mostrarse al superar el máximo?
*   **Real:** se pudieron agregar 4 intervalos al lunes (8 `input[type=time]`) y activar los 7 días sin error ni límite visible.

## Evidencia
*   `.context/testing/exploratory/ui/evidence/screenshots/2026-11-12-ipc-6-multiples-intervalos-pass.png`
*   `.context/testing/exploratory/ui/evidence/ipc-6-notes.md` · sección "Super User"
*   `.context/testing/exploratory/ui/session-2026-11-12-ipc-6.md`

## Trazabilidad
| Historia (US) | Caso / escenario | Bug | Evidencia |
| :--- | :--- | :--- | :--- |
| IPC-6 | Escenario 6 — franjas solapadas / múltiples intervalos | BUG-07 | `.context/testing/exploratory/ui/evidence/screenshots/2026-11-12-ipc-6-multiples-intervalos-pass.png` |

## Referencia Cruzada
*   **Sesión de origen:** `.context/testing/exploratory/ui/session-2026-11-12-ipc-6.md`
*   **Datos de prueba usados:** 4 intervalos en lunes y 7 días activos; no se guardó ningún cambio.
*   **IDs de traza:** requestId / correlationId no disponibles.
