# Análisis de Escenarios: IPC-6 — Disponibilidad semanal y duración de turno

**Historia:** IPC-6
**Fecha:** 2026-11-12
**Sesiones analizadas:** `.context/testing/exploratory/ui/session-2026-11-12-ipc-6.md`
**Implementación de la historia:** Parcial. Los casos miden contra una implementación existente, pero la persistencia autenticada y la grilla pública no pudieron verificarse porque no se obtuvo sesión válida.

## Candidatos para Regresión
| # | Tipo | Escenario | Origen | Estado en la sesión |
| :--- | :--- | :--- | :--- | :--- |
| 1 | Seguridad | Acceso directo a `/dashboard/availability` sin sesión: la configuración no debe renderizarse ni quedar operable | Sesión UI · escenario 1 | Ejecutado, FAIL (BUG-01) |
| 2 | Funcional / UX | Guardar un horario semanal válido sin sesión debe impedir la escritura, explicar la causa y ofrecer iniciar sesión | Sesión UI · escenario 3 | Ejecutado, FAIL (BUG-02) |
| 3 | Seguridad | Guardar la duración sin sesión no debe enviar una escritura privada; la UI debe distinguir sesión ausente de error del servidor | Sesión UI · escenario 7 | Ejecutado, FAIL (BUG-03) |
| 4 | Funcional | Rechazar una franja con hora de fin anterior o igual a la de inicio | Sesión UI · escenario 4 | Ejecutado, PASS |
| 5 | Funcional | Rechazar franjas solapadas el mismo día | Sesión UI · escenario 5 | Ejecutado, PASS |
| 6 | Funcional | Validar las duraciones permitidas: 0 inválido y 50 minutos contemplado en el PRD | Sesión UI · escenario 6 | Ejecutado, FAIL (BUG-04) |
| 7 | Borde | Soportar múltiples intervalos por día y 7 días activos sin errores ni degradación | Sesión UI · escenario 8 | Ejecutado, PASS (observación; BUG-07 como pregunta abierta) |
| 8 | UX / Responsive | La vista no debe generar desborde horizontal en 375x667 | Sesión UI · escenario 9 | Ejecutado, FAIL (BUG-05) |
| 9 | UX | Preservar el estado editado o advertir antes de perderlo al navegar con "Atrás" | Sesión UI · escenario 10 | Ejecutado, FAIL (BUG-06) |
| 10 | UX | Mensajes consistentes y accionables ante la falta de sesión en cualquier guardado | Sesión UI · escenarios 2, 3 y 7 | Ejecutado, FAIL (BUG-08) |
| 11 | E2E | Guardar reglas semanales con sesión válida y persistirlas en `availability_rules` | `story.md` · Escenario 1 | Nunca se probó: sin sesión válida |
| 12 | E2E | La grilla pública ofrece turnos de 60 minutos dentro de las franjas guardadas | `story.md` · Escenario 1 | Nunca se probó: sin slug público ni sesión |
| 13 | Funcional | Día sin franjas no ofrece turnos; los días con franjas siguen ofreciéndolos | `story.md` · Escenario 2 | Nunca se probó |
| 14 | Funcional | Cambiar la disponibilidad reemplaza la configuración previa y no deja reglas remanentes | `story.md` · Escenario 3 | Nunca se probó |
| 15 | Funcional | Duración 0 es rechazada con mensaje y no persiste cambios | `story.md` · Escenario 5 | No ejecutado: la opción 0 no es seleccionable (BUG-04) |
| 16 | Funcional | Duración de 50 minutos configurable, si el negocio la admite | `prd.md` · Flujo 1, paso 5; `story.md` · Notas de QA | Nunca se probó: opción ausente (BUG-04) |
| 17 | Seguridad / DB | Aislamiento por profesional: un profesional no puede modificar reglas de otro (`auth.uid() = professional_id`) | `story.md` · Notas de QA; `system-design.md` · RLS | Nunca se probó |
| 18 | API / DB | Recálculo de `GET /api/public/availability` tras guardar, descontando citas confirmadas y bloqueos | `story.md` · Notas de QA; `system-design.md` · ADR-02 | Nunca se probó |
| 19 | API | Rechazo server-side de franjas inválidas o solapadas y de duración inválida, independiente de la validación cliente | Sesión UI · "Sin cobertura" | Nunca se probó |
| 20 | Compatibilidad | Login y disponibilidad operativos en Safari, Firefox, Edge y navegadores in-app | `environments.md` · 1 "Matriz de Compatibilidad" | Nunca se probó: solo Chrome headless |

## Huecos de Cobertura
*   **Escenario 1 — persistencia y grilla pública:** la sesión no pudo guardar reglas ni verificar que la grilla ofrezca turnos de 60 minutos. Es el criterio central de la historia y hoy no tiene evidencia de funcionamiento.
*   **Escenario 2 — día sin franjas:** no se comprobó que sábado y domingo dejen de ofrecer turnos ni que los días configurados sigan ofreciéndolos.
*   **Escenario 3 — reemplazo de configuración:** no se verificó que el guardado elimine las reglas anteriores y no deje remanentes.
*   **Escenario 5 — duración 0:** la interfaz no permite seleccionar 0, por lo que el rechazo documentado no puede ejecutarse ni verificarse.
*   **Validación server-side:** solo se observó el `401` de duración; no se probó el rechazo de reglas inválidas con una sesión válida.
*   **Aislamiento por profesional y RLS:** sin sesión ni acceso a base de datos no se puede confirmar que un profesional no altere datos de otro.
*   **Duración de 50 minutos:** el PRD la usa como ejemplo y la UI no la ofrece; queda como decisión de producto pendiente.
*   **Navegadores e in-app browsers:** la matriz de compatibilidad es prioridad alta para el negocio, pero solo se ejecutó Chrome headless.

