# Plan de Pruebas: Generación de slug público único (IPC-3)

**Epic:** IPC-1 — Autenticación y Gestión de Cuenta del Profesional
**Historia:** IPC-3
**Fecha:** 2026-11-12

> Alcance de este plan: la historia IPC-3 (generación, normalización y unicidad del slug, y resolución de la URL pública). No cubre el alta de la cuenta ni la sesión (IPC-2), la vista pública de auto-reserva (IPC-8) ni la disponibilidad (IPC-5), que se mencionan solo como dependencias.

## 1. Matriz de Riesgos del Producto

| ID | Riesgo | Probabilidad (1-5) | Impacto (1-5) | Nivel | Mitigación |
| :--- | :--- | :--- | :--- | :--- | :--- |
| R1 | Colisión o reasignación de slug: dos profesionales comparten URL pública, o una URL ya compartida pasa a apuntar a otro profesional (cuenta eliminada o slug regenerado) | 3 | 5 | **15 · Alto** — impacto máximo | Pruebas de colisión sucesiva y simultánea; verificación de la restricción `unique` en la base real; prueba de reutilización con una cuenta eliminada; comprobación de a quién resuelve el slug anterior |
| R2 | Normalización defectuosa: nombres con acentos, `ñ`, apóstrofes, emojis o alfabeto no latino generan slugs vacíos, ilegibles o duplicados | 4 | 4 | **16 · Alto** | Tabla de entradas y salidas esperadas ejecutada como prueba parametrizada; cobertura de caracteres no ASCII, longitudes límite y cadena vacía |
| R3 | La URL pública no se muestra al profesional: el slug existe pero nadie lo encuentra, y la historia no cumple su beneficio ("tener mi URL personalizada") | 5 | 3 | **15 · Alto** | E2E de registro → panel verificando que la URL se muestre y se pueda copiar; reproducción de los tickets #38 y #44; contraste con el campo `Implementación` de la historia |
| R4 | Colisión con rutas de la aplicación: un slug como "login", "dashboard" o "api" intercepta una ruta del sistema | 2 | 5 | **10 · Medio** — impacto máximo | Prueba con nombres que normalizan a rutas conocidas; verificación de que la URL pública no rompa `/login`, `/dashboard` ni `/api` |
| R5 | Unicidad no garantizada en la base real: el modelo declara `slug unique`, pero el esquema se administra a mano sin migraciones; dos altas simultáneas pueden dejar una cuenta de Auth sin perfil en `professionals` | 3 | 4 | **12 · Medio** | Verificación de la restricción en la base; prueba de dos registros con el mismo nombre; revisión de cuentas huérfanas en `professionals` |

Los riesgos R1 y R5 salen de las preguntas abiertas de la historia (política de liberación del slug, comportamiento ante registros simultáneos) y de la inspección; R2 y R4, de los defectos 3, 5, 6 y 8; R3, del defecto 12.

## 2. Niveles de Prueba (Pyramid)

*   **Unitarias:**
    *   Función de normalización completa: recorte de extremos, colapso de espacios, quita de diacríticos, minúsculas y reemplazo de espacios por guiones sin duplicarlos.
    *   Transliteración de caracteres no latinos, apóstrofes y signos de puntuación — **pendiente de definición** (preguntas abiertas de la historia).
    *   Cálculo del sufijo incremental: primer libre, sufijos consecutivos y cruce con nombres que terminan en número ("Ana Pérez 2").
    *   Lista de rutas reservadas y su tratamiento como colisión — **pendiente de definición**.
    *   Longitud máxima y regla de truncado — **pendiente de definición**.
    *   Caso de nombre que se normaliza a cadena vacía: el slug nunca debe quedar vacío.
    *   **Estado actual:** no existe ningún test automatizado en el proyecto y no hay pipeline de CI (`environments.md` · sección 3). La cobertura de este nivel es 0.

    Este es el nivel donde más rinde invertir: la normalización es lógica pura y cada pregunta abierta se puede fijar como un caso parametrizado, sin tocar producción.

*   **Integración:**
    *   Generación del slug dentro del alta de Supabase Auth y del trigger hacia `professionals`: si el slug falla, la cuenta no debe quedar creada a medias.
    *   Restricción de unicidad de `professionals.slug` en la base real (`system-design.md` la declara `unique` y `not null`).
    *   Comportamiento ante violación de unicidad: reintento con el siguiente sufijo o error con mensaje.
    *   Resolución de `/[slug]` en la aplicación y en `/api`: slug inexistente, slug con mayúsculas, slug con caracteres codificados.
    *   Políticas RLS de `professionals`: `SELECT` público necesario para renderizar la página, con verificación de qué columnas se exponen.
    *   Respuesta de la URL pública mientras el profesional todavía no configuró disponibilidad (debe resolver sin ofrecer turnos, no romper).

