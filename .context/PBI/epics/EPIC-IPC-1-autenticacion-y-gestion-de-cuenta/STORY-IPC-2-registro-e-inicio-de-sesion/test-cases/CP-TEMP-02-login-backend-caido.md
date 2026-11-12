# Caso de Prueba: Login con credenciales inválidas y backend caído

**ID:** CP-TEMP-02
**Historia:** IPC-2
**Estado de sincronización:** SUBIDO A JIRA
**Clave Jira:** IPC-23
**Ruta:** A
**Tipo:** Cucumber
**Prioridad:** Alta
**Automatizable:** Sí

## Precondiciones
*   El backend de Supabase Auth está inaccesible o simulado como caído.
*   El usuario está en `/login` sin sesión iniciada.

## Datos de Prueba
*   Correo sintético `qa-no-existe-2026@mailinator.com`.
*   Contraseña sintética inválida; no se usa una credencial real.

## Pasos
| # | Acción | Resultado Esperado |
| :--- | :--- | :--- |
| 1 | Ir a `/login` | Se muestra el formulario |
| 2 | Ingresar el correo y una contraseña sintéticos | Los campos aceptan los valores |
| 3 | Presionar "Iniciar Sesión" con el backend inaccesible | La UI informa un error de servicio, no "Email o contraseña inválidos." |
| 4 | Repetir con credenciales incorrectas y backend operativo | La UI muestra el mensaje genérico "Email o contraseña incorrectos" |

## Escenario Gherkin
```gherkin
Feature: Manejo de errores de autenticación

  @Automate @Priority:High @CA-6 @Resilience
  Scenario: El backend de autenticación no responde
    Given el usuario está en la página de inicio de sesión
    And el servicio de autenticación no está disponible
    When ingresa un correo y una contraseña sintéticos
    And presiona iniciar sesión
    Then la interfaz muestra un error de servicio
    And no muestra el mensaje de credenciales inválidas
```

## Trazabilidad
| Historia (US) | Caso / escenario | Bug de origen | Evidencia |
| :--- | :--- | :--- | :--- |
| IPC-2 | Login con credenciales inválidas y backend caído | — (defectos 1 y 3 de la sesión) | `analisis-escenarios.md` · candidato 2; `session-2026-11-11-ipc-2.md` · escenario 2 |

## Origen
*   **Priorización:** `priorizacion-roi.md` · escenario 2, score 2.7
*   **Criterio de aceptación cubierto:** `story.md` · CA-6 y resiliencia de autenticación
