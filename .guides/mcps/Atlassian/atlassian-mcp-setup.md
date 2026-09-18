# Guía de Configuración: Atlassian MCP (Jira/Confluence)

## 📌 Descripción
Este servidor MCP conecta tu Asistente de IA directamente con tu instancia de Jira y Confluence. Permite a la IA:
*   Leer User Stories y Criterios de Aceptación.
*   Crear Tickets de Bugs.
*   Buscar documentación en Confluence.

**Servidor oficial:** el **Atlassian Remote MCP Server**. No es un paquete que instales: es un servicio al que tu asistente se conecta por internet.

**Dirección:** `https://mcp.atlassian.com/v1/mcp/authv2`

## 🛠️ Requisitos Previos

1.  **Cuenta de Atlassian** con acceso a un sitio de Jira o Confluence **en la nube**.
2.  **Que tu organización permita el dominio de tu herramienta.** Es un permiso de
    administración, no de tu usuario, y se explica en la sección siguiente.
3.  **Nada más.** No hace falta crear un token.

> 🔒 **Y eso es lo importante.** El servidor autentica con **OAuth**: se abre el navegador, iniciás sesión como siempre, das permiso, y listo. **No pegás ninguna credencial en ningún archivo.**
>
> Además la IA queda con **tus mismos permisos**: si vos no podés ver un proyecto, ella tampoco. No hay forma de que se le escape algo que a vos no te corresponde.

## 🔐 El permiso que va antes de todo

Atlassian no deja que cualquier herramienta de IA se conecte: hay una **lista de dominios
permitidos** por organización. Si el dominio de tu asistente no está en ella, la autorización
falla **antes** de llegar a pedirte usuario y contraseña.

Se administra en:

> Administración de Atlassian → elegí tu organización → **Rovo** → **Servidor MCP de Rovo**

Y tiene tres pestañas que hacen cosas distintas:

### Dominios — quién puede conectarse

*   **“Permitir los dominios compatibles con Atlassian”** habilita la lista que mantiene
    Atlassian. Suele venir encendido, y **alcanza si tu herramienta está en esa lista**.
*   Si no está, hay que agregarla a mano en **Tus dominios → Añadir dominio**.

> ⚠️ **Acá hay una trampa que cuesta una tarde.** El mensaje de error dice *“tu administrador
> **bloqueó** este dominio”*, y nadie lo bloqueó: simplemente nunca estuvo permitido. Es la
> diferencia entre una lista negra y una lista blanca, y el mensaje no la hace.
>
> **Y lo que hay que agregar es la URL de retorno completa**, la misma que nombra el error —no
> alcanza con el dominio ni con un comodín de ruta—. Si el error dice
> `https://tu-herramienta.ejemplo/oauth-callback`, eso exacto es lo que va en la lista.

El formato tiene una sola regla que no perdona: **siempre con protocolo.** `https://algo.com`
vale; `algo.com` no.

### Permisos — qué puede hacer una vez conectada

Cinco grupos, cada uno con su interruptor, y **el reparto de fábrica ya dice bastante**:

| Grupo | Cómo viene | Qué habilita |
| :--- | :--- | :--- |
| **Leer** | ✅ todo | Ver y leer datos de la organización |
| **Escribir** | ✅ todo | **Crear y modificar** datos |
| **Buscar** | ✅ todo | Buscar en la organización |
| **Eliminar** | 🔴 apagado | **Borrar de forma permanente** datos de toda la organización |
| **Gestionar** | 🔴 apagado | Administrar espacios y configuraciones de toda la organización |

> 🔐 **Dejá *Eliminar* y *Gestionar* apagados.** Vienen así a propósito, y para trabajo de
> QA no hacen falta: se escriben tickets, se comentan, se actualizan estados. **Nada de eso
> necesita borrar nada.**
>
> Y prestá atención a lo que implica que *Escribir* venga encendido: **desde el minuto uno la
> IA puede crear y modificar tickets** sin que nadie lo habilite. Es exactamente lo que
> queremos, pero conviene saberlo antes y no después.

