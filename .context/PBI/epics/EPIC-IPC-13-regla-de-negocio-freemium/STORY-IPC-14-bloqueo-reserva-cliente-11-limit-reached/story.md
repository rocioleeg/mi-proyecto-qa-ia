# Story: Como sistema, quiero bloquear la reserva del cliente 11 con código LIMIT_REACHED, para hacer cumplir el límite de 10 clientes únicos del plan gratuito
**ID:** IPC-14
**Epic:** IPC-13
**Implementación:** Sin verificar
**Refinamiento:** Borrador
**Inspección QA:** Sin inspeccionar
**Estado de sincronización:** Sincronizado con Jira

## Descripción
Como sistema, quiero bloquear la reserva del cliente 11 con código LIMIT_REACHED, para hacer cumplir el límite de 10 clientes únicos del plan gratuito.

## Criterios de Aceptación (Borrador)
- [ ] Conteo de clientes únicos acumulados por profesional basado en la dirección de correo electrónico en appointments.
- [ ] Clientes preexistentes (dentro de los primeros 10) pueden continuar reservando turnos de forma ilimitada.
- [ ] Al intento de reserva de un cliente número 11, la API retorna error 403 con código `LIMIT_REACHED`.
- [ ] La interfaz pública muestra al cliente el mensaje: "Este profesional no puede aceptar nuevos clientes a través de esta plataforma en este momento. Por favor, contactalo directamente."

## Fuentes
| Dato / afirmación | De dónde sale |
| :--- | :--- |
| Distinción de clientes únicos por email en appointments | `03-especificacion-funcional-v0.3.md` · sección 7.1; `04-notas-tecnicas.md` · "limite del plan gratuito" |
| Permiso de reservas ilimitadas para los primeros 10 clientes | `03-especificacion-funcional-v0.3.md` · sección 7.1 |
| Retorno HTTP 403 con código `LIMIT_REACHED` en `/api/public/appointments` | `04-notas-tecnicas.md` · "limite del plan gratuito"; `prd.md` · Flujo 4 |
| Mensaje amigable en pantalla para el cliente 11 | `03-especificacion-funcional-v0.3.md` · sección 7.2 |
