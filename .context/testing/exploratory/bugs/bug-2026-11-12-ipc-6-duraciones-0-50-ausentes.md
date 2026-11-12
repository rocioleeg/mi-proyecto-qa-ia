# Bug: [Disponibilidad] El selector de duración no ofrece 0 ni 50 minutos

**ID:** BUG-04
**Estado de sincronización:** PENDIENTE DE SUBIR A JIRA
**Fecha:** 2026-11-12
**Entorno:** Producción
**Severidad:** Media
**Prioridad:** Media

## Descripción
El selector de "Duración de la Cita (minutos)" solo ofrece 15, 30, 45, 60, 90 y 120 minutos. No permite seleccionar 0 minutos, por lo que el Escenario 5 de la historia no se puede ejecutar, y no incluye 50 minutos, valor usado como ejemplo en el flujo del PRD.

## Pasos para Reproducir
1.  Abrir `https://cita-ai.vercel.app/dashboard/availability`.
2.  Desplegar el `select` "Duración de la Cita (minutos)".
3.  Verificar las opciones disponibles.
4.  Intentar seleccionar `0` y luego `50`; ambos valores son rechazados y el selector permanece en `60`.

## Resultados
*   **Esperado:** el Escenario 5 exige fijar la duración en 0 minutos e intentar guardar para recibir un mensaje de error — según `story.md` · Escenario 5 y `03-especificacion-funcional-v0.3.md` · 4.2 ("debe ser mayor a cero"). El PRD usa 50 minutos como ejemplo de configuración — según `prd.md` · Flujo 1, paso 5. La obligatoriedad de ofrecer 50 minutos no está documentada como requisito cerrado; se registra como pregunta abierta.
*   **Real:** el `select` solo ofrece 15/30/45/60/90/120; `0` y `50` no son seleccionables y el valor permanece en 60.

## Evidencia
*   `.context/testing/exploratory/ui/evidence/screenshots/2026-11-12-ipc-6-duracion-opciones-fail.png`
*   `.context/testing/exploratory/ui/evidence/ipc-6-notes.md`
*   `.context/testing/exploratory/ui/session-2026-11-12-ipc-6.md`

## Trazabilidad
| Historia (US) | Caso / escenario | Bug | Evidencia |
| :--- | :--- | :--- | :--- |
| IPC-6 | Escenario 5 — duración de turno inválida es rechazada | BUG-04 | `.context/testing/exploratory/ui/evidence/screenshots/2026-11-12-ipc-6-duracion-opciones-fail.png` |
| IPC-6 | Escenario 1 — duración estándar del turno | BUG-04 | `.context/testing/exploratory/ui/evidence/screenshots/2026-11-12-ipc-6-duracion-opciones-fail.png` |

## Referencia Cruzada
*   **Sesión de origen:** `.context/testing/exploratory/ui/session-2026-11-12-ipc-6.md`
*   **Datos de prueba usados:** intento de selección de 0 y 50 minutos; no se guardó ningún cambio.
*   **IDs de traza:** requestId / correlationId no disponibles.
