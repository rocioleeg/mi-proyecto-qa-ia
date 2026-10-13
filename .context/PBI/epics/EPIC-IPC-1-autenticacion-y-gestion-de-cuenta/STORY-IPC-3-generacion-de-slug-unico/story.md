# Story: Como profesional, quiero que se genere un slug público único automáticamente al registrarme, para tener mi URL personalizada
**ID:** IPC-3
**Epic:** IPC-1
**Implementación:** Sin verificar
**Refinamiento:** Refinado
**Inspección QA:** Sin inspeccionar
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
| Testeable | Sí | Se puede verificar con diversos conjuntos de caracteres, acentos, mayúsculas y nombres duplicados concurrentes o sucesivos. |

## Criterios de Aceptación (Gherkin)

### Escenario 1: Generación exitosa de slug estándar sin colisiones
**Given** que no existe ningún profesional registrado con el slug "carlos-rojas"
**When** un profesional completa su registro con el nombre "Carlos Rojas"
**Then** el sistema normaliza el nombre convirtiéndolo a minúsculas y reemplazando espacios por guiones
**And** asigna el slug "carlos-rojas" al perfil del profesional en la tabla "professionals"
**And** la URL pública "cita-ai.vercel.app/carlos-rojas" queda inmediatamente disponible para reservas

### Escenario 2: Normalización de nombre con acentos, caracteres especiales y mayúsculas
**Given** que no existe ningún profesional registrado con el slug "maria-jose-pena"
**When** un profesional completa su registro con el nombre "  María-José   Peña  "
**Then** el sistema elimina acentos, diacríticos y espacios sobrantes
**And** convierte los caracteres especiales a formato kebab-case estándar en minúsculas
**And** asigna el slug "maria-jose-pena" al profesional

### Escenario 3: Resolución de colisión con asignación de sufijo incremental
**Given** que ya existe un profesional registrado con el slug "ana-perez"
**When** un nuevo profesional se registra con el nombre "Ana Pérez"
**Then** el sistema detecta la existencia previa del slug base "ana-perez"
**And** genera y asigna automáticamente el slug incremental "ana-perez-2"
**And** la URL pública "cita-ai.vercel.app/ana-perez-2" queda asignada al nuevo profesional

### Escenario 4: Resolución de colisiones sucesivas múltiples
**Given** que ya existen profesionales registrados con los slugs "ana-perez" y "ana-perez-2"
**When** otro nuevo profesional se registra con el nombre "Ana Pérez"
**Then** el sistema verifica los slugs existentes en toda la plataforma
**And** genera y asigna automáticamente el siguiente sufijo incremental disponible "ana-perez-3"

## Notas de QA
* Verificar el comportamiento ante nombres con caracteres no ASCII (acentos, diéresis, ñ, apóstrofes).
* Validar que la unicidad del slug sea global en toda la plataforma (columna `slug` de `professionals` con restricción de unicidad).
* Probar la navegación directa a `cita-ai.vercel.app/[slug]` para confirmar que responde con la vista pública de auto-reserva sin requerir autenticación previa.

## Fuentes
| Dato / afirmación | De dónde sale |
| :--- | :--- |
| Reglas de normalización a minúsculas, sin acentos y guiones | `03-especificacion-funcional-v0.3.md` · sección 3.4 |
| Sufijo numérico incremental (`-2`, `-3`) ante colisiones | `03-especificacion-funcional-v0.3.md` · sección 3.4 |
| Formato de URL pública (`cita-ai.vercel.app/[slug]`) | `prd.md` · sección 1 ("Visión") y Feature 1 |
| Persistencia del campo `slug` en tabla `professionals` | `04-notas-tecnicas.md` · sección "tablas" |
| Edición o cambio manual posterior del slug desde el panel | **Hipótesis** — no hay documento que defina si el slug es editable post-registro |

## Contradicciones detectadas
* Ninguna detectada

## Preguntas abiertas
* ¿Puede el profesional editar o personalizar manualmente su slug desde los ajustes del perfil tras el registro inicial, o es inmutable una vez generado?
