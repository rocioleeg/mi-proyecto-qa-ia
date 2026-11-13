# Hoja de ejecución — Consultas DB para IPC-3 (slug público único)

**Fecha:** 2026-11-13
**Entorno:** Producción (único activo)
**Sesión:** `../session-2026-11-13-ipc-3-slug.md`
**Estado:** todas las consultas **entregadas y sin ejecutar**

> ⚠️ La tabla `professionals` de producción contiene **nombres y correos reales**. Las consultas de este documento piden conteos y metadatos; si alguna devuelve filas con slugs o nombres, en el reporte se resume como conteo y no se transcriben los valores.

## Cómo conectarse

No hay MCP de base de datos conectado, ni `psql` instalado, ni `.env` en la raíz. La cadena de conexión no se escribe acá: `DB_CONNECTION_STRING` existe comentada en `.env.example` y debe cargarse como variable de entorno, nunca pegada en el chat ni en este archivo.

```bash
set -a; . ./.env; set +a
psql "$DB_CONNECTION_STRING" -f consultas.sql
```

## Bloque 1 — Persistencia y esquema

```sql
-- Q1 · Persistencia del alta observada en el smoke (esperado: 0 filas)
SELECT id, slug, name, created_at
FROM public.professionals
WHERE slug = 'test-qa-smoke-slug'
   OR name = '[TEST-QA] Smoke Slug';

-- Q2 · Restricciones de la tabla (esperado: una UNIQUE que incluya slug)
SELECT conname, contype, pg_get_constraintdef(oid) AS definicion
FROM pg_constraint
WHERE conrelid = 'public.professionals'::regclass
  AND contype IN ('u', 'p', 'c')
ORDER BY contype, conname;

-- Q3 · Definición de columnas (esperado: slug text NOT NULL, sin longitud máxima)
SELECT column_name, data_type, is_nullable, character_maximum_length
FROM information_schema.columns
WHERE table_schema = 'public' AND table_name = 'professionals'
ORDER BY ordinal_position;

-- Q14 · Triggers (esperado: trigger de alta de perfil y/o auditoría)
SELECT tgname, pg_get_triggerdef(oid) AS definicion
FROM pg_trigger
WHERE tgrelid = 'public.professionals'::regclass
  AND NOT tgisinternal;

-- Q15 · Políticas RLS (verificar si la lectura pública expone email)
SELECT polname, polcmd, pg_get_expr(polqual, polrelid) AS using_expr
FROM pg_policy
WHERE polrelid = 'public.professionals'::regclass;

-- Q16 · Volumen de referencia
SELECT count(*) AS total_profesionales FROM public.professionals;
```

## Bloque 2 — Unicidad y normalización

```sql
-- Q4 · Duplicados exactos (esperado: 0 filas)
SELECT slug, count(*) AS ocurrencias
FROM public.professionals
GROUP BY slug
HAVING count(*) > 1;

-- Q5 · Duplicados ignorando mayúsculas y espacios (esperado: 0 filas)
SELECT lower(btrim(slug)) AS slug_normalizado, count(*) AS ocurrencias
FROM public.professionals
GROUP BY 1
HAVING count(*) > 1;

-- Q6 · Formato kebab-case (esperado: 0)
SELECT count(*) AS fuera_de_formato
FROM public.professionals
WHERE slug !~ '^[a-z0-9]+(-[a-z0-9]+)*$';

-- Q7 · Vacíos o nulos (esperado: 0)
SELECT count(*) AS vacios
FROM public.professionals
WHERE slug IS NULL OR btrim(slug) = '';

-- Q8 · Guiones duplicados o en extremos (esperado: 0)
SELECT count(*) AS con_separadores_invalidos
FROM public.professionals
WHERE slug ~ '--' OR slug ~ '^-' OR slug ~ '-$';

-- Q9 · Longitudes (esperado: largo_maximo <= 100, mayores_a_100 = 0)
SELECT min(length(slug)) AS largo_minimo,
       max(length(slug)) AS largo_maximo,
       count(*) FILTER (WHERE length(slug) > 100) AS mayores_a_100
FROM public.professionals;
```

## Bloque 3 — Colisiones, sufijos y rutas reservadas

