# Fase 7: Documentación de Casos de Prueba (CPs)

## 🎯 Objetivo de la Fase
Transformar el conocimiento adquirido durante las pruebas exploratorias en activos documentales formales. Aquí creamos la "memoria a largo plazo" del proyecto para futuras regresiones y automatización.

## 🔑 Conceptos Clave

### 1. Candidatos de Prueba
No todo lo que se prueba merece ser documentado. Filtramos por:
*   **Criticidad:** ¿Es vital para el negocio?
*   **Frecuencia:** ¿Se usa mucho?
*   **Complejidad:** ¿Es propenso a fallar?

### 2. ROI de Automatización
Decisión económica sobre qué automatizar.
*   *Alto ROI:* Pruebas repetitivas, estables y críticas -> **Automatizar**.
*   *Bajo ROI:* Pruebas visuales, cambiantes o de una sola vez -> **Manual**.

### 3. Antes de documentar: ¿esto existe?

Un caso de prueba describe cómo verificar algo. **Si ese algo no está construido, el caso no
es documentación: es una lista de deseos con formato de caso de prueba.**

Por eso `test-analysis.md` mira el campo `Implementación:` de la historia antes de empezar. Una
historia `No encontrada` frena la fase; una `Sin verificar` sigue, pero queda dicho en el
reporte — porque los casos se estarán diseñando contra una especificación que nadie contrastó
con el sistema.

> Es el mismo criterio de la Fase 6, aplicado un paso más adelante: **no se mide contra algo
> que nadie fue a mirar.**

### 4. Trazabilidad
La capacidad de seguir el rastro:
`User Story` <-> `Test Case` <-> `Bug` <-> `Código`.
Esto asegura que si cambian los requisitos, sabemos qué pruebas actualizar.

## 🛠️ Herramientas Utilizadas
*   **Prompts de IA:** `test-analysis.md`, `test-prioritization.md`, `test-documentation.md`, `xray-api.md`.
*   **Jira / Xray:** Repositorio de casos de prueba.
    *   *Nota:* Si utilizas **Xray** y dado que aun no disponemos de un MCP oficial, utiliza el script `xray-api.md` ubicado en la carpeta de prompts para interactuar con su API.

## 📝 Entregables Esperados
Al finalizar esta fase tendrás:

| Archivo | Dónde queda | Lo escribe |
| :--- | :--- | :--- |
| `analisis-escenarios.md` | `.context/testing/documentation/[ID-US]/` | `test-analysis.md` |
| `priorizacion-roi.md` | `.context/testing/documentation/[ID-US]/` | `test-prioritization.md` |
| `[ID-CP]-[nombre-kebab].md` (uno por caso) | **`.context/PBI/…/STORY-[ID-US]-…/test-cases/`** | `test-documentation.md` |

**Los casos formales quedan con su historia, no en la carpeta de la fase.** El análisis y la
priorización son el trabajo intermedio y se quedan donde se produjeron; el caso de prueba es un
entregable de la historia y viaja con ella — así, quien cambia la historia ve al lado qué
pruebas quedan desactualizadas.

Los casos quedan **vinculados a su historia de usuario**: un caso de prueba sin historia vinculada no está terminado.
