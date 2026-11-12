# Caso de Prueba: Envío vacío del formulario de registro

**ID:** CP-TEMP-04
**Historia:** IPC-2
**Estado de sincronización:** SUBIDO A JIRA
**Clave Jira:** IPC-25
**Ruta:** A
**Tipo:** Manual
**Prioridad:** Baja
**Automatizable:** No

## Precondiciones
*   El usuario está en `/login` y abrió el modo registro con "Regístrate gratis".
*   El backend no es necesario porque la validación ocurre antes del envío.

## Datos de Prueba
*   Nombre, correo y contraseña vacíos.

## Pasos
| # | Acción | Resultado Esperado |
| :--- | :--- | :--- |
| 1 | Ir a `/login` y presionar "Regístrate gratis" | Se muestra el formulario "Crea tu cuenta profesional" |
| 2 | Dejar vacíos nombre, correo y contraseña | Los campos permanecen vacíos |
| 3 | Presionar "Registrarse" | El navegador marca los tres campos requeridos y no se envía ninguna petición de registro |

## Trazabilidad
| Historia (US) | Caso / escenario | Bug de origen | Evidencia |
| :--- | :--- | :--- | :--- |
| IPC-2 | Envío vacío de registro | — | `analisis-escenarios.md` · candidato 4; `session-2026-11-11-ipc-2.md` · escenario 3 |

## Origen
*   **Priorización:** `priorizacion-roi.md` · escenario 4, score 2.3
*   **Criterio de aceptación cubierto:** `story.md` · CA-4
