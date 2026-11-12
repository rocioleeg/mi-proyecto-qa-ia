# Handoff UI -> API/DB — IPC-2 Registro e inicio de sesión

**Fecha:** 2026-11-11
**Entorno:** Producción
**Sesión UI:** `../ui/session-2026-11-11-ipc-2.md`

```json
{
  "feature": "IPC-2 Registro e inicio de sesión",
  "environment": "Producción",
  "ui_actions": [
    {
      "step": "Clic en Iniciar Sesión con credenciales inválidas",
      "expected_api": {
        "method": "POST",
        "endpoint": "https://uuhstroppfwbuxudziqe.supabase.co/auth/v1/token?grant_type=password",
        "expected_status": 400
      },
      "expected_db": {
        "table": "auth.users",
        "operation": "SELECT",
        "where": { "email": "[correo sintético]" }
      }
    },
    {
      "step": "Clic en Registrarse con datos válidos (no ejecutado)",
      "expected_api": {
        "method": "POST",
        "endpoint": "https://uuhstroppfwbuxudziqe.supabase.co/auth/v1/signup",
        "expected_status": 200
      },
      "expected_db": {
        "table": "professionals",
        "operation": "INSERT",
        "where": { "id": "[auth.users.id]" }
      }
    },
    {
      "step": "Acceso directo a /dashboard sin sesión",
      "expected_api": {
        "method": "GET",
        "endpoint": "/api/appointments",
        "expected_status": 401
      },
      "expected_db": {
        "table": "appointments",
        "operation": "SELECT",
        "where": { "professional_id": "[auth.uid()]" }
      }
    }
  ],
  "test_data": {
    "email": "qa-no-existe-2026@mailinator.com (sintético, no registrado)",
    "user_id": "no disponible",
    "amount": "no aplica"
  },
  "trace_ids": {
    "request_id": "no disponible",
    "correlation_id": "no disponible"
  }
}
```

## Notas para la capa API/DB
*   Las acciones marcadas como "no ejecutado" son candidatas a verificación, no observaciones confirmadas.
*   El login falló antes de obtener respuesta HTTP: `net::ERR_NAME_NOT_RESOLVED`; el host `uuhstroppfwbuxudziqe.supabase.co` devuelve NXDOMAIN.
*   No se creó ningún dato ni se usó una cuenta real. El correo de `test_data` es sintético y no está registrado.
*   El acceso a `/dashboard` no disparó llamadas a `/api/*`; el panel se renderizó sin sesión. El `expected_status: 401` es el comportamiento esperado según el plan de pruebas, no una respuesta observada.
*   No se dispone de `requestId` ni `correlationId`; la aplicación no los expone en las respuestas observadas.
