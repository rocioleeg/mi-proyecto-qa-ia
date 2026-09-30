# Contexto de Mercado: Cita.ai

## 1. Panorama Competitivo (Competitive Landscape)
| Competidor | Fortalezas | Debilidades | Diferenciador vs Nosotros |
| :--- | :--- | :--- | :--- |
| **Calendly** | Ampliamente conocido globalmente. Interfaz muy intuitiva para agendar reuniones de trabajo. | Diseñado para reuniones corporativas; no gestiona lista de clientes/pacientes ni historial de servicios (`02-notas-entrevistas.md` · Entrevista 7). | Cita.ai está enfocado 100% en profesionales de servicios independientes que necesitan gestionar clientes y turnos, no reuniones de oficina (`01-minuta-kickoff.md` · "Contra quién competimos"). |
| **Acuity Scheduling** (Squarespace) | Plataforma sumamente potente con múltiples opciones de configuración y flujos complejos. | Demasiado complejo de configurar; abruma y desmotiva a profesionales independientes sin perfil técnico (`01-minuta-kickoff.md`; `03-especificacion-funcional-v0.3.md` · sección 1). | Cita.ai ofrece simplicidad radical con puesta en marcha en menos de 5 minutos y cero sobrecarga de configuración (`01-minuta-kickoff.md`). |
| **SimplyBook.me** | Enfocado en servicios y cuenta con un plan gratuito inicial. | Interfaz percibida como anticuada; sistema de complementos (add-ons) confuso para el usuario (`01-minuta-kickoff.md`). | Cita.ai ofrece una experiencia minimalista, limpia y fluida sin módulos opcionales confusos (`01-minuta-kickoff.md`). |
| **Status Quo (WhatsApp / Cuaderno / Google Calendar)** | Soluciones gratuitas con hábitos profundamente arraigados en el profesional. | Genera dobles reservas, ausencias (no-shows) y pérdidas de hasta 4 horas semanales de coordinación manual por chat (`01-minuta-kickoff.md`; `02-notas-entrevistas.md` · Entrevista 1 y 2). | Cita.ai automatiza la reserva en 30 segundos mediante una URL pública, eliminando el "ping-pong" de mensajes por WhatsApp (`01-minuta-kickoff.md`). |

## 2. Oportunidad de Mercado
*   **Tamaño/Tendencia:**
    *   Mercado global de software de agendamiento estimado en ~400 millones de USD (2024), con crecimiento proyectado de entre 11 % y 13 % anual (`01-minuta-kickoff.md` · "El tamaño de la torta").
    *   Foco en el segmento de profesionales independientes y microempresas (1 a 3 empleados) que cobran por tiempo u hora en América Latina y mercados de habla hispana (`01-minuta-kickoff.md` · "A quién le vendemos").
    *   Meta comercial de validación de los primeros 2 años: alcanzar entre 5.000 y 10.000 usuarios activos en el plan gratuito (`01-minuta-kickoff.md` · "El tamaño de la torta").
*   **Gap de Mercado:**
    *   Posicionamiento estratégico: *"Acuity es demasiado complejo y Calendly es demasiado básico"* (`01-minuta-kickoff.md` · "Contra quién competimos").
    *   Existe una necesidad no atendida de herramientas de reserva ultrasimples diseñadas para la relación *profesional-cliente*, que no requieran que el cliente final cree una cuenta ni que el profesional configure reglas complejas (`01-minuta-kickoff.md`; `03-especificacion-funcional-v0.3.md`).

## 3. Nuestra Ventaja Injusta (Unfair Advantage)
*   **Simplicidad Radical y Onboarding Inmediato:** Promesa de registro y puesta en marcha en menos de 5 minutos, y flujo de reserva pública en 30 segundos sin obligar al cliente a crear cuenta o contraseña (`01-minuta-kickoff.md`; `03-especificacion-funcional-v0.3.md` · sección 2.2).
*   **Diseñado para Canales Existentes (WhatsApp e Instagram):** Cada profesional cuenta con su propia URL pública (`cita-ai.vercel.app/[slug]`) pensada para integrarse directamente en la bio de Instagram o estados de WhatsApp, ordenando la demanda existente sin intentar convertirse en un marketplace (`01-minuta-kickoff.md` · "Cómo llega el cliente").

