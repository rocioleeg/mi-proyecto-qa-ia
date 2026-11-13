# Reporte de Smoke Test

**Fecha:** 2026-11-12
**Entorno:** Producción
**URL:** https://cita-ai.vercel.app/
**Modo de ejecución:** Automatizado con Playwright sobre Chrome del sistema (Windows) — **sin Playwright MCP**: el MCP no arranca en este equipo porque espera un Chrome de Linux en `/opt/google/chrome/chrome`, que no existe y no se puede instalar sin `sudo`
**Estado:** FAILED

## Verificaciones

| # | Verificación | Resultado | Evidencia |
| :--- | :--- | :--- | :--- |
| 1 | Carga de la portada | PASS | `https://cita-ai.vercel.app/` responde `308` y redirige a `/login`, que responde `200` y renderiza el formulario. Capturas: `.context/testing/exploratory/smoke/evidence/smoke-2026-11-12-produccion-portada.png` y `.../evidence/smoke-2026-11-12-produccion-login.png` |
| 2 | Título de la página | PASS | Título observado: `Iniciar Sesión - CITA AI`. No hay un valor esperado documentado; es coherente con la página. |
| 3 | Formulario de registro accesible | PASS | El enlace "Regístrate gratis" alterna el formulario a "Crea tu cuenta profesional" con los campos `#name` (texto), correo y contraseña. Captura: `.../evidence/smoke-2026-11-12-produccion-registro.png` |
| 4 | Alta de profesional y generación de slug (IPC-3) | **FAIL** | Se envió el formulario con nombre `[TEST-QA] Smoke Slug` y un correo `@mailinator.com`. La aplicación calculó el slug `test-qa-smoke-slug` y lanzó la consulta de colisión `GET https://uuhstroppfwbuxudziqe.supabase.co/rest/v1/professionals?select=id&slug=eq.test-qa-smoke-slug`, que falló con `net::ERR_NAME_NOT_RESOLVED`. La UI mostró el texto crudo `TypeError: Failed to fetch`. **No se creó ningún registro.** Captura: `.../evidence/smoke-2026-11-12-produccion-registro-resultado.png` |
| 5 | Inicio de sesión con el usuario de prueba | **No ejecutado** | No existe `.env` en la raíz del repositorio: `TEST_USER_EMAIL` y `TEST_USER_PASSWORD` no están definidas. No se pidió la contraseña por chat ni se inventó un usuario. |
| 6 | Disponibilidad del backend de autenticación y datos | **FAIL** | El host configurado por la aplicación es `uuhstroppfwbuxudziqe.supabase.co` (extraído del bundle del despliegue) y **no resuelve**: `getent hosts` sin resultado, `curl` devuelve `Could not resolve host`. El DNS general funciona (`supabase.com` resuelve y responde `200`), así que la falla es del proyecto de Supabase, no de la red del entorno. |
| 7 | Ruta pública con un slug inexistente | PASS (observación) | `https://cita-ai.vercel.app/slug-que-no-existe-qa` responde `404` sin error de servidor. Captura: `.../evidence/smoke-2026-11-12-produccion-slug-inexistente.png` |
| 8 | Normalización del nombre observada | PASS (observación) | `[TEST-QA] Smoke Slug` → `test-qa-smoke-slug`: los corchetes se descartan, mayúsculas a minúsculas y espacios a guiones, sin guiones duplicados. Es el único valor de normalización medido hasta ahora y lo produjo la propia aplicación, antes de consultar la base. |

## Bloqueante

**El proyecto de Supabase del despliegue está caído o fue eliminado.** `uuhstroppfwbuxudziqe.supabase.co` no resuelve por DNS mientras el resto de internet sí, de modo que:

*   ningún profesional puede registrarse: el alta muere en la consulta de colisión del slug;
*   ningún profesional puede iniciar sesión: el mismo host atiende Supabase Auth;
*   la página pública `/[slug]` no puede resolver ningún perfil real;
*   todo el resto de la fase 6 (UI, API, DB) queda condicionado por lo mismo.

