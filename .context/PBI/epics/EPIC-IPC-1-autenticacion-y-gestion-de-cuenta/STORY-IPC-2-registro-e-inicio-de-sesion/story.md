# Story: Como profesional, quiero registrarme e iniciar sesión con email y contraseña, para acceder al panel de administración
**ID:** IPC-2
**Epic:** IPC-1
**Implementación:** Sin verificar
**Refinamiento:** Refinado
**Inspección QA:** Sin inspeccionar
**Estado de sincronización:** Sincronizado con Jira

## Descripción
Como profesional, quiero registrarme e iniciar sesión con email y contraseña, para acceder al panel de administración.

## Análisis INVEST
| Criterio | Cumple | Observación |
| :--- | :--- | :--- |
| Independiente | Sí | La autenticación se puede construir e interactuar sin depender de la grilla de turnos. |
| Negociable | Sí | La interfaz de captura y mensajes de validación son ajustables. |
| Valiosa | Sí | Habilita el acceso seguro al panel de gestión privada del profesional. |
| Estimable | Sí | Criterios técnicos claros basados en Supabase Auth y cookies HTTP-only. |
| Pequeña | Sí | Acotada al registro, login y expiración de sesión. |
| Testeable | Sí | Se puede verificar mediante pruebas funcionales de UI y API. |

## Criterios de Aceptación (Gherkin)

### Escenario 1: Registro exitoso de nuevo profesional
**Given** que no tengo una cuenta registrada en la plataforma
**And** me encuentro en la página de registro
**When** ingreso mi nombre completo, un correo electrónico no registrado y una contraseña válida con al menos 8 caracteres, una mayúscula y un número
**And** presiono el botón de registro
**Then** el sistema crea mi usuario en Supabase Auth
**And** se genera automáticamente mi slug público único
**And** recibo un correo electrónico de bienvenida
**And** soy redirigido a mi panel de administración con la sesión iniciada

### Escenario 2: Intento de registro con correo ya existente
**Given** que existe una cuenta registrada con el correo "profesional@ejemplo.com"
**And** me encuentro en la página de registro
**When** intento registrarme utilizando el correo "profesional@ejemplo.com"
**Then** el sistema rechaza la solicitud
**And** se muestra el mensaje de error "El correo electrónico ya se encuentra registrado"

### Escenario 3: Inicio de sesión exitoso con credenciales válidas
**Given** que tengo una cuenta activa con el correo "profesional@ejemplo.com" y contraseña "ClaveSegura1"
**And** me encuentro en la página de inicio de sesión
**When** ingreso mi correo y contraseña correctos
**And** presiono el botón de iniciar sesión
**Then** el sistema valida mis credenciales
**And** establece las cookies seguras de sesión JWT httpOnly
**And** me redirige a mi panel de administración

### Escenario 4: Intento de acceso a ruta protegida sin sesión activa
**Given** que no he iniciado sesión en la plataforma
**When** intento acceder directamente a la URL de mi panel de administración "/dashboard"
**Then** el middleware del sistema me intercepta
**And** soy redirigido automáticamente a la página de login "/login"

## Notas de QA
* Requiere verificar el comportamiento del token de refresco (7 días) y la expiración del JWT (15 minutos).
* Las cookies de sesión deben contar con las banderas `httpOnly` y `Secure` activas.

## Fuentes
| Dato / afirmación | De dónde sale |
| :--- | :--- |
| Requisitos de contraseña y campos de registro | `prd.md` · Feature 1 y Flujo 1 |
| Cookies httpOnly, JWT (15 min) y Refresh Token (7 días) | `prd.md` · Feature 1 y sección 5 ("Seguridad") |
| Intercepción de rutas protegidas por middleware | `prd.md` · sección 5 ("Seguridad") |

## Contradicciones detectadas
* Ninguna detectada

## Preguntas abiertas
* Ninguna detectada