*   **E2E (UI):**
    *   Registro con un nombre estándar y verificación del slug asignado en el panel.
    *   Registro con acentos, `ñ` y espacios múltiples.
    *   Registro con apóstrofe, punto, emoji y alfabeto no latino.
    *   Colisión sucesiva: dos y tres registros con el mismo nombre.
    *   Registro con un nombre que normaliza a una ruta del sistema ("Login").
    *   Acceso anónimo a `https://cita-ai.vercel.app/[slug]` sin sesión, en ventana privada.
    *   Acceso a la URL con mayúsculas y con barra final.
    *   Recorrido en viewport móvil y en los navegadores in-app de Instagram y WhatsApp, que son los canales donde se comparte el enlace (`environments.md` · sección 1).

## 3. Pruebas No Funcionales

*   **Seguridad:**
    *   Enumeración de profesionales: los slugs derivan del nombre y son predecibles, y `professionals` tiene `SELECT` público. Verificar si se puede recorrer la plataforma adivinando slugs y si eso expone datos del profesional.
    *   Exposición de columnas en la página pública: la tabla `professionals` incluye `email` y su `SELECT` es público (`system-design.md`); confirmar que la vista por slug no devuelve el correo del profesional.
    *   Inyección en el parámetro del slug: cadenas codificadas (`%2F`, `%00`, `..`), comillas y sentencias SQL; verificar que la consulta esté parametrizada.
    *   XSS reflejado a través del slug en la página pública y en los mensajes de error.
    *   Interceptación de rutas: confirmar que ningún slug asignado puede sombrear `/login`, `/dashboard`, `/api` ni rutas de assets.
    *   PII en URLs: el slug está construido con el nombre real del profesional y se comparte públicamente; verificar que no arrastre otros datos personales.
*   **Performance:**
    *   La página `/[slug]` es la única cacheada y su objetivo es cargar en menos de 2,0 segundos en banda ancha estándar (`prd.md` · §5 Rendimiento).
    *   Latencia del lookup por slug con P95 inferior a 500 ms.
    *   Costo del cálculo del sufijo: medir el alta cuando existen muchos slugs similares (`ana-perez`, `-2`, `-3`, …) y confirmar que no degrada el registro.
*   **Accesibilidad:**
    *   La URL pública debe poder leerse, seleccionarse y copiarse con teclado; contraste y foco en el control de copiado.
    *   Anuncio del slug asignado al lector de pantalla tras el registro.
*   **Compatibilidad y localización:**
    *   Codificación de URLs con caracteres no ASCII al compartirlas desde Instagram y WhatsApp.
    *   Viewports 375x667, 390x844, 412x915, 1366x768 y 1920x1080.

## 4. Estado del plan

**Estado:** Ejecutable con limitaciones

| Bloqueante | Qué impide | Qué hace falta para levantarlo |
| :--- | :--- | :--- |
| No existe entorno UAT/Staging activo; solo producción (`environments.md` · sección 2) | Las pruebas de concurrencia (dos altas simultáneas), de enumeración de slugs y de carga de sufijos no se pueden correr sin crear decenas de profesionales reales y visibles | Reactivar un proyecto Supabase de staging y un dominio de preview; dueño: Diego (Desarrollo) |
| No hay teardown ni credencial de servicio para QA (`test-data-strategy.md` · sección 5) | Cada caso de normalización (acentos, `ñ`, emojis, apóstrofes) exige un profesional nuevo y deja un slug público permanente en la base real | Credencial `service_role` en un esquema aislado o un script de borrado de profesionales `@mailinator.com`; dueño: Diego (Desarrollo) |
| Sin acceso de lectura a la base de datos | No se puede confirmar que la restricción `unique` exista realmente, ni inspeccionar el slug persistido, ni verificar cuentas huérfanas tras una violación de unicidad | Acceso de lectura a Supabase o consultas de verificación; dueño: Diego (Desarrollo) |
| Decisiones de producto pendientes: transliteración de `ñ`, lista de rutas reservadas, longitud máxima, política de liberación del slug y ubicación de la URL en el panel (`story.md` · Preguntas abiertas) | Los escenarios 5, 6, 8, 9, 10 y 12 no tienen oráculo: no se sabe qué resultado es correcto | Confirmación de producto de esas cinco reglas; dueño: Mariana (Producto) |
| No hay tests automatizados ni CI (`environments.md` · sección 3) | El nivel unitario, que es el más barato y el que fija la normalización, arranca en cero | Definir la estrategia de automatización con desarrollo y montar el primer runner; dueño: Diego (Desarrollo) |
| No hay endpoint ni vista pública para consultar slugs existentes | Diseñar colisiones obliga a crear profesionales reales uno por uno, porque no se puede leer el directorio de slugs | Acceso de lectura a `professionals.slug` o un endpoint de verificación interno; dueño: Diego (Desarrollo) |

