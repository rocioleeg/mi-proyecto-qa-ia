# Priorización de Pruebas: IPC-6 — Disponibilidad semanal y duración de turno

**Historia:** IPC-6
**Fecha:** 2026-11-12
**Matriz de riesgos usada:** `.context/testing/test-plan-IPC-5-configuracion-de-disponibilidad-y-bloqueos.md` · R1–R5. La criticidad de los candidatos 1 a 10 se cita desde esa matriz; los candidatos 11 a 20 quedan sin puntuar porque nunca se ejecutaron.

## Matriz de Priorización
| # | Escenario | Frec. | Crit. | Compl. | Score | Decisión | Justificación |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| 1 | Acceso directo a `/dashboard/availability` sin sesión | 1 | 1 | 1 | 3.0 | AUTOMATIZAR | Mitiga R1 (20 · Alto): expone configuración privada. Verificable con un contexto sin cookies y una aserción de redirección/bloqueo. |
| 2 | Guardar horario semanal válido sin sesión | 1 | 1 | 1 | 3.0 | AUTOMATIZAR | Mitiga R1 (20 · Alto): flujo bloqueante y frecuente; el reproducer es simple y estable. |
| 3 | Guardar duración sin sesión | 1 | 1 | 1 | 3.0 | AUTOMATIZAR | Mitiga R1 (20 · Alto): endpoint privado y comportamiento de red observable; automatizable con intercepción de respuesta. |
| 4 | Rechazar franja con fin anterior o igual al inicio | 1 | 0.6 | 1 | 2.6 | AUTOMATIZAR | Mitiga R3 (12 · Medio): regla de negocio central; validación client-side estable y fácil de automatizar. |
| 5 | Rechazar franjas solapadas el mismo día | 1 | 0.6 | 1 | 2.6 | AUTOMATIZAR | Mitiga R3 (12 · Medio): regla de negocio central; misma estabilidad que la validación anterior. |
| 6 | Opciones de duración: 0 inválido y 50 documentado | 1 | 0.6 | 1 | 2.6 | AUTOMATIZAR | Mitiga R3 (12 · Medio) y el bug confirmado BUG-04; la aserción sobre las opciones del `select` es directa y estable. |
| 7 | Múltiples intervalos por día y 7 días activos | 0.5 | 0.5 | 0.8 | 1.8 | REGRESIÓN MANUAL | Mitiga parcialmente R2 (12 · Medio): caso borde sin límite documentado; el valor esperado sigue abierto (BUG-07). Se ejecuta manualmente hasta definir la regla. |
| 8 | Sin desborde horizontal en 375x667 | 1 | 0.7 | 0.9 | 2.6 | AUTOMATIZAR | Mitiga R4 (15 · Alto): Mobile Web 375x667 es prioridad alta según `environments.md`; la medición de `scrollWidth` es estable en Playwright. |
| 9 | Cambios sin guardar y botón "Atrás" | 0.5 | 0.5 | 0.8 | 1.8 | REGRESIÓN MANUAL | Mitiga parcialmente R2 (12 · Medio): comportamiento de navegación poco frecuente y sin regla documentada; conviene observarlo manualmente. |
| 10 | Mensajes consistentes ante falta de sesión | 1 | 0.5 | 0.8 | 2.3 | REGRESIÓN MANUAL | Se relaciona con R1 (20 · Alto) solo como consistencia del mensaje; no mitiga el control de acceso. El texto esperado no está definido, se manualiza hasta acordarlo. |
| 11 | Persistencia de reglas semanales con sesión válida | — | — | — | — | PROBAR ANTES DE DECIDIR | Nunca se ejecutó por falta de sesión. No puede descartarse: se desconoce si funciona. |
| 12 | Grilla pública ofrece turnos de 60 minutos | — | — | — | — | PROBAR ANTES DE DECIDIR | Criterio de aceptación nunca probado; requiere slug público y sesión válida. |
| 13 | Día sin franjas no ofrece turnos | — | — | — | — | PROBAR ANTES DE DECIDIR | Escenario de aceptación nunca ejecutado. |
| 14 | Cambio de disponibilidad reemplaza la configuración previa | — | — | — | — | PROBAR ANTES DE DECIDIR | Escenario de aceptación nunca ejecutado; no se verificó la ausencia de remanentes. |
| 15 | Duración 0 rechazada con mensaje | — | — | — | — | PROBAR ANTES DE DECIDIR | No ejecutado: la opción 0 no es seleccionable (BUG-04). Primero hay que resolver la decisión de producto. |
| 16 | Duración de 50 minutos configurable | — | — | — | — | PROBAR ANTES DE DECIDIR | Nunca se probó y la opción no existe; requiere decisión de producto antes de puntuar. |
| 17 | Aislamiento por profesional y RLS | — | — | — | — | PROBAR ANTES DE DECIDIR | Nunca se probó; requiere sesión válida y acceso a base de datos o API. |
| 18 | Recálculo de `GET /api/public/availability` | — | — | — | — | PROBAR ANTES DE DECIDIR | Nunca se probó; requiere datos guardados, citas y bloqueos para verificar el descuento. |
| 19 | Validación server-side de reglas y duración | — | — | — | — | PROBAR ANTES DE DECIDIR | Nunca se probó con sesión válida; solo se observó el `401` de duración. |
| 20 | Compatibilidad con Safari, Firefox, Edge e in-app browsers | — | — | — | — | PROBAR ANTES DE DECIDIR | Nunca se probó; la sesión solo cubrió Chrome headless. |

