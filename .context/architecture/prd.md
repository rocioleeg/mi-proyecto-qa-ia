# Product Requirement Document (PRD): Cita.ai

**Versión del documento:** 1.0 (Reconstrucción para QA y Línea Base)  
**Estado:** Vigente  
**Tipo de proyecto:** Brownfield  
**Autor:** Senior Technical Product Manager (TPM)  

---

## 1. Introducción y Objetivos

### Visión
Cita.ai es una plataforma web SaaS diseñada bajo el principio de **simplicidad radical**, dirigida a profesionales independientes y microempresas (1 a 3 personas) que comercializan su tiempo por hora (psicólogos, entrenadores personales, estilistas, tutores particulares y nutricionistas). Resuelve la pérdida de entre 30 minutos y 4 horas semanales de gestión administrativa manual por canales no integrados (WhatsApp, Instagram, llamadas), previene las dobles reservas accidentales y mitiga las ausencias de clientes (no-shows) mediante un enlace público de auto-reserva (`cita-ai.vercel.app/[slug]`) operativo en menos de 5 minutos, donde el cliente final agenda en 30 segundos sin necesidad de registrarse.

### Alcance del Release

*   **Dentro del alcance de la versión actual (Operativa en Producción / Soft Launch):**
    *   Registro e inicio de sesión de profesionales con validación de credenciales (Supabase Auth).
    *   Generación automática de slug único y URL pública personalizada (`cita-ai.vercel.app/[slug]`).
    *   Configuración de disponibilidad semanal recurrente por día (0 = Domingo a 6 = Sábado) y duración fija de turno en minutos.
    *   Bloqueo manual de rangos horarios o jornadas completas (vacaciones, ausencias personales).
    *   Portal web público de auto-reserva para clientes finales sin cuenta ni contraseña.
    *   Cálculo dinámico de turnos disponibles en tiempo real con control de concurrencia y prevención de superposiciones.
    *   Lógica del plan gratuito con límite estricto de hasta 10 clientes únicos acumulados por profesional, bloqueo al cliente 11 y botón de interés comercial (*"Más información sobre el Plan Pro"*).
    *   Notificaciones transaccionales automáticas por email (bienvenida, confirmación de cita al cliente, aviso de reserva al profesional y notificación de límite alcanzado vía Resend).
    *   Visualización de agenda básica y listado de clientes en el panel del profesional.
*   **Fuera del alcance de esta versión (Excluido explícitamente o postergado):**
    *   Recordatorio automático por email el día anterior al turno (postergado por restricciones técnicas/costos de cron en Vercel).
    *   Pasarelas de cobro o pagos en línea (Mercado Pago, Stripe, señas).
    *   Sincronización con calendarios externos (Google Calendar, Outlook).
    *   Soporte multi-profesional por cuenta (más de una persona o agenda por establecimiento).
    *   Múltiples servicios con duraciones o precios diferenciados por profesional.
    *   Formularios personalizados de admisión o recolección de datos adicionales al agendar.
    *   Gestión de ausencias o estado *"No se presentó"* (no-show) en el panel.
    *   Cancelación autónoma o reprogramación directa por parte del cliente final.
    *   Aplicaciones móviles nativas (iOS / Android).
    *   Reportes analíticos o métricas de conversión en el panel.
    *   Entorno de pruebas (UAT) aislado y migraciones automatizadas.

---

## 2. User Personas

*   **Profesional Independiente — Tipo A: "Laura" (Psicóloga clínica)**
    *   *Perfil:* 38 años, atiende en consultorio privado. Familiarizada con tecnología básica (iPhone, Mac, Google Calendar), pero sobrecargada por la gestión manual.
    *   *Dolor principal:* Pierde hasta 4 horas semanales coordinando horarios por WhatsApp fuera de su horario laboral y sufre el impacto reputacional de eventuales dobles turnos.
    *   *Expectativa:* Puesta en marcha en menos de 5 minutos, agenda predecible y cero configuración técnica compleja.
