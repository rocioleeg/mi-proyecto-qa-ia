# Validación de Datos (capa DB)

**Fecha:** 2026-11-13
**Entorno:** Producción (único entorno activo; UAT inactivo)
**Origen de los datos:** Datos reales de producción — UAT histórico se refrescaba clonando producción y la anonimización era parcial (solo `clients`)
**Modo de ejecución:** Solo consultas entregadas — **no se ejecutó ninguna consulta**

> ⚠️ **Advertencia previa obligatoria.** La base contra la que apuntan estas consultas **contiene datos personales reales**: `04-notas-tecnicas.md` admite que en el clon de UAT se anonimizó únicamente la tabla `clients`, y que `professionals` conservó nombres y correos reales para no romper los slugs. Sobre producción no hay anonimización de ningún tipo. Por eso: las consultas de este documento devuelven **conteos, booleanos y nombres de restricciones**, nunca filas con datos de personas. Si alguna devuelve slugs o nombres reales, en el reporte se resumen como conteo y se descartan los valores.

## Fuente de Entrada

*   **Directa** — historia `STORY-IPC-3-generacion-de-slug-unico/story.md` más la sesión de smoke del 2026-11-12 (`.context/testing/exploratory/smoke/smoke-2026-11-12-produccion.md`).
*   **No hay handoff de UI para IPC-3.** En `.context/testing/exploratory/handoffs/` solo existen `ui-to-api-db-2026-11-11-ipc-2.md` y `ui-to-api-db-2026-11-12-ipc-6.md`. La operación a validar se tomó del intento de alta registrado en el smoke de IPC-3; si la operación que querés validar es otra, decímelo y reescribo el mapeo.

## Mapeo UI/API -> DB Utilizado

*   **Tabla objetivo:** `public.professionals`
*   **Operación esperada:** `INSERT` durante el alta del profesional, precedido de una consulta de colisión por `slug`
*   **Clave de búsqueda:** `slug = 'test-qa-smoke-slug'` y `name = '[TEST-QA] Smoke Slug'`
*   **IDs de traza:** ninguno. El smoke no obtuvo `requestId` ni `correlationId`; la única evidencia de la operación es la petición `GET https://uuhstroppfwbuxudziqe.supabase.co/rest/v1/professionals?select=id&slug=eq.test-qa-smoke-slug`, que falló con `net::ERR_NAME_NOT_RESOLVED` antes de salir del equipo.
*   **Resultado conocido de antemano:** el `INSERT` **nunca se envió**, porque la consulta previa no llegó a destino. Por eso la validación de persistencia tiene un esperado de 0 filas y no de 1.

## Consultas Ejecutadas

**Ninguna.** No hay MCP de base de datos conectado en esta sesión (los servidores disponibles son Atlassian, Playwright, filesystem y opencode; ninguno expone SQL) y tampoco existe un cliente `psql` en el equipo ni un `.env` con `DB_CONNECTION_STRING` (`DB_CONNECTION_STRING` está comentada en `.env.example`).

Las consultas quedan entregadas abajo y en `.context/testing/exploratory/db/evidence/ipc-3-slug-query-results.md`, listas para ejecutar contra la base cuando haya acceso.

## Consultas Entregadas para Ejecutar

Todas son de **solo lectura**. Ejecutar en este orden: las primeras son baratas y las últimas dependen de privilegios sobre el esquema `auth`.

**Q1 — Persistencia del alta observada (0 filas esperadas):**
```sql
SELECT id, slug, name, created_at
FROM public.professionals
WHERE slug = 'test-qa-smoke-slug'
   OR name = '[TEST-QA] Smoke Slug';
```

**Q2 — Existencia real de la restricción de unicidad (bloqueante del plan de pruebas):**
```sql
SELECT conname, contype, pg_get_constraintdef(oid) AS definicion
FROM pg_constraint
WHERE conrelid = 'public.professionals'::regclass
  AND contype IN ('u', 'p', 'c')
ORDER BY contype, conname;
```

**Q3 — Definición de la columna `slug` (nulabilidad y longitud):**
```sql
SELECT column_name, data_type, is_nullable, character_maximum_length
FROM information_schema.columns
WHERE table_schema = 'public' AND table_name = 'professionals'
ORDER BY ordinal_position;
```

**Q4 — Duplicados exactos de slug (unicidad global):**
```sql
SELECT slug, count(*) AS ocurrencias
FROM public.professionals
GROUP BY slug
HAVING count(*) > 1;
```