Levantarlo requiere **recrear o reactivar el proyecto de Supabase** y volver a desplegar con la URL y las claves nuevas. Dueño: Diego (Desarrollo). No lo puede resolver QA.

## Trazabilidad

| Historia (US) | Caso / escenario | Bug | Evidencia |
| :--- | :--- | :--- | :--- |
| N/A — sanidad general | Carga de la portada y título | — | `.../evidence/smoke-2026-11-12-produccion-portada.png` |
| IPC-3 | Escenario 1 — generación de slug estándar | — (bloqueado por el backend) | `.../evidence/smoke-2026-11-12-produccion-registro-resultado.png` |
| IPC-3 | Escenarios 3 y 4 — colisión y sufijo incremental | — | No ejecutados: requieren un alta exitosa y acceso a la tabla `professionals` |
| IPC-3 | Escenario 5 — nombre sin caracteres representables | — | No ejecutado: el alta no llega a la base |
| IPC-3 | Escenario 6 — colisión con rutas del sistema | — | No ejecutado: sin lista de rutas reservadas ni alta exitosa |
| IPC-3 | Escenario 9 — el profesional ve su URL en el panel | — | No ejecutado: no hay sesión (sin credenciales y sin backend) |
| IPC-3 | Escenario 11 — URL con mayúsculas | — | No ejecutado: no existe un slug real que resolver |
| IPC-2 | Escenario 5 — inicio de sesión exitoso | — | No ejecutado: falta `.env` y el backend de Auth está caído |

## Observaciones para reportar

Estas dos no se redactaron como bug en esta sesión porque el prompt de smoke no las cubre; conviene pasarlas por `.prompts/6-Testing Exploratorio/bug-report.md`:

*   **Mensaje de error técnico expuesto al usuario.** Cuando falla el alta, el formulario muestra `TypeError: Failed to fetch` en lugar de un mensaje comprensible. Cualquier usuario que se registre en este momento ve un texto de programador.
*   **El nombre se envía a la base antes de validar el correo.** El primer efecto del envío fue la consulta de colisión del slug, no la creación del usuario en Auth: el orden expone cuánto trabajo se hace antes de saber si el alta puede completarse, y con la base caída deja al usuario sin diagnóstico.

## Sin cobertura

*   **Inicio de sesión y sesión autenticada:** no existe `.env` en la raíz, así que `TEST_USER_EMAIL` y `TEST_USER_PASSWORD` no están disponibles. Se necesitan los valores reales en `.env` (nunca en el chat) para poder ejecutar el paso 4 del prompt.
*   **Alta completa y slug persistido:** el backend de Supabase no resuelve; no se creó ningún profesional y no se pudo leer el valor guardado en `professionals.slug`.
*   **Colisiones, sufijo incremental, nombres no representables y longitud máxima:** todos requieren altas exitosas contra una base viva.
*   **Página pública con un slug real:** solo se pudo verificar el `404` de un slug inexistente; sin base no hay perfil que renderizar.
*   **Visibilidad de la URL en el panel (defecto 12 de la inspección):** requiere una sesión, que no se pudo obtener.
*   **Errores de consola completos:** se capturó solo `Failed to load resource: net::ERR_NAME_NOT_RESOLVED`; no se recorrió el resto de la aplicación.
*   **Matriz de navegadores y viewports:** solo Chrome a 1366x768. No se cubrieron móviles ni navegadores in-app de Instagram y WhatsApp.
*   **Playwright MCP:** no está operativo en este equipo (falta el Chrome de Linux que espera el servidor). La ejecución se hizo con Playwright sobre el Chrome de Windows, invocando el navegador por línea de comandos.
*   **Datos reales:** no se usó la cuenta comercial de Fernando ni se creó ningún registro. La contraseña descartable usada en el intento de alta se creó para la prueba y no se transcribe en este reporte.
