# Estrategia de Datos de Prueba: Cita.ai

**Versión:** 1.0 (Estrategia de Test Data & Privacidad para QA)  
**Estado:** Vigente  
**Tipo de proyecto:** Brownfield  
**Rol:** Test Data Architect & QA Privacy Lead  

---

## 1. Fuentes de Datos

En el estado actual de Cita.ai conviven tres realidades documentales respecto al origen de los datos de prueba:

*   **Producción Viva (Fuente Actual Real):** Debido a que no existe un ambiente de homologación/UAT activo (`documentacion para QA/nota-ambientes-y-accesos.md`), todas las interacciones de validación de QA se ejecutan directamente sobre la base de datos de producción alojada en Supabase Cloud. Los datos existentes corresponden a clientes y profesionales reales captados desde el soft launch de marzo de 2026.
*   **Copia de Producción con Anonimización Parcial (Histórica en UAT):** Cuando existía el proyecto de UAT, su base se refrescaba clonando la base de datos de producción (`04-notas-tecnicas.md` · "los datos de UAT").
*   **Datos Generados al Vuelo (QA Autónomo):** Los testers aprovisionan profesionales y turnos en tiempo real mediante el flujo de registro público y la interfaz de reserva, utilizando casillas temporales de correo descartables (`@mailinator.com`).
*   **Inexistencia de Seeds Automatizados:** No existen scripts de migración versionados, ni scripts de poblado inicial (`db/seed.ts`), ni fábricas de datos (`04-notas-tecnicas.md` · "los datos de UAT"). Si se requiriera rearmar la base desde cero, no existe procedimiento automatizado para recrearla.

---

## 2. Gestión de Usuarios de Prueba

Para garantizar la cobertura funcional de los diferentes roles sin alterar cuentas de usuarios reales ni violar las políticas de seguridad:

| Rol | Identificador / Cuenta sugerida | Contraseña | Propósito y Alcance de Pruebas |
| :--- | :--- | :--- | :--- |
| **Profesional (Cuenta Fija QA)** | `qa-profesional-base@mailinator.com` | *Definida en .env local* | Pruebas de configuración de agenda, reemplazo de reglas semanales, creación/eliminación de bloqueos y verificación del dashboard. |
| **Profesional (Alerta Freemium)** | `qa-profesional-limite@mailinator.com` | *Definida en .env local* | Validación de la regla de 10 clientes únicos, recepción de error `LIMIT_REACHED`, visualización del banner del Plan Pro y correo festivo. |
| **Cliente Final (Invitado / Anónimo)** | `qa-cliente-[id]@mailinator.com` | *No requiere (Flujo sin auth)* | Flujos de auto-reserva pública en 30 segundos, validación de concurrencia y recepción de confirmaciones de turno por email. |
| **Profesional Real (Fernando M.)** | `[Cuenta Real de Fernando]` | *Restringida / Privada* | **PROHIBIDO EL ACCESO Y MODIFICACIÓN.** Contiene clientes y reservas comerciales legítimas en producción viva (`documentacion para QA/nota-ambientes-y-accesos.md`). |

---

## 3. Generación de Datos Sintéticos

Dada la necesidad imperiosa de desvincular las pruebas de los datos de producción reales, se establece el siguiente plan para la generación de datos sintéticos controlados:

*   **Librerías Recomendadas:** Adopción de `@faker-js/faker` acoplada a Node.js / TypeScript para la confección de fixtures reproducibles.
*   **Estrategia de Generación de Entidades:**
    *   *Profesionales Sintéticos:* Nombres ficticios, especialidades realistas (psicología, entrenamiento, nutrición), duración de citas válidas (45, 50, 60 min) y slugs normalizados (`[faker.person.firstName]-[faker.person.lastName]-[hash]`).
    *   *Reglas de Disponibilidad:* Generación de franjas estándar continuas de lunes a viernes (ej. 09:00 a 18:00) evitando el cruce de medianoche (limitación conocida en `04-notas-tecnicas.md`).
    *   *Citas y Clientes de Carga:* Creación de lotes de clientes sintéticos con correos únicos del tipo `test-client-####@example.com` o `@mailinator.com` para testear el umbral del cliente 11 y la velocidad de cálculo de slots en memoria.
