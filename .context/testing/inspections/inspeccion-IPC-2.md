# Reporte de Inspección de Requisitos: Registro e inicio de sesión del profesional

**Historia:** IPC-2
**Fecha:** 2026-11-11
**Estado de sincronización:** Sincronizado con Jira

## 1. Defectos Encontrados

| ID | Tipo | Descripción del Defecto | Sugerencia de Corrección |
| :--- | :--- | :--- | :--- |
| 1 | Contradicción entre fuentes | El destino posterior al registro no coincide: `prd.md` (Flujo 1, pasos 4-5) y la historia dicen "panel de administración"; `03-especificacion-funcional-v0.3.md` (3.1, paso 3) dice "asistente de configuración inicial". | Confirmar con el Product Owner y alinear el Escenario 1 con una sola versión. |
| 2 | Contradicción entre fuentes / Seguridad | El mensaje "El correo electrónico ya se encuentra registrado" revela si una dirección existe, en contradicción con el principio anti-enumeración que la especificación aplica en 3.3 para recuperación de contraseña. Además, el texto difiere del documentado en 3.1 ("El email ya está en uso" con enlace a recuperación). | Decidir la política: mensaje neutro con enlace a login/recuperación, o mantener el mensaje explícito y aceptar la enumeración. Unificar el texto en la especificación. |
| 3 | Caso Borde / Completitud | No existe escenario de inicio de sesión con credenciales incorrectas, aunque la especificación 3.2 define el comportamiento y el mensaje genérico. | Agregar escenario de login fallido con el mensaje "Email o contraseña incorrectos", sin indicar cuál dato falló. |
| 4 | Caso Borde / Completitud | No se definen los casos de contraseña inválida (menos de 8 caracteres, sin mayúscula, sin número) ni los mensajes por cada regla. La especificación solo documenta "La contraseña es muy corta". | Agregar un escenario por cada regla incumplida y definir el mensaje correspondiente. |
| 5 | Caso Borde / Completitud | No se definen límites ni formato de los datos de entrada: nombre vacío o mayor a 100 caracteres, correo con formato inválido o mayor a 254 caracteres. La especificación 3.1 sí los establece. | Incorporar las validaciones de la tabla de 3.1 como criterios verificables. |
| 6 | Ambigüedad | No se define la normalización del correo electrónico: espacios al inicio/fin, mayúsculas/minúsculas y su impacto en la unicidad. | Definir si el correo se recorta y compara sin distinguir mayúsculas, y reflejarlo en el criterio. |
| 7 | Completitud | Los criterios no cubren la expiración ni la renovación de sesión, aunque las Notas de QA y el PRD mencionan JWT de 15 minutos y refresh token de 7 días. Sin escenario no se puede verificar. | Agregar escenario de renovación a los 15 minutos y de sesión vencida a los 7 días con redirección a `/login`. |
| 8 | Completitud | No se define el cierre de sesión ni la invalidación del token. El ticket de soporte #69 reporta que, tras cerrar sesión, el panel siguió accesible sin pedir contraseña. | Definir el criterio de logout (qué se borra, a dónde redirige) y verificar el caso reportado en #69 contra la aplicación. |
| 9 | Resiliencia | No se define qué ocurre si Supabase Auth falla (error 500 o timeout) durante el registro o el login, ni si el registro se completa cuando falla el envío del correo de bienvenida. | Definir el comportamiento ante fallos: si el alta queda creada, qué mensaje se muestra y si hay reintento. |
| 10 | Trazabilidad / Dependencia | El Escenario 1 incluye la generación del slug (núcleo de IPC-3) y el envío del correo de bienvenida (núcleo de IPC-16) sin declararlos como dependencias. | Declarar la dependencia con IPC-3 e IPC-16, o mover esos resultados a sus historias y dejar aquí solo la referencia. |
| 11 | Seguridad / Requisito no funcional | No hay criterio de rate limiting ni de bloqueo por intentos fallidos de login. La especificación lo deja como `TBD` (3.3) y el PRD lo registra como hipótesis sin decisión. | Abrir la decisión de negocio (umbral y duración del bloqueo) y agregarla como criterio cuando se resuelva. |
| 12 | Testabilidad | "Se establecen las cookies seguras de sesión JWT httpOnly" no es observable desde la interfaz y no se indica cómo ni dónde verificarlo, ni el entorno HTTPS requerido para la bandera `Secure`. | Especificar la verificación técnica (encabezado `Set-Cookie` o DevTools) y el entorno donde se valida. |
| 13 | Ambigüedad | Solo se nombran las rutas `/dashboard` y `/login`; la URL de la página de registro no está definida, aunque los escenarios la referencian. | Definir la ruta de registro (por ejemplo `/register` o `/signup`) o indicar que se llega desde `/login`. |
| 14 | Supuesto no declarado | El Escenario 1 asume que el alta deja sesión iniciada de inmediato, lo que exige que Supabase no pida confirmación de correo. Ese supuesto no está escrito. | Dejar explícito que la confirmación de correo está deshabilitada, o agregar el escenario de confirmación previa si se habilita. |

