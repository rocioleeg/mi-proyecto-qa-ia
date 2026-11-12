# Caso de Prueba: Envío vacío del formulario de login

**ID:** CP-TEMP-03
**Historia:** IPC-2
**Estado de sincronización:** SUBIDO A JIRA
**Clave Jira:** IPC-24
**Ruta:** A
**Tipo:** Manual
**Prioridad:** Baja
**Automatizable:** No

## Precondiciones
*   El usuario está en `/login` sin sesión iniciada.
*   El backend no es necesario porque la validación ocurre antes del envío.

## Datos de Prueba
*   Correo y contraseña vacíos.

## Pasos
| # | Acción | Resultado Esperado |
| :--- | :--- | :--- |
| 1 | Ir a `/login` | Se muestra el formulario |
| 2 | Dejar vacíos correo y contraseña | Los campos permanecen vacíos |
| 3 | Presionar "Iniciar Sesión" | El navegador marca los campos requeridos y no se envía ninguna petición de autenticación |

## Trazabilidad
| Historia (US) | Caso / escenario | Bug de origen | Evidencia |
| :--- | :--- | :--- | :--- |
| IPC-2 | Envío vacío de login | — | `analisis-escenarios.md` · candidato 3; `session-2026-11-11-ipc-2.md` · escenario 3 |

## Origen
*   **Priorización:** `priorizacion-roi.md` · escenario 3, score 2.3
*   **Criterio de aceptación cubierto:** `story.md` · CA-6 y validación básica de entradas
