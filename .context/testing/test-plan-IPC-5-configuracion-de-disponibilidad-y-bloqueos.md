# Plan de Pruebas: Configuración de Disponibilidad y Bloqueos de Agenda

**Epic:** IPC-5
**Fecha:** 2026-11-12
**Alcance de esta versión:** IPC-6 — Disponibilidad semanal y duración de turno. IPC-7 (bloqueos de tiempo) queda fuera de este plan.

## 1. Matriz de Riesgos del Producto
| ID | Riesgo | Probabilidad (1-5) | Impacto (1-5) | Nivel | Mitigación |
| :--- | :--- | :--- | :--- | :--- | :--- |
| R1 | Acceso no autorizado a la configuración de disponibilidad o a sus endpoints privados | 5 | 4 | **20 · Alto** | Probar `/dashboard/availability` sin sesión y verificar redirección o bloqueo; probar `POST /api/availability/rules` y `PUT /api/professionals/settings` sin JWT; revisar RLS por profesional. |
| R2 | Configuración de disponibilidad incorrecta, incompleta o con reglas remanentes tras guardar | 3 | 4 | **12 · Medio** | Guardar, modificar y volver a guardar reglas; verificar reemplazo atómico en `availability_rules`, ausencia de remanentes y consistencia de la duración. |
| R3 | Validaciones incompletas permiten franjas inválidas, solapadas o duraciones fuera de rango | 3 | 4 | **12 · Medio** | Probar franja con fin anterior al inicio, solapamientos y duración 0; repetir con sesión válida para verificar validación server-side, no solo client-side. |
| R4 | La disponibilidad configurada no se refleja en la grilla pública o no se puede operar en móvil/in-app | 3 | 5 | **15 · Alto** | Guardar reglas y consultar `GET /api/public/availability`; verificar turnos de 60 minutos, días sin franjas y descuento de citas/bloqueos; probar 375x667 y navegadores in-app. |
| R5 | Autenticación inoperante por dependencia externa o DNS inestable | 4 | 4 | **16 · Alto** | Smoke test de login antes de cada sesión; verificar que el error de red se muestre como error de servicio y no como credenciales inválidas; coordinar DNS/uptime de Supabase Auth. |

## 2. Niveles de Prueba (Pyramid)
*   **Unitarias:** generación de intervalos según `appointment_duration_minutes`; validación de rango inicio/fin; detección de solapamientos; reemplazo atómico de reglas. No se pudo verificar qué pruebas unitarias existen hoy porque el repositorio de QA no contiene el código de la aplicación; queda como pregunta abierta.
*   **Integración:** `POST /api/availability/rules`, `PUT /api/professionals/settings`, `GET /api/public/availability`, Supabase Auth, persistencia en PostgreSQL y políticas RLS de `availability_rules`, `professionals` y `time_blocks`.
*   **E2E (UI):** login con profesional de prueba; configuración de lunes a viernes 09:00-18:00; guardado de duración; validaciones de franja inválida y solapada; modificación de reglas; verificación de la grilla pública; comportamiento responsive en 375x667.
*   **No funcionales:** seguridad de rutas privadas y RLS; performance de `GET /api/public/availability` con P95 menor a 500 ms; compatibilidad con Safari, Firefox, Edge y webviews de Instagram/WhatsApp.

## 3. Pruebas No Funcionales
*   **Seguridad:** acceso directo a `/dashboard/availability` sin sesión; llamadas a endpoints privados sin JWT; intento de leer o modificar reglas de otro profesional (IDOR/RLS); exposición de mensajes internos en errores `401`.
*   **Performance:** cálculo de disponibilidad pública dentro del objetivo P95 < 500 ms documentado, con reglas, citas confirmadas y bloqueos descontados.
*   **Compatibilidad:** 375x667 y 390x844 en móvil; 768x1024, 1366x768 y 1920x1080 en desktop/tablet; Safari, Firefox, Edge y navegadores in-app.
*   **Accesibilidad básica:** navegación por teclado y foco en el formulario de disponibilidad; no hay criterio formal documentado, se registra como pregunta abierta.

## 4. Estado del plan

**Estado:** Ejecutable con limitaciones

| Bloqueante | Qué impide | Qué hace falta para levantarlo |
| :--- | :--- | :--- |
| Login de prueba bloqueado por DNS de Supabase Auth (`net::ERR_NAME_NOT_RESOLVED`) | Camino feliz autenticado, persistencia, grilla pública y verificación de RLS | Infraestructura/Backend debe restablecer la resolución del dominio de Supabase Auth y confirmar una sesión válida con el usuario de prueba de `.env`. |
| No existe entorno aislado de pruebas: solo Producción | Pruebas destructivas, carga, concurrencia y creación de datos sin riesgo para usuarios reales | DevOps/Producto debe habilitar un entorno UAT o staging con Supabase separado. |
| Sin acceso a base de datos ni `service_role` para QA | Verificar `availability_rules`, remanentes, `appointment_duration_minutes` y políticas RLS | Backend/DB admin debe entregar acceso de lectura controlado o un mecanismo de verificación seguro. |
| Sin slug público disponible para el profesional de prueba | Verificar la grilla pública y el recálculo de `GET /api/public/availability` | Obtener una sesión válida y el slug del profesional de prueba, o crear uno sintético autorizado. |
| No hay código de la aplicación en este repositorio | Confirmar qué pruebas unitarias existen y cuáles faltan | Desarrollo debe indicar la ubicación del repositorio de la aplicación y el estado de sus tests. |