*   **Profesional Independiente — Tipo B: "Carlos" (Entrenador personal)**
    *   *Perfil:* 42 años, gestiona un estudio funcional. Usuario intensivo de Android, WhatsApp e Instagram; reacio a sistemas informáticos complejos ("usa libreta de papel").
    *   *Dolor principal:* Cancelaciones con 15 minutos de anticipación que le impiden reasignar el horario, y prospectos nuevos que se pierden por no responder al instante.
    *   *Expectativa:* Un enlace directo para la biografía de Instagram y el estado de WhatsApp que permita auto-agendar sin intermediación.
*   **Cliente Final — "Sofía" (Consumidora digital / Diseñadora UX)**
    *   *Perfil:* 31 años, usuaria de herramientas digitales de autoservicio. Nula tolerancia a la fricción administrativa y al "ping-pong" de mensajes.
    *   *Dolor principal:* Esperar horas o días para que un profesional le confirme si un turno está libre, y tener que rebuscar en chats pasados para recordar la fecha/hora de su cita.
    *   *Expectativa:* Visualizar la disponibilidad completa en tiempo real, agendar en 30 segundos sólo con nombre y correo (sin claves ni registros), y recibir confirmación inmediata.

---

## 3. Funcionalidades Principales (Core Features)

### Feature 1: Autenticación y Gestión de Cuenta del Profesional
*   **Descripción:** Registro de profesionales mediante nombre completo, correo electrónico único y contraseña (mínimo 8 caracteres, al menos una mayúscula y un número). Autenticación con sesión persistente (cookies httpOnly, expiración a los 15 minutos, refresh token a 7 días vía Supabase Auth). Mecanismo de recuperación de contraseña vía correo con token de un solo uso válido por 1 hora. Generación automática e incremental de slug público único (`cita-ai.vercel.app/[slug]`).
*   **Valor para el usuario:** Permite acceder a la administración de turnos de manera segura y habilitar una presencia digital personalizada en minutos.
*   **Criterios de éxito:** El profesional se registra y accede al panel sin pasos superfluos; el slug asignado es único en toda la plataforma y no genera colisiones.

### Feature 2: Configuración de Disponibilidad y Bloqueos de Agenda
*   **Descripción:** Módulo de administración donde el profesional define sus franjas horarias semanales recurrentes por día de la semana y establece una duración estándar de turno en minutos (ej. 45 o 60 minutos) aplicable a todas sus sesiones. Permite además registrar bloqueos puntuales de tiempo (`time_blocks`) con fecha/hora de inicio y fin para vacaciones, licencias o trámites personales.
*   **Valor para el usuario:** Automatiza la oferta horaria sin obligar al profesional a abrir y cerrar turnos individualmente todos los días.
*   **Criterios de éxito:** Al guardar la disponibilidad se reemplaza de forma atómica la configuración previa; los horarios bloqueados se descuentan inmediatamente de la disponibilidad pública.

### Feature 3: Portal Público de Auto-Reserva (Cliente Final)
*   **Descripción:** Interfaz web pública accesible vía `cita-ai.vercel.app/[slug]` que calcula dinámicamente los intervalos libres para la semana en curso (descontando citas confirmadas y bloqueos). El cliente final elige el día y horario, introduce obligatoriamente su nombre y correo electrónico, y confirma la cita sin requerir cuenta, login ni contraseña.
*   **Valor para el usuario:** Elimina las barreras de entrada para el cliente, posibilitando agendar en 30 segundos y en cualquier horario del día.
*   **Criterios de éxito:** El turno se registra con estado `confirmed` de forma inmediata (sin requerir aprobación manual del profesional); los datos del cliente se vinculan al registro y el slot desaparece de la oferta pública al instante.

