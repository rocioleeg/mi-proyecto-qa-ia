# Estrategia de Infraestructura y Entornos: Cita.ai

**Versión:** 1.0 (Auditoría de Entornos para QA)  
**Estado:** Vigente  
**Tipo de proyecto:** Brownfield  
**Rol:** DevOps & Infrastructure Lead / QA Infrastructure  

---

## 1. Tipo de Aplicación y Alcance

*   **Plataforma:** Aplicación Web Responsive Fullstack (Next.js con App Router alojada en infraestructura Edge y Serverless de Vercel). No existen aplicaciones móviles nativas (`03-especificacion-funcional-v0.3.md` · sección 1; `04-notas-tecnicas.md` · "stack").
*   **Matriz de Compatibilidad:**
    *   *Navegadores Soportados:* Google Chrome, Apple Safari (iOS y macOS), Mozilla Firefox y Microsoft Edge en sus versiones estables recientes.
    *   *Navegadores In-App (Críticos para el negocio):* Webviews integradas de Instagram In-App Browser y WhatsApp Web/Mobile, dado que los clientes finales acceden habitualmente desde el enlace publicado en biografías o chats (`01-minuta-kickoff.md` · "Cómo llega el cliente"; `02-notas-entrevistas.md` · Entrevistas 1, 2 y 4).
    *   *Resoluciones Clave de Validación:*
        *   **Mobile Web (Prioridad Alta):** 375x667 (iPhone SE), 390x844 (iPhone 12/13/14), 412x915 (Android estándar / Pixel / Samsung Galaxy).
        *   **Desktop / Tablet (Prioridad Media):** 1366x768 (Laptop estándar), 1920x1080 (FHD Desktop), 768x1024 (iPad vertical para panel de profesional).

---

## 2. Mapa de Entornos (Matriz de URLs)

> **Hallazgo Crítico de QA:** Aunque la documentación preliminar (`03-especificacion-funcional-v0.3.md` y `04-notas-tecnicas.md`) mencionaba un ambiente UAT en `uat.cita.ai`, la evidencia operativa más reciente confirma que **el ambiente UAT fue discontinuado**. Actualmente **existe un único entorno activo y es el de Producción**, donde conviven usuarios reales y pruebas (`documentacion para QA/nota-ambientes-y-accesos.md` · "lo del ambiente de UAT").

| Componente | Local (Desarrollo) | UAT / Homologación *(Estado)* | Producción (Entorno Único Activo) |
| :--- | :--- | :--- | :--- |
| **Frontend Web** | `http://localhost:3000` | `https://uat.cita.ai` *(Abandonado / Inactivo)* | `https://cita-ai.vercel.app/` |
| **Páginas Públicas (Slug)** | `http://localhost:3000/[slug]` | `https://uat.cita.ai/[slug]` *(Inactivo)* | `https://cita-ai.vercel.app/[slug]` |
| **Backend API** | `http://localhost:3000/api` | `https://uat.cita.ai/api` *(Inactivo)* | `https://cita-ai.vercel.app/api` |
| **Base de Datos (PostgreSQL)** | Conexión directa a Supabase Cloud | Proyecto Supabase UAT *(Desconectado/Desactualizado)* | Supabase Cloud (PostgreSQL administrado con RLS) |
| **Servicio de Autenticación** | Supabase Auth (Local/Cloud) | Supabase Auth UAT *(Inactivo)* | Supabase Auth (Producción) |
| **Servicio de Emails** | Resend API (Sandbox / Dev) | No configurado | Resend API (Dominio de prueba) |

### Detalles de Acceso y Credenciales

