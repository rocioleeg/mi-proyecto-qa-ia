# Caso de Prueba: Duraciones 0 y 50 minutos

**ID:** CP-TEMP-06
**Historia:** IPC-6
**Estado de sincronización:** PENDIENTE DE SUBIR A JIRA
**Ruta:** A
**Tipo:** Cucumber
**Prioridad:** Media
**Automatizable:** Sí

## Precondiciones
*   La sección `/dashboard/availability` está abierta.
*   La duración se elige en un `select` cerrado.
*   La decisión de producto sobre la duración de 50 minutos sigue abierta.

## Datos de Prueba
*   Opciones observadas: 15, 30, 45, 60, 90 y 120 minutos.
*   Valor inicial observado: 60 minutos.
*   Valores a inspeccionar: 0 y 50 minutos.

## Pasos
| # | Acción | Resultado Esperado |
| :--- | :--- | :--- |
| 1 | Abrir `/dashboard/availability` | La sección muestra el selector de duración |
| 2 | Inspeccionar las opciones del selector | Están disponibles 15, 30, 45, 60, 90 y 120 minutos |
| 3 | Verificar el valor inicial | El selector arranca en 60 minutos |
| 4 | Verificar la opción 0 | No se ofrece o, si se ofrece, se rechaza con un mensaje al guardar |
| 5 | Verificar la opción 50 | Se registra su ausencia; queda pendiente la decisión de producto |
| 6 | Si 0 es seleccionable, intentar guardar | Se muestra un error y no se persiste ningún cambio |

## Escenario Gherkin
```gherkin
Feature: Opciones de duración del turno

  @Automate @Priority:Medium @CA-5 @R3 @BUG-04
  Scenario: Duración 0 no se persiste
    Given el usuario está en la sección de disponibilidad
    When inspecciona las opciones de duración del turno
    Then el selector ofrece 15, 30, 45, 60, 90 y 120 minutos
    And la opción 0 no está disponible o es rechazada al guardar
    And no se guarda ningún cambio con duración 0
```

**Nota:** la duración de 50 minutos no se usa como aserción PASS/FAIL hasta que producto confirme si debe existir. Su ausencia queda registrada como BUG-04 y como pregunta abierta.

## Trazabilidad
| Historia (US) | Caso / escenario | Bug de origen | Evidencia |
| :--- | :--- | :--- | :--- |
| IPC-6 | Opciones de duración: 0 inválido y 50 pendiente | BUG-04 | `session-2026-11-12-ipc-6.md` · escenario 6; `2026-11-12-ipc-6-duracion-opciones-fail.png` |

## Origen
*   **Priorización:** `priorizacion-roi.md` · escenario 6, score 2.6
*   **Criterio de aceptación cubierto:** `story.md` · Escenario 5 (duración 0); `prd.md` · Flujo 1 y `story.md` · Notas de QA (duración 50)
