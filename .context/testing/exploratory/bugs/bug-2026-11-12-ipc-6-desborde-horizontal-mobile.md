# Bug: [Disponibilidad] Desborde horizontal en viewport móvil de 375x667

**ID:** BUG-05
**Estado de sincronización:** PENDIENTE DE SUBIR A JIRA
**Fecha:** 2026-11-12
**Entorno:** Producción
**Severidad:** Media
**Prioridad:** Media

## Descripción
En el viewport móvil de 375x667, la página de disponibilidad genera un desborde horizontal de 14 px. El `scrollWidth` del documento es 389 px frente a un `innerWidth` de 375 px. El botón "Registrarse Gratis" del header llega hasta 389 px y los `input[type=time]` presentan un desborde interno menor.

## Pasos para Reproducir
1.  Abrir `https://cita-ai.vercel.app/dashboard/availability` en Chrome.
2.  Configurar el viewport en 375x667 (iPhone SE).
3.  Ejecutar `document.documentElement.scrollWidth` e `window.innerWidth`.
4.  Observar el desplazamiento horizontal y el desborde del header.

## Resultados
*   **Esperado:** la aplicación debe adaptarse sin desborde horizontal a las resoluciones móviles prioritarias, incluida 375x667 — según `environments.md` · 1 "Matriz de Compatibilidad", donde Mobile Web 375x667 es prioridad alta.
*   **Real:** `innerWidth = 375`, `scrollWidth = 389` y `bodyScrollWidth = 389`; desborde horizontal de 14 px. Los `input[type=time]` tienen `scrollWidth = 133` y `clientWidth = 130`.

## Evidencia
*   `.context/testing/exploratory/ui/evidence/screenshots/2026-11-12-ipc-6-responsive-mobile-fail.png`
*   `.context/testing/exploratory/ui/evidence/ipc-6-notes.md` · sección "Responsive"
*   `.context/testing/exploratory/ui/session-2026-11-12-ipc-6.md`

## Trazabilidad
| Historia (US) | Caso / escenario | Bug | Evidencia |
| :--- | :--- | :--- | :--- |
| IPC-6 | Responsive 375x667 — no cubierto por los escenarios Gherkin | BUG-05 | `.context/testing/exploratory/ui/evidence/screenshots/2026-11-12-ipc-6-responsive-mobile-fail.png` |

## Referencia Cruzada
*   **Sesión de origen:** `.context/testing/exploratory/ui/session-2026-11-12-ipc-6.md`
*   **Datos de prueba usados:** ninguno; solo se abrió la página en 375x667.
*   **IDs de traza:** requestId / correlationId no disponibles.