### Feature 4: Control de Concurrencia y Prevención de Dobles Reservas
*   **Descripción:** Validación estricta que impide solapamientos de citas. El sistema ejecuta una comprobación de disponibilidad tanto al consultar la grilla horaria como en una segunda verificación inmediata previa a la inserción en base de datos. Si dos usuarios intentan tomar el mismo intervalo simultáneamente, el segundo intento es rechazado con el mensaje: *"¡Casi! Parece que alguien más reservó este horario. Por favor, elegí otro."*, preservando en pantalla los datos ya ingresados por el cliente.
*   **Valor para el usuario:** Protege la reputación del profesional evitando superposiciones bochornosas y asegura equidad en la asignación de turnos.
*   **Criterios de éxito:** Cero reservas superpuestas para un mismo profesional en el mismo intervalo horario.

### Feature 5: Regla de Negocio Freemium (Límite de 10 Clientes Únicos)
*   **Descripción:** Restricción de uso basada en la acumulación de un máximo de 10 clientes únicos (distinguidos por dirección de correo electrónico) vinculados a los turnos del profesional. Quienes pertenezcan al grupo de los primeros 10 pueden reservar ilimitadamente. Al intento de reserva del cliente número 11 (o al intento de carga manual en el panel), la operación es bloqueada (`LIMIT_REACHED`), notificando al cliente que contacte al profesional y disparando un correo festivo al profesional junto con un banner visible en el panel con el botón *"Más información sobre el Plan Pro"*.
*   **Valor para el usuario:** Permite validar el producto sin costo en etapas iniciales y establece un disparador transparente para la monetización.
*   **Criterios de éxito:** Las reservas de los 10 clientes originales continúan sin afectación; el intento número 11 activa los avisos y bloquea la creación del turno.

### Feature 6: Sistema de Notificaciones Transaccionales por Correo
*   **Descripción:** Envío automático de correos electrónicos transaccionales a través de la API de Resend: 1) Bienvenida al profesional al completar el registro; 2) Confirmación de reserva al cliente final con los datos de fecha, hora y profesional; 3) Aviso de nueva reserva al profesional con los datos del cliente; 4) Notificación de límite freemium alcanzado al profesional.
*   **Valor para el usuario:** Provee comprobante digital fehaciente tanto al cliente como al profesional sin requerir consulta proactiva al sistema.
*   **Criterios de éxito:** Despacho sincronizado de emails tras cada evento clave con detalle exacto del turno confirmado.

---

## 4. User Journeys (Flujos Clave)

### Flujo 1: Registro y Puesta en Marcha del Profesional
1. El profesional accede a `cita-ai.vercel.app/login` y hace clic en *"Regístrate gratis"*.
2. Completa nombre completo, correo electrónico y contraseña válida.
3. El sistema valida los campos, genera el slug único incremental en base al nombre, crea el usuario en Supabase Auth y dispara el trigger hacia la tabla `professionals`.
4. El sistema inicia sesión automáticamente, envía el correo de bienvenida y redirige al profesional a su panel de control.
5. El profesional accede a la sección de configuración de agenda, define sus franjas horarias semanales y la duración del turno (ej. 50 min), y guarda los cambios.
6. El profesional obtiene su URL pública (`cita-ai.vercel.app/[slug]`) y la comparte en sus canales de contacto (WhatsApp, Instagram).

### Flujo 2: Auto-Reserva del Cliente Final
1. El cliente hace clic en el enlace público compartido por el profesional.
2. La aplicación web consulta `/api/public/availability` y renderiza el calendario semanal con los horarios libres calculados.
3. El cliente selecciona el día y el bloque de horario preferido.
4. El formulario solicita nombre y correo electrónico.
5. El cliente presiona *"Confirmar reserva"*.
6. La API pública (`/api/public/appointments`) verifica que el profesional no haya superado los 10 clientes únicos y que el turno siga libre.
7. El sistema registra al cliente en `clients` (si no existía) e inserta el registro en `appointments` con estado `confirmed`.
8. Se envían los correos de confirmación (al cliente) y aviso (al profesional) mediante Resend.
9. La pantalla muestra la confirmación exitosa de la cita.