## 5. Necesidades de Entorno y Datos
| Necesidad | Entorno | ¿Disponible hoy? | Referencia |
| :--- | :--- | :--- | :--- |
| Profesional de prueba `qa-profesional-base@mailinator.com` | Producción | Parcial: la cuenta está definida, pero el login falla por DNS | `test-data-strategy.md` · 2 "Gestión de Usuarios de Prueba" |
| Contraseña del usuario de prueba | Producción | Parcial: vive en `.env` como `TEST_USER_PASSWORD`; no se transcribe | `.env.example`; `test-data-strategy.md` · 2 |
| Correo descartable para pruebas | Producción | Sí: Mailinator | `test-data-strategy.md` · 2 y 4 |
| Entorno UAT/staging aislado | No existe | No: UAT discontinuado; solo Producción activa | `environments.md` · 2 "Mapa de Entornos" |
| Slug público del profesional de prueba | Producción | No disponible en la sesión exploratoria | `session-2026-11-12-ipc-6.md` · "Sin cobertura" |
| Acceso a base de datos o `service_role` | Producción | No para QA | `test-data-strategy.md` · 5 "Limpieza y Reset" |
| Navegadores e in-app browsers | Producción | No verificado: solo Chrome headless | `environments.md` · 1 "Matriz de Compatibilidad" |
| Datos de citas y bloqueos para probar el descuento de slots | Producción | Parcial: no se deben crear datos sin autorización | `test-data-strategy.md` · 1 y 5 |

## Fuentes
| Dato / afirmación | De dónde sale |
| :--- | :--- |
| R1 — acceso no autorizado a configuración y endpoints privados | `story.md` · Escenario 1; `system-design.md` · "Seguridad" y "Políticas de Row Level Security (RLS)"; `bug-2026-11-12-ipc-6-acceso-sin-sesion.md`; `bug-2026-11-12-ipc-6-guardado-duracion-sin-sesion.md` |
| R2 — configuración incorrecta o con reglas remanentes | `story.md` · Escenario 3; `prd.md` · Feature 2, "Criterios de éxito"; `system-design.md` · `POST /api/availability/rules` |
| R3 — validaciones incompletas | `story.md` · Escenarios 4, 5 y 6; `03-especificacion-funcional-v0.3.md` · 4.1 y 4.2 |
| R4 — grilla pública desactualizada o uso móvil defectuoso | `story.md` · Escenario 1; `prd.md` · Feature 3; `system-design.md` · ADR-02; `environments.md` · 1 |
| R5 — autenticación inoperante por DNS | `session-2026-11-12-ipc-6.md` · escenario 2; `smoke-2026-11-11-produccion.md` · verificación 3; `session-2026-11-11-ipc-2.md` · defecto 1 |
| P95 < 500 ms para endpoints de disponibilidad | `prd.md` · Requisitos No Funcionales, "Latencia de Endpoints" |
| Solo Producción está activo; UAT discontinuado | `environments.md` · 2 "Mapa de Entornos" |
| Profesional de prueba y contraseña en `.env` | `test-data-strategy.md` · 2 "Gestión de Usuarios de Prueba"; `.env.example` |
| Sin `service_role` ni teardown automático | `test-data-strategy.md` · 5 "Limpieza y Reset" |
| Existencia de pruebas unitarias en la aplicación | **Hipótesis** — el repositorio de QA no contiene el código ni la configuración de tests |
| Criterio de accesibilidad formal | **Hipótesis** — ningún documento define criterios de accesibilidad |

## Contradicciones detectadas
*   **Entorno de pruebas:** la especificación funcional y las notas técnicas originales describen un UAT activo, pero `environments.md` y la nota operativa confirman que fue discontinuado y que solo existe Producción. Este plan usa **Producción** como único entorno real y trata la falta de aislamiento como bloqueante.
*   **Sesión en la configuración:** `story.md` asume un profesional con sesión iniciada, pero la UI renderiza la configuración sin sesión. Se prioriza el supuesto de la historia y el diseño de endpoints privados, y la observación se registra como BUG-01.
*   **Duración de 50 minutos:** `prd.md` la usa como ejemplo de configuración, pero la UI solo ofrece 15/30/45/60/90/120 y no permite 0. Se registra como BUG-04 y como decisión de producto pendiente.
*   **Duración inválida 0:** el Escenario 5 exige probar 0 minutos, pero la UI no permite seleccionarlo; el caso no es ejecutable hasta que se defina si 0 debe ser una opción rechazada o una opción ausente.

## Preguntas abiertas
*   ¿Qué debe ocurrir exactamente al abrir `/dashboard/availability` sin sesión: redirección a `/login`, mensaje de error o bloqueo de la vista?
*   ¿Cuál es el mensaje único para sesión ausente o expirada en los guardados?
*   ¿La duración de 50 minutos es un requisito de producto o solo un ejemplo del PRD?
*   ¿La duración 0 debe estar disponible para ser rechazada o simplemente no debe ofrecerse?
*   ¿Cuántos intervalos por día se permiten y qué mensaje debe mostrarse al superar el máximo?
*   ¿Debe advertirse al usuario antes de perder cambios sin guardar al navegar?
*   ¿En qué huso horario se interpretan las franjas al calcular la grilla pública?
*   ¿Qué ocurre con los turnos ya confirmados si el profesional reduce su disponibilidad?
*   ¿Se habilitará un entorno UAT/staging con Supabase separado para QA?
*   ¿Quién es el dueño del DNS de Supabase Auth y cómo se monitorea su disponibilidad?
*   ¿Existe una suite de pruebas unitarias en la aplicación y dónde se ejecuta?
*   ¿QA tendrá acceso de lectura a la base o una API administrativa para verificar RLS y persistencia?
*   ¿Se definirán criterios formales de accesibilidad para el panel?
