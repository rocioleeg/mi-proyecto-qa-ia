# Plan de Pruebas: Registro e inicio de sesión del profesional (IPC-2)

**Epic:** IPC-1 — Autenticación y Gestión de Cuenta del Profesional
**Historia:** IPC-2
**Fecha:** 2026-11-11

> Alcance de este plan: la historia IPC-2 (registro, login, sesión y protección de rutas). No cubre la generación del slug (IPC-3) ni la recuperación de contraseña (IPC-4), que se mencionan solo como dependencias.

## 1. Matriz de Riesgos del Producto

| ID | Riesgo | Probabilidad (1-5) | Impacto (1-5) | Nivel | Mitigación |
| :--- | :--- | :--- | :--- | :--- | :--- |
| R1 | Acceso al panel sin credenciales por sesión que no se invalida al cerrar sesión | 4 | 5 | **20 · Alto** — impacto máximo | E2E de logout y acceso directo a `/dashboard`; verificación de cookies y de invalidación del refresh token; reproducción del ticket de soporte #69 |
| R2 | Enumeración de usuarios y fuerza bruta: el registro confirma si un correo existe y no hay rate limiting | 4 | 4 | **16 · Alto** | Pruebas de mensajes y tiempos de respuesta con correo existente e inexistente; prueba de solicitudes repetidas de login y registro; revisión de logs y de la política anti-enumeración |
| R3 | Registro parcial: se crea el usuario en Supabase Auth pero falla el perfil en `professionals`, el slug o el correo de bienvenida | 3 | 4 | **12 · Medio** | Pruebas de integración del alta y del trigger de `professionals`; prueba con el proveedor de correo caído o lento; verificación de cuentas huérfanas |
| R4 | Política de contraseña validada solo en el cliente y evadible por API | 3 | 4 | **12 · Medio** | Pruebas a nivel API con contraseñas débiles; verificación de que la validación y el rechazo ocurren en el servidor, no solo en el formulario |
| R5 | Correo de bienvenida no entregado por proveedor contradictorio y dominio de prueba sin verificar | 4 | 3 | **12 · Medio** | Verificación de recepción en casillas de prueba y de carpeta de spam; confirmación del proveedor vigente (Resend vs Supabase); prueba con fallo del proveedor |

## 2. Niveles de Prueba (Pyramid)

*   **Unitarias:**
    *   Validador de contraseña: mínimo 8 caracteres, al menos una mayúscula y un número.
    *   Normalización de correo electrónico (recorte de espacios y comparación sin distinguir mayúsculas), **pendiente de definición**.
    *   Cálculo de expiración y renovación de sesión: JWT 15 minutos y refresh token 7 días.
    *   Mapeo de errores de Supabase Auth a mensajes de usuario.
    *   **Estado actual:** no existe ningún test automatizado en el proyecto (`04-notas-tecnicas.md` · "lo que no esta hecho"). La cobertura de este nivel es 0 y depende del equipo de desarrollo.
*   **Integración:**
    *   Alta e inicio de sesión contra Supabase Auth (`signUp` / `signIn`).
    *   Trigger que crea el registro en `professionals` al registrarse.
    *   Emisión de cookies `httpOnly` y `Secure` y renovación del refresh token.
    *   Middleware de protección de `/dashboard` y redirección a `/login`.
    *   Despacho del correo de bienvenida y comportamiento ante fallo del proveedor.
    *   Políticas RLS de `professionals`: lectura pública y escritura solo del dueño.
*   **E2E (UI):**
    *   Registro exitoso con datos válidos.
    *   Registro con correo ya existente.
    *   Registro con contraseña que no cumple la política.
    *   Registro con nombre o correo inválidos (vacío, formato, longitudes máximas).
    *   Login exitoso.
    *   Login con credenciales incorrectas (mensaje genérico).
    *   Acceso directo a `/dashboard` sin sesión.
    *   Expiración y renovación de sesión; refresh vencido.
    *   Logout y acceso posterior al panel (pendiente de definición).
    *   Recorrido en viewports móviles y en navegadores in-app de Instagram y WhatsApp.

## 3. Pruebas No Funcionales

*   **Seguridad:**
    *   Enumeración de usuarios: comparar mensaje, código HTTP y tiempo de respuesta entre correo registrado y no registrado, tanto en registro como en login.
    *   Rate limiting y bloqueo por intentos fallidos de login (requisito sin definir; la prueba documenta el comportamiento actual).
    *   Flags de cookie: `httpOnly`, `Secure`, `SameSite`; verificar que el token no quede en `localStorage`.
    *   Inyección y XSS en el campo nombre; ausencia de contraseña en logs y en respuestas.
    *   CSRF en los formularios de registro y login.
    *   Verificación de que el logout invalida el token en el servidor.
