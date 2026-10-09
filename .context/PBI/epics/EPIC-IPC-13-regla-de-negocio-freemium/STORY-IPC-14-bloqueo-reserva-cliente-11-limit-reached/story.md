# Story: Como sistema, quiero bloquear la reserva del cliente 11 con código LIMIT_REACHED, para hacer cumplir el límite de 10 clientes únicos del plan gratuito
**ID:** IPC-14
**Epic:** IPC-13
**Implementación:** Sin verificar
**Refinamiento:** Refinado
**Inspección QA:** Sin inspeccionar
**Estado de sincronización:** Sincronizado con Jira

## Descripción
Como sistema, quiero bloquear la reserva del cliente 11 con código LIMIT_REACHED, para hacer cumplir el límite de 10 clientes únicos del plan gratuito.

## Análisis INVEST
| Criterio | Cumple | Observación |
| :--- | :--- | :--- |
| Independiente | Sí | Se prueba a nivel de API con 10 clientes precargados, sin depender de otras historias. |
| Negociable | Sí | El umbral vive en una constante; el mensaje al cliente es ajustable. |
| Valiosa | Sí | Es el disparador de monetización del plan gratuito. |
| Estimable | Sí | Conteo por join más bloqueo con código exacto ya definidos. |
| Pequeña | Sí | Acotada al conteo, el bloqueo del cliente 11 y el mensaje en pantalla. |
| Testeable | Sí | Tras este refinamiento: cada escenario tiene un resultado observable en API o pantalla. |

## Criterios de Aceptación (Gherkin)

### Escenario 1: Cliente nuevo número 11 es bloqueado
**Given** que un profesional ya tiene 10 clientes únicos con reservas
**And** un cliente con correo no registrado para ese profesional elige un horario libre
**When** completa nombre y correo
**And** confirma la reserva
**Then** la API responde 403 con código LIMIT_REACHED
**And** no se crea ningún turno
**And** se muestra el mensaje "Este profesional no puede aceptar nuevos clientes a través de esta plataforma en este momento. Por favor, contactalo directamente."

### Escenario 2: Primeros 10 clientes siguen reservando sin límite
**Given** que un profesional ya tiene 10 clientes únicos con reservas
**And** uno de esos 10 elige un horario libre
**When** completa su nombre y su correo ya registrado
**And** confirma la reserva
**Then** el turno queda creado con estado confirmado
**And** no se muestra ningún aviso de límite

### Escenario 3: Mismo correo no suma un cliente nuevo
**Given** que un profesional tiene 9 clientes únicos con reservas
**When** uno de esos 9 confirma otro turno con su mismo correo
**Then** el turno queda creado con estado confirmado
**And** el conteo de clientes únicos sigue en 9

### Escenario 4: Carga manual desde el panel también se bloquea
**Given** que un profesional ya tiene 10 clientes únicos
**When** intenta cargar manualmente un cliente nuevo desde el panel
**Then** la operación es bloqueada
**And** se muestra el mensaje correspondiente sin guardar el cliente

### Escenario 5: Conteo por profesional ante correo compartido
**Given** que un correo ya reservó con otro profesional pero nunca con este
**And** este profesional ya tiene 10 clientes únicos
**When** ese correo intenta reservar con este profesional
**Then** la API responde 403 con código LIMIT_REACHED
**And** no se crea ningún turno

## Notas de QA
* Preparar el estado inicial con 10 clientes únicos reales por profesional de prueba (correos descartables distintos).
* Verificar el conteo por join contra `appointments`: el registro en `clients` es global por email y se comparte entre profesionales.
* Medir el costo del conteo en cada reserva: no hay índice dedicado y se ejecuta por request.
* El umbral está hardcodeado en `FREE_PLAN_CLIENT_LIMIT = 10`: cambiarlo exige tocar código y redeploy.
* Cada intento de prueba crea citas reales en producción: coordinar limpieza de turnos de prueba.

## Fuentes
| Dato / afirmación | De dónde sale |
| :--- | :--- |
| Límite de 10 clientes únicos identificados por correo | `03-especificacion-funcional-v0.3.md` · sección 7.1; `prd.md` · Feature 5 |
| Primeros 10 reservan sin restricción | `03-especificacion-funcional-v0.3.md` · sección 7.1; `prd.md` · Feature 5 |
| Respuesta 403 con código LIMIT_REACHED | `prd.md` · Flujo 4; `04-notas-tecnicas.md` · "limite del plan gratuito"; `system-design.md` · Endpoints |
| Mensaje exacto en pantalla para el cliente 11 | `03-especificacion-funcional-v0.3.md` · sección 7.2 |
| Bloqueo también en carga manual desde el panel | `03-especificacion-funcional-v0.3.md` · sección 7.1 |
| Conteo por profesional vía join con `appointments` (email global compartido) | `03-especificacion-funcional-v0.3.md` · sección 7.1; `04-notas-tecnicas.md` · "tablas" y "limite del plan gratuito" |
| Constante hardcodeada sin panel de configuración | `04-notas-tecnicas.md` · "limite del plan gratuito" |
| Mensaje exacto ante carga manual bloqueada | **Hipótesis** — ningún documento fija si repite el texto del cliente 11 u otro |

## Contradicciones detectadas
* Ninguna detectada

## Preguntas abiertas
* ¿Qué mensaje exacto se muestra ante la carga manual bloqueada en el panel?
* ¿El correo compartido entre profesionales genera algún aviso al profesional sobre el origen del cliente?
* ¿Se agregará un mecanismo para reactivar o depurar clientes inactivos y liberar cupo, o el límite es acumulativo e irreversible?
* ¿Cuáles serán los planes de pago y pasarelas asociados al Plan Pro?