**Q5 — Duplicados ignorando mayúsculas y espacios:**
```sql
SELECT lower(btrim(slug)) AS slug_normalizado, count(*) AS ocurrencias
FROM public.professionals
GROUP BY 1
HAVING count(*) > 1;
```

**Q6 — Slugs fuera del formato kebab-case:**
```sql
SELECT count(*) AS fuera_de_formato
FROM public.professionals
WHERE slug !~ '^[a-z0-9]+(-[a-z0-9]+)*$';
```

**Q7 — Slugs vacíos o nulos:**
```sql
SELECT count(*) AS vacios
FROM public.professionals
WHERE slug IS NULL OR btrim(slug) = '';
```

**Q8 — Guiones duplicados o en los extremos:**
```sql
SELECT count(*) AS con_separadores_invalidos
FROM public.professionals
WHERE slug ~ '--' OR slug ~ '^-' OR slug ~ '-$';
```

**Q9 — Longitudes extremas del slug:**
```sql
SELECT min(length(slug)) AS largo_minimo,
       max(length(slug)) AS largo_maximo,
       count(*) FILTER (WHERE length(slug) > 100) AS mayores_a_100
FROM public.professionals;
```

**Q10 — Slugs que ocupan rutas reservadas de la aplicación:**
```sql
SELECT slug
FROM public.professionals
WHERE slug IN ('login', 'register', 'signup', 'registro', 'dashboard', 'api',
               'admin', 'settings', 'availability', 'appointments', 'clients',
               'logout', 'reset-password', 'favicon.ico', 'robots.txt');
```

**Q11 — Slugs con sufijo numérico y nombres terminados en dígito (cruce del defecto 7):**
```sql
SELECT count(*) FILTER (WHERE slug ~ '-[0-9]+$') AS con_sufijo_numerico,
       count(*) FILTER (WHERE name ~ '[0-9]$')   AS nombres_terminados_en_digito
FROM public.professionals;
```

**Q12 — Familias de slugs que comparten base (colisiones resueltas):**
```sql
SELECT regexp_replace(slug, '-[0-9]+$', '') AS base, count(*) AS variantes
FROM public.professionals
GROUP BY 1
HAVING count(*) > 1
ORDER BY variantes DESC
LIMIT 50;
```

**Q13 — Usuarios de Auth sin perfil en `professionals` (relación y alta parcial):**
```sql
SELECT count(*) AS auth_sin_perfil
FROM auth.users u
LEFT JOIN public.professionals p ON p.id = u.id
WHERE p.id IS NULL;
```

**Q14 — Triggers sobre `professionals`:**
```sql
SELECT tgname, pg_get_triggerdef(oid) AS definicion
FROM pg_trigger
WHERE tgrelid = 'public.professionals'::regclass
  AND NOT tgisinternal;
```

**Q15 — Políticas RLS y qué condición aplican:**
```sql
SELECT polname, polcmd, pg_get_expr(polqual, polrelid) AS using_expr
FROM pg_policy
WHERE polrelid = 'public.professionals'::regclass;
```

**Q16 — Volumen de referencia (contexto, sin PII):**
```sql
SELECT count(*) AS total_profesionales FROM public.professionals;
```

## Resultados

| Validación | Esperado | Observado | Resultado |
| :--- | :--- | :--- | :--- |
| V1 · Persistencia del alta observada en el smoke (Q1) | 0 filas | — | No ejecutado |
| V2 · Restricción `UNIQUE` sobre `slug` existe en la base real (Q2) | 1 restricción `unique` que incluya `slug` | — | No ejecutado |
| V3 · `slug` es `NOT NULL` y sin longitud máxima (Q3) | `is_nullable = NO`, sin `character_maximum_length` | — | No ejecutado |
| V4 · Unicidad global exacta (Q4) | 0 filas | — | No ejecutado |
| V5 · Unicidad sin distinguir mayúsculas ni espacios (Q5) | 0 filas | — | No ejecutado |
| V6 · Formato kebab-case en minúsculas (Q6) | 0 filas | — | No ejecutado |
| V7 · Sin slugs vacíos ni nulos (Q7) | 0 filas | — | No ejecutado |
| V8 · Sin guiones duplicados ni en extremos (Q8) | 0 filas | — | No ejecutado |
| V9 · Longitud máxima del slug (Q9) | `largo_maximo ≤ 100` y `mayores_a_100 = 0` | — | No ejecutado |
| V10 · Ningún slug ocupa una ruta reservada (Q10) | 0 filas | — | No ejecutado |
| V11 · Cruce sufijo numérico / nombres con dígito (Q11) | Conteos coherentes con el criterio que se defina | — | No ejecutado |
| V12 · Familias de slugs con sufijo incremental (Q12) | Sin huecos ni saltos en la numeración | — | No ejecutado |
| V13 · Todo usuario de Auth tiene perfil en `professionals` (Q13) | `auth_sin_perfil = 0` | — | No ejecutado |
| V14 · Triggers del alta y de auditoría (Q14) | Trigger de creación de perfil identificado | — | No ejecutado |
| V15 · Política RLS de lectura pública de `professionals` (Q15) | Política `SELECT` pública; verificar si expone `email` | — | No ejecutado |
| V16 · Volumen de referencia (Q16) | Solo contexto | — | No ejecutado |

