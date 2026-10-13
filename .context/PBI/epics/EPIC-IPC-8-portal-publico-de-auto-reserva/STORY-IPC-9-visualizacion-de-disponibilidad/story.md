# Story: Como cliente final, quiero visualizar la disponibilidad semanal en tiempo real, para elegir un horario que me convenga
**ID:** IPC-9
**Epic:** IPC-8
**Implementación:** Sin verificar
**Refinamiento:** Refinado
**Inspección QA:** Sin inspeccionar
**Estado de sincronización:** Sincronizado con Jira

## Descripción
Como cliente final, quiero visualizar la disponibilidad semanal en tiempo real, para elegir un horario que me convenga.

## Análisis INVEST
| Criterio | Cumple | Observación |
| :--- | :--- | :--- |
| Independiente | Sí | Solo lectura pública; se prueba sin reservar ni autenticarse. |
| Negociable | Sí | El formato de la grilla y la navegación entre semanas son ajustables. |
| Valiosa | Sí | Es la puerta de entrada de la auto-reserva en 30 segundos. |
| Estimable | Sí | Un endpoint de cálculo más una vista con reglas responsivas ya definidas. |
| Pequeña | Sí | Acotada a consultar y renderizar la grilla semanal. |
| Testeable | Sí | Tras este refinamiento: cada escenario tiene un resultado observable en la página pública. |

## Criterios de Aceptación (Gherkin)

### Escenario 1: Visualizar la grilla semanal de un profesional
**Given** que accedo a la URL pública de un profesional con reglas de disponibilidad definidas
**When** se carga la página de reservas
**Then** se muestran los turnos libres de la semana calculados al momento
**And** cada turno indica día, fecha y hora de inicio

### Escenario 2: Turnos ocupados y bloqueos no se ofrecen
**Given** que un profesional tiene citas confirmadas y bloqueos registrados en su semana
**When** un cliente abre su URL pública
**Then** los horarios con citas confirmadas no aparecen como opciones
**And** los rangos bloqueados no aparecen como opciones

### Escenario 3: Vista móvil en español
**Given** que accedo a la URL pública desde un smartphone Android o iOS
**When** se carga la página de reservas
**Then** la grilla se adapta a la pantalla sin desplazamiento horizontal forzado
**And** las fechas y horas se muestran en idioma español

### Escenario 4: Profesional sin disponibilidad muestra estado vacío
**Given** que accedo a la URL pública de un profesional sin reglas de disponibilidad
**When** se carga la página de reservas
**Then** no se ofrece ningún turno
**And** se muestra un mensaje que indica que no hay horarios disponibles

### Escenario 5: Slug inexistente muestra error
**Given** que accedo a una URL pública con un slug que no existe
**When** se carga la página
**Then** no se muestra la grilla de reservas
**And** se muestra un mensaje que indica que el profesional no existe

## Notas de QA
* Verificar el cálculo contra `GET /api/public/availability` con parámetros `slug` y `date`, incluyendo reglas, citas confirmadas y bloqueos.
* Probar en Chrome, Firefox, Safari y Edge, más navegadores integrados de Instagram y WhatsApp.
* Validar fechas límite: cruces de mes, años bisiestos y semanas parciales.
* Revisar el huso horario: el cálculo usa `date-fns` con locale `es` y hay usuarios en Argentina, México y Chile.
* Medir carga inicial menor a 2 segundos y P95 de disponibilidad menor a 500 ms (valores medidos, no objetivos acordados).
* Usar cuentas de prueba: la página pública opera sobre la base única de producción.

## Fuentes
| Dato / afirmación | De dónde sale |
| :--- | :--- |
| Cálculo dinámico de intervalos libres para la semana | `prd.md` · Feature 3; `system-design.md` · Endpoints (`GET /api/public/availability`) |
| Descuento de citas confirmadas y bloqueos | `prd.md` · Feature 3; `03-especificacion-funcional-v0.3.md` · sección 5.2 (RN-01); `system-design.md` · ADR-02 |
| Acceso por URL pública sin autenticación | `prd.md` · Feature 3; `03-especificacion-funcional-v0.3.md` · sección 2.2 |
| Localización en español con `date-fns` locale `es` | `prd.md` · sección 5 ("Compatibilidad y Usabilidad") |
| Soporte móvil y navegadores modernos | `prd.md` · sección 5 ("Compatibilidad y Usabilidad") |
| Tiempos medidos (carga menor a 2 s, P95 menor a 500 ms) | `system-design.md` · Fuentes (medición del desarrollador, no objetivo acordado) |
| Mensaje exacto ante grilla vacía | **Hipótesis** — no hay documento que fije el texto |
| Comportamiento y mensaje ante slug inexistente | **Hipótesis** — no hay documento que lo defina |

## Contradicciones detectadas
* Ninguna detectada

## Preguntas abiertas
* ¿Qué mensaje exacto se muestra cuando el profesional no tiene disponibilidad?
* ¿Qué muestra la página ante un slug inexistente (error 404, mensaje, redirección)?
* ¿Cuál es la ventana máxima a futuro para navegar la grilla (hoy sin límite documentado)?
* ¿En qué huso horario ve el cliente los turnos si está en un país distinto al profesional?
