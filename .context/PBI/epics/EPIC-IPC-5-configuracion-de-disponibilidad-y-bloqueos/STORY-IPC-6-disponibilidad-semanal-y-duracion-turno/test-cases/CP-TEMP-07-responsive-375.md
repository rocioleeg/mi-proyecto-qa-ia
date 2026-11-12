# Caso de Prueba: Sin desborde horizontal en 375x667

**ID:** CP-TEMP-07
**Historia:** IPC-6
**Estado de sincronización:** PENDIENTE DE SUBIR A JIRA
**Ruta:** A
**Tipo:** Cucumber
**Prioridad:** Alta
**Automatizable:** Sí

## Precondiciones
*   El navegador está configurado con viewport 375x667 (iPhone SE).
*   La sección `/dashboard/availability` está abierta.

## Datos de Prueba
*   Viewport 375x667.
*   Lunes activo con franja 09:00–18:00.
*   Ningún dato real de profesionales.

## Pasos
| # | Acción | Resultado Esperado |
| :--- | :--- | :--- |
| 1 | Abrir `/dashboard/availability` a 375x667 | El contenido se adapta sin desborde horizontal |
| 2 | Medir `document.documentElement.scrollWidth` e `innerWidth` | Ambos valores son iguales a 375 |
| 3 | Activar el lunes y añadir un intervalo | Los controles siguen siendo accesibles |
| 4 | Repetir la medición con la franja visible | `scrollWidth` e `innerWidth` siguen siendo 375 |

## Escenario Gherkin
```gherkin
Feature: Uso de disponibilidad en móvil

  @Automate @Priority:High @CA-1 @R4 @BUG-05
  Scenario: La vista no desborda a 375x667
    Given el usuario abre la sección de disponibilidad en un viewport de 375x667
    When mide el ancho del documento y el ancho de la ventana
    Then ambos valores son iguales a 375
    And no aparece desplazamiento horizontal
    And los controles de la franja siguen siendo accesibles
```

## Trazabilidad
| Historia (US) | Caso / escenario | Bug de origen | Evidencia |
| :--- | :--- | :--- | :--- |
| IPC-6 | Sin desborde horizontal en 375x667 | BUG-05 | `session-2026-11-12-ipc-6.md` · escenario 9; `2026-11-12-ipc-6-responsive-mobile-fail.png` |

## Origen
*   **Priorización:** `priorizacion-roi.md` · escenario 8, score 2.6
*   **Criterio de aceptación cubierto:** `environments.md` · Matriz de Compatibilidad (Mobile Web 375x667); riesgo R4