**Por qué todo quedó sin ejecutar:** no hay canal de consulta disponible. Faltan las tres cosas a la vez: un MCP de base de datos (no hay ninguno conectado), un cliente SQL local (`psql` no está instalado) y una cadena de conexión (`.env` no existe y `DB_CONNECTION_STRING` está comentada en `.env.example`). A eso se suma que el proyecto de Supabase del despliegue no resuelve por DNS desde el 2026-11-12, así que ni siquiera una cadena correcta habría conectado.

## Trazabilidad

| Historia (US) | Caso / escenario | Bug | Evidencia |
| :--- | :--- | :--- | :--- |
| IPC-3 | Escenario 1 — alta y asignación de `carlos-rojas` (persistencia) | pendiente | `.../db/evidence/ipc-3-slug-query-results.md` · Q1 |
| IPC-3 | Escenarios 3 y 4 — colisión y sufijo incremental | pendiente | `.../evidence/ipc-3-slug-query-results.md` · Q4, Q12 |
| IPC-3 | Escenario 2 y 5 — normalización y nombres no representables | pendiente | `.../evidence/ipc-3-slug-query-results.md` · Q6, Q7, Q8 |
| IPC-3 | Escenario 6 — colisión con rutas del sistema | pendiente | `.../evidence/ipc-3-slug-query-results.md` · Q10 |
| IPC-3 | Escenario 7 — registros simultáneos (violación de unicidad) | pendiente | `.../evidence/ipc-3-slug-query-results.md` · Q2, Q13 |
| IPC-3 | Escenario 10 — longitud máxima del slug | pendiente | `.../evidence/ipc-3-slug-query-results.md` · Q3, Q9 |
| IPC-3 | Escenario 12 — liberación del slug de una cuenta eliminada | pendiente | No verificable en solo lectura: requiere borrar una cuenta |
| IPC-3 | Observación de seguridad — exposición de `email` en la lectura pública | pendiente | `.../evidence/ipc-3-slug-query-results.md` · Q15 |

## Sin cobertura

*   **Toda la capa DB de IPC-3:** no se ejecutó ninguna de las 16 validaciones. No hay MCP de base de datos, ni cliente `psql`, ni cadena de conexión, y el proyecto de Supabase no resuelve por DNS.
*   **Persistencia del slug del alta observada:** el smoke del 2026-11-12 ya mostró que el `INSERT` nunca salió del equipo, pero eso es evidencia de red, no de base: no se confirmó contra la tabla que no exista el registro.
*   **Liberación o reutilización del slug de una cuenta eliminada (Escenario 12):** exige una operación de escritura (borrar la cuenta) que este prompt prohíbe. Queda fuera de alcance hasta que la ejecute una persona.
*   **Concurrencia real (Escenario 7):** no se puede reproducir en solo lectura; hace falta un entorno aislado y dos altas simultáneas.
*   **Datos de otros profesionales:** las consultas de unicidad, formato y longitud recorren toda la tabla `professionals`, que en producción contiene personas reales. Cuando se ejecuten, hay que quedarse con los conteos y no transcribir slugs ni nombres.
*   **Migraciones y esquema:** no hay migraciones versionadas (`04-notas-tecnicas.md`), así que la única fuente de verdad del esquema es la base misma. Mientras no se consulte, lo que dicen `system-design.md` y `04-notas-tecnicas.md` sigue siendo intención, no hecho verificado.
