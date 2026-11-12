# Caso de Prueba: Acceso directo a /dashboard sin sesión

**ID:** CP-TEMP-11
**Historia:** IPC-2
**Estado de sincronización:** SUBIDO A JIRA
**Clave Jira:** IPC-32
**Ruta:** A
**Tipo:** Cucumber
**Prioridad:** Alta
**Automatizable:** Sí

## Precondiciones
*   El usuario no tiene sesión iniciada ni cookies de autenticación.
*   El middleware de rutas protegidas está desplegado.

## Datos de Prueba
*   Ninguno; se accede directamente por URL.

## Pasos
| # | Acción | Resultado Esperado |
| :--- | :--- | :--- |
| 1 | Abrir una ventana sin cookies de sesión | No hay sesión activa |
| 2 | Ir directamente a `/dashboard` | El middleware intercepta la solicitud |
| 3 | Observar la URL final | Redirige a `/login` y no se renderiza el panel privado |

## Escenario Gherkin
```gherkin
Feature: Protección de rutas privadas

  @Automate @Priority:High @CA-8 @Security
  Scenario: Acceso a /dashboard sin sesión
    Given el usuario no ha iniciado sesión
    When intenta acceder directamente a /dashboard
    Then el middleware intercepta la solicitud
    And es redirigido a /login
    And el panel privado no se renderiza
```

## Trazabilidad
| Historia (US) | Caso / escenario | Bug de origen | Evidencia |
| :--- | :--- | :--- | :--- |
| IPC-2 | Acceso directo a `/dashboard` sin sesión | — (defecto 2 de la sesión; ticket #69) | `analisis-escenarios.md` · candidato 11; `session-2026-11-11-ipc-2.md` · escenario 7 |

## Origen
*   **Priorización:** `priorizacion-roi.md` · escenario 11, score 3.0
*   **Criterio de aceptación cubierto:** `story.md` · CA-8
