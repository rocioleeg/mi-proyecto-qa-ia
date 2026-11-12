# Handoff UI -> API/DB — IPC-6 Disponibilidad semanal y duración de turno

**Fecha:** 2026-11-12
**Entorno:** Producción
**Sesión UI:** `../ui/session-2026-11-12-ipc-6.md`

```json
{
  "feature": "IPC-6 Disponibilidad semanal y duración de turno",
  "environment": "Producción",
  "ui_actions": [
    {
      "step": "Abrir /dashboard/availability sin sesión",
      "expected_api": null,
      "expected_db": null
    },
    {
      "step": "Enviar formulario de /login con TEST_USER_EMAIL y TEST_USER_PASSWORD",
      "expected_api": {
        "method": "POST",
        "endpoint": "https://uuhstroppfwbuxudziqe.supabase.co/auth/v1/token?grant_type=password",
        "expected_status": 200
      },
      "expected_db": null
    },
    {
      "step": "Clic en Guardar Cambios con horario válido sin sesión",
      "expected_api": {
        "method": "POST",
        "endpoint": "/api/availability/rules",
        "expected_status": 200
      },
      "expected_db": {
        "table": "availability_rules",
        "operation": "DELETE+INSERT",
        "where": { "professional_id": "[auth.uid()]" }
      }
    },
    {
      "step": "Clic en Guardar (duración) sin sesión",
      "expected_api": {
        "method": "PUT",
        "endpoint": "/api/professionals/settings",
        "expected_status": 200
      },
      "expected_db": {
        "table": "professionals",
        "operation": "UPDATE",
        "where": { "id": "[auth.uid()]" }
      }
    },
    {
      "step": "Clic en Guardar Cambios con franja inválida o solapada",
      "expected_api": null,
      "expected_db": null
    },
    {
      "step": "Navegar al dashboard y volver con Atrás tras editar sin guardar",
      "expected_api": null,
      "expected_db": null
    }
  ],
  "test_data": {
    "email": "TEST_USER_EMAIL (definido en .env; no transcrito)",
    "user_id": "no disponible",
    "amount": "no aplica",
    "duration_minutes": 45,
    "schedule": "Lunes a viernes 09:00-18:00; Lunes 09:00-12:00 + 11:00-14:00; Lunes 18:00-09:00; Lunes con 4 intervalos; 7 días activos"
  },
  "trace_ids": {
    "request_id": "no disponible",
    "correlation_id": "no disponible"
  }
}
```

## Notas para la capa API/DB
*   El guardado semanal sin sesión no disparó ninguna petición; el `POST /api/availability/rules` es el candidato documentado en `system-design.md`, no una llamada observada.
*   El guardado de duración sin sesión sí disparó `PUT /api/professionals/settings` con `{"appointment_duration":45}`; la respuesta observada fue `401 {"error":"Unauthorized"}`. El `expected_status: 200` es el comportamiento esperado con sesión, no el observado.
*   El intento de login disparó `POST https://uuhstroppfwbuxudziqe.supabase.co/auth/v1/token?grant_type=password`, que falló con `net::ERR_NAME_NOT_RESOLVED` sin respuesta HTTP. `expected_status: 200` es el comportamiento esperado si el dominio resuelve.
*   Las validaciones de franja inválida y solapamiento son client-side: no hay petición que analizar en la capa API.
*   No se creó ningún dato ni se usó una cuenta real; no hay `professional_id` ni `auth.uid()` disponible.
*   No se dispone de `requestId` ni `correlationId`; la aplicación no los expone en las respuestas observadas.
*   La grilla pública y el recálculo (`GET /api/public/availability`) no se pudieron verificar: no hay sesión, el login está bloqueado por DNS y el dashboard no tiene slug público.