```sql
-- Q10 · Rutas reservadas ocupadas por un slug (esperado: 0 filas)
SELECT slug
FROM public.professionals
WHERE slug IN ('login', 'register', 'signup', 'registro', 'dashboard', 'api',
               'admin', 'settings', 'availability', 'appointments', 'clients',
               'logout', 'reset-password', 'favicon.ico', 'robots.txt');

-- Q11 · Cruce entre sufijo numérico y nombres que terminan en dígito
SELECT count(*) FILTER (WHERE slug ~ '-[0-9]+$') AS con_sufijo_numerico,
       count(*) FILTER (WHERE name ~ '[0-9]$')   AS nombres_terminados_en_digito
FROM public.professionals;

-- Q12 · Familias que comparten base (esperado: numeración sin huecos)
SELECT regexp_replace(slug, '-[0-9]+$', '') AS base, count(*) AS variantes
FROM public.professionals
GROUP BY 1
HAVING count(*) > 1
ORDER BY variantes DESC
LIMIT 50;

-- Q13 · Usuarios de Auth sin perfil (esperado: 0; requiere acceso al esquema auth)
SELECT count(*) AS auth_sin_perfil
FROM auth.users u
LEFT JOIN public.professionals p ON p.id = u.id
WHERE p.id IS NULL;
```

## Registro de resultados

Completar la columna **Observado** al ejecutar. Una celda vacía es *No ejecutado*, nunca *PASS*.

| # | Validación | Esperado | Observado | Resultado |
| :--- | :--- | :--- | :--- | :--- |
| Q1 | Persistencia del alta `[TEST-QA] Smoke Slug` | 0 filas | | No ejecutado |
| Q2 | Restricción `UNIQUE` sobre `slug` | 1 restricción | | No ejecutado |
| Q3 | `slug` `NOT NULL` y sin longitud máxima | `NO` / sin `character_maximum_length` | | No ejecutado |
| Q4 | Unicidad exacta global | 0 filas | | No ejecutado |
| Q5 | Unicidad sin distinguir mayúsculas ni espacios | 0 filas | | No ejecutado |
| Q6 | Formato kebab-case en minúsculas | 0 | | No ejecutado |
| Q7 | Sin slugs vacíos ni nulos | 0 | | No ejecutado |
| Q8 | Sin guiones duplicados ni en extremos | 0 | | No ejecutado |
| Q9 | Longitud máxima del slug | ≤ 100 | | No ejecutado |
| Q10 | Sin slugs que ocupen rutas reservadas | 0 filas | | No ejecutado |
| Q11 | Cruce sufijo numérico / nombres con dígito | conteos coherentes con el criterio definido | | No ejecutado |
| Q12 | Familias con sufijo incremental sin huecos | sin saltos | | No ejecutado |
| Q13 | Todo usuario de Auth con perfil | 0 | | No ejecutado |
| Q14 | Triggers del alta y de auditoría | trigger identificado | | No ejecutado |
| Q15 | RLS de lectura pública de `professionals` | política `SELECT` pública, sin `email` | | No ejecutado |
| Q16 | Volumen de referencia | solo contexto | | No ejecutado |

## Notas de ejecución

*   **Q6 y Q9 dependen de decisiones que no existen:** el formato exacto (qué pasa con `ñ`, apóstrofes, emojis) y la longitud máxima no están definidos en ningún documento. Si la consulta devuelve valores distintos de 0, primero hay que decidir si son defectos o si el criterio esperado estaba mal.
*   **Q10 necesita la lista de rutas reservadas**, que tampoco está documentada; la del `WHERE` es una hipótesis armada con las rutas que el PRD menciona.
*   **Q12 y Q11 juntas responden el defecto 7** de la inspección: si un profesional se llama "Ana Pérez 2", su slug base es `ana-perez-2` y compite con el sufijo incremental de otra persona.
*   **Q13 requiere privilegios sobre `auth.users`.** Si el usuario de consulta no los tiene, la validación queda sin ejecutar y no se puede reemplazar por otra: no hay vista pública equivalente.
*   **Nada de escritura.** Todas las consultas son `SELECT`. Para validar el Escenario 12 (liberación del slug de una cuenta eliminada) hace falta un `DELETE` que debe ejecutar una persona, no QA.
