# Reporte de Inspección de Requisitos: Generación de slug público único

**Historia:** IPC-3
**Fecha:** 2026-11-12
**Estado de sincronización:** Sincronizado con Jira

## 1. Defectos Encontrados

| ID | Tipo | Descripción del Defecto | Sugerencia de Corrección |
| :--- | :--- | :--- | :--- |
| 1 | Contradicción interna | El Escenario 1 define la normalización solo como "minúsculas y espacios por guiones", mientras que el Escenario 2 y la especificación 3.4 exigen además quitar acentos y espacios sobrantes. Hay dos reglas distintas para el mismo cálculo dentro de la misma historia. | Unificar una única regla de normalización ordenada y referenciarla desde todos los escenarios. |
| 2 | Ambigüedad | "Reemplazar espacios por guiones" no define recorte de extremos ni colapso de separadores repetidos. Aplicada literalmente al nombre del Escenario 2 ("␠␠María-José␠␠␠Peña␠␠") no produce "maria-jose-pena", que es el valor que el propio escenario exige. | Escribir la regla completa: recortar extremos, colapsar espacios consecutivos en un único guion, no duplicar guiones y no dejar guiones en los extremos. |
| 3 | Valor no respaldado (hipótesis) | El Escenario 2 exige "Peña" → "pena". Ningún documento define la transliteración de la `ñ`: la especificación 3.4 solo habla de "quitar acentos" (la `ñ` no es un acento) y las Notas de QA de la propia historia la listan entre los caracteres cuyo comportamiento hay que "verificar". | Dejar el valor como `PENDIENTE` y pedir la decisión al Product Owner. Si se confirma la transliteración, incorporarla a la regla de normalización. |
| 4 | Ambigüedad / Testabilidad | "Convierte los caracteres especiales a formato kebab-case estándar" no define qué ocurre con apóstrofes, puntos, comas, ampersands, emojis ni alfabetos no latinos, y no existe una tabla de entradas y salidas esperadas. Sin ella el criterio no es reproducible. | Acompañar la regla con una tabla de casos (entrada → slug esperado) que cubra al menos apóstrofe ("O'Higgins"), punto ("Dr. Pérez"), ampersand, dígitos y alfabeto no latino. |
| 5 | Caso Borde | No se define qué ocurre si el nombre se normaliza a cadena vacía o solo a guiones (nombre compuesto únicamente por símbolos, emojis o alfabeto no latino). La validación de registro (3.1) solo exige "no vacío" y hasta 100 caracteres, sin restringir el juego de caracteres. | Definir un respaldo (slug derivado del correo o identificador generado) o un rechazo explícito del registro, y agregar el escenario. |
| 6 | Caso Borde | No existe una lista de slugs reservados. Un profesional llamado "Login" generaría el slug "login", y su URL pública `cita-ai.vercel.app/login` colisionaría con la ruta de inicio de sesión que el PRD documenta en el Flujo 1. Lo mismo aplica a `/register`, `/dashboard` y `/api`. | Definir las rutas reservadas y hacer que el generador las trate como colisión, aplicando el sufijo incremental. |
| 7 | Caso Borde | El espacio de nombres del sufijo no está separado del que producen los nombres: "Ana Pérez 2" genera el slug base "ana-perez-2", idéntico al que el Escenario 3 asigna por colisión. No se define qué pasa en ese cruce ni si el número forma parte del nombre. | Definir si el sufijo se calcula sobre el primer slug libre (`-2`, `-3`, …) y agregar el escenario del nombre terminado en número. |
| 8 | Caso Borde | Solo se cubren colisiones sucesivas (Escenarios 3 y 4), no simultáneas. Dos registros concurrentes con el mismo nombre calculan el mismo slug base, la restricción `unique` de `professionals.slug` hace fallar al segundo y la historia no define la reacción del sistema. | Definir el comportamiento ante la violación de unicidad (reintento con el siguiente sufijo o error con mensaje) y agregar el escenario de concurrencia. |
| 9 | Caso Borde | No se define si el slug se libera al eliminar la cuenta del profesional ni si puede reasignarse. Reutilizarlo dejaría enlaces ya compartidos en bios de Instagram o estados de WhatsApp apuntando a otra persona. | Definir la política de liberación y reutilización del slug (por ejemplo, reserva permanente) y dejarla como criterio. |
| 10 | Caso Borde / Completitud | No hay longitud máxima del slug. El nombre admite hasta 100 caracteres (3.1) y la columna `slug` es `text` sin límite en el modelo de datos, de modo que el slug puede superar los 100 caracteres y crecer todavía más con el sufijo. | Definir una longitud máxima y la regla de truncado, indicando si el truncado puede generar nuevas colisiones. |
| 11 | Contradice al PRD | El Escenario 1 afirma que la URL queda "inmediatamente disponible para reservas" al completar el registro. El PRD (Flujo 1) ubica la configuración de disponibilidad después del registro y la Feature 3 calcula los turnos a partir de `availability_rules`; ningún documento define una disponibilidad por defecto. Con el registro recién creado la URL resuelve, pero no ofrece turnos reservables. | Cambiar el criterio a "la URL resuelve a la página pública del profesional" y tratar la disponibilidad inicial como dependencia (IPC-5) o como decisión aparte. |
| 12 | Completitud / Valor no entregado | La historia no contiene ningún criterio sobre cómo el profesional conoce su slug, aunque ese es su beneficio declarado ("tener mi URL personalizada"). Los tickets de soporte #38 y #44 y las notas técnicas documentan que el slug se genera y no se muestra en ninguna parte del panel; el PRD lo registra como riesgo alto y deja abierta la ubicación del widget. | Agregar un criterio que muestre la URL pública al profesional, con la ubicación marcada como `PENDIENTE`, y referenciar la pregunta abierta del PRD. |
| 13 | Trazabilidad | El disparador "al registrarme" pertenece a IPC-2, y la entrega de valor depende de IPC-8 (vista pública) e IPC-5 (disponibilidad). La historia no declara esas dependencias y absorbe resultados de otras historias sin citarlas. | Declarar las dependencias con IPC-2, IPC-5 e IPC-8 en las Notas de QA y en los escenarios afectados. |
| 14 | Ambigüedad | No se define si la resolución de la URL distingue mayúsculas y minúsculas: el slug se genera en minúsculas, pero no se dice si `cita-ai.vercel.app/Carlos-Rojas` debe resolver al mismo profesional. | Definir la regla de resolución (por ejemplo, redirección a la versión en minúsculas) y dejarla como criterio verificable. |
| 15 | Testabilidad | Los Escenarios 3 y 4 exigen "el sistema detecta la existencia previa del slug base" y "verifica los slugs existentes en toda la plataforma", que son comportamientos internos no observables desde fuera. | Reformular el resultado observable (el slug asignado y la URL que resuelve) y dejar la verificación interna como nota técnica. |