*   **Performance:**
    *   Latencia de registro y login dentro del objetivo P95 menor a 500 ms del PRD.
    *   Registro y login usables en menos de 3 segundos en banda ancha estándar.
    *   Comportamiento del alta cuando el envío de correo es sincrónico y lento (`04-notas-tecnicas.md` · "mails").
*   **Accesibilidad:**
    *   Etiquetas de formulario, orden de foco, navegación por teclado y anuncio de mensajes de error.
*   **Compatibilidad:**
    *   Chrome, Firefox, Safari y Edge en versiones estables.
    *   Viewports 375x667, 390x844, 412x915, 1366x768 y 1920x1080.
    *   Webviews de Instagram y WhatsApp.

## 4. Estado del plan

**Estado:** Ejecutable con limitaciones

| Bloqueante | Qué impide | Qué hace falta para levantarlo |
| :--- | :--- | :--- |
| No existe entorno UAT/Staging activo; solo producción (`environments.md` · sección 2) | Pruebas destructivas, de fuerza bruta, de rate limiting y de carga masiva no se pueden correr sin afectar usuarios reales | Reactivar un proyecto Supabase UAT y un dominio de preview; dueño: Diego (Desarrollo) |
| No hay teardown ni `service_role` para QA (`test-data-strategy.md` · sección 5) | Las cuentas creadas quedan en producción y consumen el cupo freemium; no se pueden limpiar los datos de prueba | Credencial de servicio y esquema aislado, o script de limpieza; dueño: Diego (Desarrollo) |
| Sin acceso a base de datos ni a logs de Supabase/Vercel | No se puede verificar el trigger de `professionals`, la invalidación de tokens ni el estado interno de la sesión | Acceso de lectura a logs y a la base, o consultas de verificación; dueño: Diego (Desarrollo) |
| Logout e invalidación de sesión sin definir (`story.md` · Preguntas abiertas) | No hay oráculo para probar el ticket #69 ni el cierre de sesión | Decisión de producto sobre logout; dueño: Mariana (Producto) |
| Destino posterior al registro sin definir (panel vs asistente) (`story.md` · Contradicciones) | El criterio del Escenario 1 no es verificable | Confirmación de producto; dueño: Mariana (Producto) |
| No hay tests automatizados ni CI (`environments.md` · sección 3) | La pirámide de pruebas arranca desde cero; no hay base de regresión | Definir estrategia de automatización con desarrollo; dueño: Diego (Desarrollo) |

## 5. Necesidades de Entorno y Datos

| Necesidad | Entorno | ¿Disponible hoy? | Referencia |
| :--- | :--- | :--- | :--- |
| URL base de la aplicación | Producción (`https://cita-ai.vercel.app/`) | Sí | `environments.md` · sección 2 |
| Entorno aislado para pruebas de seguridad y carga | UAT / Staging | No (inactivo) | `environments.md` · sección 2 |
| Registro autónomo de profesionales con correos descartables | Producción | Sí | `environments.md` · sección 2; `test-data-strategy.md` · sección 2 |
| Cuenta fija `qa-profesional-base@mailinator.com` | Producción | Parcial: la credencial está en `.env` local, no se debe leer ni transcribir | `test-data-strategy.md` · sección 2 |
| Buzón de correo de prueba (`@mailinator.com`) | Externo | Sí | `environments.md` · sección 2; `test-data-strategy.md` · sección 2 |
| Correo ya registrado para probar duplicados | Producción | Sí, se genera registrando una cuenta de prueba | `story.md` · Escenario 2 |
| Token vencido o refresh vencido | Producción | Parcial: no se puede forzar sin acceso al token o esperar 7 días | `story.md` · Escenario 7; **Hipótesis** de método |
| Datos para probar rate limiting y fuerza bruta | Producción | No, por impacto en usuarios reales | `environments.md` · sección 5; `04-notas-tecnicas.md` · "lo que no esta hecho" |
| Acceso a logs y base de datos para verificación | Producción | No | `test-data-strategy.md` · sección 5 |
| Cuenta comercial de Fernando | Producción | Prohibido su uso | `environments.md` · sección 2 |

