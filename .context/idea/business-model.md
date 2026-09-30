# Business Model Canvas: Cita.ai

**Tipo de proyecto:** Brownfield

## 1. Propuesta de Valor (Value Propositions)
*   **Para el Profesional Independiente:**
    *   **Simplicidad radical:** Gestión automatizada de agenda sin la complejidad de herramientas avanzadas ni la insuficiencia de calendarios genéricos (`01-minuta-kickoff.md` · "Contra quién competimos"; `03-especificacion-funcional-v0.3.md` · sección 1).
    *   **Ahorro de tiempo administrativo:** Reducción comprobada de al menos 30 minutos semanales (y hasta 4 horas) dedicados a la coordinación manual por WhatsApp, chat o llamadas (`01-minuta-kickoff.md` · "Qué queremos que pase"; `02-notas-entrevistas.md` · Entrevista 1 y conclusiones).
    *   **Puesta en marcha inmediata:** Registro e inicio de operaciones con URL pública en menos de 5 minutos (`01-minuta-kickoff.md` · "Qué queremos que pase"; `03-especificacion-funcional-v0.3.md` · sección 9).
    *   **Prevención de dobles reservas:** Eliminación de superposiciones accidentales de turnos mediante control de disponibilidad en tiempo real (`01-minuta-kickoff.md`; `03-especificacion-funcional-v0.3.md` · sección 5.2).
*   **Para el Cliente Final:**
    *   **Reserva autónoma e inmediata:** Posibilidad de visualizar horarios disponibles y agendar en 30 segundos sin esperar respuesta por chat (`02-notas-entrevistas.md` · Entrevista 4).
    *   **Sin fricción de registro:** Proceso de reserva completable únicamente con nombre y correo electrónico, sin necesidad de crear cuenta ni contraseña (`01-minuta-kickoff.md` · "Los dos usuarios"; `03-especificacion-funcional-v0.3.md` · sección 2.2).

## 2. Segmentos de Clientes (Customer Segments)
*   **Usuarios Principales (Profesionales):**
    *   Profesionales independientes y microempresas (de 1 a 3 empleados) que cobran por hora / tiempo y gestionan su propia agenda (`01-minuta-kickoff.md` · "A quién le vendemos").
    *   Sectores iniciales identificados: Terapeutas/psicólogos, entrenadores personales, estilistas/peluqueros, tutores/profesores particulares y nutricionistas en mercados de habla hispana (`01-minuta-kickoff.md`; `02-notas-entrevistas.md`).
*   **Usuarios Secundarios (Clientes finales):**
    *   Pacientes, alumnos o clientes de los profesionales que requieren agendar servicios de manera digital y directa sin coordinar por mensajería (`01-minuta-kickoff.md`; `02-notas-entrevistas.md` · Entrevista 4).

## 3. Canales (Channels)
*   **Enlace Público Directo:** URL pública personalizada por profesional (`cita-ai.vercel.app/[slug]`), difundida de manera autónoma en perfiles de Instagram (bio), estados de WhatsApp y sitios web (`01-minuta-kickoff.md` · "Cómo llega el cliente"; `03-especificacion-funcional-v0.3.md` · sección 3.4; `documentacion para QA/nota-ambientes-y-accesos.md`).
*   **Notificaciones por Correo Electrónico:** Notificaciones transaccionales automáticas al cliente y al profesional ante la confirmación de una reserva, enviadas vía Resend (`03-especificacion-funcional-v0.3.md` · sección 6; `05-hilo-mail-cambio-de-alcance.md`).

## 4. Relación con Clientes (Customer Relationships)
*   **Autoservicio (Self-service):** Experiencia 100% autónoma tanto para la configuración de agenda por parte del profesional como para la reserva por parte del cliente final (`01-minuta-kickoff.md`; `03-especificacion-funcional-v0.3.md`).
*   **Soporte Directo:** Atención de consultas e incidentes mediante casilla de correo electrónico de soporte (`06-tickets-soporte-resumen.md`).

