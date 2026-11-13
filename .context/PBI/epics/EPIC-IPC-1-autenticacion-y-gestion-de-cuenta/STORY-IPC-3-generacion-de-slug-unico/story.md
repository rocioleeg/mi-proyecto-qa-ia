# Story: Como profesional, quiero que se genere un slug público único automáticamente al registrarme, para tener mi URL personalizada
**ID:** IPC-3
**Epic:** IPC-1
**Implementación:** Sin verificar
**Refinamiento:** Refinado
**Inspección QA:** Requiere cambios
**Estado de sincronización:** Sincronizado con Jira

## Descripción
Como profesional, quiero que se genere un slug público único automáticamente al registrarme, para tener mi URL personalizada.

## Análisis INVEST
| Criterio | Cumple | Observación |
| :--- | :--- | :--- |
| Independiente | Sí | La lógica de normalización y unicidad del slug se puede implementar y probar aisladamente. |
| Negociable | Sí | El formato de normalización y la convención del sufijo incremental son parametrizables. |
| Valiosa | Sí | Otorga al profesional una dirección web pública amigable y unívoca (`cita-ai.vercel.app/[slug]`) para compartir su agenda. |
| Estimable | Sí | Se basa en reglas estándar de sanitización de texto (kebab-case, transliteración) y verificación de colisión en base de datos. |
| Pequeña | Sí | Acotada a la generación del slug durante el registro y la resolución de nombres duplicados. |
| Testeable | Sí | Se puede verificar con diversos conjuntos de caracteres, acentos, mayúsculas y nombres duplicados concurrentes o sucesivos, siempre que la regla de normalización se acompañe de una tabla de entradas y salidas esperadas (ver Inspección QA, defecto 4). |

## Criterios de Aceptación (Gherkin)

### Regla de normalización (única, aplicada en todos los escenarios)
Sobre el nombre completo ingresado en el registro, en este orden:
1. Recortar los espacios de los extremos y colapsar los espacios consecutivos en uno solo.
2. Quitar acentos y diacríticos.
3. Convertir el texto a minúsculas.
4. Reemplazar los espacios por guiones, sin duplicar guiones y sin guiones en los extremos.

> [PENDIENTE: la conversión de `ñ` a `n` no la define ninguna fuente — ver Preguntas abiertas]
> [PENDIENTE: el tratamiento de apóstrofes, puntos, comas, ampersands, emojis y alfabetos no latinos no está definido — ver Preguntas abiertas]

### Escenario 1: Generación exitosa de slug estándar sin colisiones
**Given** que no existe ningún profesional registrado con el slug "carlos-rojas"
**When** un profesional completa su registro con el nombre "Carlos Rojas"
**Then** el sistema aplica la regla de normalización y obtiene "carlos-rojas"
**And** asigna el slug "carlos-rojas" al perfil del profesional en la tabla "professionals"
**And** la URL pública "cita-ai.vercel.app/carlos-rojas" resuelve a la página pública del profesional sin requerir autenticación (depende de IPC-8)

> [PENDIENTE: con el registro recién creado y sin disponibilidad configurada la URL resuelve, pero no ofrece turnos reservables; ver Preguntas abiertas]

### Escenario 2: Normalización de nombre con acentos, caracteres especiales y mayúsculas
**Given** que no existe ningún profesional registrado con el slug "maria-jose-pena"
**When** un profesional completa su registro con el nombre "  María-José   Peña  "
**Then** el sistema recorta los espacios de los extremos y colapsa los espacios consecutivos
**And** elimina los acentos y diacríticos y convierte el texto a minúsculas
**And** reemplaza los espacios por guiones sin duplicarlos, conservando el guion que ya existía entre "María" y "José"
**And** asigna el slug "maria-jose-pena" al profesional

> [PENDIENTE: la conversión de "Peña" a "pena" no está respaldada por ningún documento]

### Escenario 3: Resolución de colisión con asignación de sufijo incremental
**Given** que ya existe un profesional registrado con el slug "ana-perez"
**When** un nuevo profesional se registra con el nombre "Ana Pérez"
**Then** el slug base "ana-perez" ya está ocupado en la plataforma
**And** el sistema asigna automáticamente el slug incremental "ana-perez-2"
**And** la URL pública "cita-ai.vercel.app/ana-perez-2" queda asociada al nuevo profesional