### Autenticación — cómo prueba quién es

OAuth por defecto, que es lo que usamos acá. También se puede habilitar autenticación **por
token de API**, para herramientas que no pueden abrir un navegador. Si vas por ese camino,
ese token es una credencial: **nunca dentro del repositorio**.

## ⚙️ Configuración

### Opción A: Conexión directa (recomendada)

Si tu asistente soporta servidores MCP remotos, alcanza con darle la dirección.

**Claude Code**, desde la terminal:

```bash
claude mcp add --transport http atlassian https://mcp.atlassian.com/v1/mcp/authv2
```

**Aplicaciones de escritorio y otros clientes**, en el archivo JSON de configuración:

```json
"atlassian": {
  "url": "https://mcp.atlassian.com/v1/mcp/authv2"
}
```

La primera vez que la IA use una herramienta de Jira se va a abrir el navegador pidiéndote que autorices. Aceptá y ya está.

### Opción B: Con puente, si tu asistente solo habla con procesos locales

Algunos clientes todavía no saben conectarse a un servidor remoto y esperan un comando. Para esos hay un puente:

```json
"atlassian": {
  "command": "npx",
  "args": [
    "-y",
    "mcp-remote",
    "https://mcp.atlassian.com/v1/mcp/authv2"
  ]
}
```

Hace exactamente lo mismo —incluida la autenticación por navegador—, solo que corriendo un proceso local que reenvía las llamadas.

> 🔄 **Si usás Jira Server o Data Center** (instalado en tu empresa, no en la nube de Atlassian), el servidor oficial no te sirve: es solo para Cloud. Ahí hace falta un servidor comunitario y sí vas a necesitar un token. Para el curso trabajamos con **Jira Cloud en su plan gratuito**, que es el caso de la Opción A.

## 🧪 Verificación

Pídele a tu IA:
> "Busca en Jira el ticket con clave PROJ-1 y dime su estado."

Si responde con el título y estado del ticket, ¡la conexión es exitosa!

## ⚠️ Solución de Problemas

**Se abre el navegador pero no vuelve, o el asistente sigue diciendo que no está conectado:**
*   Cerrá y volvé a abrir el asistente. La autorización se guarda recién cuando la sesión se reinicia.
*   Fijate que hayas iniciado sesión con **la cuenta que tiene acceso al proyecto**, y no con otra que también tengas abierta en ese navegador.

**“Access to this domain is restricted” / “tu administrador bloqueó este dominio”:**
*   El dominio de tu herramienta no está en la lista de la organización. Agregá **la URL de
    retorno completa que nombra el propio error**, no el dominio a secas.
*   Después de agregarla, **cerrá y reabrí el asistente**: el enlace de autorización que ya
    tenías es de un solo uso y hay que generar uno nuevo.

**Te pide pegar un código largo en la consola y entra cortado:**
*   Si estás en una consola de Windows, el corte suele caer en **1024 caracteres exactos**, y
    después no te deja ni seguir escribiendo a mano. **No es la herramienta: es el buffer de
    línea del terminal.**
*   Probá en otra terminal —una que no use el `conhost` clásico—, o en Git Bash o WSL, donde
    el buffer es de 4096. Reiniciar el asistente y rehacer el flujo también suele destrabarlo.

**Error 401 o 403:**
*   La autorización venció o se revocó. Volvé a conectar el servidor y autorizá de nuevo.
*   Si el error aparece solo con **algunos** proyectos, no es un problema de configuración: es que tu usuario no tiene permiso sobre ellos. El MCP no te da más acceso del que ya tenés.

**La IA no encuentra un ticket que vos sí ves:**
*   Verificá que estés apuntando al sitio correcto. Si tu cuenta tiene más de un sitio de Atlassian, pedile a la IA que te liste los sitios accesibles antes de buscar.
