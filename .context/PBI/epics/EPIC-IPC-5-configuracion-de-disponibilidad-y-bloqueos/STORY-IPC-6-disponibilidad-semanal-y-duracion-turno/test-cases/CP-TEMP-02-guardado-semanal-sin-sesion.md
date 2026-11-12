# Caso de Prueba: Guardado semanal sin sesión

**ID:** CP-TEMP-02
**Historia:** IPC-6
**Estado de sincronización:** PENDIENTE DE SUBIR A JIRA
**Ruta:** A
**Tipo:** Cucumber
**Prioridad:** Alta
**Automatizable:** Sí

## Precondiciones
*   El navegador no tiene cookies ni sesión iniciada.
*   La sección `/dashboard/availability` se abrió directamente.
*   El backend de autenticación no está disponible o no hay JWT válido.

## Datos de Prueba
*   Lunes activo con franja 09:00–18:00.
*   Duración de turno 60 minutos.
*   Ningún dato real de profesionales.

## Pasos
| # | Acción | Resultado Esperado |
| :--- | :--- | :--- |
| 1 | Abrir `/dashboard/availability` sin sesión | La vista puede renderizarse, pero el guardado privado no debe completarse |
| 2 | Activar lunes y definir 09:00–18:00 | La interfaz permite editar la franja |
| 3 | Seleccionar duración de 60 minutos | El selector conserva el valor |
| 4 | Presionar "Guardar" | No se envía ninguna escritura privada sin JWT |
| 5 | Observar el mensaje y la pestaña Network | La UI indica que falta la sesión y ofrece iniciar sesión; no confirma el guardado |

## Escenario Gherkin
```gherkin
Feature: Guardado de disponibilidad semanal

  @Automate @Priority:High @CA-1 @R1 @BUG-02
  Scenario: Guardar horario semanal sin sesión
    Given el usuario no ha iniciado sesión
    And abrió la sección de disponibilidad
    And configuró el lunes de 09:00 a 18:00
    When presiona guardar
    Then no se envía ninguna escritura privada sin JWT
    And la interfaz muestra un mensaje de sesión ausente
    And ofrece una acción para iniciar sesión
    And no confirma la persistencia de los cambios
```

## Trazabilidad
| Historia (US) | Caso / escenario | Bug de origen | Evidencia |
| :--- | :--- | :--- | :--- |
| IPC-6 | Guardado semanal válido sin sesión | BUG-02 | `session-2026-11-12-ipc-6.md` · escenario 3; `2026-11-12-ipc-6-guardado-semanal-fail.png` |

## Origen
*   **Priorización:** `priorizacion-roi.md` · escenario 2, score 3.0
*   **Criterio de aceptación cubierto:** `story.md` · Escenario 1 (precondición de sesión iniciada); riesgo R1
