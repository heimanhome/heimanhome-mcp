# Heiman Smart Home MCP — Guía de uso (Español)

Consulta y controla dispositivos del hogar inteligente en lenguaje natural con un asistente de IA: luces, climatización, cortinas, temperatura y humedad, registros de dispositivos, estancias, escenas y alarma.

| | |
| --- | --- |
| Endpoint | `https://spmcp.heimanhome.com/mcp` |
| Transporte | MCP Streamable HTTP |
| Autorización | OAuth 2.1 con PKCE (inicia sesión con tu cuenta domótica) |
| Repositorio | `https://github.com/heimanhome/heimanhome-mcp.git` |
| Soporte | `help@heimanhome.com` |

> La versión inglesa [`../README.md`](../README.md) es la autoritativa; esta traducción es orientativa.

## 1. Conectar y autorizar

1. Añade el endpoint a tu cliente MCP con una de las configuraciones siguientes.
2. El cliente abre la página de autorización; inicia sesión con tu cuenta domótica.
3. Revisa los permisos: solo lectura por defecto; activa permisos de escritura solo si vas a controlar dispositivos.
4. Confirma. El cliente guarda el token; las credenciales no se guardan en este repositorio.

## 2. Configuración del cliente

**`mcp.json` genérico** (Claude Desktop, Cursor, Cline, Windsurf, Zed, VS Code + GitHub Copilot, MCP Inspector):

```json
{
  "mcpServers": {
    "heiman-smart-home": {
      "type": "streamableHttp",
      "url": "https://spmcp.heimanhome.com/mcp",
      "timeout": 30000
    }
  }
}
```

**Claude Code**:

```bash
claude mcp add --transport http heiman-smart-home https://spmcp.heimanhome.com/mcp
```

**Clientes solo stdio** — puente con `mcp-remote`:

```json
{
  "mcpServers": {
    "heiman-smart-home": {
      "command": "npx",
      "args": ["-y", "mcp-remote", "https://spmcp.heimanhome.com/mcp"]
    }
  }
}
```

**Clientes alojados** (ChatGPT, Claude.ai, Copilot Studio, Gemini CLI, WorkBuddy / CodeBuddy): registra un servidor MCP remoto en su interfaz con la misma URL.

## 3. Ámbitos y escrituras seguras

| Ámbito | Permite |
| --- | --- |
| `device:read` | Listas, estado, capacidades, firmware, registros, sensores |
| `device:control` | Encendido/apagado, brillo, temperatura objetivo, cortinas |
| `scene:read` | Escenas, estancias, hogares, automatizaciones |
| `scene:execute` | Ejecutar, crear, modificar, eliminar escenas y automatizaciones |
| `security:control` | Armado / desarmado, confirmación de alarmas |

- Sin solicitud explícita solo se concede acceso de lectura.
- Las acciones sensibles exigen `confirm=true`; previsualízalas con `dry_run=true`.
- Al reintentar, usa `idempotent_key` y consulta `get_command_status(trace_id)`. Una respuesta correcta significa que el comando fue **aceptado**, no necesariamente ejecutado.
- `whoami` muestra la identidad y los ámbitos concedidos. Si se rechaza una llamada, corrige el ámbito, el parámetro o la confirmación.

## 4. Ejemplos de peticiones

```
Ajusta el brillo de la luz del salón al 50 %
¿Cuál es la temperatura y la humedad actuales del salón?
Enciende el aire acondicionado del salón y ajústalo a 26 °C
Cierra las cortinas y ejecuta el escenario «En casa»
Muestra la temperatura y la humedad medias de las últimas 24 horas
```

El asistente responde en tu idioma; los ejemplos en 8 idiomas están en `connector-meta.json` → `i18n.locales`.

## 5. Problemas frecuentes

| Síntoma | Solución |
| --- | --- |
| `401` / autorización caducada | Vuelve a conectar y autorizar |
| Ámbito no concedido | Vuelve a autorizar y marca el ámbito necesario |
| Comando aceptado, dispositivo sin cambios | Consulta `get_command_status(trace_id)` y espera |
| Demasiadas peticiones | Espera antes de reintentar, sin repetir la misma llamada |
| Sin conexión | Comprueba Streamable HTTP + OAuth y el acceso HTTPS al endpoint |

## 6. Seguridad y privacidad

- Los comandos modifican dispositivos reales: revisa la vista previa `dry_run` y limita los permisos de escritura.
- Los tokens y credenciales permanecen en el servidor. Nunca publiques tokens ni contraseñas en issues; reporta incidencias a `help@heimanhome.com`.
- El operador del servicio remoto es el responsable de los datos: consulta `OPEN-SOURCE-STATEMENT.md`.

## Licencia y más documentación

MIT (ver `LICENSE`); propiedad y marcas en `NOTICE`; lista completa de herramientas en `skills/heimanhome-skill/SKILL.md`.
