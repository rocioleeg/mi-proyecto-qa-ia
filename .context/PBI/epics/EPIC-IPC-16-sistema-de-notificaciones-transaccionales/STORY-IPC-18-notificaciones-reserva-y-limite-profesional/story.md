# Story: Como profesional, quiero recibir notificaciones por correo de nuevas reservas y límite alcanzado, para dar seguimiento a mi agenda y cupo comercial
**ID:** IPC-18
**Epic:** IPC-16
**Implementación:** Sin verificar
**Refinamiento:** Refinado
**Inspección QA:** Sin inspeccionar
**Estado de sincronización:** Sincronizado con Jira

## Descripción
Como profesional, quiero recibir notificaciones por correo de nuevas reservas y límite alcanzado, para dar seguimiento a mi agenda y cupo comercial.

## Análisis INVEST
| Criterio | Cumple | Observación |
| :--- | :--- | :--- |
| Independiente | Sí | Se prueba con reservas de ejemplo sin depender de otras historias. |
| Negociable | Sí | Las plantillas y el momento exacto del aviso de cupo son ajustables. |
| Valiosa | Sí | Da seguimiento a la agenda y alerta la oportunidad comercial del límite. |
| Estimable | Sí | Dos disparos vía Resend con contenido ya definido. |
| Pequeña | Sí | Acotada a los dos correos al profesional. |
| Testeable | Sí | Tras este refinamiento: cada escenario tiene un resultado observable en la casilla del profesional. |

## Criterios de Aceptación (Gherkin)

### Escenario 1: Aviso de nueva reserva con datos del cliente
**Given** que un cliente confirma una reserva en mi agenda
**When** el turno queda creado con estado confirmado
**Then** recibo un correo de aviso en mi casilla de profesional
**And** el correo incluye nombre, correo y horario del cliente

### Escenario 2: Correo de celebración al alcanzar el límite
**Given** que un cliente nuevo intenta reservar cuando ya tengo 10 clientes únicos
**When** su intento es bloqueado con LIMIT_REACHED
**Then** recibo un correo con tono de celebración por el crecimiento de mi negocio
**And** el correo avisa que alcancé el límite de 10 clientes del plan gratuito
**And** el correo aclara que mis clientes actuales pueden seguir reservando

### Escenario 3: Envíos por el proveedor vigente
**Given** que un cliente confirma una reserva en mi agenda
**When** el turno queda creado
**Then** el aviso se despacha a través de Resend

## Notas de QA
* Usar casillas descartables para el profesional de prueba y revisar también spam por el dominio de prueba no verificado.
* Verificar que el aviso llegue por cada reserva, sin duplicados ante reintentos del cliente.
* Coordinar con IPC-14: el correo de límite se dispara sobre el intento bloqueado del cliente 11.
* Cada prueba crea citas reales en producción: coordinar limpieza de turnos de prueba.

## Fuentes
| Dato / afirmación | De dónde sale |
| :--- | :--- |
| Aviso de reserva nueva con datos del cliente | `03-especificacion-funcional-v0.3.md` · sección 6 (tabla) |
| Correo de límite con tono de celebración y contenido | `03-especificacion-funcional-v0.3.md` · sección 7.2; `prd.md` · Flujo 4 |
| Disparo en paralelo al bloqueo del cliente 11 | `prd.md` · Flujo 4 |
| Proveedor Resend para correos del producto | `05-hilo-mail-cambio-de-alcance.md` · Mail de Diego (28/02/2026); `prd.md` · Feature 6 |

## Contradicciones detectadas
* Ninguna detectada

## Preguntas abiertas
* ¿El aviso de nueva reserva incluye también la duración del turno y el enlace del panel?
* ¿El correo de límite se reenvía si otro cliente nuevo vuelve a intentar, o es único al alcanzar el hito?