### Flujo 3: Bloqueo de Horarios por el Profesional
1. El profesional inicia sesión y se dirige a la sección de disponibilidad/bloqueos en el dashboard.
2. Selecciona la opción de añadir bloqueo e ingresa fecha/hora de inicio, fecha/hora de fin y un motivo opcional.
3. El sistema persiste el registro en `time_blocks`.
4. De inmediato, cualquier consulta a la URL pública descuenta esa franja de la grilla de turnos disponibles.

### Flujo 4: Intento de Reserva con Límite Freemium Alcanzado (Cliente 11)
1. Un cliente nuevo (con correo no registrado previamente para ese profesional) accede a la URL pública de un profesional que ya cuenta con 10 clientes únicos.
2. Selecciona un horario e ingresa sus datos personales.
3. Al presionar *"Confirmar reserva"*, la API detecta la condición de cupo agotado y retorna un estado 403 con código `LIMIT_REACHED`.
4. La interfaz pública informa amablemente que el profesional no puede recibir nuevos clientes por la plataforma en ese momento e invita a contactarlo de forma directa.
5. En paralelo, el sistema envía un correo al profesional felicitándolo por el crecimiento de su negocio y activa en su panel el banner informativo con el botón *"Más información sobre el Plan Pro"*.

---

## 5. Requisitos No Funcionales (NFRs)

### Seguridad
*   **Autenticación y Sesiones:** Almacenamiento de tokens JWT de sesión en cookies `httpOnly` y seguras, con expiración de 15 minutos y renovación mediante refresh token de 7 días provisto por Supabase Auth.
*   **Aislamiento de Datos (RLS):** Activación mandataria de Row Level Security en PostgreSQL para todas las tablas. El acceso a `appointments`, `availability_rules` y `time_blocks` está estrictamente restringido a `auth.uid() = professional_id` para operaciones de modificación y lectura privada.
*   **Protección de Rutas:** Middleware centralizado en Next.js (`middleware.ts`) que intercepta accesos a rutas protegidas del panel y redirige a `/login` ante sesiones inválidas.
*   **Manejo Seguro de Contraseñas:** Token de recuperación de contraseña de un solo uso con caducidad estricta a los 60 minutos. Mensaje unificado de recuperación ante correos existentes o inexistentes para evitar ataques de enumeración de usuarios.
*   *Hipótesis / Brecha Detectada:* No existe rate limiting configurado en los endpoints públicos (`/api/public/*`), lo cual expone la aplicación a ataques de denegación o scraping.

### Rendimiento
*   **Tiempo de Carga Inicial:** La página pública de reservas debe renderizarse y estar interactiva en menos de 2.0 segundos en conexiones de banda ancha estándar (medición empírica observada en UAT/local).
*   **Latencia de Endpoints:** Tiempos de respuesta para endpoints de disponibilidad y confirmación de turnos con percentil 95 (P95) inferior a 500 ms (medición técnica interna).
*   **Concurrencia:** Capacidad para soportar un volumen simultáneo inicial de al menos 100 usuarios activos concurrentes sin degradación del servicio sobre infraestructura Serverless de Vercel y Supabase (*Hipótesis basada en estimación de arquitectura*).
*   **Eficiencia de Consultas:** El cálculo de disponibilidad semanal se resuelve en memoria descartando turnos confirmados y bloqueos sobre la franja horaria solicitada.

### Compatibilidad y Usabilidad
*   **Navegadores:** Soporte completo en versiones modernas de Google Chrome, Mozilla Firefox, Apple Safari y Microsoft Edge.
*   **Diseño Responsivo:** Interfaz 100% adaptable a pantallas móviles (smartphones Android e iOS) y de escritorio, optimizada para acceso directo desde navegadores integrados de redes sociales (Instagram In-App Browser y WhatsApp).
*   **Localización:** Soporte nativo de fechas, calendarios y mensajes en idioma español (configuración con `date-fns` locale `es`).

---

## 6. Riesgos y Mitigaciones