## 5. Fuentes de Ingresos (Revenue Streams)
*   **Plan Gratuito (Freemium):** Acceso sin costo con límite de hasta 10 clientes únicos acumulados por profesional (`01-minuta-kickoff.md` · "El modelo: freemium"; `03-especificacion-funcional-v0.3.md` · sección 7.1).
*   **Futuro Plan Pro / Pago (Suscripción):** Captura de interés mediante el botón *"Más información sobre el Plan Pro"* al alcanzar el límite de 10 clientes (`03-especificacion-funcional-v0.3.md` · sección 7.3; `05-hilo-mail-cambio-de-alcance.md`).
*   **Hipótesis:** Definición exacta de tarifas, periodicidad (mensual/anual) y empaquetado del Plan Pago (pendiente de especificación de negocio).

## 6. Recursos Clave (Key Resources)
*   **Plataforma Web:** Aplicación construida en Next.js con App Router, alojada en Vercel (`04-notas-tecnicas.md` · "stack").
*   **Infraestructura Backend y Base de Datos:** Base de datos PostgreSQL, Autenticación y RLS provistos por Supabase (`04-notas-tecnicas.md` · "stack").
*   **Servicio de Correo Transaccional:** Integración con Resend para el envío de notificaciones (`05-hilo-mail-cambio-de-alcance.md`).
*   **Equipo Core:** Roles de Producto (Mariana), Desarrollo (Diego), UI/UX/Soporte (Sol) y Comercial (Fernando) (`01-minuta-kickoff.md`; `05-hilo-mail-cambio-de-alcance.md`).

## 7. Actividades Clave (Key Activities)
*   **Desarrollo y Mantenimiento de Software:** Mantenimiento de la aplicación web, corrección de defectos y optimizaciones de UI/UX (`04-notas-tecnicas.md`).
*   **Gestión de Infraestructura Cloud:** Monitoreo y despliegues en Vercel y Supabase (`04-notas-tecnicas.md`).
*   **Atención y Soporte a Usuarios:** Resolución de dudas y recepción de feedback operacional vía correo (`06-tickets-soporte-resumen.md`).
*   **Atracción y Validación Comercial:** Prospección directa de profesionales independientes y redes de consultorios (`05-hilo-mail-cambio-de-alcance.md`; `documentacion para QA/hilo-mail-alcance-qa.md`).
*   **Aseguramiento de Calidad y Documentación:** Definición del comportamiento esperado del producto, reconstrucción de especificaciones y armado de backlog (`documentacion para QA/hilo-mail-alcance-qa.md`; `documentacion para QA/transcripcion-reunion-2026-05-19.md`).

## 8. Socios Clave (Key Partners)
*   **Vercel:** Proveedor de hosting, infraestructura serverless y despliegue continuo (`04-notas-tecnicas.md`).
*   **Supabase:** Proveedor de servicios backend, base de datos Postgres y gestión de usuarios (`04-notas-tecnicas.md`).
*   **Resend:** Proveedor de infraestructura para correos transaccionales (`05-hilo-mail-cambio-de-alcance.md`).
*   **Redes y Alianzas Comerciales:** Contactos comerciales y redes de consultorios para adopción masiva (`documentacion para QA/hilo-mail-alcance-qa.md`).

## 9. Estructura de Costos (Cost Structure)
*   **Costos de Infraestructura Cloud:** Suscripciones/planes de Vercel y Supabase (`04-notas-tecnicas.md`; `documentacion para QA/transcripcion-reunion-2026-05-19.md`).
*   **Costos de Proveedores de Email:** Consumo de servicios de envío de correos vía Resend (`05-hilo-mail-cambio-de-alcance.md`).
*   **Mantenimiento de Dominios:** Costos anuales de registro del dominio corporativo (`cita.ai`) (`documentacion para QA/nota-ambientes-y-accesos.md`).
*   **Costos de Adquisición de Clientes (CAC):** Inversión de tiempo y recursos comerciales para atracción de profesionales (`01-minuta-kickoff.md` · "Lo que nos va a costar entrar").

