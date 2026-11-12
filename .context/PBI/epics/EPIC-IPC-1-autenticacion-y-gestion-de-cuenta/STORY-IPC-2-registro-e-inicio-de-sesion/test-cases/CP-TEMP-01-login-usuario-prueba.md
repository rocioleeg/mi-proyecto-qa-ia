# Caso de Prueba: Login con el usuario de prueba

**ID:** CP-TEMP-01
**Historia:** IPC-2
**Estado de sincronización:** SUBIDO A JIRA
**Clave Jira:** IPC-22
**Ruta:** A
**Tipo:** Cucumber
**Prioridad:** Alta
**Automatizable:** Sí

## Precondiciones
*   El backend de Supabase Auth está operativo y el dominio de Auth resuelve.
*   Existe la cuenta de prueba `qa-profesional-base@mailinator.com` definida en `.env`.
*   El usuario está en `/login` sin sesión iniciada.

## Datos de Prueba
*   `TEST_USER_EMAIL` y `TEST_USER_PASSWORD` desde `.env`; no se transcriben en este caso.
*   Ningún dato real de clientes o profesionales.

## Pasos
| # | Acción | Resultado Esperado |
| :--- | :--- | :--- |
| 1 | Ir a `https://cita-ai.vercel.app/login` | Se muestra el formulario de inicio de sesión |
| 2 | Ingresar el correo y la contraseña del usuario de prueba desde `.env` | Los campos aceptan los valores sin error |
| 3 | Presionar "Iniciar Sesión" | La petición de autenticación responde correctamente |
| 4 | Observar la navegación y las cookies | Redirige al panel y se establece una sesión con cookie `httpOnly` |

## Escenario Gherkin
```gherkin
Feature: Inicio de sesión del profesional

  @Automate @Priority:High @CA-5
  Scenario: Login con credenciales válidas del usuario de prueba
    Given el usuario está en la página de inicio de sesión
    When ingresa el correo y la contraseña válidos definidos en el entorno
    And presiona el botón de iniciar sesión
    Then el sistema valida las credenciales
    And establece una sesión con cookie httpOnly
    And redirige al panel de administración
```

## Trazabilidad
| Historia (US) | Caso / escenario | Bug de origen | Evidencia |
| :--- | :--- | :--- | :--- |
| IPC-2 | Login con el usuario de prueba | — | `analisis-escenarios.md` · candidato 1; `smoke-2026-11-11-produccion.md` · verificación 3 |

## Origen
*   **Priorización:** `priorizacion-roi.md` · escenario 1, score 2.8
*   **Criterio de aceptación cubierto:** `story.md` · CA-5