*   **Automatización de Seeds:** Creación de un script `scripts/seed-synthetic.ts` que se conecte vía Supabase JS Client tipado y pueble un esquema de prueba aislado sin recurrir a clones de producción.

---

## 4. Privacidad y Seguridad (PII — Personally Identifiable Information)

> ⚠️ [!CAUTION]
> **ALERTA CRÍTICA DE PRIVACIDAD Y SEGURIDAD:**  
> Se han detectado **dos vulnerabilidades críticas de PII** en la gestión de infraestructura y datos del proyecto:
> 1. **Anonimización Parcial Ineficaz:** En el ambiente UAT previo, el script de anonimización implementado por desarrollo **únicamente ofuscó la tabla `clients`**. La tabla `professionals` se mantuvo con **nombres y direcciones de correo reales de profesionales de producción** para no romper los slugs de las URLs (`04-notas-tecnicas.md`). Esto constituye una fuga de datos personales en entornos de prueba.
> 2. **Operación Directa sobre Base de Producción:** Al estar UAT inactivo, cualquier prueba de QA se ejecuta sobre la base real de producción, teniendo visibilidad de nombres y correos reales de usuarios que operan la plataforma desde marzo de 2026.

### Política de Protección de Datos Personales
1. **Regla de Cero PII en Testing:** Queda estrictamente prohibido clonar bases de producción hacia entornos de prueba locales o compartidos sin un pipeline de enmascaramiento total y determinista.
2. **Protocolo de Anonimización Obligatorio (Si se reactiva UAT o Staging):**
   *   `clients.name`: Reemplazar por datos generados vía Faker (`faker.person.fullName()`).
   *   `clients.email`: Reemplazar por formato determinista anonimizado (`client-[UUID]@test-anonymized.local`).
   *   `professionals.name` y `professionals.email`: Deben anonimizarse obligatoriamente (`prof-[UUID]@test-anonymized.local`), regenerando los `slugs` correspondientes en las URLs públicas (`prof-[UUID]`).
   *   `appointments.notes` / `time_blocks.reason`: Truncar o reemplazar por cadenas genéricas para evitar fugas de información médica o confidencial de pacientes.
3. **Control de Fugas por Correo Electrónico:** Todas las direcciones de correo empleadas en pruebas funcionales deben pertenecer a dominios reservados RFC 2606 (`@example.com`, `@test.com`) para evitar envíos indeseados, o a casillas efímeras controladas (`@mailinator.com`) cuando se valide la entrega efectiva vía Resend.

---

## 5. Limpieza y Reset (Teardown)

*   **Estado Actual:** No existe mecanismo de rollback ni limpieza automática en la base de datos (`04-notas-tecnicas.md` · "no hay script de seed ni migraciones versionadas"; `documentacion para QA/nota-ambientes-y-accesos.md` · "lo que se cargó, se cargó").
*   **Riesgo de Acumulación:** La creación descontrolada de clientes y turnos de prueba incrementa permanentemente el conteo freemium (`appointments` acumulados) y satura la tabla global de `clients`.
*   **Procedimiento de Limpieza Transitorio (Manual / Script de Soporte):**
    *   Identificar registros creados durante sesiones de prueba mediante el prefijo formal `[TEST-QA]` en el nombre y correos con subdominio `@mailinator.com`.
    *   Ejecutar periódicamente una rutina de depuración en base de datos:
      ```sql
      -- Eliminar turnos de prueba creados por QA
      DELETE FROM appointments 
      WHERE client_id IN (SELECT id FROM clients WHERE email LIKE '%@mailinator.com');

      -- Eliminar clientes de prueba creados por QA
      DELETE FROM clients 
      WHERE email LIKE '%@mailinator.com';

      -- Eliminar bloqueos de agenda de prueba
      DELETE FROM time_blocks 
      WHERE reason LIKE '[TEST-QA]%';
      ```
