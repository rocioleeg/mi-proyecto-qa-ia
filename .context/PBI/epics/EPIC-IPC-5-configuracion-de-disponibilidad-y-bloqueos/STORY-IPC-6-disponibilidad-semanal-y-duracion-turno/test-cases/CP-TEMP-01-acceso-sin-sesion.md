# Caso de Prueba: Acceso directo a disponibilidad sin sesión

**ID:** CP-TEMP-01
**Historia:** IPC-6
**Estado de sincronización:** PENDIENTE DE SUBIR A JIRA
**Ruta:** A
**Tipo:** Cucumber
**Prioridad:** Alta
**Automatizable:** Sí

## Precondiciones
*   El navegador no tiene cookies ni sesión iniciada.
*   La aplicación está desplegada en Producción.

## Datos de Prueba
*   Ninguno; se accede directamente por URL.

## Pasos
| # | Acción | Resultado Esperado |
| :--- | :--- | :--- |
| 1 | Abrir una ventana privada sin cookies | No hay sesión activa |
| 2 | Ir directamente a `https://cita-ai.vercel.app/dashboard/availability` | El middleware intercepta la solicitud antes de renderizar la configuración |
| 3 | Observar la URL final y el contenido | Redirige a `/login` o bloquea la vista privada |
| 4 | Revisar la pestaña Network | No se solicitan datos privados de disponibilidad sin autenticación |

## Escenario Gherkin
```gherkin
Feature: Protección de la configuración de disponibilidad

  @Automate @Priority:High @CA-1 @R1 @BUG-01
  Scenario: Acceso directo a disponibilidad sin sesión
    Given el usuario no ha iniciado sesión
    When intenta acceder directamente a /dashboard/availability
    Then el middleware intercepta la solicitud
    And es redirigido a /login o la vista privada queda bloqueada
    And la configuración de disponibilidad no se renderiza
    And no se solicitan datos privados sin autenticación
```

## Trazabilidad
| Historia (US) | Caso / escenario | Bug de origen | Evidencia |
| :--- | :--- | :--- | :--- |
| IPC-6 | Acceso directo a `/dashboard/availability` sin sesión | BUG-01 | `session-2026-11-12-ipc-6.md` · escenario 1; `2026-11-12-ipc-6-acceso-sin-sesion-fail.png` |

## Origen
*   **Priorización:** `priorizacion-roi.md` · escenario 1, score 3.0
*   **Criterio de aceptación cubierto:** `story.md` · Escenario 1 (precondición de sesión iniciada); riesgo R1