| Riesgo Técnico / de Producto | Impacto | Severidad | Mitigación Propuesta |
| :--- | :--- | :--- | :--- |
| **Ausencia de recordatorios automáticos por email** | Elevada tasa de inasistencias (no-shows) de clientes; es el principal motivo de reclamos en la casilla de soporte. | Alta | Priorizar en la siguiente iteración la implementación de un cron job (Vercel Cron o disparador programado externo) que ejecute el envío diario vía Resend. |
| **Operación sobre entorno único de producción sin staging/UAT** | Las pruebas de QA y validaciones funcionales se ejecutan sobre la base real de clientes, con riesgo de corromper datos o disparar emails a personas reales. | Crítica | Utilizar exclusivamente cuentas de prueba y correos descartables (Mailinator); aislar las cuentas operativas reales (ej. cuenta de Fernando); recrear un entorno de staging formal con script de seed anonimizado. |
| **Condición de carrera en reservas simultáneas** | Riesgo de dobles turnos accidentales si dos clientes confirman exactamente el mismo slot al mismo tiempo, al no contar con transacción atómica en base de datos. | Alta | Implementar una función almacenada (RPC) en PostgreSQL con transacción y bloqueo pesimista/serializable para la reserva de turnos. |
| **Ocultamiento de la URL pública en el panel del profesional** | Frustración severa en el onboarding; los nuevos usuarios no encuentran su enlace para compartir y recurren a soporte, dañando la promesa de 5 minutos. | Alta | Incorporar en la vista principal del dashboard un widget destacado que exhiba la URL pública personalizada con botón de copiado rápido al portapapeles. |
| **Persistencia de citas previas ante bloqueos de agenda** | Cuando un profesional bloquea un rango donde ya existían turnos reservados, el sistema no cancela ni alerta, manteniendo las citas activas para los clientes. | Media | Diseñar una advertencia en el flujo de bloqueo que liste los turnos comprometidos y provea opción de cancelación masiva y notificación automática. |
| **Entregabilidad deficiente de correos salientes** | Los correos de confirmación emitidos desde dominios no verificados caen en las carpetas de spam/correo no deseado de clientes y profesionales. | Media | Configurar y verificar los registros SPF, DKIM y DMARC del dominio oficial en Resend para garantizar alta reputación de entrega. |

---

## Fuentes

| Dato / afirmación | De dónde sale |
| :--- | :--- |
| Definición del público objetivo (profesionales independientes 1-3 empleados) | `01-minuta-kickoff.md` · "A quién le vendemos" |
| Promesa de valor de puesta en marcha en menos de 5 minutos y reserva en 30 segundos | `01-minuta-kickoff.md` · "Qué queremos que pase"; `03-especificacion-funcional-v0.3.md` · sección 9 |
| Regla mandataria de no requerir registro ni contraseña al cliente final | `01-minuta-kickoff.md` · "Los dos usuarios"; `03-especificacion-funcional-v0.3.md` · sección 2.2 |
| Perfiles de usuario (Laura, Carlos, Sofía) | `02-notas-entrevistas.md` · Entrevistas 1, 2 y 4; `03-especificacion-funcional-v0.3.md` · sección 2 |
| Estructura de reglas de disponibilidad semanal y reemplazo total al guardar | `03-especificacion-funcional-v0.3.md` · sección 4.1; `04-notas-tecnicas.md` · "los slots" |
| Generación dinámica de turnos disponibles en memoria | `04-notas-tecnicas.md` · "los slots" |
| Doble verificación de disponibilidad previa al insert de reserva | `03-especificacion-funcional-v0.3.md` · sección 5.2 (RN-02); `04-notas-tecnicas.md` · "la reserva" |
| Límite del plan gratuito en 10 clientes únicos y código de error `LIMIT_REACHED` | `03-especificacion-funcional-v0.3.md` · sección 7.1; `04-notas-tecnicas.md` · "limite del plan gratuito" |
| Denominación del botón comercial *"Más información sobre el Plan Pro"* | `05-hilo-mail-cambio-de-alcance.md` · Mail de Mariana (22/02/2026) |
| Proveedor de correos transaccionales Resend | `05-hilo-mail-cambio-de-alcance.md` · Mail de Diego (28/02/2026) |
| Exclusión del recordatorio previo al turno en el lanzamiento | `05-hilo-mail-cambio-de-alcance.md`; `transcripcion-reunion-2026-05-19.md` |
| URL real del sistema en producción (`https://cita-ai.vercel.app/`) | `documentacion para QA/nota-ambientes-y-accesos.md` · "la direccion" |
| Operación exclusiva sobre base única de producción (sin ambiente UAT activo) | `documentacion para QA/nota-ambientes-y-accesos.md` · "lo del ambiente de UAT" |
| Esquema de datos y nombres de tablas (`professionals`, `clients`, `appointments`, etc.) | `04-notas-tecnicas.md` · "tablas" |
| Parámetros de expiración de sesión (15 min token, 7 días refresh, 1 hora reset) | `04-notas-tecnicas.md` · "auth" |
| Latencias y métricas de rendimiento observadas (carga < 2s, P95 < 500 ms) | `04-notas-tecnicas.md` · "numeros de rendimiento" (estado actual medido) |
| Tasa de concurrencia teórica estimada en ~100 usuarios simultáneos | `04-notas-tecnicas.md` · "numeros de rendimiento" (estimación del desarrollador) |
| Precios, periodicidad y características detalladas del futuro Plan Pro | **Hipótesis** — no hay documento que lo respalde |
| Política de expiración o depuración de turnos históricos en la base | **Hipótesis** — no hay documento que lo respalde |
| Política de intentos fallidos antes de bloquear la cuenta del profesional | **Hipótesis** — no hay documento que lo respalde (`03-especificacion-funcional-v0.3.md` lo marca `TBD`) |

