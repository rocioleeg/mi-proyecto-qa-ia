# Story: Como sistema, quiero validar la disponibilidad antes de insertar la cita, para prevenir dobles reservas coincidentes
**ID:** IPC-12
**Epic:** IPC-11
**Implementación:** Sin verificar
**Refinamiento:** Refinado
**Inspección QA:** Sin inspeccionar
**Estado de sincronización:** Sincronizado con Jira

## Descripción
Como sistema, quiero validar la disponibilidad antes de insertar la cita, para prevenir dobles reservas coincidentes.

## Análisis INVEST
| Criterio | Cumple | Observación |
| :--- | :--- | :--- |
| Independiente | Sí | Se prueba a nivel de API sin depender de la interfaz pública. |
| Negociable | Sí | El mecanismo (doble lectura hoy, RPC transaccional a futuro) es una decisión técnica abierta. |
| Valiosa | Sí | Protege la reputación del profesional ante superposiciones. |
| Estimable | Sí | Regla acotada al handler de reserva con mensaje exacto definido. |
| Pequeña | Sí | Acotada a la verificación previa al insert y el rechazo concurrente. |
| Testeable | Sí | Tras este refinamiento: cada escenario tiene un resultado observable en API o pantalla. |

## Criterios de Aceptación (Gherkin)

### Escenario 1: Segundo intento concurrente es rechazado con mensaje exacto
**Given** que dos clientes eligen el mismo horario libre casi al mismo tiempo
**And** ambos completan nombre y correo
**When** confirman la reserva simultáneamente
**Then** una de las reservas queda creada con estado confirmado
**And** la otra recibe el mensaje "¡Casi! Parece que alguien más reservó este horario. Por favor, elegí otro."

### Escenario 2: Datos ingresados se preservan ante el rechazo
**Given** que mi intento de reserva fue rechazado por concurrencia
**When** veo el mensaje de horario ya reservado
**Then** mi nombre y mi correo siguen completos en el formulario
**And** la grilla muestra los horarios actualizados para elegir otro

### Escenario 3: Reserva sin competencia se crea con normalidad
**Given** que elijo un horario libre sin otros clientes compitiendo
**When** completo nombre y correo
**And** confirmo la reserva
**Then** el turno queda creado con estado confirmado
**And** la respuesta de la API es 201

### Escenario 4: Reserva sobre horario ya ocupado es rechazada
**Given** que un horario ya tiene una cita confirmada
**When** otro cliente intenta confirmar ese mismo horario
**Then** la API rechaza la inserción
**And** se muestra el mensaje "¡Casi! Parece que alguien más reservó este horario. Por favor, elegí otro."

## Notas de QA
* Simular concurrencia real con requests paralelos al mismo slot sobre `POST /api/public/appointments`.
* La doble verificación achica la ventana de carrera pero no la cierra: documentar cualquier doble reserva observada como defecto crítico.
* La solución definitiva propuesta es un RPC transaccional en PostgreSQL (pregunta abierta de arquitectura).
* Sin rate limiting en el endpoint público: las pruebas de carga deben ser acotadas y con cuentas descartables.
* Cada intento de prueba crea citas reales en producción: limpiar o marcar los turnos de prueba.

## Fuentes
| Dato / afirmación | De dónde sale |
| :--- | :--- |
| Doble verificación al mostrar y al instante previo al insert | `prd.md` · Feature 4; `03-especificacion-funcional-v0.3.md` · sección 5.2 (RN-02) |
| Mensaje exacto de rechazo por concurrencia | `prd.md` · Feature 4; `03-especificacion-funcional-v0.3.md` · sección 5.2 (RN-02) |
| Preservación de los datos ingresados ante el rechazo | `prd.md` · Feature 4; `03-especificacion-funcional-v0.3.md` · sección 5.2 (RN-02) |
| Respuesta 201 ante reserva exitosa | `system-design.md` · Endpoints (`POST /api/public/appointments`) |
| La doble lectura no cierra la ventana de carrera | `04-notas-tecnicas.md` · "la reserva"; `system-design.md` · ADR-03 |
| Respuesta 409 ante slot tomado concurrentemente | `system-design.md` · Endpoints (`POST /api/public/appointments`) |

## Contradicciones detectadas
* Ninguna detectada

## Preguntas abiertas
* ¿El mensaje exacto de concurrencia viaja también en el cuerpo del 409, o solo en la pantalla?
* ¿Se migrará la inserción a un RPC transaccional con aislamiento serializable para cerrar la ventana de carrera?
* ¿Cuándo se incorporará rate limiting en `POST /api/public/appointments` para las pruebas de concurrencia y abuso?