## Problem Statement (Resumen)
Los profesionales independientes que cobran por hora (terapeutas, entrenadores, estilistas, tutores) pierden entre 30 minutos y 4 horas semanales gestionando turnos manualmente por canales no integrados (WhatsApp, Instagram, llamadas). Esta desorganización genera dobles reservas que dañan su reputación y ausencias de clientes (no-shows) que destruyen sus ingresos. Las soluciones del mercado resultan o bien demasiado complejas y costosas de configurar (Acuity), o demasiado básicas al no estar enfocadas en la gestión de servicios/clientes (Calendly). Cita.ai resuelve este problema ofreciendo una plataforma de auto-reserva web ultrasimple, que se configura en menos de 5 minutos, permite al cliente agendar en 30 segundos sin registrarse, y elimina la fricción administrativa en un modelo freemium de hasta 10 clientes.

## Fuentes
| Dato / afirmación | De dónde sale |
| :--- | :--- |
| Definición del público objetivo (1-3 empleados, cobro por hora) | `01-minuta-kickoff.md` · "A quién le vendemos" |
| Ahorro prometido de 30 min a 4 horas semanales | `01-minuta-kickoff.md` · "Qué queremos que pase"; `02-notas-entrevistas.md` · Entrevista 1 |
| Promesa de configuración en menos de 5 minutos | `01-minuta-kickoff.md` · "Qué queremos que pase"; `03-especificacion-funcional-v0.3.md` · sección 9 |
| Regla de no exigir registro al cliente final | `01-minuta-kickoff.md` · "Los dos usuarios"; `03-especificacion-funcional-v0.3.md` · sección 2.2 |
| Posicionamiento ("Acuity es complejo, Calendly es básico") | `01-minuta-kickoff.md` · "Contra quién competimos"; `03-especificacion-funcional-v0.3.md` · sección 1 |
| Modelo Freemium con límite de 10 clientes únicos | `01-minuta-kickoff.md` · "El modelo: freemium"; `03-especificacion-funcional-v0.3.md` · sección 7.1 |
| Texto del botón al alcanzar el límite ("Más información sobre el Plan Pro") | `05-hilo-mail-cambio-de-alcance.md` · sección "De: Mariana (22/02/2026)" |
| Uso de Resend para correos transaccionales | `05-hilo-mail-cambio-de-alcance.md` · sección "De: Diego (28/02/2026)" |
| Exclusión del recordatorio previo al turno en el lanzamiento | `05-hilo-mail-cambio-de-alcance.md`; `documentacion para QA/transcripcion-reunion-2026-05-19.md` |
| URL real del sistema en producción (`cita-ai.vercel.app`) | `documentacion para QA/nota-ambientes-y-accesos.md` · "La dirección" |
| Inexistencia de ambiente UAT aislado (entorno único de producción) | `documentacion para QA/nota-ambientes-y-accesos.md` · "Lo del ambiente de UAT" |
| Stack técnico (Next.js, Supabase, Vercel, TypeScript, Tailwind) | `04-notas-tecnicas.md` · "stack" |
| Cierre o definición de precios y paquetes del Plan Pro pago | **Hipótesis** — no hay documento que lo respalde |
| Criterio de expiración o retención de datos en la plataforma | **Hipótesis** — no hay documento que lo respalde |

