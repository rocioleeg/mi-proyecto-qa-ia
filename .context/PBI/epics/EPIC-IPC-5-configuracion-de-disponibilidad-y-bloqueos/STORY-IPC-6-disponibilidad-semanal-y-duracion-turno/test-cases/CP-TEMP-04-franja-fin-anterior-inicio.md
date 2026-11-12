# Caso de Prueba: Franja con fin anterior o igual al inicio

**ID:** CP-TEMP-04
**Historia:** IPC-6
**Estado de sincronización:** PENDIENTE DE SUBIR A JIRA
**Ruta:** A
**Tipo:** Cucumber
**Prioridad:** Media
**Automatizable:** Sí

## Precondiciones
*   La sección `/dashboard/availability` está abierta.
*   La validación de franjas ocurre en el cliente antes de enviar la petición.

## Datos de Prueba
*   Lunes con inicio 18:00 y fin 09:00.

## Pasos
| # | Acción | Resultado Esperado |
| :--- | :--- | :--- |
| 1 | Activar el lunes | Se muestran los campos de inicio y fin |
| 2 | Definir inicio 18:00 y fin 09:00 | Los campos aceptan los valores |
| 3 | Presionar "Guardar" | Se muestra un error en la franja inválida |
| 4 | Revisar la pestaña Network | No se envía ninguna petición de guardado |
| 5 | Observar la configuración | No se confirma ningún cambio persistido |

## Escenario Gherkin
```gherkin
Feature: Validación de franjas horarias

  @Automate @Priority:Medium @CA-4 @R3
  Scenario: Franja con fin anterior al inicio
    Given el usuario está en la sección de disponibilidad
    And definió el lunes de 18:00 a 09:00
    When intenta guardar los cambios
    Then se muestra un mensaje de error en la franja inválida
    And no se envía ninguna petición de guardado
    And no se guarda ningún cambio en la configuración
```

## Trazabilidad
| Historia (US) | Caso / escenario | Bug de origen | Evidencia |
| :--- | :--- | :--- | :--- |
| IPC-6 | Franja inválida con fin anterior al inicio | — | `session-2026-11-12-ipc-6.md` · escenario 4; `2026-11-12-ipc-6-franja-invalida-pass.png` |

## Origen
*   **Priorización:** `priorizacion-roi.md` · escenario 4, score 2.6
*   **Criterio de aceptación cubierto:** `story.md` · Escenario 4