## 2. Versión Corregida de la Historia

# Story: Como profesional, quiero registrarme e iniciar sesión con email y contraseña, para acceder al panel de administración
**ID:** IPC-2
**Epic:** IPC-1
**Implementación:** Sin verificar
**Refinamiento:** Refinado
**Inspección QA:** Requiere cambios
**Estado de sincronización:** Sincronizado con Jira

## Descripción
Como profesional, quiero registrarme e iniciar sesión con email y contraseña, para acceder al panel de administración.

## Criterios de Aceptación (Gherkin)

### Escenario 1: Registro exitoso de nuevo profesional
**Given** que no tengo una cuenta registrada en la plataforma
**And** me encuentro en la página de registro
**When** ingreso mi nombre completo (obligatorio, máximo 100 caracteres)
**And** ingreso un correo electrónico con formato válido (máximo 254 caracteres) que no esté registrado
**And** ingreso una contraseña de al menos 8 caracteres, con al menos una mayúscula y un número
**And** presiono el botón de registro
**Then** el sistema crea mi usuario en Supabase Auth
**And** se genera automáticamente mi slug público único (depende de IPC-3)
**And** recibo un correo electrónico de bienvenida (depende de IPC-16)
**And** la sesión queda iniciada
**And** soy redirigido a [PENDIENTE: panel de administración (PRD, Flujo 1) o asistente de configuración inicial (especificación 3.1)]

### Escenario 2: Intento de registro con correo ya existente
**Given** que existe una cuenta registrada con el correo "profesional@ejemplo.com"
**And** me encuentro en la página de registro
**When** intento registrarme utilizando el correo "profesional@ejemplo.com"
**Then** el sistema rechaza la solicitud
**And** se muestra un mensaje de error [PENDIENTE: "El correo electrónico ya se encuentra registrado" (historia) / "El email ya está en uso" con enlace a recuperación (especificación 3.1) / mensaje neutro anti-enumeración (criterio de 3.3)]

### Escenario 3: Registro con contraseña que no cumple la política
**Given** que no tengo una cuenta registrada en la plataforma
**And** me encuentro en la página de registro
**When** ingreso una contraseña con menos de 8 caracteres, o sin mayúscula, o sin número
**Then** el sistema rechaza el registro
**And** muestra un mensaje que indica la regla incumplida [PENDIENTE: la especificación solo define "La contraseña es muy corta"]

### Escenario 4: Registro con datos de entrada inválidos
**Given** que no tengo una cuenta registrada en la plataforma
**And** me encuentro en la página de registro
**When** ingreso un nombre vacío o de más de 100 caracteres, o un correo con formato inválido o de más de 254 caracteres
**Then** el sistema rechaza el registro
**And** muestra el error de validación correspondiente

### Escenario 5: Inicio de sesión exitoso con credenciales válidas
**Given** que tengo una cuenta activa con el correo "profesional@ejemplo.com" y contraseña "ClaveSegura1"
**And** me encuentro en la página de inicio de sesión
**When** ingreso mi correo y contraseña correctos
**And** presiono el botón de iniciar sesión
**Then** el sistema valida mis credenciales
**And** establece las cookies seguras de sesión JWT httpOnly
**And** me redirige a mi panel de administración

### Escenario 6: Inicio de sesión con credenciales incorrectas
**Given** que tengo una cuenta activa
**And** me encuentro en la página de inicio de sesión
**When** ingreso un correo o una contraseña incorrectos
**Then** el sistema rechaza el acceso
**And** muestra el mensaje genérico "Email o contraseña incorrectos", sin indicar cuál de los dos datos falló

### Escenario 7: Expiración y renovación de sesión
**Given** que tengo una sesión iniciada
**When** el token de acceso JWT supera los 15 minutos
**Then** el sistema lo renueva automáticamente con el refresh token
**And** si el refresh token supera los 7 días, la sesión se considera vencida y soy redirigido a "/login"

### Escenario 8: Intento de acceso a ruta protegida sin sesión activa
**Given** que no he iniciado sesión en la plataforma
**When** intento acceder directamente a la URL de mi panel de administración "/dashboard"
**Then** el middleware del sistema me intercepta
**And** soy redirigido automáticamente a la página de login "/login"