*   **Acceso a la Aplicación:** Acceso público vía HTTPS sin restricciones de VPN ni listas blancas de IP (`documentacion para QA/nota-ambientes-y-accesos.md`).
*   **Aprovisionamiento de Cuentas para QA:** El tester debe registrarse de manera autónoma en `https://cita-ai.vercel.app/login` como profesional independiente para validar flujos de punta a punta (`documentacion para QA/nota-ambientes-y-accesos.md` · "como entrar"; `documentacion para QA/hilo-mail-alcance-qa.md` · Mail de Diego).
*   **Regla de Seguridad Mandataria:** **Queda estrictamente prohibido utilizar o alterar la cuenta de Fernando** (`qa@cita.ai` / cuenta comercial), debido a que contiene turnos reales de clientes y profesionales en producción (`documentacion para QA/nota-ambientes-y-accesos.md` · "un par de cosas para que no pierdas tiempo").
*   **Gestión de Casillas de Correo para Pruebas:** Es indispensable emplear servicios de casillas de correo temporales o descartables (ej. Mailinator, Guerrilla Mail) para validar la recepción de confirmaciones de reserva y tokens de recuperación sin afectar bandejas personales ni emitir SPAM masivo (`documentacion para QA/nota-ambientes-y-accesos.md`).
*   **Ubicación de Secretos y Variables:** Las variables de entorno de producción (`NEXT_PUBLIC_SUPABASE_URL`, `SUPABASE_SERVICE_ROLE_KEY`, `RESEND_API_KEY`) residen exclusivamente en el panel de configuración de Vercel. En local, residen en el archivo ignorado `.env` en la raíz del proyecto (`04-notas-tecnicas.md`).

---

## 3. Pipeline de CI/CD (Integración Continua y Despliegue)

*   **Existencia de Pipeline de CI:** **No existe pipeline de Integración Continua ni de Testing Automatizado.**
    *   No hay workflows configurados en `.github/workflows`.
    *   No hay ejecución de pruebas unitarias, de integración o E2E previa a los despliegues.
    *   El compilador TypeScript en local opera con `strict: false`, por lo que errores de tipado pueden llegar al repositorio (`04-notas-tecnicas.md` · "stack").
*   **Mecanismo de Despliegue Actual (CD Directo):**
    *   **Trigger:** Cada `git push` directo o merge hacia la rama `main` de GitHub.
    *   **Ejecutor:** Vercel GitHub App vinculada al repositorio.
    *   **Proceso:** Vercel compila la aplicación (`next build`) e implementa los cambios en producción en aproximadamente 2 a 3 minutos (`04-notas-tecnicas.md` · "deploy").
*   **Estrategia de Rollback:**
    *   En caso de fallo en producción, el rollback se efectúa manualmente desde el panel de control de Vercel, reasignando el tráfico al despliegue previo (`04-notas-tecnicas.md` · "recuperacion").

---

## 4. Herramientas de Infraestructura

*   **Hosting y Edge Computing:** **Vercel** (Plataforma Serverless, Edge CDN, Route Handlers para endpoints de API y Next.js App Router para renderizado híbrido SSR/Cliente).
*   **Base de Datos y Backend-as-a-Service:** **Supabase Cloud** (Motor PostgreSQL 15+, Supabase Auth para gestión de sesiones con JWT y Row Level Security nativo).
*   **Comunicaciones Transaccionales:** **Resend API** (Servicio HTTP para despacho de correos de confirmación, bienvenida y avisos operativos).
*   **Control de Versiones y Repositorio:** **GitHub** (Esquema de rama única `main` sin ramas de staging protegidas).

---

## 5. Riesgos del Mapa de Entornos para QA

| Riesgo de Infraestructura | Impacto en Calidad y Negocio | Severidad | Mitigación Recomendada para QA |
| :--- | :--- | :--- | :--- |
| **Pruebas en Entorno Único de Producción** | No existe aislamiento. Cada turno o cliente creado por QA queda asentado en la base de datos real y puede confundirse con transacciones de usuarios legítimos. | **Crítica** | Utilizar prefijos identificatorios claros (ej. `[TEST-QA] Juan Pérez`, casillas `@mailinator.com`) y documentar cada registro creado. |
| **Inexistencia de Ambiente UAT / Staging Aislado** | Se valida sobre un terreno inestable que cambia mientras se prueba; no se pueden forzar fallos de sistema ni caídas de red sin afectar a usuarios reales. | **Crítica** | Solicitar al equipo de desarrollo la reactivación prioritaria del proyecto Supabase UAT y un dominio preview en Vercel. |
| **Envío Real de Correos Electrónicos** | Las pruebas de reserva disparan correos reales vía Resend. Si se ingresa un correo involuntariamente de un tercero, recibirá la confirmación del turno. | **Alta** | Restringir el ingreso de direcciones de correo exclusivamente a dominios de prueba controlados (`@mailinator.com`). |
| **Ausencia de Rollback y Teardown de Base de Datos** | La base de datos no cuenta con scripts de seed ni migraciones versionadas; los datos creados en pruebas son permanentes. | **Alta** | Diseñar un inventario de datos de prueba y un protocolo de limpieza manual mientras se construye un script de teardown. |
| **Deploy Automático a Producción sin Barreras de QA** | Un cambio en `main` se publica directamente en la web sin validación previa por testing ni aprobación formal. | **Alta** | Establecer una política de Pull Requests obligatorios y configurar una GitHub Action básica con linting y validación de tipos. |

