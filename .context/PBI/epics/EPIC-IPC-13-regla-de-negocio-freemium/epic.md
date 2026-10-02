# Epic: [Epic] Regla de Negocio Freemium (Límite de 10 Clientes Únicos)
**ID:** IPC-13
**Estado de sincronización:** Sincronizado con Jira
**Estado del trabajo:** To Do

## Descripción
Restricción de uso basada en la acumulación de un máximo de 10 clientes únicos por profesional en el plan gratuito. Los primeros 10 clientes pueden reservar turnos de forma ilimitada. Al intento de reserva del cliente número 11 (o carga manual desde el panel), la operación es bloqueada retornando error 403 con código `LIMIT_REACHED`, informando al cliente que contacte al profesional y activando en el panel del profesional un banner con el botón "Más información sobre el Plan Pro". Referencia: `prd.md` · Feature 5 y Flujo 4.

## User Stories
- [ ] IPC-14: Como sistema, quiero bloquear la reserva del cliente 11 con código LIMIT_REACHED, para hacer cumplir el límite de 10 clientes únicos del plan gratuito
- [ ] IPC-15: Como profesional, quiero ver un banner informativo con el botón "Más información sobre el Plan Pro" al alcanzar 10 clientes, para gestionar el crecimiento de mi negocio

## Fuentes
| Dato / afirmación | De dónde sale |
| :--- | :--- |
| Límite freemium de hasta 10 clientes únicos por profesional | `prd.md` · Feature 5; `03-especificacion-funcional-v0.3.md` · sección 7.1 |
| Identificación de clientes únicos por correo electrónico | `03-especificacion-funcional-v0.3.md` · sección 7.1; `04-notas-tecnicas.md` · "limite del plan gratuito" |
| Código de error HTTP 403 `LIMIT_REACHED` al cliente 11 | `prd.md` · Flujo 4; `04-notas-tecnicas.md` · "limite del plan gratuito" |
| Mensaje al cliente número 11 para contactar al profesional | `03-especificacion-funcional-v0.3.md` · sección 7.2 |
| Texto del botón comercial "Más información sobre el Plan Pro" | `05-hilo-mail-cambio-de-alcance.md` · Mail de Mariana (22/02/2026) |
| Bloqueo a la carga manual de clientes en el panel | `03-especificacion-funcional-v0.3.md` · sección 7.2 |
| Precios y funcionalidades definitivas del Plan Pro | **Hipótesis** — no hay documento que lo respalde |
