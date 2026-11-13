# Heiman Smart Home Connector — Português

Um **pacote de metadados de conector MCP** para clientes compatíveis com WorkBuddy / CodeBuddy. Permite que um assistente de IA consulte e controle dispositivos de casa inteligente em linguagem natural: luzes, ar-condicionado, cortinas, temperatura e umidade, registros de dispositivos, casas e cômodos, cenários, capacidades dos dispositivos, alarme, notificações e informações de firmware (somente leitura).

> **Este repositório contém apenas os metadados do conector, não a implementação do servidor.**
> Trata-se de uma integração de terceiros: **não é um produto oficial da Heiman nem da WorkBuddy / CodeBuddy**, sem endosso ou certificação de qualquer uma dessas partes. Veja `OPEN-SOURCE-STATEMENT.md`.

**Idioma:** o inglês é a versão autoritativa. Esta tradução é apenas informativa; em caso de divergência, prevalece [`../README.md`](../README.md). Os textos jurídicos (`LICENSE`, `NOTICE`, `OPEN-SOURCE-STATEMENT.md`) só são vinculantes em inglês.

> Nota de variação: esta tradução usa português com redação neutra, compatível com `pt-PT` e `pt-BR`. Se o marketplace exigir variantes separadas, crie `README.pt-BR.md` a partir deste arquivo.

## Idiomas

| Idioma | Código | Documento |
| --- | --- | --- |
| English (autoritativo) | `en` | [`../README.md`](../README.md) |
| Deutsch | `de` | [`README.de.md`](README.de.md) |
| Français | `fr` | [`README.fr.md`](README.fr.md) |
| Español | `es` | [`README.es.md`](README.es.md) |
| Português | `pt` | `README.pt.md` |
| العربية (RTL) | `ar` | [`README.ar.md`](README.ar.md) |
| Русский | `ru` | [`README.ru.md`](README.ru.md) |
| 简体中文 | `zh` | [`README.zh.md`](README.zh.md) |

Os códigos correspondem a `connector-meta.json` → `i18n.supported_locales`. A fonte única dos textos localizados é `i18n.locales`.

## Conteúdo do pacote

Os caminhos são relativos à raiz do repositório.

| Caminho | Função |
| --- | --- |
| `connector-meta.json` | Descritor do conector, textos localizados, `auth_mode: server-side`, scopes, URL OAuth |
| `mcp.json` | Configuração de conexão do cliente MCP (`streamableHttp`) |
| `icon.svg` | Ícone (obra gráfica de terceiros; fora do MIT enquanto os direitos não forem confirmados) |
| `skills/heimanhome-skill/SKILL.md` | Instruções da Skill: 39 ferramentas, 5 scopes, regras multilíngues |
| `README.md`, `i18n/` | Documentação principal em inglês e traduções |
| `LICENSE`, `NOTICE`, `OPEN-SOURCE-STATEMENT.md` | Licença, avisos de marca e titularidade, declaração de código aberto |

## Antes de usar

`https://spmcp.heimanhome.com/mcp` em `mcp.json` e `connector-meta.json` é o **endereço do serviço implantado**. Verifique se o serviço está acessível antes de conectar. É necessário:

1. Implantar um servidor MCP acessível publicamente por HTTPS (Streamable HTTP + OAuth, RFC 9728 / 8414 / 7591 / 7636 / 7009) com as 39 ferramentas de `SKILL.md`.
2. Se implantar seu próprio servidor, substitua o endereço nos dois JSON pelo seu endereço HTTPS.
3. Confirmar a versão de cliente exigida por `auth_mode: server-side` (`minWorkbuddyVersion: 4.24.0`).
4. Obter legalmente `clientId` / `clientSecret` e a autorização de integração junto à plataforma de origem; as credenciais ficam **apenas no servidor**.
5. Validar os dois arquivos: `python3 -m json.tool connector-meta.json` e `python3 -m json.tool mcp.json`.

Sem autorização de marca: usar um nome de projeto neutro e um ícone próprio, atualizando os dois JSON, `SKILL.md` e todas as traduções.

## Scopes

Quando nenhum scope é solicitado, o servidor deve conceder apenas leitura (`device:read`, `scene:read`). Os permissões de escrita (`device:control`, `scene:execute`, `security:control`) precisam ser marcadas explicitamente na página de consentimento. Armar/desarmar e editar cenários exigem ainda `confirm=true`; visualize ações sensíveis com `dry_run=true`.

## Limites do empacotamento

- **Repositório público no GitHub:** todos os arquivos deste diretório, incluindo `i18n/`, `LICENSE`, `NOTICE` e `OPEN-SOURCE-STATEMENT.md`.
- **Envio ao marketplace WorkBuddy Connector:** apenas os arquivos de metadados exigidos (`connector-meta.json`, `mcp.json`, `icon.svg`, `skills/**/SKILL.md`). Não inclua textos jurídicos, `README*` nem `i18n/`.

## Declaração de código aberto (resumo)

- Publicado sob MIT: `connector-meta.json`, `mcp.json`, `SKILL.md`, `README*`, `NOTICE`, `OPEN-SOURCE-STATEMENT.md`.
- Excluídos: a imagem embutida em `icon.svg`, todas as marcas e qualquer conteúdo privado não distribuído.
- Sem garantia, fornecido «no estado em que se encontra». O operador do serviço remoto é o controlador dos dados pessoais.
- Texto completo e vinculante: `OPEN-SOURCE-STATEMENT.md`.

## Textos localizados para o marketplace (`pt`)

Fonte: `connector-meta.json` → `i18n.locales.pt`.

- **Nome:** Heiman Smart Home
- **Descrição:** Controle e consulte os dispositivos Heiman em linguagem natural: luzes, ar-condicionado, cortinas, temperatura, registros, cômodos e cenários.
- **Exemplos:**
  1. Ajuste o brilho da luz da sala para 50%
  2. Qual é a temperatura e a umidade atuais da sala?
  3. Ligue o ar-condicionado da sala e ajuste para 26 °C
  4. Feche as cortinas e execute o cenário "Em casa"
  5. Mostre a temperatura e a umidade médias das últimas 24 horas

## Licença e contato

O conteúdo original está sob MIT (veja `LICENSE`); marcas, `icon.svg` e conteúdos não distribuídos ficam de fora (veja `NOTICE`). Os pontos pendentes antes da publicação (marca, ícone, marcadores de posição) estão na seção «Remediation status» de [`../README.md`](../README.md).

Problemas de segurança: `help@heimanhome.com` — nunca publique credenciais em issues públicas.