## 2. Versión Corregida de la Historia

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
| Testeable | Sí | Se puede verificar con diversos conjuntos de caracteres, acentos, mayúsculas y nombres duplicados concurrentes o sucesivos, siempre que la regla de normalización se acompañe de una tabla de entradas y salidas esperadas (ver defecto 4). |

## Criterios de Aceptación (Gherkin)

### Regla de normalización (única, aplicada en todos los escenarios)
Sobre el nombre completo ingresado en el registro, en este orden:
1. Recortar los espacios de los extremos y colapsar los espacios consecutivos en uno solo.
2. Quitar acentos y diacríticos.
3. Convertir el texto a minúsculas.
4. Reemplazar los espacios por guiones, sin duplicar guiones y sin guiones en los extremos.

> `PENDIENTE`: la conversión de `ñ` a `n` no la define ninguna fuente (ver Preguntas abiertas).
> `PENDIENTE`: el tratamiento de apóstrofes, puntos, comas, ampersands, emojis y alfabetos no latinos no está definido (ver Preguntas abiertas).

### Escenario 1: Generación exitosa de slug estándar sin colisiones
**Given** que no existe ningún profesional registrado con el slug "carlos-rojas"
**When** un profesional completa su registro con el nombre "Carlos Rojas"
**Then** el sistema aplica la regla de normalización y obtiene "carlos-rojas"
**And** asigna el slug "carlos-rojas" al perfil del profesional en la tabla "professionals"
**And** la URL pública "cita-ai.vercel.app/carlos-rojas" resuelve a la página pública del profesional sin requerir autenticación (depende de IPC-8)