## 5. Necesidades de Entorno y Datos

> Los entornos de este proyecto son tres: Producción (`https://cita-ai.vercel.app/`), Local (`http://localhost:3000`) y UAT (`https://uat.cita.ai`), este último **inactivo**. El entorno local existe, pero apunta al mismo Supabase Cloud, así que no aísla los datos.

| Necesidad | Entorno | ¿Disponible hoy? | Referencia |
| :--- | :--- | :--- | :--- |
| URL base de la aplicación | Producción (`https://cita-ai.vercel.app/`) | Sí | `environments.md` · sección 2 |
| Entorno con datos aislados para concurrencia y enumeración | UAT | No (inactivo) | `environments.md` · sección 2 |
| Entorno local | Local (`http://localhost:3000`) | Parcial: comparte el Supabase Cloud de producción, no aísla | `environments.md` · sección 2 |
| Alta autónoma de profesionales con correos descartables | Producción | Sí, pero cada alta deja un slug público permanente | `environments.md` · sección 2; `test-data-strategy.md` · sección 2 |
| Nombres con acentos, `ñ`, apóstrofes y espacios múltiples | Producción | Sí, se generan al vuelo | `story.md` · Escenario 2 |
| Nombres con emojis, símbolos o alfabeto no latino | Producción | Sí, se generan al vuelo | `story.md` · Escenario 5 |
| Nombre que normaliza a una ruta del sistema ("Login", "Dashboard") | Producción | Sí, se genera al vuelo; sin lista de rutas reservadas la expectativa queda indefinida | `story.md` · Escenario 6 |
| Nombre de 100 caracteres para probar longitud y truncado | Producción | Parcial: el nombre se puede ingresar, la expectativa de longitud no está definida | `story.md` · Escenario 10 |
| Dos altas simultáneas con el mismo nombre | UAT | No: en producción no es aceptable y sin acceso a la base no se puede forzar por API | `story.md` · Escenario 7; `environments.md` · sección 5 |
| Verificación de la restricción `unique` y del slug persistido | Producción | No sin acceso a la base | `system-design.md` · Entidades Clave; `test-data-strategy.md` · sección 5 |
| Cuenta fija `qa-profesional-base@mailinator.com` | Producción | Parcial: la credencial está en `.env` local; no se lee ni se transcribe | `test-data-strategy.md` · sección 2 |
| Buzón de correo descartable para verificar el alta | Externo | Sí | `environments.md` · sección 2; `test-data-strategy.md` · sección 2 |
| Slugs reales de profesionales en producción | Producción | Existen, pero no se deben usar como datos de prueba ni leer como PII | `test-data-strategy.md` · sección 4 |
| Cuenta comercial de Fernando | Producción | Prohibido su uso | `environments.md` · sección 2 |

**Herramientas sugeridas:**
*   **Playwright** para E2E y para pruebas de API con `request`; el repositorio ya usa Playwright (`.playwright-mcp`). Es la herramienta natural para parametrizar la tabla de normalización contra el formulario de registro.
*   **Vitest o Jest** para el nivel unitario de la función de normalización y del generador de sufijos: es lógica pura y no necesita entorno.
*   **Postman/Newman** para verificar el lookup por slug y las respuestas de `/[slug]` inexistente, con mayúsculas y con caracteres codificados.
*   **OWASP ZAP** para enumeración de slugs, inyección y XSS sobre la URL pública.
*   **Lighthouse** para el objetivo de carga de `/[slug]` y para accesibilidad del control de copiado.
*   **k6 o Artillery** solo si se habilita un entorno aislado: sobre producción no es aceptable.

## Fuentes

