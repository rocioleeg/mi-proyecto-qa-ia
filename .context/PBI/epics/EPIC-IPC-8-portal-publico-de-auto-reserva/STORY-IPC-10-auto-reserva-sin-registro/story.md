# Story: Como cliente final, quiero agendar un turno ingresando solo mi nombre y email, para confirmar la reserva sin crear cuenta
**ID:** IPC-10
**Epic:** IPC-8
**Implementación:** Sin verificar
**Refinamiento:** Borrador
**Inspección QA:** Sin inspeccionar
**Estado de sincronización:** Sincronizado con Jira

## Descripción
Como cliente final, quiero agendar un turno ingresando solo mi nombre y email, para confirmar la reserva sin crear cuenta.

## Criterios de Aceptación (Borrador)
- [ ] El formulario de confirmación solicita de forma obligatoria únicamente nombre completo y correo electrónico del cliente.
- [ ] Al presionar "Confirmar reserva", el sistema registra al cliente (si no existía previamente) y crea el registro en `appointments` con estado `confirmed`.
- [ ] El intervalo reservado desaparece de inmediato de la oferta pública para otros usuarios.
- [ ] La pantalla muestra un mensaje fehaciente de confirmación con los datos del turno.

## Fuentes
| Dato / afirmación | De dónde sale |
| :--- | :--- |
| Formulario con solo nombre y correo (sin cuenta) | `prd.md` · Feature 3 y Flujo 2 |
| Creación inmediata en `appointments` con estado `confirmed` | `prd.md` · Feature 3 y Flujo 2 |
| Ocultamiento inmediato del slot reservado | `prd.md` · Feature 3 |
