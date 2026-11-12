# Caso de Prueba: Triple envío rápido del login

**ID:** CP-TEMP-10
**Historia:** IPC-2
**Estado de sincronización:** SUBIDO A JIRA
**Clave Jira:** IPC-31
**Ruta:** A
**Tipo:** Manual
**Prioridad:** Alta
**Automatizable:** No

## Precondiciones
*   El usuario está en `/login` sin sesión iniciada.
*   El backend de Auth está inaccesible o simulado como caído para contar las solicitudes.

## Datos de Prueba
*   Correo sintético `qa-no-existe-2026@mailinator.com`.
*   Contraseña sintética inválida; no se usa una credencial real.

## Pasos
| # | Acción | Resultado Esperado |
| :--- | :--- | :--- |
| 1 | Ir a `/login` | Se muestra el formulario |
| 2 | Ingresar correo y contraseña sintéticos | Los campos aceptan los valores |
| 3 | Presionar "Iniciar Sesión" tres veces con 100 ms de intervalo | Se envía una sola solicitud de autenticación o el botón permanece bloqueado hasta terminar |
| 4 | Revisar la pestaña Network | No hay tres solicitudes duplicadas ni reenvíos después del fallo |

## Trazabilidad
| Historia (US) | Caso / escenario | Bug de origen | Evidencia |
| :--- | :--- | :--- | :--- |
| IPC-2 | Triple envío rápido del login | — (defecto 6 de la sesión) | `analisis-escenarios.md` · candidato 10; `session-2026-11-11-ipc-2.md` · escenario 6 |

## Origen
*   **Priorización:** `priorizacion-roi.md` · escenario 10, score 2.2
*   **Criterio de aceptación cubierto:** `story.md` · CA-5 y CA-6 (sin envíos duplicados)
