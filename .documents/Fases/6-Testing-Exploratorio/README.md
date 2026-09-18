# Fase 6: Testing Exploratorio (La Trifuerza)

## 🎯 Objetivo de la Fase
Ejecutar pruebas dinámicas manuales o asistidas para validar la funcionalidad y encontrar defectos no obvios. Es la fase de "romper el sistema". Se continúa con la valoración o calificación de cada CP propuesto en las US para la etapa de documentación.

> ⚠️ **Con una condición que no siempre se cumple: que haya un sistema que se pueda romper.** Antes de diseñar la sesión hay que mirar `environments.md`. Si el proyecto no tiene un entorno separado de pruebas, **lo que vas a atacar es lo que usan las personas reales**, y la sesión se acota: se prueban validaciones —que rechazan y no dejan rastro— y **no** se carga volumen, ni se disparan avisos que salen de verdad.
>
> Los escenarios que queden fuera **se diseñan igual y se marcan `No ejecutado`, con el motivo**. Esa lista es el mejor argumento que vas a tener para pedir un entorno de pruebas.

## 🔑 Conceptos Clave

### 1. La Trifuerza del Testing
Un enfoque holístico que cubre las 3 capas de la aplicación:
*   **UI Layer:** Lo que ve el usuario (Front-end).
*   **API Layer:** La lógica de negocio y comunicación (Back-end).
*   **DB Layer:** La integridad y persistencia de datos (Storage).

### 2. Charter de Prueba
Una misión exploratoria con un objetivo claro y un tiempo limitado (Timeboxing). Evita probar sin rumbo.

### 3. Bug Reporting de Calidad
Un buen reporte de bug debe ser:
*   **Reproducible:** Pasos claros.
*   **Específico:** Qué pasó vs qué debió pasar.
*   **Evidenciado:** Screenshots, logs, datos.

## 🛠️ Herramientas Utilizadas
*   **Prompts de IA:** `smoke-test.md`, `exploratory-ui-test.md`, `exploratory-api-test.md`, `exploratory-db-test.md`, `bug-report.md`.
*   **Playwright MCP:** Para navegación asistida.
*   **Postman / cURL:** Para pruebas de API.
*   **BD MCP:** Para pruebas de Bases de Datos

### 4. Lo que no se ejecutó no está probado
La regla que sostiene todos los reportes de esta fase: un caso sin evidencia se marca **No ejecutado**, nunca *PASS*. Un caso diseñado no es un caso corrido, y una sesión que no distingue las dos cosas es peor que no tener sesión.

## 📝 Entregables Esperados
Al finalizar esta fase tendrás, en `.context/testing/exploratory/`:

| Archivo | Lo escribe |
| :--- | :--- |
| `smoke/smoke-[fecha]-[entorno].md` | `smoke-test.md` |
| `ui/session-[fecha]-[feature].md` + capturas | `exploratory-ui-test.md` |
| `handoffs/ui-to-api-db-[fecha]-[feature].md` | `exploratory-ui-test.md` |
| `api/session-[fecha]-[endpoint].md` + colección Postman | `exploratory-api-test.md` |
| `db/session-[fecha]-[feature].md` | `exploratory-db-test.md` |
| `bugs/bug-[fecha]-[slug].md` | `bug-report.md` |

> ⚠️ El archivo `*.postman_environment.json` **no se versiona**: `.gitignore` lo excluye porque es el que lleva los tokens. Las colecciones sí se versionan.