| Dato / afirmación | De dónde sale |
| :--- | :--- |
| Criterios de normalización, colisión, sufijo incremental y URL pública | `story.md` · Criterios de Aceptación, escenarios 1 a 12 |
| Riesgo de normalización defectuosa y valores esperados sin respaldo | `.context/testing/inspections/inspeccion-IPC-3.md` · defectos 1 a 4 y 10 |
| Riesgo de colisión con rutas del sistema | `inspeccion-IPC-3.md` · defecto 6; `prd.md` · Flujo 1, paso 1 |
| Riesgo de URL no visible en el panel | `inspeccion-IPC-3.md` · defecto 12; `06-tickets-soporte-resumen.md` · #38 y #44; `04-notas-tecnicas.md` · "la url publica" |
| Riesgo de concurrencia y de unicidad | `inspeccion-IPC-3.md` · defecto 8; `story.md` · Escenario 7; `system-design.md` · Entidades Clave |
| Restricción `unique` y `not null` de `professionals.slug` | `system-design.md` · Entidades Clave |
| `SELECT` público de `professionals` y presencia de la columna `email` | `system-design.md` · Políticas de RLS y Entidades Clave |
| Esquema administrado a mano, sin migraciones versionadas | `04-notas-tecnicas.md` · "los datos de UAT — leer esto" |
| Existencia únicamente de producción y estado inactivo de UAT | `environments.md` · sección 2 |
| Ausencia de CI y de tests automatizados | `environments.md` · sección 3 |
| Ausencia de teardown, de seed y de acceso a la base | `test-data-strategy.md` · secciones 1 y 5 |
| Objetivos de performance (`/[slug]` < 2 s, P95 < 500 ms) | `prd.md` · §5 Rendimiento; `04-notas-tecnicas.md` · "numeros de rendimiento" |
| Viewports, navegadores e in-app browsers | `environments.md` · sección 1 |
| Necesidad de aislamiento para concurrencia, enumeración y carga | **Hipótesis** — no hay documento que defina cómo ejecutarlas sin entorno aislado |
| Umbral a partir del cual el cálculo del sufijo degrada el alta | **Hipótesis** — no hay documento que lo respalde |

## Contradicciones detectadas

*   **Existencia de la restricción de unicidad.** `system-design.md` declara `professionals.slug` como `text, unique, not null`, pero `04-notas-tecnicas.md` explica que no hay migraciones versionadas y que el esquema se modifica a mano desde el panel de Supabase. Se toma el modelo de datos como intención documentada, y por eso la verificación de la restricción en la base real queda como bloqueante del plan: es una afirmación que ningún documento puede garantizar.
*   **Dominio de la URL pública.** `03-especificacion-funcional-v0.3.md` (3.4) ejemplifica con `cita.ai/carlos-rojas`; el PRD y `environments.md` fijan `https://cita-ai.vercel.app/[slug]` como única URL válida. Se adopta la versión del PRD, que documenta que el dominio `cita.ai` no tiene las DNS configuradas.
*   **Disponibilidad inmediata de la URL.** La historia original afirmaba que la URL queda "inmediatamente disponible para reservas" al registrarse; el PRD ubica la configuración de disponibilidad después del registro y la Feature 3 calcula los turnos desde `availability_rules`. Se adoptó el PRD y la historia quedó corregida en la inspección: la prueba verifica que la URL resuelva, no que ofrezca turnos.
*   **Existencia de UAT.** La especificación (sección 10) y las notas técnicas originales lo describen activo; la evidencia operativa confirma que fue discontinuado. Se adopta producción como único entorno, según `environments.md`, y eso condiciona todo el plan.
*   **Entorno local como aislamiento.** `environments.md` lista `http://localhost:3000` como entorno de desarrollo, pero aclara que se conecta al mismo Supabase Cloud. Se trata como entorno sin aislamiento de datos, no como staging.

## Preguntas abiertas

*   ¿La `ñ` se translitera a `n` y con qué criterio se tratan apóstrofes, puntos y otros caracteres no latinos?
*   ¿Cuál es la lista de rutas reservadas que un slug no puede ocupar y qué sufijo se aplica cuando hay colisión con una de ellas?
*   ¿Cuál es la longitud máxima del slug y qué regla de truncado se aplica?
*   ¿El slug de una cuenta eliminada se libera y puede reasignarse a otro profesional?
*   ¿Dónde se muestra la URL pública en el panel del profesional y con qué formato?
*   ¿La URL pública debe ofrecer turnos reservables inmediatamente después del registro, es decir, existe una disponibilidad por defecto?
*   ¿El slug es editable después del registro?
*   ¿Qué ocurre si dos registros simultáneos generan el mismo slug base: se reintenta con el siguiente sufijo o se devuelve un error?
*   ¿La URL pública distingue mayúsculas y minúsculas al resolver el slug?
*   ¿Se habilitará un entorno aislado (staging o preview de Vercel con Supabase propio) para las pruebas de concurrencia, enumeración y carga?
*   ¿Se otorgará a QA una credencial de servicio o un acceso de lectura para verificar la restricción `unique`, el slug persistido y limpiar profesionales de prueba?
*   ¿La página pública del slug debe exponer el correo del profesional, o el `SELECT` público de `professionals` debería restringir esa columna?
