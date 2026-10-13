# Story: Como profesional, quiero ver un banner informativo con el botón "Más información sobre el Plan Pro" al alcanzar 10 clientes, para gestionar el crecimiento de mi negocio
**ID:** IPC-15
**Epic:** IPC-13
**Implementación:** Sin verificar
**Refinamiento:** Refinado
**Inspección QA:** Sin inspeccionar
**Estado de sincronización:** Sincronizado con Jira

## Descripción
Como profesional, quiero ver un banner informativo con el botón "Más información sobre el Plan Pro" al alcanzar 10 clientes, para gestionar el crecimiento de mi negocio.

## Análisis INVEST
| Criterio | Cumple | Observación |
| :--- | :--- | :--- |
| Independiente | Sí | Se prueba con 10 clientes precargados, sin depender del flujo de reserva. |
| Negociable | Sí | El texto, la ubicación y el estilo del aviso son ajustables. |
| Valiosa | Sí | Es el segundo punto de contacto comercial junto al correo de límite. |
| Estimable | Sí | Un aviso condicional más un botón con texto exacto ya definido. |
| Pequeña | Sí | Acotada al banner, el botón y el bloqueo de carga manual. |
| Testeable | Sí | Tras este refinamiento: cada escenario tiene un resultado observable en el panel. |

## Criterios de Aceptación (Gherkin)

### Escenario 1: Banner visible al alcanzar 10 clientes
**Given** que inicié sesión como profesional con 10 clientes únicos acumulados
**When** accedo a mi panel de administración
**Then** se muestra un aviso permanente visible pero no invasivo
**And** el aviso informa que alcancé el límite de 10 clientes

### Escenario 2: Botón con denominación oficial
**Given** que inicié sesión como profesional con 10 clientes únicos acumulados
**When** veo el aviso de límite en mi panel
**Then** el aviso incluye el botón "Más información sobre el Plan Pro"

### Escenario 3: Clic en el botón registra el interés
**Given** que inicié sesión como profesional con 10 clientes únicos acumulados
**And** veo el aviso de límite en mi panel
**When** presiono el botón "Más información sobre el Plan Pro"
**Then** mi interés comercial queda registrado
**And** el panel sigue operativo sin bloqueos

### Escenario 4: Carga manual bloqueada al superar el cupo
**Given** que inicié sesión como profesional con 10 clientes únicos acumulados
**When** intento cargar manualmente un cliente nuevo desde el panel
**Then** la operación es bloqueada
**And** se muestra el mensaje correspondiente sin guardar el cliente

### Escenario 5: Sin límite no hay banner
**Given** que inicié sesión como profesional con 9 clientes únicos acumulados
**When** accedo a mi panel de administración
**Then** no se muestra el aviso de límite
**And** la carga manual de clientes sigue habilitada

## Notas de QA
* Preparar profesionales de prueba con 9 y con 10 clientes únicos para cubrir ambos lados del umbral.
* Verificar que el aviso no bloquee la operatoria del panel: es informativo, no un modal bloqueante.
* Revisar el banner en móvil y escritorio.
* Rastrear dónde queda registrado el interés del botón (sin plan pago no hay destino comercial real).

## Fuentes
| Dato / afirmación | De dónde sale |
| :--- | :--- |
| Aviso permanente visible pero no invasivo al llegar a 10 clientes | `03-especificacion-funcional-v0.3.md` · sección 7.2 |
| Denominación oficial "Más información sobre el Plan Pro" | `05-hilo-mail-cambio-de-alcance.md` · Mail de Mariana (22/02/2026); `prd.md` · Feature 5 |
| El botón solo registra el interés (sin plan pago) | `03-especificacion-funcional-v0.3.md` · sección 7.3 |
| Bloqueo de carga manual con mensaje sin guardar | `03-especificacion-funcional-v0.3.md` · secciones 7.1 y 7.2 |
| Ubicación exacta del aviso dentro del panel | **Hipótesis** — ningún documento fija la sección del dashboard |
| Confirmación visible tras presionar el botón | **Hipótesis** — ningún documento describe qué ve el profesional tras el clic |

## Contradicciones detectadas
* **Texto del botón comercial:** `03-especificacion-funcional-v0.3.md` · sección 7.2 ("Ver Opciones") contra `05-hilo-mail-cambio-de-alcance.md` · Mail de Mariana 22/02/2026 ("Más información sobre el Plan Pro"). Se adopta la definición de Mariana por ser la decisión de producto vigente (ya registrada en `epic-tree.md`).

## Preguntas abiertas
* ¿En qué sección fija del panel aparece el aviso (principal, clientes, configuración)?
* ¿Qué ve el profesional tras presionar el botón (confirmación, formulario, nada)?
* ¿Dónde queda registrado el interés comercial para su seguimiento posterior?
* ¿Qué mensaje exacto se muestra ante la carga manual bloqueada?