## Contradicciones detectadas
*   **Dominio y URL pública del servicio:** `01-minuta-kickoff.md` y `03-especificacion-funcional-v0.3.md` establecen que la plataforma opera bajo `cita.ai` y que las URL públicas son del tipo `cita.ai/[slug]`. Sin embargo, `documentacion para QA/nota-ambientes-y-accesos.md` y `04-notas-tecnicas.md` aclaran que el dominio `cita.ai` nunca fue apuntado por DNS y la aplicación funciona realmente en `cita-ai.vercel.app` (siendo la URL pública real `cita-ai.vercel.app/[slug]`). Se toma la evidencia de la aplicación en producción.
*   **Proveedor de correo transaccional:** `01-minuta-kickoff.md` menciona SendGrid, mientras que `03-especificacion-funcional-v0.3.md` y `04-notas-tecnicas.md` indican el servicio integrado de Supabase. No obstante, `05-hilo-mail-cambio-de-alcance.md` documenta la decisión formal posterior de utilizar **Resend**. Se toma Resend como la evidencia vigente.
*   **Disponibilidad del recordatorio del día anterior:** `01-minuta-kickoff.md` y `03-especificacion-funcional-v0.3.md` definen los recordatorios automáticos por email el día previo al turno como una funcionalidad indispensable. Sin embargo, `05-hilo-mail-cambio-de-alcance.md`, `06-tickets-soporte-resumen.md` y `documentacion para QA/transcripcion-reunion-2026-05-19.md` confirman que dicha funcionalidad quedó fuera de la versión lanzada debido a limitaciones técnicas y de costos en Vercel.
*   **Texto del botón al alcanzar el límite de 10 clientes:** `01-minuta-kickoff.md` lo llama "Solicitar Upgrade", `03-especificacion-funcional-v0.3.md` indica "Ver Opciones", y `05-hilo-mail-cambio-de-alcance.md` documenta la definición final por "Más información sobre el Plan Pro". Se adopta la decisión de `05-hilo-mail-cambio-de-alcance.md`.
*   **Existencia de ambiente UAT independiente:** `03-especificacion-funcional-v0.3.md` y `04-notas-tecnicas.md` describen un ambiente UAT (`uat.cita.ai`) separado de producción. Sin embargo, `documentacion para QA/nota-ambientes-y-accesos.md` y `documentacion para QA/transcripcion-reunion-2026-05-19.md` aclaran que no existe dicho ambiente aislado y que todas las pruebas y operaciones corren directamente sobre el entorno único de producción en Vercel.

## Preguntas abiertas
*   ¿Qué comportamiento exacto debe tener el sistema cuando un profesional bloquea un rango de fechas/horas en el cual ya existen turnos previamente reservados por clientes? (`03-especificacion-funcional-v0.3.md` · 4.3; `06-tickets-soporte-resumen.md` #52; `documentacion para QA/transcripcion-reunion-2026-05-19.md`).
*   ¿Cómo se registrarán y gestionarán las ausencias (no-shows) o inasistencias en la agenda desde el panel del profesional? (`03-especificacion-funcional-v0.3.md` · 8; `06-tickets-soporte-resumen.md` #55, #71; `documentacion para QA/transcripcion-reunion-2026-05-19.md`).
*   ¿Existe un límite máximo de anticipación para que un cliente pueda agendar turnos a futuro? (`03-especificacion-funcional-v0.3.md` · 5.2; `06-tickets-soporte-resumen.md` #63).
*   ¿Cómo se debe abordar el modelo de atención multi-profesional para establecimientos o consultorios compartidos (más de una persona/agenda por cuenta)? (`01-minuta-kickoff.md`; `02-notas-entrevistas.md` #5; `06-tickets-soporte-resumen.md` #22; `documentacion para QA/transcripcion-reunion-2026-05-19.md`).
*   ¿Cómo se maneja la información de reserva cuando quien realiza la reserva (ej. un padre/madre) no es quien asistirá a la cita (ej. hijo/a)? (`02-notas-entrevistas.md` #6; `06-tickets-soporte-resumen.md` #40; `documentacion para QA/transcripcion-reunion-2026-05-19.md`).
*   ¿Cuáles serán los precios, niveles de servicios y métodos de pago aplicables al Plan Pro una vez implementada la suscripción? (`01-minuta-kickoff.md`; `03-especificacion-funcional-v0.3.md` · 7.3).
*   ¿Dónde y cómo debe visualizarse o copiarse la URL pública personalizada del profesional dentro del panel/dashboard principal para evitar confusiones de onboarding? (`06-tickets-soporte-resumen.md` #38, #44; `documentacion para QA/transcripcion-reunion-2026-05-19.md`).
