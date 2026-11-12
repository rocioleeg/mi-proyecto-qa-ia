# Priorización de Pruebas: IPC-2 — Registro e inicio de sesión

**Historia:** IPC-2
**Fecha:** 2026-11-12
**Matriz de riesgos usada:** `.context/testing/test-plan-IPC-2-registro-e-inicio-de-sesion.md` · R1–R5. La criticidad de los candidatos ejecutados se cita desde esa matriz; los candidatos 13 a 24 quedan sin puntuar porque nunca se ejecutaron.

## Matriz de Priorización
| # | Escenario | Frec. | Crit. | Compl. | Score | Decisión | Justificación |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| 1 | Login con el usuario de prueba del `.env` | 1 | 1 | 0.8 | 2.8 | AUTOMATIZAR | Habilita la verificación de R1 (20 · Alto) y del camino autenticado; el flujo es repetible, aunque hoy falla por DNS. |
| 2 | Login con credenciales inválidas y backend caído | 1 | 0.8 | 0.9 | 2.7 | AUTOMATIZAR | Mitiga R2 (16 · Alto): mensaje genérico y distinción entre credenciales inválidas y fallo de servicio; automatizable interceptando la respuesta. |
| 3 | Envío vacío del formulario de login | 1 | 0.3 | 1 | 2.3 | REGRESIÓN MANUAL | Validación básica sin riesgo directo en R1–R5; criticidad estimada. Automatizable, pero de bajo valor frente a los flujos de autenticación. |
| 4 | Envío vacío del formulario de registro | 1 | 0.3 | 1 | 2.3 | REGRESIÓN MANUAL | Validación básica sin riesgo directo en R1–R5; criticidad estimada. Se mantiene como smoke manual. |
| 5 | Registro con contraseña de 3 caracteres | 1 | 0.8 | 1 | 2.8 | AUTOMATIZAR | Mitiga R4 (12 · Medio): política de contraseña validada solo en el cliente; la aserción es directa y estable. |
| 6 | Registro con contraseña sin mayúscula | 1 | 0.8 | 1 | 2.8 | AUTOMATIZAR | Mitiga R4 (12 · Medio): misma política, distinta regla incumplida. |
| 7 | Registro con contraseña sin número | 1 | 0.8 | 1 | 2.8 | AUTOMATIZAR | Mitiga R4 (12 · Medio): misma política, distinta regla incumplida. |
| 8 | Registro con nombre de 101 caracteres | 1 | 0.5 | 1 | 2.5 | REGRESIÓN MANUAL | Sin riesgo directo en R1–R5; criticidad estimada por integridad de datos. La aserción es fácil, pero el servidor no pudo verificarse. |
| 9 | Registro con correo de 255 caracteres | 1 | 0.5 | 1 | 2.5 | REGRESIÓN MANUAL | Sin riesgo directo en R1–R5; criticidad estimada por integridad de datos. Falta confirmar el rechazo server-side. |
| 10 | Triple envío rápido del login | 1 | 0.6 | 0.6 | 2.2 | REGRESIÓN MANUAL | Mitiga R2 (16 · Alto): fuerza bruta y solicitudes repetidas; la medición de concurrencia es menos estable y depende del backend. |
| 11 | Acceso directo a `/dashboard` sin sesión | 1 | 1 | 1 | 3.0 | AUTOMATIZAR | Mitiga R1 (20 · Alto): el panel se renderiza sin autenticación; verificación simple con un contexto sin cookies. |
| 12 | Responsive 375x667 en login y registro | 1 | 0.4 | 1 | 2.4 | REGRESIÓN MANUAL | Sin riesgo directo en R1–R5; criticidad estimada por compatibilidad móvil. La medición de `scrollWidth` es estable. |
| 13 | Registro exitoso de un profesional nuevo | — | — | — | — | PROBAR ANTES DE DECIDIR | Nunca se ejecutó: no se autorizó crear una cuenta y el backend no responde. No puede descartarse. |
| 14 | Registro con un correo ya existente | — | — | — | — | PROBAR ANTES DE DECIDIR | Nunca se ejecutó; requiere una cuenta accesible y backend operativo. |
| 15 | Expiración y renovación de sesión | — | — | — | — | PROBAR ANTES DE DECIDIR | Nunca se ejecutó; requiere sesión válida y control del token. |
| 16 | Cookies `httpOnly`/`Secure` y logout | — | — | — | — | PROBAR ANTES DE DECIDIR | Nunca se ejecutó; logout e invalidación siguen sin definirse. |
| 17 | Rate limiting y bloqueo de intentos | — | — | — | — | PROBAR ANTES DE DECIDIR | Nunca se ejecutó con backend operativo; el umbral no está definido. |
| 18 | Recepción del correo de bienvenida | — | — | — | — | PROBAR ANTES DE DECIDIR | Nunca se ejecutó; no se creó ninguna cuenta y el proveedor está en contradicción. |
| 19 | Destino posterior al registro | — | — | — | — | PROBAR ANTES DE DECIDIR | Nunca se ejecutó; el criterio sigue pendiente entre panel y asistente inicial. |
| 20 | Validación server-side de contraseña, nombre y correo | — | — | — | — | PROBAR ANTES DE DECIDIR | Nunca se ejecutó; solo se observó la validación del cliente. |
| 21 | Matriz de navegadores e in-app browsers | — | — | — | — | PROBAR ANTES DE DECIDIR | Nunca se ejecutó; solo Chrome headless. |
| 22 | Normalización del correo | — | — | — | — | PROBAR ANTES DE DECIDIR | Nunca se ejecutó; el criterio de normalización no está definido. |
| 23 | Registro parcial y trigger de `professionals` | — | — | — | — | PROBAR ANTES DE DECIDIR | Nunca se ejecutó; no se creó ninguna cuenta y no hay acceso a base o logs. |
| 24 | Anti-enumeración de usuarios | — | — | — | — | PROBAR ANTES DE DECIDIR | Nunca se ejecutó; requiere medir mensaje, código y tiempos con cuentas existentes e inexistentes. |