### Escenario 4: Resolución de colisiones sucesivas múltiples
**Given** que ya existen profesionales registrados con los slugs "ana-perez" y "ana-perez-2"
**When** otro nuevo profesional se registra con el nombre "Ana Pérez"
**Then** el sistema asigna automáticamente el siguiente sufijo incremental libre "ana-perez-3"

### Escenario 5: Registro con un nombre que no produce caracteres representables
**Given** que no existe ningún profesional registrado con un slug derivado de ese nombre
**When** un profesional completa su registro con un nombre compuesto solo por emojis, símbolos o alfabeto no latino
**Then** el sistema no persiste un slug vacío ni una URL raíz
**And** [PENDIENTE: definir si se asigna un slug de respaldo derivado del correo o un identificador generado, o si el registro se rechaza con un mensaje]

### Escenario 6: Colisión con una ruta reservada de la aplicación
**Given** que las rutas de la aplicación (por ejemplo "/login", "/register", "/dashboard" y "/api") no pueden usarse como slug
**When** un profesional se registra con el nombre "Login"
**Then** el slug "login" no se asigna
**And** [PENDIENTE: definir si se aplica el sufijo incremental ("login-2") u otro criterio]
**And** la URL pública del profesional no intercepta ninguna ruta del sistema

> [PENDIENTE: la lista de rutas reservadas no está definida en ninguna fuente]

### Escenario 7: Registros simultáneos con el mismo nombre
**Given** que dos profesionales completan su registro con el nombre "Ana Pérez" de forma simultánea
**When** el sistema calcula el slug para ambos
**Then** la restricción de unicidad de la columna `slug` impide que los dos obtengan "ana-perez"
**And** [PENDIENTE: definir si el segundo recibe automáticamente el siguiente sufijo o un error con mensaje]

### Escenario 8: Nombre terminado en número
**Given** que no existe ningún profesional registrado con el slug "ana-perez-2"
**When** un profesional completa su registro con el nombre "Ana Pérez 2"
**Then** [PENDIENTE: definir si el número forma parte del slug ("ana-perez-2") y cómo se resuelve el cruce con los slugs que genera el sufijo incremental]

### Escenario 9: El profesional conoce su URL pública
**Given** que un profesional completó su registro
**When** accede a su panel de administración
**Then** el sistema le muestra su URL pública completa, lista para compartir

> [PENDIENTE: la ubicación y el formato de presentación en el panel no están definidos — ver Preguntas abiertas]

### Escenario 10: Nombre en el límite de longitud permitido
**When** un profesional completa su registro con un nombre de 100 caracteres
**Then** [PENDIENTE: definir la longitud máxima del slug y la regla de truncado; hoy la columna `slug` no tiene límite en el modelo de datos]

### Escenario 11: Acceso a la URL pública con mayúsculas
**When** un cliente accede a "cita-ai.vercel.app/Carlos-Rojas"
**Then** [PENDIENTE: definir si resuelve al perfil "carlos-rojas" mediante redirección o si responde un error]

### Escenario 12: Slug de una cuenta eliminada
**Given** que existía un profesional con el slug "ana-perez" y su cuenta fue eliminada
**When** un nuevo profesional se registra con el nombre "Ana Pérez"
**Then** [PENDIENTE: definir si el slug liberado se reutiliza ("ana-perez") o queda reservado de forma permanente ("ana-perez-2")]