---

## Contradicciones detectadas

*   **Recordatorio automático el día anterior:**  
    *   *Qué dice la documentación inicial:* En `01-minuta-kickoff.md` y `03-especificacion-funcional-v0.3.md` (sección 6), el recordatorio por email es catalogado como requisito no negociable y pilar clave contra los no-shows.  
    *   *Qué dice la realidad y documentos recientes:* En `04-notas-tecnicas.md`, `05-hilo-mail-cambio-de-alcance.md`, `06-tickets-soporte-resumen.md` y `transcripcion-reunion-2026-05-19.md`, se constata que nunca fue programado debido a que el plan de Vercel no incluye tareas programadas (cron) y se lanzó a producción sin esta funcionalidad, convirtiéndose en el reclamo principal de los usuarios.  
    *   *Postura adoptada en el PRD:* Se registra como una funcionalidad **postergada y fuera del alcance del release actual**, manteniéndola como deuda técnica y riesgo de producto prioritario.
*   **Dominio oficial y URL del servicio:**  
    *   *Qué dicen los primeros documentos:* `01-minuta-kickoff.md` y `03-especificacion-funcional-v0.3.md` establecen que el sistema opera en `cita.ai` y los enlaces públicos son `cita.ai/[slug]`.  
    *   *Qué dice la realidad:* En `documentacion para QA/nota-ambientes-y-accesos.md` y la aplicación observada, se confirma que el dominio `cita.ai` no tiene configuradas las DNS y el servicio opera real y únicamente en `https://cita-ai.vercel.app/` (enlaces: `cita-ai.vercel.app/[slug]`).  
    *   *Postura adoptada en el PRD:* Se adopta la URL real en producción (`cita-ai.vercel.app`), que es la efectivamente utilizada por los clientes.
*   **Existencia de ambiente UAT aislado:**  
    *   *Qué dice la especificación:* `03-especificacion-funcional-v0.3.md` (sección 10) y notas iniciales de `04-notas-tecnicas.md` describen un ambiente UAT en `uat.cita.ai` con base de datos propia para homologación de QA.  
    *   *Qué dicen las notas de acceso:* `documentacion para QA/nota-ambientes-y-accesos.md` aclara explícitamente que dicho ambiente fue abandonado y hoy existe una única base en producción donde interactúan tanto pruebas como usuarios reales.  
    *   *Postura adoptada en el PRD:* Se documenta la realidad de un único entorno de producción, señalando la carencia de UAT como riesgo crítico para testing.