## Resumen
*   **Candidatos a automatización:** 6
*   **Regresión manual:** 6
*   **Probar antes de decidir:** 12
*   **Descartados:** 0

## Trazabilidad
| Historia (US) | Caso / escenario | Riesgo mitigado | Origen |
| :--- | :--- | :--- | :--- |
| IPC-2 | Login con el usuario de prueba | R1 (prerrequisito de sesión) | `analisis-escenarios.md` · candidato 1 |
| IPC-2 | Login con credenciales inválidas y backend caído | R2 | `analisis-escenarios.md` · candidato 2 |
| IPC-2 | Envío vacío de login | — (sin riesgo directo) | `analisis-escenarios.md` · candidato 3 |
| IPC-2 | Envío vacío de registro | — (sin riesgo directo) | `analisis-escenarios.md` · candidato 4 |
| IPC-2 | Contraseña de 3 caracteres | R4 | `analisis-escenarios.md` · candidato 5 |
| IPC-2 | Contraseña sin mayúscula | R4 | `analisis-escenarios.md` · candidato 6 |
| IPC-2 | Contraseña sin número | R4 | `analisis-escenarios.md` · candidato 7 |
| IPC-2 | Nombre de 101 caracteres | — (sin riesgo directo) | `analisis-escenarios.md` · candidato 8 |
| IPC-2 | Correo de 255 caracteres | — (sin riesgo directo) | `analisis-escenarios.md` · candidato 9 |
| IPC-2 | Triple envío rápido del login | R2 | `analisis-escenarios.md` · candidato 10 |
| IPC-2 | Acceso directo a `/dashboard` sin sesión | R1 | `analisis-escenarios.md` · candidato 11 |
| IPC-2 | Responsive 375x667 | — (sin riesgo directo) | `analisis-escenarios.md` · candidato 12 |
| IPC-2 | Registro exitoso de un profesional nuevo | R3 | `analisis-escenarios.md` · candidato 13 |
| IPC-2 | Registro con un correo ya existente | R2 | `analisis-escenarios.md` · candidato 14 |
| IPC-2 | Expiración y renovación de sesión | R1 | `analisis-escenarios.md` · candidato 15 |
| IPC-2 | Cookies `httpOnly`/`Secure` y logout | R1 | `analisis-escenarios.md` · candidato 16 |
| IPC-2 | Rate limiting y bloqueo de intentos | R2 | `analisis-escenarios.md` · candidato 17 |
| IPC-2 | Recepción del correo de bienvenida | R5 | `analisis-escenarios.md` · candidato 18 |
| IPC-2 | Destino posterior al registro | R3 | `analisis-escenarios.md` · candidato 19 |
| IPC-2 | Validación server-side | R4 | `analisis-escenarios.md` · candidato 20 |
| IPC-2 | Matriz de navegadores e in-app | — (sin riesgo directo) | `analisis-escenarios.md` · candidato 21 |
| IPC-2 | Normalización del correo | R2 | `analisis-escenarios.md` · candidato 22 |
| IPC-2 | Registro parcial y trigger de `professionals` | R3 | `analisis-escenarios.md` · candidato 23 |
| IPC-2 | Anti-enumeración de usuarios | R2 | `analisis-escenarios.md` · candidato 24 |

## Sin cobertura
*   **Candidatos 13 a 24:** no se puntuaron porque nunca se ejecutaron. Las causas son el fallo de DNS de Supabase Auth, la falta de una cuenta válida, la ausencia de entorno aislado, la falta de acceso a base/logs y la falta de decisiones de producto sobre logout, rate limiting y normalización del correo.
*   **Criticidad:** los candidatos 1 a 12 citan R1, R2 o R4 cuando existe relación directa. Los que no mitigan un riesgo de la matriz usan criticidad estimada, indicada en la justificación.
*   **Automatización de los candidatos 13 a 24:** no se recomienda decidirla hasta ejecutarlos una vez y contar con un backend operativo; el score queda pendiente.