## Descartados
*   **Login con el usuario de prueba:** se ejecutó como prerrequisito y falló por `net::ERR_NAME_NOT_RESOLVED`, pero pertenece al alcance de IPC-2. Se conserva como dependencia, no como caso de regresión de IPC-6.
*   **Bloqueos de Tiempo (IPC-7):** la sección está visible en la misma página, pero sus reglas y persistencia corresponden a otra historia.
*   **Inspección puramente visual de capturas:** no aporta un criterio repetible; los hallazgos se sustentan en DOM, red y texto observados.
*   **Repeticiones de la misma validación con distintos horarios válidos:** aportan poco valor frente a los casos borde ya listados.

## Trazabilidad
| Historia (US) | Caso / escenario | Bug | Evidencia |
| :--- | :--- | :--- | :--- |
| IPC-6 | Acceso sin sesión a la configuración | BUG-01 | `.context/testing/exploratory/ui/evidence/screenshots/2026-11-12-ipc-6-acceso-sin-sesion-fail.png` |
| IPC-6 | Guardado semanal sin sesión | BUG-02 | `.context/testing/exploratory/ui/evidence/screenshots/2026-11-12-ipc-6-guardado-semanal-fail.png` |
| IPC-6 | Guardado de duración sin sesión | BUG-03 | `.context/testing/exploratory/ui/evidence/screenshots/2026-11-12-ipc-6-guardado-duracion-fail.png` |
| IPC-6 | Franja inválida | — | `.context/testing/exploratory/ui/evidence/screenshots/2026-11-12-ipc-6-franja-invalida-pass.png` |
| IPC-6 | Franjas solapadas | — | `.context/testing/exploratory/ui/evidence/screenshots/2026-11-12-ipc-6-franjas-solapadas-pass.png` |
| IPC-6 | Duraciones 0 y 50 | BUG-04 | `.context/testing/exploratory/ui/evidence/screenshots/2026-11-12-ipc-6-duracion-opciones-fail.png` |
| IPC-6 | Múltiples intervalos y 7 días activos | BUG-07 | `.context/testing/exploratory/ui/evidence/screenshots/2026-11-12-ipc-6-multiples-intervalos-pass.png` |
| IPC-6 | Responsive 375x667 | BUG-05 | `.context/testing/exploratory/ui/evidence/screenshots/2026-11-12-ipc-6-responsive-mobile-fail.png` |
| IPC-6 | Cambios sin guardar y botón "Atrás" | BUG-06 | `.context/testing/exploratory/ui/evidence/screenshots/2026-11-12-ipc-6-back-button-fail.png` |
| IPC-6 | Mensajes de autenticación inconsistentes | BUG-08 | `.context/testing/exploratory/ui/evidence/screenshots/2026-11-12-ipc-6-guardado-semanal-fail.png`, `.context/testing/exploratory/ui/evidence/screenshots/2026-11-12-ipc-6-guardado-duracion-fail.png` |
| IPC-6 | Persistencia de reglas y grilla pública (candidato 11-12) | — | `story.md` · Escenario 1 (sin evidencia de ejecución) |
| IPC-6 | Día sin franjas (candidato 13) | — | `story.md` · Escenario 2 (sin evidencia de ejecución) |
| IPC-6 | Reemplazo de configuración (candidato 14) | — | `story.md` · Escenario 3 (sin evidencia de ejecución) |
| IPC-6 | Duración 0 (candidato 15) | BUG-04 | `story.md` · Escenario 5; opción ausente en la UI |
| IPC-6 | Duración 50 (candidato 16) | BUG-04 | `prd.md` · Flujo 1, paso 5 |
| IPC-6 | Aislamiento por profesional / RLS (candidato 17) | — | `story.md` · Notas de QA; `system-design.md` · RLS |
| IPC-6 | Recálculo de disponibilidad pública (candidato 18) | — | `story.md` · Notas de QA; `system-design.md` · ADR-02 |
| IPC-6 | Validación server-side (candidato 19) | — | Sesión UI · "Sin cobertura" |
| IPC-6 | Matriz de navegadores (candidato 20) | — | `environments.md` · 1 |

## Sin cobertura
*   **Sesión válida:** sin ella no se pueden ejecutar los caminos autenticados, la persistencia ni el aislamiento por profesional. Hace falta que Supabase Auth resuelva y que el usuario de prueba pueda iniciar sesión.
*   **Grilla pública:** hace falta el slug público del profesional y una sesión válida para guardar reglas antes de consultar `GET /api/public/availability`.
*   **Base de datos:** verificar `availability_rules`, `professionals.appointment_duration_minutes`, RLS y ausencia de remanentes requiere acceso de lectura o `service_role`.
*   **Validación server-side:** requiere ejecutar las mismas reglas inválidas con una sesión válida y observar la respuesta de `POST /api/availability/rules` y `PUT /api/professionals/settings`.
*   **Duración 0 y 50:** requieren una decisión de producto sobre las opciones válidas del selector antes de poder automatizarse.
*   **Matriz de navegadores:** requiere Safari, Firefox, Edge y webviews de Instagram/WhatsApp; la sesión solo cubrió Chrome headless.