*   **Proveedor del servicio de emails:**  
    *   *Qué dicen los documentos previos:* `01-minuta-kickoff.md` mencionó SendGrid; `03-especificacion-funcional-v0.3.md` y `04-notas-tecnicas.md` indicaban el servicio transaccional por defecto de Supabase.  
    *   *Qué dice la decisión final:* En `05-hilo-mail-cambio-de-alcance.md`, Diego documenta la migración formal a **Resend** para todos los correos transaccionales del producto.  
    *   *Postura adoptada en el PRD:* Se define Resend como el proveedor oficial vigente.
*   **Texto y comportamiento del botón de límite del Plan Gratuito:**  
    *   *Qué decían las versiones anteriores:* *"Solicitar Upgrade"* en `01-minuta-kickoff.md`; *"Ver Opciones"* en `03-especificacion-funcional-v0.3.md`.  
    *   *Qué se resolvió:* En `05-hilo-mail-cambio-de-alcance.md`, Mariana definió unificarlo como *"Más información sobre el Plan Pro"*, ya que no existían planes pagos configurados.  
    *   *Postura adoptada en el PRD:* Se adopta la etiqueta *"Más información sobre el Plan Pro"*.

---

## Preguntas abiertas

*   **Comportamiento de bloqueos ante turnos preexistentes:** ¿Qué debe suceder funcionalmente si un profesional bloquea un rango de días en el que ya tiene turnos reservados? (¿Se cancelan automáticamente, se notifican por email o se mantienen visibles en el panel?). (`03-especificacion-funcional-v0.3.md` · 4.3; `06-tickets-soporte-resumen.md` #52; `transcripcion-reunion-2026-05-19.md`).
*   **Registro y penalización de ausencias (No-Shows):** ¿Se incorporará un estado formal *"No se presentó"* en la tabla de turnos o un campo de notas privadas para que el profesional lleve la trazabilidad de clientes que incumplieron? (`03-especificacion-funcional-v0.3.md` · 8; `06-tickets-soporte-resumen.md` #55, #71).
*   **Límite de anticipación máxima para reservas:** ¿Cuál es la ventana máxima a futuro en la que un cliente puede agendar (ej. 30 o 60 días)? Actualmente el calendario permite agendar sin límite hacia adelante (`03-especificacion-funcional-v0.3.md` · 5.2; `06-tickets-soporte-resumen.md` #63).
*   **Visibilidad de la URL pública en el Dashboard:** ¿En qué sección del panel del profesional se ubicará de forma fija la URL pública y el botón para copiarla, a fin de subsanar la principal causa de contacto a soporte? (`06-tickets-soporte-resumen.md` #38, #44; `transcripcion-reunion-2026-05-19.md`).
*   **Manejo de reservas por cuenta de terceros:** ¿Cómo debe registrar el sistema los casos donde quien reserva (ej. padre/madre) difiere de quien recibe el servicio (ej. alumno/hijo)? (`02-notas-entrevistas.md` #6; `06-tickets-soporte-resumen.md` #40; `transcripcion-reunion-2026-05-19.md`).
*   **Estructura y pasarela de cobro del Plan Pro:** ¿Cuáles serán los precios, períodos de facturación y pasarelas de pago (Stripe, Mercado Pago) para los profesionales que deseen superar los 10 clientes? (`01-minuta-kickoff.md`; `03-especificacion-funcional-v0.3.md` · 7.3).
*   **Estrategia para multi-profesionales:** ¿Cómo evolucionará el modelo de datos para permitir consultorios o salones con múltiples profesionales compartiendo una cuenta? (`01-minuta-kickoff.md`; `06-tickets-soporte-resumen.md` #22; `transcripcion-reunion-2026-05-19.md`).