*   **Estrategia Definitiva Recomendada:** Diseñar un hook de teardown en Playwright / Jest que limpie automáticamente los registros creados al finalizar cada suite de pruebas utilizando la clave de servicio (`SUPABASE_SERVICE_ROLE_KEY`) en un esquema aislado.

---

## Fuentes

| Dato / afirmación | De dónde sale |
| :--- | :--- |
| Refresco de base copiando producción y anonimización parcial solo en `clients` | `04-notas-tecnicas.md` · "los datos de UAT — leer esto" |
| Persistencia de nombres y mails reales de profesionales en pruebas | `04-notas-tecnicas.md` · "los datos de UAT — leer esto" |
| Inexistencia de scripts de seed y migraciones versionadas | `04-notas-tecnicas.md` · "los datos de UAT" |
| Credenciales en archivo txt no ignorado en la raíz del proyecto | `04-notas-tecnicas.md` · "los datos de UAT" |
| Uso obligatorio de casillas Mailinator para validar recepción de emails | `04-notas-tecnicas.md`; `documentacion para QA/nota-ambientes-y-accesos.md` |
| Prohibición estricta de manipular la cuenta comercial de Fernando | `documentacion para QA/nota-ambientes-y-accesos.md` · "un par de cosas"; `documentacion para QA/hilo-mail-alcance-qa.md` |
| Operación obligada de pruebas sobre la base viva de producción | `documentacion para QA/nota-ambientes-y-accesos.md` · "lo del ambiente de UAT" |
| Especificación que prometía datos ficticios y sintéticos en UAT | `03-especificacion-funcional-v0.3.md` · sección 10 |
| Imposibilidad de reversión o backup propio de base de datos | `documentacion para QA/nota-ambientes-y-accesos.md` · "lo del ambiente de UAT" |
| Volúmenes exactos de registros de clientes reales actualmente en producción | **Hipótesis** — no hay documento que lo respalde |
| Tasa de crecimiento diario de la tabla `appointments` | **Hipótesis** — no hay documento que lo respalde |

---

## Contradicciones detectadas

*   **Naturaleza de los Datos de Prueba (Sintéticos vs. Clon de Producción con Fuga de PII):**  
    *   *Qué afirma la Especificación Funcional:* `03-especificacion-funcional-v0.3.md` (sección 10) sostiene formalmente que la validación funcional en UAT se ejecuta contra *"datos de prueba generados para ese fin"* y que los usuarios cargados *"son ficticios y no corresponden a personas reales"*.  
    *   *Qué revela la confesión técnica de Diego:* En `04-notas-tecnicas.md` se admite explícitamente que la base se generó clonando producción y que la anonimización fue parcial, manteniendo los profesionales y correos reales. Para agravar la situación, `documentacion para QA/nota-ambientes-y-accesos.md` confirma que ni siquiera se usa UAT, operando directamente sobre producción real.  
    *   *Postura adoptada en este documento:* Se califica la afirmación de la especificación como **falsa en la práctica**, se cataloga la gestión como **riesgo crítico de PII** y se exige la implementación de datos sintéticos reales.

---

## Preguntas abiertas

*   **¿Se otorgará a QA una credencial de servicio (`service_role`) para ejecutar scripts de limpieza (teardown) automáticos?** Actualmente, al no contar con acceso directo a la consola de base de datos o API administrativa, no es posible limpiar turnos de prueba de forma programática.
*   **¿Cuándo se incorporará la tabla `professionals` al script de ofuscación?** Si se reactiva un entorno secundario, es imprescindible asegurar que los profesionales y sus slugs sean anonimizados sin romper la integridad referencial.
*   **¿Existe un plan de backup periódico e independiente de Supabase?** Al no contar con migraciones ni seeds, cualquier corrupción provocada durante pruebas en producción pondría en riesgo la totalidad de las citas del negocio.
