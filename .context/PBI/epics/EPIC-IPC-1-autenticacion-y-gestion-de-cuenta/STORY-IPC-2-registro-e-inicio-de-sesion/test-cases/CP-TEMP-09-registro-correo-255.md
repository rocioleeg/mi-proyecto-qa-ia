# Caso de Prueba: Registro con correo de 255 caracteres

**ID:** CP-TEMP-09
**Historia:** IPC-2
**Estado de sincronización:** SUBIDO A JIRA
**Clave Jira:** IPC-30
**Ruta:** A
**Tipo:** Manual
**Prioridad:** Media
**Automatizable:** No

## Precondiciones
*   El usuario está en el modo registro dentro de `/login`.
*   El backend está operativo para verificar también el rechazo del servidor.

## Datos de Prueba
*   Nombre sintético válido.
*   Correo sintético de 255 caracteres con formato válido, generado en la ejecución.
*   Contraseña válida que cumpla la política.

## Pasos
| # | Acción | Resultado Esperado |
| :--- | :--- | :--- |
| 1 | Abrir el modo registro en `/login` | Se muestra el formulario |
| 2 | Ingresar un correo de 255 caracteres | La UI impide superar los 254 caracteres o muestra el error de longitud |
| 3 | Completar nombre y contraseña válidos | Los campos aceptan los valores |
| 4 | Presionar "Registrarse" | El registro se rechaza con un error de validación de correo |

## Trazabilidad
| Historia (US) | Caso / escenario | Bug de origen | Evidencia |
| :--- | :--- | :--- | :--- |
| IPC-2 | Correo de 255 caracteres | — (defecto 5 de la sesión) | `analisis-escenarios.md` · candidato 9; `session-2026-11-11-ipc-2.md` · escenario 5 |

## Origen
*   **Priorización:** `priorizacion-roi.md` · escenario 9, score 2.5
*   **Criterio de aceptación cubierto:** `story.md` · CA-4