**Herramientas sugeridas:** Playwright para E2E y pruebas de API con `request` (el repositorio ya tiene `.playwright-mcp`), Postman/Newman para verificación de endpoints, OWASP ZAP para seguridad, Lighthouse para accesibilidad y performance. Para carga, k6 o Artillery **solo si se habilita un entorno aislado**: sobre producción no es aceptable.

## Fuentes

| Dato / afirmación | De dónde sale |
| :--- | :--- |
| Criterios de registro, login, sesión y ruta protegida | `story.md` · Criterios de Aceptación |
| Riesgo de sesión persistente tras logout | `06-tickets-soporte-resumen.md` · #69 — reporte de usuario, no verificado por QA |
| Riesgo de enumeración y rate limiting | `.context/testing/inspections/inspeccion-IPC-2.md` · Defectos 2 y 11; `03-especificacion-funcional-v0.3.md` · 3.3 (`TBD`) |
| Riesgo de registro parcial y fallo del correo | `04-notas-tecnicas.md` · "tablas", "mails"; `prd.md` · Feature 1 y Feature 6 |
| JWT 15 minutos, refresh 7 días y cookies httpOnly | `prd.md` · §5 Seguridad; `04-notas-tecnicas.md` · "auth" |
| Objetivos de latencia P95 y carga inicial | `prd.md` · §5 Rendimiento; `04-notas-tecnicas.md` · "numeros de rendimiento" |
| Existencia únicamente de producción y prohibición de la cuenta de Fernando | `environments.md` · sección 2 |
| Ausencia de UAT, teardown y acceso a base | `environments.md` · secciones 2 y 5; `test-data-strategy.md` · secciones 4 y 5 |
| Ausencia de tests y de CI | `environments.md` · sección 3; `04-notas-tecnicas.md` · "lo que no esta hecho" |
| Viewports y navegadores a cubrir | `environments.md` · sección 1 |
| Método para forzar token vencido y umbral de rate limiting | **Hipótesis** — no hay documento que lo respalde |

## Contradicciones detectadas

*   **Destino posterior al registro:** `prd.md` (Flujo 1) indica panel de control; `03-especificacion-funcional-v0.3.md` (3.1) indica asistente de configuración inicial. Se tomó la versión del PRD, pero el criterio queda marcado como pendiente en la historia y afecta la verificación del Escenario 1.
*   **Mensaje para correo ya registrado:** la historia y la especificación 3.1 admiten un mensaje explícito que revela la existencia de la cuenta; la especificación 3.3 establece el principio anti-enumeración para recuperación de contraseña. Se prueba el mensaje actual y se reporta la diferencia; la decisión queda en producto.
*   **Proveedor del correo de bienvenida:** el PRD establece Resend; `04-notas-tecnicas.md` describe el servicio transaccional de Supabase. Se toma Resend por ser la decisión vigente, pero la verificación debe confirmar cuál está activo.
*   **Existencia de UAT:** la especificación (sección 10) y las notas técnicas originales lo describen activo; la evidencia operativa confirma que fue discontinuado. Se adopta producción como único entorno, según `environments.md`.
*   **Sesión tras cerrar sesión:** el criterio 8 exige redirección a `/login` sin sesión; el ticket #69 reporta acceso al panel tras el logout. No se resuelve: puede ser defecto del sistema o criterio incompleto.

## Preguntas abiertas

*   ¿El profesional debe ir al panel o al asistente de configuración inicial al terminar el registro?
*   ¿Qué política se adopta para el correo ya registrado: mensaje explícito o mensaje neutro anti-enumeración?
*   ¿El logout debe invalidar el token en el servidor? ¿Qué explica el ticket #69?
*   ¿Se implementará rate limiting o bloqueo por intentos fallidos de login? ¿Con qué umbral y duración?
*   ¿El correo electrónico se normaliza (recorte de espacios, comparación sin distinguir mayúsculas)?
*   ¿Qué ocurre si falla el envío del correo de bienvenida? ¿El registro se considera exitoso igual?
*   ¿Está deshabilitada la confirmación de correo en Supabase, para que el alta deje sesión iniciada?
*   ¿Cuál es la ruta de la página de registro?
*   ¿Qué mensaje se muestra para cada regla de contraseña incumplida?
*   ¿Se habilitará un entorno UAT/Staging o un preview aislado para las pruebas de seguridad y carga?
*   ¿Se otorgará a QA acceso de lectura a logs y base, o una credencial de servicio para verificar el alta y limpiar datos?