> `PENDIENTE`: con el registro recién creado y sin disponibilidad configurada la URL resuelve, pero no ofrece turnos reservables (ver Preguntas abiertas).

### Escenario 2: Normalización de nombre con acentos, caracteres especiales y mayúsculas
**Given** que no existe ningún profesional registrado con el slug "maria-jose-pena"
**When** un profesional completa su registro con el nombre "  María-José   Peña  "
**Then** el sistema recorta los espacios de los extremos y colapsa los espacios consecutivos
**And** elimina los acentos y diacríticos y convierte el texto a minúsculas
**And** reemplaza los espacios por guiones sin duplicarlos, conservando el guion que ya existía entre "María" y "José"
**And** asigna el slug "maria-jose-pena" al profesional

> `PENDIENTE`: la conversión de "Peña" a "pena" no está respaldada por ningún documento.

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
**And** `PENDIENTE`: definir si se asigna un slug de respaldo derivado del correo o un identificador generado, o si el registro se rechaza con un mensaje

### Escenario 6: Colisión con una ruta reservada de la aplicación
**Given** que las rutas de la aplicación (por ejemplo "/login", "/register", "/dashboard" y "/api") no pueden usarse como slug
**When** un profesional se registra con el nombre "Login"
**Then** el slug "login" no se asigna
**And** `PENDIENTE`: definir si se aplica el sufijo incremental ("login-2") u otro criterio
**And** la URL pública del profesional no intercepta ninguna ruta del sistema

> `PENDIENTE`: la lista de rutas reservadas no está definida en ninguna fuente.

### Escenario 7: Registros simultáneos con el mismo nombre
**Given** que dos profesionales completan su registro con el nombre "Ana Pérez" de forma simultánea
**When** el sistema calcula el slug para ambos
**Then** la restricción de unicidad de la columna `slug` impide que los dos obtengan "ana-perez"
**And** `PENDIENTE`: definir si el segundo recibe automáticamente el siguiente sufijo o un error con mensaje

### Escenario 8: Nombre terminado en número
**Given** que no existe ningún profesional registrado con el slug "ana-perez-2"
**When** un profesional completa su registro con el nombre "Ana Pérez 2"
**Then** `PENDIENTE`: definir si el número forma parte del slug ("ana-perez-2") y cómo se resuelve el cruce con los slugs que genera el sufijo incremental

### Escenario 9: El profesional conoce su URL pública
**Given** que un profesional completó su registro
**When** accede a su panel de administración
**Then** el sistema le muestra su URL pública completa, lista para compartir

> `PENDIENTE`: la ubicación y el formato de presentación en el panel no están definidos (ver Preguntas abiertas).

### Escenario 10: Nombre en el límite de longitud permitido
**When** un profesional completa su registro con un nombre de 100 caracteres
**Then** `PENDIENTE`: definir la longitud máxima del slug y la regla de truncado; hoy la columna `slug` no tiene límite en el modelo de datos

### Escenario 11: Acceso a la URL pública con mayúsculas
**When** un cliente accede a "cita-ai.vercel.app/Carlos-Rojas"
**Then** `PENDIENTE`: definir si resuelve al perfil "carlos-rojas" mediante redirección o si responde un error

### Escenario 12: Slug de una cuenta eliminada
**Given** que existía un profesional con el slug "ana-perez" y su cuenta fue eliminada
**When** un nuevo profesional se registra con el nombre "Ana Pérez"
**Then** `PENDIENTE`: definir si el slug liberado se reutiliza ("ana-perez") o queda reservado de forma permanente ("ana-perez-2")

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
| La URL pública no se muestra en el panel | `04-notas-tecnicas.md` · "la url publica"; `06-tickets-soporte-resumen.md` · #38 y #44 — documental, no verificado por QA |
| Riesgo de URL pública oculta y mitigación propuesta | `prd.md` · §6 Riesgos; § Preguntas abiertas |
| Longitud máxima del slug, rutas reservadas, regla de concurrencia y política de liberación | **Hipótesis** — no hay documento que las defina |
| Conversión de `ñ` a `n` y colapso de separadores repetidos | **Hipótesis** — derivadas del valor esperado del propio escenario, sin documento que las respalde |
| Edición o cambio manual posterior del slug desde el panel | **Hipótesis** — no hay documento que defina si el slug es editable post-registro |

