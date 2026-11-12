# Caso de Prueba: Mensajes consistentes ante falta de sesión

**ID:** CP-TEMP-10
**Historia:** IPC-6
**Estado de sincronización:** PENDIENTE DE SUBIR A JIRA
**Ruta:** A
**Tipo:** Manual
**Prioridad:** Media
**Automatizable:** No

## Precondiciones
*   El navegador no tiene cookies ni sesión iniciada.
*   La sección `/dashboard/availability` está abierta.
*   El backend de autenticación no está disponible o no hay JWT válido.

## Datos de Prueba
*   Horario semanal: lunes 09:00–18:00.
*   Duración de turno: 45 minutos.
*   Ningún dato real de profesionales.

## Pasos
| # | Acción | Resultado Esperado |
| :--- | :--- | :--- |
| 1 | Configurar el lunes 09:00–18:00 y presionar "Guardar" | Se muestra un mensaje claro de sesión ausente |
| 2 | Anotar el mensaje del guardado semanal | El mensaje explica la causa y ofrece iniciar sesión |
| 3 | Cambiar la duración a 45 minutos y presionar "Guardar" | Se muestra el mismo tipo de mensaje de sesión ausente |
| 4 | Anotar el mensaje del guardado de duración | No aparece un mensaje genérico como "Error al guardar" |
| 5 | Comparar ambos mensajes y revisar Network | Ambos son consistentes y accionables; no se confirma ninguna escritura privada |

## Trazabilidad
| Historia (US) | Caso / escenario | Bug de origen | Evidencia |
| :--- | :--- | :--- | :--- |
| IPC-6 | Mensajes inconsistentes ante falta de sesión | BUG-08 | `session-2026-11-12-ipc-6.md` · escenarios 2, 3 y 7; `2026-11-12-ipc-6-guardado-semanal-fail.png`, `2026-11-12-ipc-6-guardado-duracion-fail.png` |

## Origen
*   **Priorización:** `priorizacion-roi.md` · escenario 10, score 2.3
*   **Criterio de aceptación cubierto:** `story.md` · Escenario 1 (precondición de sesión iniciada); riesgo R1 (consistencia del mensaje)
