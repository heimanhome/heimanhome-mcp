# Heiman Smart Home Connector — Español

Un **paquete de metadatos de conector MCP** para clientes compatibles con WorkBuddy / CodeBuddy. Permite a un asistente de IA consultar y controlar dispositivos del hogar inteligente en lenguaje natural: luces, climatización, cortinas, temperatura y humedad, registros de dispositivos, hogares y estancias, escenas, capacidades de los dispositivos, alarma, avisos e información de firmware (solo lectura).

> **Este repositorio contiene únicamente metadatos del conector, no la implementación del servidor.**
> Es una integración de terceros: **no es un producto oficial de Heiman ni de WorkBuddy / CodeBuddy**, y no cuenta con el respaldo ni la certificación de ninguna de esas partes. Véase `OPEN-SOURCE-STATEMENT.md`.

**Idioma:** el inglés es la versión autoritativa. Esta traducción es orientativa; en caso de discrepancia prevalece [`../README.md`](../README.md). Los textos legales (`LICENSE`, `NOTICE`, `OPEN-SOURCE-STATEMENT.md`) solo son vinculantes en inglés.

## Idiomas

| Idioma | Código | Documento |
| --- | --- | --- |
| English (autoritativo) | `en` | [`../README.md`](../README.md) |
| Deutsch | `de` | [`README.de.md`](README.de.md) |
| Français | `fr` | [`README.fr.md`](README.fr.md) |
| Español | `es` | `README.es.md` |
| Português | `pt` | [`README.pt.md`](README.pt.md) |
| العربية (RTL) | `ar` | [`README.ar.md`](README.ar.md) |
| Русский | `ru` | [`README.ru.md`](README.ru.md) |
| 简体中文 | `zh` | [`README.zh.md`](README.zh.md) |

Los códigos coinciden con `connector-meta.json` → `i18n.supported_locales`. La fuente de verdad de los textos localizados es `i18n.locales`.

## Contenido del paquete

Las rutas son relativas a la raíz del repositorio.

| Ruta | Función |
| --- | --- |
| `connector-meta.json` | Descriptor del conector, textos localizados, `auth_mode: server-side`, scopes, URL OAuth |
| `mcp.json` | Configuración de conexión del cliente MCP (`streamableHttp`) |
| `icon.svg` | Icono (obra gráfica de terceros; fuera de MIT mientras no se confirmen los derechos) |
| `skills/heimanhome-skill/SKILL.md` | Instrucciones del Skill: 39 herramientas, 5 scopes, reglas multilingües |
| `README.md`, `i18n/` | Documentación principal en inglés y traducciones |
| `LICENSE`, `NOTICE`, `OPEN-SOURCE-STATEMENT.md` | Licencia, avisos de marca y titularidad, declaración de código abierto |

## Antes de usarlo

`https://smart-home-mcp.example.com/mcp` en `mcp.json` y `connector-meta.json` es una **dirección de ejemplo**. Instalado tal cual, el paquete no se conecta a ningún servicio. Es necesario:

1. Desplegar un servidor MCP accesible públicamente por HTTPS (Streamable HTTP + OAuth, RFC 9728 / 8414 / 7591 / 7636 / 7009) con las 39 herramientas de `SKILL.md`.
2. Sustituir `example.com` en ambos JSON por su dirección real `https://<dominio>/mcp`.
3. Confirmar la versión de cliente que exige `auth_mode: server-side` (`minWorkbuddyVersion: 4.24.0`).
4. Obtener legalmente `clientId` / `clientSecret` y la autorización de integración de la plataforma original; las credenciales deben estar **solo en el servidor**.
5. Validar ambos archivos: `python3 -m json.tool connector-meta.json` y `python3 -m json.tool mcp.json`.

Sin autorización de marca: usar un nombre de proyecto neutro y un icono propio, y actualizar ambos JSON, `SKILL.md` y todas las traducciones.

## Scopes

Si no se solicita ningún scope, el servidor debe conceder solo lectura (`device:read`, `scene:read`). Los permisos de escritura (`device:control`, `scene:execute`, `security:control`) deben marcarse explícitamente en la página de consentimiento. Armar/desarmar y editar escenas exigen además `confirm=true`; conviene previsualizar las acciones delicadas con `dry_run=true`.

## Límites del empaquetado

- **Repositorio público en GitHub:** todos los archivos de este directorio, incluidos `i18n/`, `LICENSE`, `NOTICE` y `OPEN-SOURCE-STATEMENT.md`.
- **Envío al marketplace de WorkBuddy Connector:** solo los archivos de metadatos exigidos (`connector-meta.json`, `mcp.json`, `icon.svg`, `skills/**/SKILL.md`). No incluir textos legales, `README*` ni `i18n/`.

## Declaración de código abierto (resumen)

- Publicado bajo MIT: `connector-meta.json`, `mcp.json`, `SKILL.md`, `README*`, `NOTICE`, `OPEN-SOURCE-STATEMENT.md`.
- Excluidos: la imagen incrustada en `icon.svg`, todas las marcas y cualquier contenido privado no distribuido.
- Sin garantía, se ofrece «tal cual». El operador del servicio remoto es el responsable del tratamiento de datos personales.
- Texto completo y vinculante: `OPEN-SOURCE-STATEMENT.md`.

## Textos localizados para el marketplace (`es`)

Fuente: `connector-meta.json` → `i18n.locales.es`.

- **Nombre:** Heiman Smart Home
- **Descripción:** Controla y consulta los dispositivos Heiman en lenguaje natural: luces, climatización, cortinas, temperatura, registros, estancias y escenas.
- **Ejemplos:**
  1. Ajusta el brillo de la luz del salón al 50 %
  2. ¿Cuál es la temperatura y la humedad actuales del salón?
  3. Enciende el aire acondicionado del salón y ajústalo a 26 °C
  4. Cierra las cortinas y ejecuta la escena «En casa»
  5. Muestra la temperatura y la humedad medias de las últimas 24 horas

## Licencia y contacto

El contenido original está bajo MIT (véase `LICENSE`); marcas, `icon.svg` y contenidos no distribuidos quedan fuera (véase `NOTICE`). Los pendientes de validación antes de publicar (marca, icono, marcadores de posición) están en la sección «Remediation status» de [`../README.md`](../README.md).

Incidentes de seguridad a `<security@example.com>`; nunca publiques credenciales en una issue pública.