## Notas de QA
* Requiere verificar el comportamiento del token de refresco (7 días) y la expiración del JWT (15 minutos).
* Las cookies de sesión deben contar con las banderas `httpOnly` y `Secure` activas.
* Pendiente de definición: cierre de sesión e invalidación del token (ticket de soporte #69).
* Pendiente de definición: rate limiting y bloqueo por intentos fallidos de login.
* Pendiente de definición: normalización del correo electrónico.
* Pendiente de definición: comportamiento si falla Supabase Auth o el envío del correo de bienvenida.
* Pendiente de definición: ruta de la página de registro.

## Fuentes
| Dato / afirmación | De dónde sale |
| :--- | :--- |
| Validaciones de nombre, correo y contraseña | `03-especificacion-funcional-v0.3.md` · 3.1 |
| Destino posterior al registro: panel | `prd.md` · Flujo 1, pasos 4-5 |
| Destino posterior al registro: asistente de configuración inicial | `03-especificacion-funcional-v0.3.md` · 3.1, paso 3 |
| Mensaje genérico de login fallido | `03-especificacion-funcional-v0.3.md` · 3.2 |
| Principio anti-enumeración | `03-especificacion-funcional-v0.3.md` · 3.3 |
| JWT 15 minutos, refresh 7 días y cookies httpOnly | `prd.md` · §5 Seguridad; `04-notas-tecnicas.md` · "auth" |
| Intercepción de rutas protegidas | `prd.md` · §5 Seguridad; `04-notas-tecnicas.md` · "auth" |
| Sesión persistente tras cerrar sesión | `06-tickets-soporte-resumen.md` · #69 — reporte de usuario, **no verificado por QA** |
| Rate limiting e intentos fallidos sin definir | `03-especificacion-funcional-v0.3.md` · 3.3 (`TBD`); `prd.md` · Fuentes (hipótesis) |
| Proveedor del correo de bienvenida | `prd.md` · Contradicciones (Resend); `04-notas-tecnicas.md` · "mails" (Supabase) |
| Mensajes, normalización de correo y rutas propuestas | **Hipótesis** — no hay documento que las respalde |

## Contradicciones detectadas

* **Destino posterior al registro.** `prd.md` (Flujo 1, pasos 4-5) indica que el profesional es redirigido a su panel de control y luego entra por su cuenta a la configuración de agenda. `03-especificacion-funcional-v0.3.md` (3.1, paso 3) indica que es redirigido al asistente de configuración inicial. Se tomó la versión del PRD para la historia porque es el documento vigente, pero la diferencia queda abierta: no hay decisión posterior que la resuelva.
* **Mensaje para correo ya registrado.** La historia y la especificación 3.1 admiten un mensaje explícito que confirma la existencia de la cuenta ("El correo electrónico ya se encuentra registrado" / "El email ya está en uso"). La especificación 3.3 establece el principio opuesto para recuperación de contraseña: el mensaje debe ser idéntico exista o no la cuenta, para evitar enumeración de usuarios. Se citan ambas versiones y no se resuelve: es una decisión de seguridad y de producto.
* **Sesión que persiste tras cerrar sesión.** El criterio 8 exige que el middleware redirija a `/login` sin sesión activa. El ticket de soporte #69 reporta lo contrario en la aplicación: el usuario cerró sesión, pegó la URL del panel y entró sin contraseña. El ticket es un reporte de usuario, no una observación de QA, y no se verificó contra el sistema; se deja abierto porque puede ser un defecto del sistema o un criterio incompleto (logout no definido).
* **Proveedor del correo de bienvenida.** El PRD establece Resend; `04-notas-tecnicas.md` describe el servicio transaccional de Supabase. No afecta directamente al criterio de esta historia, pero condiciona la verificación del correo de bienvenida que aquí se menciona.

## Preguntas abiertas

* ¿El profesional debe ir al panel o al asistente de configuración inicial al terminar el registro?
* ¿Qué política se adopta para el correo ya registrado: mensaje explícito o mensaje neutro anti-enumeración?
* ¿El cierre de sesión debe invalidar el token en el servidor? ¿Qué explica el caso del ticket #69?
* ¿Se implementará rate limiting o bloqueo por intentos fallidos de login? ¿Con qué umbral y duración?
* ¿El correo electrónico se normaliza (recorte de espacios, comparación sin distinguir mayúsculas)?
* ¿Qué ocurre si falla el envío del correo de bienvenida? ¿El registro se considera exitoso igual?
* ¿Está deshabilitada la confirmación de correo en Supabase, para que el alta deje sesión iniciada?
* ¿Cuál es la ruta de la página de registro?
* ¿Qué mensaje se muestra para cada regla de contraseña incumplida?

## 3. Valoración de Calidad
*   **Veredicto:** Requiere cambios
*   **Riesgo:** Alto

El veredicto surge de las reglas del prompt: hay defectos que se pueden corregir leyendo las fuentes (validaciones, login fallido, expiración de sesión) y quedan preguntas abiertas que afectan criterios de aceptación (destino posterior al registro, política del mensaje de correo existente, logout, rate limiting). El riesgo es alto por tratarse del módulo de autenticación y por el ticket #69, que reporta un acceso al panel sin credenciales.

## Nota sobre el análisis 5
La historia trae `**Implementación:** Sin verificar` y su tabla de Fuentes no contiene filas `Observado`. Por eso el análisis contra el comportamiento observado **no aplica**: no se navegó la aplicación en esta inspección. El ticket #69 se cita como evidencia documental reportada por un usuario, no como comportamiento observado por QA.
