# Caso de Prueba: Responsive 375x667 en login y registro

**ID:** CP-TEMP-12
**Historia:** IPC-2
**Estado de sincronización:** SUBIDO A JIRA
**Clave Jira:** IPC-33
**Ruta:** A
**Tipo:** Manual
**Prioridad:** Media
**Automatizable:** No

## Precondiciones
*   El navegador está configurado con viewport 375x667 (iPhone SE).
*   El usuario está en `/login`.

## Datos de Prueba
*   Viewport 375x667.
*   Sin credenciales; solo se valida el layout.

## Pasos
| # | Acción | Resultado Esperado |
| :--- | :--- | :--- |
| 1 | Abrir `/login` a 375x667 | El formulario se adapta sin desborde horizontal |
| 2 | Medir `document.documentElement.scrollWidth` e `innerWidth` | Ambos valores son iguales a 375 |
| 3 | Abrir el modo registro con "Regístrate gratis" | El formulario de registro se adapta sin desborde horizontal |
| 4 | Repetir la medición en registro | `scrollWidth` e `innerWidth` siguen siendo 375 |

## Trazabilidad
| Historia (US) | Caso / escenario | Bug de origen | Evidencia |
| :--- | :--- | :--- | :--- |
| IPC-2 | Responsive 375x667 | — | `analisis-escenarios.md` · candidato 12; `session-2026-11-11-ipc-2.md` · escenario 8 |

## Origen
*   **Priorización:** `priorizacion-roi.md` · escenario 12, score 2.4
*   **Criterio de aceptación cubierto:** `environments.md` · 1 (compatibilidad móvil); soporta CA-1 y CA-5