## 4. Riesgos y Supuestos
*   **Resistencia al Cambio y Barrera Comercial:** El costo de cambiar desde soluciones tradicionales "suficientemente buenas" (cuaderno o Google Calendar) es alto, y la adquisición de usuarios requiere esfuerzo comercial constante (`01-minuta-kickoff.md` · "Lo que nos va a costar entrar").
*   **Promesa de Valor Incompleta por Ausencia de Recordatorios:** El recordatorio automático por email el día anterior al turno se identificó como el 50% de la solución contra no-shows, pero fue excluido del lanzamiento por limitaciones técnicas en Vercel, generando reclamos en soporte (`05-hilo-mail-cambio-de-alcance.md`; `06-tickets-soporte-resumen.md`; `documentacion para QA/transcripcion-reunion-2026-05-19.md`).
*   **Fricción de Usabilidad en el Panel:** La URL pública no se muestra en el dashboard del profesional, provocando que los nuevos usuarios no sepan cómo compartir su agenda y afectando la promesa de 5 minutos (`06-tickets-soporte-resumen.md` #38, #44; `documentacion para QA/transcripcion-reunion-2026-05-19.md`).
*   **Riesgo de Operación sin Ambiente de Pruebas:** Inexistencia de un entorno UAT aislado (las pruebas corren en producción directa) y falta de tests automatizados (`04-notas-tecnicas.md`; `documentacion para QA/nota-ambientes-y-accesos.md`).

## Fuentes
| Dato / afirmación | De dónde sale |
| :--- | :--- |
| Comparativa de competidores (Calendly, Acuity, SimplyBook) | `01-minuta-kickoff.md` · "Contra quién competimos"; `02-notas-entrevistas.md` · Entrevista 7 |
| Estimaciones cualitativas del mercado global ($400M USD, 11-13% crecimiento) | `01-minuta-kickoff.md` · "El tamaño de la torta" |
| Meta de 5.000 a 10.000 usuarios activos a 2 años | `01-minuta-kickoff.md` · "El tamaño de la torta" |
| Eslogan de posicionamiento ("Acuity es complejo, Calendly es básico") | `01-minuta-kickoff.md` · "Contra quién competimos" |
| Promesa de onboarding de 5 minutos y reserva en 30 segundos | `01-minuta-kickoff.md` · "Qué queremos que pase"; `02-notas-entrevistas.md` · Entrevista 4 |
| Exclusión del recordatorio del día anterior por límites de Vercel | `05-hilo-mail-cambio-de-alcance.md`; `documentacion para QA/transcripcion-reunion-2026-05-19.md` |
| Problema de visibilidad del link público en el panel de control | `06-tickets-soporte-resumen.md` #38, #44; `documentacion para QA/transcripcion-reunion-2026-05-19.md` |
| Métricas de conversión reales hacia el futuro Plan Pro pago | **Hipótesis** — no hay documento que lo respalde |
| Tendencias de penetración de nuevas herramientas de IA en el rubro | **Hipótesis** — no hay documento que lo respalde |

## Contradicciones detectadas
*   **Valor del Recordatorio vs. Estado de Implementación:** `01-minuta-kickoff.md` y `03-especificacion-funcional-v0.3.md` establecen que el recordatorio automático el día anterior es un pilar fundamental para resolver el problema de ausencias (no-shows). Sin embargo, `05-hilo-mail-cambio-de-alcance.md` y `documentacion para QA/transcripcion-reunion-2026-05-19.md` confirman que se decidió salir a producción sin esta funcionalidad debido a costos y limitaciones técnicas de Vercel Cron, convirtiéndose en el reclamo principal de los usuarios en soporte (`06-tickets-soporte-resumen.md`).
*   **Estimaciones de Mercado e Hipótesis de Negocio:** En `01-minuta-kickoff.md`, las cifras de $400M USD y el crecimiento del 11-13% se mencionan explícitamente con la advertencia de ser "estimaciones públicas no tomadas como verdad", mientras que en discusiones posteriores se usan como referencia general del tamaño del mercado.

## Preguntas abiertas
*   ¿Cuándo y cómo se implementará técnicamente la funcionalidad de recordatorios automáticos por email/WhatsApp para cerrar la brecha con la propuesta de valor prometida? (`05-hilo-mail-cambio-de-alcance.md`; `documentacion para QA/transcripcion-reunion-2026-05-19.md`).
*   ¿Qué herramientas de analítica se utilizarán para validar las métricas clave de éxito (retención a 4 semanas > 40% y auto-reserva > 60%)? (`01-minuta-kickoff.md` · "Qué queremos que pase"; `05-hilo-mail-cambio-de-alcance.md`).
*   ¿Cómo responderá Cita.ai si competidores como Calendly o Acuity simplifican sus flujos de onboarding o lanzan planes enfocados en profesionales independientes de habla hispana? (`01-minuta-kickoff.md`).