## Contradicciones detectadas

* **Dominio de la URL pública (resuelta en el PRD).** `03-especificacion-funcional-v0.3.md` (3.4) ejemplifica con `cita.ai/carlos-rojas`, mientras que `prd.md` (Visión y Feature 1) y la aplicación operan en `cita-ai.vercel.app/[slug]`. Se tomó la versión del PRD, que documenta que el dominio `cita.ai` no tiene las DNS configuradas. La historia ya usaba la URL correcta, así que no se modificó.
* **URL disponible para reservas al registrarse.** El criterio original afirmaba que la URL queda "inmediatamente disponible para reservas" al completar el registro. El PRD (Flujo 1) ubica la configuración de disponibilidad después del registro, la Feature 3 calcula los turnos a partir de `availability_rules` y ningún documento define una disponibilidad por defecto. Se tomó el PRD: el criterio se corrigió a "la URL resuelve a la página pública" y el punto quedó como pregunta abierta, porque la decisión de producto sigue sin existir.
* **Valor esperado de la `ñ`.** El Escenario 2 exige "Peña" → "pena", mientras que las Notas de QA de la propia historia listan la `ñ` entre los caracteres cuyo comportamiento hay que "verificar" y la especificación 3.4 solo habla de "quitar acentos". Se citan ambas versiones, el valor quedó marcado como `PENDIENTE` y no se resolvió por cuenta propia.
* **Visibilidad de la URL pública.** El PRD promete una "URL pública personalizada" y registra el ocultamiento en el panel como riesgo alto con una mitigación propuesta (widget con botón de copiado), pero a la vez deja la ubicación como pregunta abierta. `04-notas-tecnicas.md` y los tickets #38 y #44 describen la situación opuesta a la promesa. Se documentó como defecto 12 y no se resolvió.

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

## 3. Valoración de Calidad
*   **Veredicto:** Requiere cambios
*   **Riesgo:** Medio

El veredicto sale de la segunda regla de la tabla: hay defectos que se corrigen leyendo las fuentes (normalización incompleta, resultados internos no observables, dependencias no declaradas, criterio de disponibilidad inmediata) y quedan preguntas abiertas que afectan criterios de aceptación (transliteración de la `ñ`, nombre sin caracteres representables, rutas reservadas, concurrencia, longitud, liberación del slug y visibilidad en el panel). No se aplica `Bloqueante` porque la contradicción con el PRD está resuelta por el propio PRD —la Feature 3 y el Flujo 1 son explícitos sobre el orden entre registro y configuración de disponibilidad— y por lo tanto el criterio se pudo corregir leyendo, sin decisión pendiente sobre cuál de las dos versiones vale.

El riesgo es Medio: el slug es la identidad pública del profesional y su enlace se comparte en biografías y estados de WhatsApp, de modo que un error de unicidad, de reutilización o de resolución deja enlaces apuntando al lugar equivocado; pero no compromete autenticación ni datos sensibles, y la generación es una operación de un solo paso sobre el registro.

## Nota sobre el análisis 5

La historia trae `**Implementación:** Sin verificar` y su tabla de Fuentes no contiene filas `Observado`. Por eso el análisis contra el comportamiento observado **no aplica**: no se navegó la aplicación en esta inspección. Los tickets #38 y #44 y las notas técnicas se citan como evidencia documental —reportes de usuarios y notas del desarrollador—, no como comportamiento observado por QA. Si se quiere convertir el defecto 12 en un hallazgo contra el sistema, hay que abrir la aplicación con una cuenta de prueba y verificar si el slug se muestra en el panel.
