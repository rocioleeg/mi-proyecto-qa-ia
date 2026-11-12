# Caso de Prueba: Registro con contraseña de 3 caracteres

**ID:** CP-TEMP-05
**Historia:** IPC-2
**Estado de sincronización:** SUBIDO A JIRA
**Clave Jira:** IPC-26
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
*   Contraseña: `Ab1` (3 caracteres).
*   No se crea ninguna cuenta real.

## Pasos
| # | Acción | Resultado Esperado |
| :--- | :--- | :--- |
| 1 | Abrir el modo registro en `/login` | Se muestra el formulario |
| 2 | Ingresar nombre y correo válidos | Los campos aceptan los valores |
| 3 | Ingresar `Ab1` en contraseña | La UI indica que no cumple el mínimo de 8 caracteres |
| 4 | Presionar "Registrarse" | El registro se rechaza; no se envía la solicitud o el servidor la rechaza |

## Escenario Gherkin
```gherkin
Feature: Política de contraseña en el registro

  @Automate @Priority:High @CA-3
  Scenario: Contraseña con menos de 8 caracteres
    Given el usuario está en el formulario de registro
    When ingresa una contraseña de 3 caracteres
    And presiona registrarse
    Then el sistema rechaza el registro
    And muestra un mensaje que indica la regla incumplida
```

## Trazabilidad
| Historia (US) | Caso / escenario | Bug de origen | Evidencia |
| :--- | :--- | :--- | :--- |
| IPC-2 | Contraseña de 3 caracteres | — (defecto 4 de la sesión) | `analisis-escenarios.md` · candidato 5; `session-2026-11-11-ipc-2.md` · escenario 4 |

## Origen
*   **Priorización:** `priorizacion-roi.md` · escenario 5, score 2.8
*   **Criterio de aceptación cubierto:** `story.md` · CA-3
