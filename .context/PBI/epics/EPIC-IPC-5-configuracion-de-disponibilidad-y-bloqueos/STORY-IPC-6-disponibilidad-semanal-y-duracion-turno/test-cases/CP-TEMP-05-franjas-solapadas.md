# Caso de Prueba: Franjas solapadas el mismo día

**ID:** CP-TEMP-05
**Historia:** IPC-6
**Estado de sincronización:** PENDIENTE DE SUBIR A JIRA
**Ruta:** A
**Tipo:** Cucumber
**Prioridad:** Media
**Automatizable:** Sí

## Precondiciones
*   La sección `/dashboard/availability` está abierta.
*   La validación de solapamientos ocurre en el cliente antes de enviar la petición.

## Datos de Prueba
*   Lunes con dos franjas: 09:00–12:00 y 11:00–14:00.

## Pasos
| # | Acción | Resultado Esperado |
| :--- | :--- | :--- |
| 1 | Activar el lunes | Se muestran los campos de inicio y fin |
| 2 | Definir la primera franja 09:00–12:00 | La franja se acepta |
| 3 | Añadir una segunda franja 11:00–14:00 | La segunda franja se muestra en el formulario |
| 4 | Presionar "Guardar" | Se muestra un error en las franjas solapadas |
| 5 | Revisar la pestaña Network | No se envía ninguna petición de guardado |

## Escenario Gherkin
```gherkin
Feature: Validación de solapamientos

  @Automate @Priority:Medium @CA-6 @R3
  Scenario: Franjas solapadas el mismo día
    Given el usuario está en la sección de disponibilidad
    And definió el lunes de 09:00 a 12:00 y de 11:00 a 14:00
    When intenta guardar los cambios
    Then se muestra un mensaje de error en las franjas solapadas
    And no se envía ninguna petición de guardado
    And no se guarda ningún cambio en la configuración
```

## Trazabilidad
| Historia (US) | Caso / escenario | Bug de origen | Evidencia |
| :--- | :--- | :--- | :--- |
| IPC-6 | Franjas solapadas el mismo día | — | `session-2026-11-12-ipc-6.md` · escenario 5; `2026-11-12-ipc-6-franjas-solapadas-pass.png` |

## Origen
*   **Priorización:** `priorizacion-roi.md` · escenario 5, score 2.6
*   **Criterio de aceptación cubierto:** `story.md` · Escenario 6
