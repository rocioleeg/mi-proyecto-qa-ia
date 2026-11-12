# Caso de Prueba: Guardado de duración sin sesión

**ID:** CP-TEMP-03
**Historia:** IPC-6
**Estado de sincronización:** PENDIENTE DE SUBIR A JIRA
**Ruta:** A
**Tipo:** Cucumber
**Prioridad:** Alta
**Automatizable:** Sí

## Precondiciones
*   El navegador no tiene cookies ni sesión iniciada.
*   La sección `/dashboard/availability` se abrió directamente.
*   El endpoint `PUT /api/professionals/settings` requiere JWT.

## Datos de Prueba
*   Duración de turno 45 minutos, valor sintético observado en la UI.
*   Ningún dato real de profesionales.

## Pasos
| # | Acción | Resultado Esperado |
| :--- | :--- | :--- |
| 1 | Abrir `/dashboard/availability` sin sesión | La vista puede renderizarse, pero el guardado privado no debe completarse |
| 2 | Cambiar la duración del turno a 45 minutos | El selector conserva el valor |
| 3 | Presionar "Guardar" | No se envía `PUT /api/professionals/settings` sin JWT |
| 4 | Revisar la pestaña Network y la respuesta | Si hay una petición, el `401` se presenta como sesión ausente, no como "Error al guardar" |
| 5 | Observar el estado de la configuración | La UI no confirma la persistencia ni deja la escritura como exitosa |

## Escenario Gherkin
```gherkin
Feature: Guardado de la duración del turno

  @Automate @Priority:High @CA-1 @R1 @BUG-03
  Scenario: Guardar duración sin sesión
    Given el usuario no ha iniciado sesión
    And abrió la sección de disponibilidad
    When cambia la duración del turno a 45 minutos
    And presiona guardar
    Then no se envía una escritura privada sin JWT
    And la interfaz distingue la sesión ausente de un error del servidor
    And no confirma la persistencia del cambio
```

## Trazabilidad
| Historia (US) | Caso / escenario | Bug de origen | Evidencia |
| :--- | :--- | :--- | :--- |
| IPC-6 | Guardado de duración sin sesión | BUG-03 | `session-2026-11-12-ipc-6.md` · escenario 7; `2026-11-12-ipc-6-guardado-duracion-fail.png` |

## Origen
*   **Priorización:** `priorizacion-roi.md` · escenario 3, score 3.0
*   **Criterio de aceptación cubierto:** `story.md` · Escenario 1 (precondición de sesión iniciada); riesgo R1
