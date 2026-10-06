# Índice del Backlog

**Origen:** `.context/architecture/prd.md`  
**Fuentes del backlog:** Documentación local (`.context/architecture/prd.md`, `.context/Confluence-corporativo/`) y proyecto Jira `Prueba qa ia` (`IPC`)  
**Tipo de proyecto:** Brownfield  
**Fecha:** 2026-10-02  

| Epic | Stories | Sin verificar | Estado de sincronización |
| :--- | :--- | :--- | :--- |
| [IPC-1] [Epic] Autenticación y Gestión de Cuenta del Profesional | 3 | 3 | Sincronizado con Jira |
| [IPC-5] [Epic] Configuración de Disponibilidad y Bloqueos de Agenda | 2 | 2 | Sincronizado con Jira |
| [IPC-8] [Epic] Portal Público de Auto-Reserva | 2 | 2 | Sincronizado con Jira |
| [IPC-11] [Epic] Control de Concurrencia y Prevención de Dobles Reservas | 1 | 1 | Sincronizado con Jira |
| [IPC-13] [Epic] Regla de Negocio Freemium (Límite de 10 Clientes Únicos) | 2 | 2 | Sincronizado con Jira |
| [IPC-16] [Epic] Sistema de Notificaciones Transaccionales | 3 | 3 | Sincronizado con Jira |

## Epics identificadas, pendientes de desglosar
* Ninguna: el backlog está desglosado entero

## Pendiente de subir a Jira
* Ninguna: todo sincronizado

## Pendiente de verificar contra la aplicación
* `IPC-2`: Como profesional, quiero registrarme e iniciar sesión con email y contraseña, para acceder al panel de administración
* `IPC-3`: Como profesional, quiero que se genere un slug público único automáticamente al registrarme, para tener mi URL personalizada
* `IPC-4`: Como profesional, quiero recuperar mi contraseña mediante correo electrónico, para restablecer mi acceso si lo olvido
* `IPC-6`: Como profesional, quiero definir mi disponibilidad semanal por día y la duración del turno, para estructurar mi oferta horaria
* `IPC-7`: Como profesional, quiero registrar bloqueos de tiempo en mi agenda, para no recibir turnos durante ausencias o vacaciones
* `IPC-9`: Como cliente final, quiero visualizar la disponibilidad semanal en tiempo real, para elegir un horario que me convenga
* `IPC-10`: Como cliente final, quiero agendar un turno ingresando solo mi nombre y email, para confirmar la reserva sin crear cuenta
* `IPC-12`: Como sistema, quiero validar la disponibilidad antes de insertar la cita, para prevenir dobles reservas coincidentes
* `IPC-14`: Como sistema, quiero bloquear la reserva del cliente 11 con código LIMIT_REACHED, para hacer cumplir el límite de 10 clientes únicos del plan gratuito
* `IPC-15`: Como profesional, quiero ver un banner informativo con el botón "Más información sobre el Plan Pro" al alcanzar 10 clientes, para gestionar el crecimiento de mi negocio
* `IPC-17`: Como cliente final, quiero recibir un correo de confirmación con los detalles de mi turno, para tener un comprobante de la cita agendada
* `IPC-18`: Como profesional, quiero recibir notificaciones por correo de nuevas reservas y límite alcanzado, para dar seguimiento a mi agenda y cupo comercial
* `IPC-21`: Como cliente final, quiero recibir un correo de recordatorio 24 horas antes de mi turno, para no olvidar mi cita y reducir ausencias

## Contradicciones detectadas
* **Proveedor de correo transaccional:** `01-minuta-kickoff.md` mencionó SendGrid y `03-especificacion-funcional-v0.3.md` / `04-notas-tecnicas.md` indicaban Supabase; `05-hilo-mail-cambio-de-alcance.md` resolvió formalmente usar Resend. Se tomó Resend por ser la decisión de producto vigente.
* **Recordatorio del día anterior:** Requisito clave en `01-minuta-kickoff.md` y `03-especificacion-funcional-v0.3.md` (sección 6), pero en `04-notas-tecnicas.md` y `05-hilo-mail-cambio-de-alcance.md` se acordó que no entraba en el lanzamiento inicial v1.0. Se ha incorporado al backlog como User Story evolutiva `IPC-21` en la Epic `IPC-16`.
* **Texto del botón comercial de límite freemium:** `01-minuta-kickoff.md` ("Solicitar Upgrade"), `03-especificacion-funcional-v0.3.md` ("Ver Opciones") y `05-hilo-mail-cambio-de-alcance.md` ("Más información sobre el Plan Pro"). Se adoptó la definición de `05-hilo-mail-cambio-de-alcance.md`.

## Preguntas abiertas
* ¿Cuáles serán los planes de pago, tarifas y pasarelas de facturación asociados al Plan Pro una vez que se lance la suscripción comercial?
* ¿Se agregará en el panel del profesional un mecanismo para reactivar o depurar clientes inactivos para liberar cupo del plan gratuito, o el límite es estrictamente acumulativo e irreversible?
