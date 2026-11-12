# Caso de Prueba: Registro con contraseña sin mayúscula

**ID:** CP-TEMP-06
**Historia:** IPC-2
**Estado de sincronización:** SUBIDO A JIRA
**Clave Jira:** IPC-27
**Ruta:** A
**Tipo:** Cucumber
**Prioridad:** Alta
**Automatizable:** Sí

## Precondiciones
*   El usuario está en el modo registro dentro de `/login`.
*   El backend está operativo para verificar también el rechazo del servidor.

## Datos de Prueba
*   Nombre sintético válido.
*   Correo sintético válido y no registrado.
*   Contraseña: `abcdefgh` (8 caracteres, sin mayúscula).
*   No se crea ninguna cuenta real.

## Pasos
| # | Acción | Resultado Esperado |
| :--- | :--- | :--- |
| 1 | Abrir el modo registro en `/login` | Se muestra el formulario |
| 2 | Ingresar nombre y correo válidos | Los campos aceptan los valores |
| 3 | Ingresar `abcdefgh` en contraseña | La UI indica que falta una mayúscula |
| 4 | Presionar "Registrarse" | El registro se rechaza; no se envía la solicitud o el servidor la rechaza |

## Escenario Gherkin
```gherkin
Feature: Política de contraseña en el registro

  @Automate @Priority:High @CA-3
  Scenario: Contraseña sin mayúscula
    Given el usuario está en el formulario de registro
    When ingresa una contraseña de 8 caracteres sin mayúscula
    And presiona registrarse
    Then el sistema rechaza el registro
    And muestra un mensaje que indica la regla incumplida
```

## Trazabilidad
| Historia (US) | Caso / escenario | Bug de origen | Evidencia |
| :--- | :--- | :--- | :--- |
| IPC-2 | Contraseña sin mayúscula | — (defecto 4 de la sesión) | `analisis-escenarios.md` · candidato 6; `session-2026-11-11-ipc-2.md` · escenario 4 |

## Origen
*   **Priorización:** `priorizacion-roi.md` · escenario 6, score 2.8
*   **Criterio de aceptación cubierto:** `story.md` · CA-3
