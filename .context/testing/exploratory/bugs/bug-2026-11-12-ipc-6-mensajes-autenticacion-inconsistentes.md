# Bug: [Disponibilidad] Mensajes inconsistentes ante la ausencia de sesión

**ID:** BUG-08
**Estado de sincronización:** PENDIENTE DE SUBIR A JIRA
**Fecha:** 2026-11-12
**Entorno:** Producción
**Severidad:** Baja
**Prioridad:** Baja

## Descripción
Ante el mismo problema —guardar sin sesión— la interfaz muestra mensajes distintos según la acción: "No autenticado" al guardar el horario semanal y "Error al guardar" al guardar la duración. El segundo mensaje oculta que la causa es la falta de sesión.

## Pasos para Reproducir
1.  Abrir `https://cita-ai.vercel.app/dashboard/availability` sin sesión.
2.  Configurar un horario semanal válido y pulsar "Guardar Cambios"; observar "No autenticado".
3.  Cambiar la duración a 45 minutos y pulsar "Guardar"; observar "Error al guardar" tras el `401`.
4.  Comparar ambos mensajes para la misma condición de sesión.

## Resultados
*   **Esperado:** no hay documento que defina los mensajes para acciones de guardado sin sesión. La especificación solo define el mensaje genérico de credenciales incorrectas en el login — según `03-especificacion-funcional-v0.3.md` · 3.2. Se registra como pregunta abierta: ¿qué mensaje único debe mostrarse cuando falta la sesión y debe redirigirse al login?
*   **Real:** el guardado semanal muestra "No autenticado" y el de duración muestra "Error al guardar" para la misma causa.

## Evidencia
*   `.context/testing/exploratory/ui/evidence/screenshots/2026-11-12-ipc-6-guardado-semanal-fail.png`
*   `.context/testing/exploratory/ui/evidence/screenshots/2026-11-12-ipc-6-guardado-duracion-fail.png`
*   `.context/testing/exploratory/ui/evidence/ipc-6-notes.md`
*   `.context/testing/exploratory/ui/session-2026-11-12-ipc-6.md`

## Trazabilidad
| Historia (US) | Caso / escenario | Bug | Evidencia |
| :--- | :--- | :--- | :--- |
| IPC-6 | Guardado sin sesión — horario semanal y duración | BUG-08 | `.context/testing/exploratory/ui/evidence/screenshots/2026-11-12-ipc-6-guardado-semanal-fail.png` |

## Referencia Cruzada
*   **Sesión de origen:** `.context/testing/exploratory/ui/session-2026-11-12-ipc-6.md`
*   **Datos de prueba usados:** horario lunes a viernes 09:00–18:00 y duración 45; no se guardó ningún cambio.
*   **IDs de traza:** requestId / correlationId no disponibles.
