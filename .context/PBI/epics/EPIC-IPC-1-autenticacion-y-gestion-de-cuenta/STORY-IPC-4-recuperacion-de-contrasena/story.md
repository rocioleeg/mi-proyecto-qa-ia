# Story: Como profesional, quiero recuperar mi contraseña mediante correo electrónico, para restablecer mi acceso si lo olvido
**ID:** IPC-4
**Epic:** IPC-1
**Implementación:** Sin verificar
**Refinamiento:** Refinado
**Inspección QA:** Sin inspeccionar
**Estado de sincronización:** Sincronizado con Jira

## Descripción
Como profesional, quiero recuperar mi contraseña mediante correo electrónico, para restablecer mi acceso si lo olvido.

## Análisis INVEST
| Criterio | Cumple | Observación |
| :--- | :--- | :--- |
| Independiente | Sí | El flujo de recuperación opera de forma autónoma respecto a la gestión de turnos y agenda. |
| Negociable | Sí | Los mensajes de pantalla, la plantilla del email y el tiempo de validez del enlace son configurables. |
| Valiosa | Sí | Garantiza la continuidad del acceso del profesional a su cuenta y panel de control ante olvido de contraseña. |
| Estimable | Sí | Se apoya en el flujo de recuperación de Supabase Auth con PKCE y despacho vía Resend. |
| Pequeña | Sí | Comprende la solicitud de recuperación, el token temporal y el cambio de contraseña. |
| Testeable | Sí | Se puede verificar mediante pruebas funcionales de UI y validaciones de seguridad de tokens. |

## Criterios de Aceptación (Gherkin)

### Escenario 1: Solicitud exitosa de recuperación para un correo registrado
**Given** que existe una cuenta registrada con el correo "profesional@ejemplo.com"
**And** me encuentro en la página de recuperación de contraseña "/forgot-password"
**When** ingreso el correo "profesional@ejemplo.com"
**And** presiono el botón para solicitar la recuperación
**Then** la interfaz muestra el mensaje "Si el email existe en nuestro sistema, recibirás un enlace para recuperar tu contraseña"
**And** el sistema envía un correo electrónico transaccional a "profesional@ejemplo.com" conteniendo un enlace con un token de un solo uso válido por 60 minutos

### Escenario 2: Prevención de enumeración de usuarios ante correo no registrado
**Given** que el correo "inexistente@ejemplo.com" no está registrado en la plataforma
**And** me encuentro en la página de recuperación de contraseña "/forgot-password"
**When** ingreso el correo "inexistente@ejemplo.com"
**And** presiono el botón para solicitar la recuperación
**Then** la interfaz muestra exactamente el mismo mensaje "Si el email existe en nuestro sistema, recibirás un enlace para recuperar tu contraseña"
**And** el sistema no envía ningún correo electrónico

### Escenario 3: Restablecimiento exitoso de contraseña con token válido
**Given** que he recibido un enlace de recuperación con un token válido generado hace menos de 60 minutos
**When** accedo al enlace en el navegador
**And** ingreso una nueva contraseña que cumple las reglas de seguridad (mínimo 8 caracteres, al menos una mayúscula y un número)
**And** confirmo el cambio de contraseña
**Then** el sistema actualiza la contraseña del usuario en Supabase Auth
**And** invalida el token de recuperación utilizado
**And** muestra el mensaje de confirmación de contraseña actualizada
**And** me redirige a la página de inicio de sesión

### Escenario 4: Intento de uso de token de recuperación expirado
**Given** que poseo un enlace de recuperación con un token generado hace más de 60 minutos
**When** intento acceder al enlace de recuperación en el navegador
**Then** el sistema rechaza la solicitud por token caducado
**And** muestra un mensaje de error indicando que el enlace ha expirado y ofreciendo solicitar uno nuevo

### Escenario 5: Intento de reutilización de token de recuperación ya utilizado
**Given** que ya utilicé un token de recuperación para cambiar exitosamente mi contraseña
**When** intento abrir nuevamente el mismo enlace de recuperación
**Then** el sistema rechaza el token de un solo uso
**And** muestra un mensaje informando que el enlace ya no es válido

### Escenario 6: Validación de complejidad de la nueva contraseña
**Given** que me encuentro en el formulario de restablecimiento mediante un enlace con token válido
**When** ingreso una nueva contraseña que no cumple los requisitos mínimos (menos de 8 caracteres, o sin mayúscula, o sin número)
**And** presiono el botón para guardar la nueva contraseña
**Then** el sistema rechaza la contraseña ingresada
**And** se muestran en pantalla los mensajes de validación indicando los requisitos no cumplidos

## Notas de QA
* Validar que el token expire de forma exacta a los 60 minutos (1 hora).
* Confirmar que el token sea de un solo uso (`single-use`) y quede revocado inmediatamente tras el primer cambio exitoso.
* Verificar que los correos transaccionales de recuperación sean despachados a través del proveedor de email configurado (Resend).
* Comprobar que en ningún caso la respuesta HTTP o el DOM revele si el correo ingresado existía o no en la base de datos (seguridad anti-enumeración).

## Fuentes
| Dato / afirmación | De dónde sale |
| :--- | :--- |
| Mensaje unificado para prevención de enumeración de usuarios | `03-especificacion-funcional-v0.3.md` · sección 3.3 / `prd.md` · sección 5 ("Seguridad") |
| Token de un solo uso con expiración de 1 hora (60 min) | `03-especificacion-funcional-v0.3.md` · sección 3.3 / `04-notas-tecnicas.md` · sección "auth" / `prd.md` · Feature 1 |
| Requisitos de contraseña (mínimo 8 caracteres, mayúscula, número) | `03-especificacion-funcional-v0.3.md` · sección 3.1 / `prd.md` · Feature 1 |
| Autenticación y reseteo PKCE con Supabase Auth | `04-notas-tecnicas.md` · sección "auth" |
| Despacho de emails transaccionales vía Resend | `05-hilo-mail-cambio-de-alcance.md` / `prd.md` · Feature 6 |
| Límite de tasa (rate limiting) de solicitudes de recuperación | **Hipótesis** — no hay documento que defina la cantidad máxima de solicitudes por IP o correo |

## Contradicciones detectadas
* Ninguna detectada

## Preguntas abiertas
* ¿Existe una política o límite de tasa (rate limiting) ante solicitudes sucesivas de recuperación para prevenir spam o saturación de la API de correos?
