# Heiman Smart Home MCP — Guia de uso (Português)

Consulte e controle dispositivos de casa inteligente em linguagem natural com um assistente de IA: luzes, ar-condicionado, cortinas, temperatura e umidade, registros de dispositivos, cômodos, cenários e alarme.

| | |
| --- | --- |
| Endpoint | `https://spmcp.heimanhome.com/mcp` |
| Transporte | MCP Streamable HTTP |
| Autorização | OAuth 2.1 com PKCE (entre com a conta de casa inteligente) |
| Repositório | `https://github.com/heimanhome/heimanhome-mcp.git` |
| Suporte | `help@heimanhome.com` |

> A versão em inglês [`../README.md`](../README.md) é a autoritativa; esta tradução é apenas informativa. Esta versão usa português neutro, compatível com `pt-PT` e `pt-BR`.

## 1. Conectar e autorizar

1. Adicione o endpoint ao seu cliente MCP usando uma das configurações abaixo.
2. O cliente abre a página de autorização; entre com sua conta de casa inteligente.
3. Revise as permissões: por padrão, apenas leitura; marque permissões de escrita somente se for controlar dispositivos.
4. Confirme. O cliente guarda o token; as credenciais não ficam neste repositório.

## 2. Configuração do cliente

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

**Clientes apenas stdio** — ponte com `mcp-remote`:

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

**Clientes hospedados** (ChatGPT, Claude.ai, Copilot Studio, Gemini CLI, WorkBuddy / CodeBuddy): registre um servidor MCP remoto na interface do cliente com a mesma URL.

## 3. Escopos e escrita segura

| Escopo | Permite |
| --- | --- |
| `device:read` | Listas, estado, capacidades, firmware, registros, sensores |
| `device:control` | Ligar/desligar, brilho, temperatura alvo, cortinas |
| `scene:read` | Cenários, cômodos, casas, automações |
| `scene:execute` | Executar, criar, alterar, excluir cenários e automações |
| `security:control` | Armar / desarmar, confirmação de alarmes |

- Sem solicitação explícita, apenas leitura é concedida.
- Ações de alto impacto exigem `confirm=true`; visualize antes com `dry_run=true`.
- Ao repetir um comando, use `idempotent_key` e consulte `get_command_status(trace_id)`. Uma resposta positiva significa que o comando foi **aceito**, não necessariamente executado.
- `whoami` mostra a identidade e os escopos. Em caso de recusa, corrija o escopo, o parâmetro ou a confirmação.

## 4. Exemplos de pedidos

```
Ajuste o brilho da luz da sala para 50%
Qual é a temperatura e a umidade atuais da sala?
Ligue o ar-condicionado da sala e ajuste para 26 °C
Feche as cortinas e execute o cenário «Em casa»
Mostre a temperatura e a umidade médias das últimas 24 horas
```

O assistente responde no seu idioma; exemplos em 8 idiomas estão em `connector-meta.json` → `i18n.locales`.

## 5. Problemas comuns

| Sintoma | Solução |
| --- | --- |
| `401` / autorização expirada | Reconecte e autorize novamente |
| Escopo não concedido | Reautorize e marque o escopo necessário |
| Comando aceito, dispositivo inalterado | Consulte `get_command_status(trace_id)` e aguarde |
| Muitas requisições | Aguarde antes de repetir, sem loop na mesma chamada |
| Sem conexão | Verifique Streamable HTTP + OAuth e o acesso HTTPS ao endpoint |

## 6. Segurança e privacidade

- Comandos de controle alteram dispositivos reais: revise o `dry_run` e minimize as permissões de escrita.
- Tokens e credenciais ficam no servidor. Nunca publique tokens ou senhas em issues; reporte falhas a `help@heimanhome.com`.
- O operador do serviço remoto é o controlador dos dados: veja `OPEN-SOURCE-STATEMENT.md`.

## Licença e mais documentos

MIT (ver `LICENSE`); propriedade e marcas em `NOTICE`; lista completa de ferramentas em `skills/heimanhome-skill/SKILL.md`.