## Notas de QA
* Verificar el comportamiento ante nombres con caracteres no ASCII (acentos, diéresis, ñ, apóstrofes), con una tabla de entradas y salidas esperadas.
* Validar que la unicidad del slug sea global en toda la plataforma (columna `slug` de `professionals` con restricción de unicidad, `unique` y `not null` según el modelo de datos).
* Validar el comportamiento ante registros simultáneos con el mismo nombre (violación de la restricción de unicidad).
* Probar la navegación directa a `cita-ai.vercel.app/[slug]` para confirmar que responde con la vista pública de auto-reserva sin requerir autenticación previa.
* Verificar que el profesional pueda consultar su propia URL pública (tickets de soporte #38 y #44: la URL se genera y no se muestra en el panel).
* Dependencias: el disparador del slug pertenece a IPC-2 (registro); la vista pública de `cita-ai.vercel.app/[slug]` a IPC-8; la existencia de turnos reservables a IPC-5 (disponibilidad configurada).

## Fuentes
| Dato / afirmación | De dónde sale |
| :--- | :--- |
| Reglas de normalización a minúsculas, sin acentos y guiones | `03-especificacion-funcional-v0.3.md` · sección 3.4 |
| Sufijo numérico incremental (`-2`, `-3`) ante colisiones | `03-especificacion-funcional-v0.3.md` · sección 3.4 |
| Formato de URL pública (`cita-ai.vercel.app/[slug]`) | `prd.md` · sección 1 ("Visión") y Feature 1 |
| Persistencia del campo `slug` en tabla `professionals` | `04-notas-tecnicas.md` · sección "tablas"; `system-design.md` · Entidades Clave |
| Restricción `unique` y `not null` de `professionals.slug` | `system-design.md` · Entidades Clave |
| Disponibilidad configurada después del registro y turnos calculados desde `availability_rules` | `prd.md` · Flujo 1, pasos 4-6; Feature 3 |
| Rutas `/login` y `/dashboard` de la aplicación | `prd.md` · Flujo 1, paso 1; §5 Seguridad |
| La URL pública no se muestra en el panel | `04-notas-tecnicas.md` · "la url publica"; `06-tickets-soporte-resumen.md` · #38 y #44 — **documental, no verificado por QA** |
| Riesgo de URL pública oculta y mitigación propuesta | `prd.md` · §6 Riesgos; § Preguntas abiertas |
| Longitud máxima del slug, rutas reservadas, regla de concurrencia y política de liberación | **Hipótesis** — no hay documento que las defina |
| Conversión de `ñ` a `n` y colapso de separadores repetidos | **Hipótesis** — derivadas del valor esperado del propio escenario, sin documento que las respalde |
| Edición o cambio manual posterior del slug desde el panel | **Hipótesis** — no hay documento que defina si el slug es editable post-registro |

## Contradicciones detectadas
* **Dominio de la URL pública (resuelta en el PRD).** `03-especificacion-funcional-v0.3.md` (3.4) ejemplifica con `cita.ai/carlos-rojas`, mientras que `prd.md` (Visión y Feature 1) y la aplicación operan en `cita-ai.vercel.app/[slug]`. Se tomó la versión del PRD, que documenta que el dominio `cita.ai` no tiene las DNS configuradas. La historia ya usaba la URL correcta.
* **URL disponible para reservas al registrarse.** El criterio original afirmaba que la URL queda "inmediatamente disponible para reservas" al completar el registro; el PRD (Flujo 1) ubica la configuración de disponibilidad después del registro y la Feature 3 calcula los turnos a partir de `availability_rules`, sin disponibilidad por defecto definida en ningún documento. Se corrigió el criterio a "la URL resuelve a la página pública" y el punto quedó como pregunta abierta.
* **Valor esperado de la ñ.** El Escenario 2 exige "Peña" → "pena", mientras que las Notas de QA de la misma historia listan la ñ entre los caracteres cuyo comportamiento hay que "verificar" y la especificación 3.4 solo habla de "quitar acentos". Se dejó el valor marcado como pendiente de decisión y no se resolvió por cuenta propia.

## Preguntas abiertas
* ¿La `ñ` se translitera a `n` (y con qué criterio se tratan otros caracteres no latinos y los apóstrofes)?
* ¿La URL pública debe ofrecer turnos reservables inmediatamente después del registro, es decir, existe una disponibilidad por defecto, o el profesional debe configurarla antes de compartir su enlace?
* ¿Puede el profesional editar o personalizar manualmente su slug desde los ajustes del perfil tras el registro inicial, o es inmutable una vez generado?
* ¿Qué slug recibe un nombre compuesto solo por símbolos, emojis o alfabeto no latino?
* ¿Cuál es la lista de rutas reservadas que un slug no puede ocupar y qué sufijo se aplica cuando hay colisión con una de ellas?
* ¿Qué ocurre si dos registros simultáneos generan el mismo slug base: se reintenta con el siguiente sufijo o se devuelve un error?
* ¿Cuál es la longitud máxima del slug y qué regla de truncado se aplica?
* ¿El slug de una cuenta eliminada se libera y puede reasignarse a otro profesional?
* ¿La URL pública distingue mayúsculas y minúsculas al resolver el slug?
* ¿Dónde se muestra la URL pública en el panel del profesional y con qué formato (texto, botón de copiado)?