## Resumen
*   **Candidatos a automatización:** 7
*   **Regresión manual:** 3
*   **Probar antes de decidir:** 10
*   **Descartados:** 0

## Trazabilidad
| Historia (US) | Caso / escenario | Riesgo mitigado | Origen |
| :--- | :--- | :--- | :--- |
| IPC-6 | Acceso sin sesión a `/dashboard/availability` | R1 | `analisis-escenarios.md` · candidato 1 |
| IPC-6 | Guardado semanal sin sesión | R1 | `analisis-escenarios.md` · candidato 2 |
| IPC-6 | Guardado de duración sin sesión | R1 | `analisis-escenarios.md` · candidato 3 |
| IPC-6 | Franja inválida | R3 | `analisis-escenarios.md` · candidato 4 |
| IPC-6 | Franjas solapadas | R3 | `analisis-escenarios.md` · candidato 5 |
| IPC-6 | Duraciones 0 y 50 | R3 | `analisis-escenarios.md` · candidato 6 |
| IPC-6 | Múltiples intervalos y 7 días activos | R2 | `analisis-escenarios.md` · candidato 7 |
| IPC-6 | Responsive 375x667 | R4 | `analisis-escenarios.md` · candidato 8 |
| IPC-6 | Cambios sin guardar y "Atrás" | R2 | `analisis-escenarios.md` · candidato 9 |
| IPC-6 | Mensajes ante falta de sesión | R1 (parcial) | `analisis-escenarios.md` · candidato 10 |
| IPC-6 | Persistencia de reglas semanales | R2 | `analisis-escenarios.md` · candidato 11 |
| IPC-6 | Grilla pública de 60 minutos | R4 | `analisis-escenarios.md` · candidato 12 |
| IPC-6 | Día sin franjas | R4 | `analisis-escenarios.md` · candidato 13 |
| IPC-6 | Reemplazo de configuración | R2 | `analisis-escenarios.md` · candidato 14 |
| IPC-6 | Duración 0 rechazada | R3 | `analisis-escenarios.md` · candidato 15 |
| IPC-6 | Duración de 50 minutos | R3 | `analisis-escenarios.md` · candidato 16 |
| IPC-6 | Aislamiento por profesional / RLS | R1 | `analisis-escenarios.md` · candidato 17 |
| IPC-6 | Recálculo de disponibilidad pública | R4 | `analisis-escenarios.md` · candidato 18 |
| IPC-6 | Validación server-side | R3 | `analisis-escenarios.md` · candidato 19 |
| IPC-6 | Matriz de navegadores | — (sin riesgo directo) | `analisis-escenarios.md` · candidato 20 |

## Sin cobertura
*   **Candidatos 11 a 20:** no se pudieron puntuar porque nunca se ejecutaron. Las causas son la falta de sesión válida, el fallo de DNS de Supabase Auth, la ausencia de slug público, la falta de acceso a base de datos y la ausencia de la opción de duración 0/50.
*   **Criticidad:** los candidatos 1 a 10 citan R1–R5 del plan de IPC-5. Los candidatos 11 a 20 no reciben criticidad ni score sin evidencia de ejecución.
*   **Automatización de los candidatos 11 a 20:** no se recomienda decidirla hasta ejecutarlos una vez y contar con un entorno estable; el score queda pendiente.