---

## Fuentes

| Dato / afirmación | De dónde sale |
| :--- | :--- |
| Tipo de aplicación (Next.js App Router Web, sin app nativa) | `03-especificacion-funcional-v0.3.md` · sección 1; `04-notas-tecnicas.md` · "stack" |
| In-App Browsers de Instagram y WhatsApp como canales clave | `01-minuta-kickoff.md` · "Cómo llega el cliente"; `02-notas-entrevistas.md` · Entrevistas 1 y 2 |
| URL real del sistema en producción (`https://cita-ai.vercel.app/`) | `documentacion para QA/nota-ambientes-y-accesos.md` · "la direccion" |
| Discontinuación y estado inactivo del ambiente UAT | `documentacion para QA/nota-ambientes-y-accesos.md` · "lo del ambiente de UAT" |
| Prohibición explícita de usar la cuenta de Fernando | `documentacion para QA/nota-ambientes-y-accesos.md` · "un par de cosas"; `documentacion para QA/hilo-mail-alcance-qa.md` |
| Recomendación de uso de Mailinator para casillas de prueba | `documentacion para QA/nota-ambientes-y-accesos.md` · "como entrar" |
| Proveedores cloud (Vercel, Supabase, Resend) | `04-notas-tecnicas.md` · "stack"; `05-hilo-mail-cambio-de-alcance.md` |
| Inexistencia de CI/CD y despliegue por push directo a `main` | `04-notas-tecnicas.md` · "deploy", "lo que no esta hecho" |
| Tiempo estimado de despliegue y rollback en Vercel (2-3 minutos) | `04-notas-tecnicas.md` · "recuperacion" |
| Costos detallados de suscripción a Vercel Pro y Supabase Pro | **Hipótesis** — no hay documento que lo respalde |
| Límites de cuota diaria de envíos en la cuenta actual de Resend | **Hipótesis** — no hay documento que lo respalde |

---

## Contradicciones detectadas

*   **Disponibilidad del Entorno UAT:**  
    *   *Qué afirma la especificación:* `03-especificacion-funcional-v0.3.md` (sección 10) y las notas originales de `04-notas-tecnicas.md` describen un ambiente UAT activo en `https://uat.cita.ai` con su propia base de datos para la certificación de calidad.  
    *   *Qué revela la realidad operativa:* En `documentacion para QA/nota-ambientes-y-accesos.md`, Diego admite que hace meses dejó de mantener UAT y que todas las validaciones corren sobre el proyecto único de producción en Vercel.  
    *   *Postura adoptada en este mapa:* Se descarta la existencia funcional de UAT y se cataloga la operación como **entorno único de producción**.
*   **Nombre de Dominio y Configuración DNS:**  
    *   *Qué afirman los documentos iniciales:* Se hace referencia recurrente al dominio comercial `cita.ai`.  
    *   *Qué revela la realidad técnica:* El dominio no tiene registros DNS configurados y la aplicación opera exclusivamente bajo el subdominio gratuito de Vercel (`cita-ai.vercel.app`).  
    *   *Postura adoptada en este mapa:* Se fija `https://cita-ai.vercel.app/` como la única URL base válida para ejecución de pruebas.

---

## Preguntas abiertas

*   **¿En qué fecha se reactivará formalmente un entorno de Staging/UAT independiente?** Sin este entorno, la ejecución de pruebas destructivas o de carga masiva resulta inviable por el riesgo de impacto a los usuarios reales (`documentacion para QA/nota-ambientes-y-accesos.md`).
*   **¿Se implementará un entorno de previsualización (Vercel Preview Deployments) para Pull Requests?** Vercel soporta generación automática de URLs temporales por rama/PR; se requiere confirmar si Supabase proveerá bases de datos efímeras o esquemas aislados para ese fin.
*   **¿Qué política de rotación de credenciales y claves de API de Supabase y Resend se implementará?** Actualmente las credenciales se cargan manualmente en los paneles cloud sin auditoría de accesos.
