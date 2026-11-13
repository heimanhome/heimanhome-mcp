# Heiman Smart Home Connector — 简体中文

面向 WorkBuddy / CodeBuddy 兼容客户端的 **MCP Connector 元数据包**。让 AI 助手用自然语言查询与控制智能家居设备：灯光、空调、窗帘、温湿度、设备日志、家庭与房间、场景联动、物模型能力、安防布撤防、告警消息、成员分享与固件只读查询。

> **本仓库只包含连接器元数据，不包含服务端实现。**
> 这是第三方集成，**非海曼官方产品，亦非 WorkBuddy / CodeBuddy 官方产品**，未获任何一方背书或认证。详见 `OPEN-SOURCE-STATEMENT.md`。

**文档语言**：英文为权威版本。本翻译仅供参考，如有冲突以 [`../README.md`](../README.md) 为准；法律文件（`LICENSE`、`NOTICE`、`OPEN-SOURCE-STATEMENT.md`）仅英文版具有约束力。

## 语言

| 语言 | 代码 | 文档 |
| --- | --- | --- |
| English（权威） | `en` | [`../README.md`](../README.md) |
| Deutsch | `de` | [`README.de.md`](README.de.md) |
| Français | `fr` | [`README.fr.md`](README.fr.md) |
| Español | `es` | [`README.es.md`](README.es.md) |
| Português | `pt` | [`README.pt.md`](README.pt.md) |
| العربية（RTL） | `ar` | [`README.ar.md`](README.ar.md) |
| Русский | `ru` | [`README.ru.md`](README.ru.md) |
| 简体中文 | `zh` | `README.zh.md` |

语言代码与 `connector-meta.json` → `i18n.supported_locales` 一致；8 语言展示文案的唯一真源是 `i18n.locales`。`ar` 为从右到左排版，代码块、工具名与 JSON 保持从左至右。

> WorkBuddy 市场目前只消费顶层 `*_zh` / `*_en` 字段，其他语言回退到英文 `name` / `description`；`i18n` 对象是项目自带的文案真源，供 Skill、登录页与打包脚本共用。提交市场前请与平台确认该扩展字段是否被接受。

## 包内容

以下路径均相对仓库根目录。

| 路径 | 用途 |
| --- | --- |
| `connector-meta.json` | 连接器描述、多语言文案、`auth_mode: server-side`、scope、OAuth 服务地址 |
| `mcp.json` | MCP 客户端连接配置（`streamableHttp`） |
| `icon.svg` | 连接器图标（第三方美术资产，权属确认前不在 MIT 授权范围） |
| `skills/heimanhome-skill/SKILL.md` | Skill 说明：39 个工具、5 个 scope、多语言应答规则 |
| `README.md`、`i18n/` | 英文主文档与各语言翻译 |
| `LICENSE`、`NOTICE`、`OPEN-SOURCE-STATEMENT.md` | MIT 许可、商标与权属声明、完整开源声明 |

## 使用前必读

`mcp.json` 与 `connector-meta.json` 中的 `https://spmcp.heimanhome.com/mcp` 是**当前部署地址**，连接前请确认服务可访问。如自行部署，请按以下步骤处理：

1. 部署公网 HTTPS 可访问的 MCP 服务端（Streamable HTTP + OAuth，RFC 9728 / 8414 / 7591 / 7636 / 7009），实现 `SKILL.md` 中的 39 个工具；
2. 如部署自有服务，把两个 JSON 里的地址改为自己的 HTTPS 地址；
3. 确认 `auth_mode: server-side` 所需客户端版本（`minWorkbuddyVersion: 4.24.0`）；
4. 向上游智能家居平台合法取得 `clientId` / `clientSecret` 与接入授权，凭据**只放服务端**，不写入本包；
5. 校验：`python3 -m json.tool connector-meta.json`、`python3 -m json.tool mcp.json`。

「Heiman」/ 海曼商标的使用已获权利人授权；`icon.svg` 内嵌美术作品的权属仍待确认，如无法确认请替换为自绘图标。

## 权限

未指定 scope 时服务端应只授只读权限（`device:read`、`scene:read`）；写权限（`device:control`、`scene:execute`、`security:control`）需用户在同意页显式勾选。布撤防与场景编辑另需 `confirm=true` 二次确认，高影响操作建议先 `dry_run=true` 预演。

## 打包边界

- **GitHub 公开仓库**：保留本目录全部文件，含 `i18n/`、`LICENSE`、`NOTICE`、`OPEN-SOURCE-STATEMENT.md` 与全部 README。
- **提交 WorkBuddy Connector 市场审核**：只提交平台要求的元数据文件（`connector-meta.json`、`mcp.json`、`icon.svg`、`skills/**/SKILL.md`），**不要**附带法律文件、`README*` 或 `i18n/`。

## 开源声明要点（中文摘要，以英文为准）

- **MIT 授权范围**：`connector-meta.json`、`mcp.json`、`SKILL.md`、`README*`、`NOTICE`、`OPEN-SOURCE-STATEMENT.md`。
- **不在授权范围**：`icon.svg` 内嵌美术作品、所有第三方商标、未随包发布的私有内容（服务端源码、测试、上游接口契约文档、授权登录页及其品牌图片）。
- **非官方声明**：本项目为第三方集成，非海曼 / WorkBuddy / CodeBuddy 官方产品，未获背书；「Heiman」/ 海曼商标的使用已获权利人授权，但 MIT 许可本身不授予商标权。
- **数据与隐私**：本包自身不收集、不存储、不传输个人数据；其指向的远端服务会处理家庭、设备、告警、成员等数据，**该服务的部署方是数据控制者**，需履行告知同意、最小必要、访问控制、存储期限、删除响应与跨境评估等义务。
- **无担保**：按「现状」提供，不保证与上游平台或特定 MCP 客户端版本兼容。控制类工具的 `success` 只表示指令被接受，不等于设备已执行。
- **安全上报**：`help@heimanhome.com`，请勿在公开 Issue 中粘贴凭据或漏洞细节。
- **完整且权威的文本**：`OPEN-SOURCE-STATEMENT.md`。

## 市场展示文案（`zh`）

来源：`connector-meta.json` → `i18n.locales.zh`。

- **名称**：海曼智能家居
- **描述**：用自然语言查询与控制海曼智能家居：灯光、空调、窗帘、温湿度、设备日志、家庭房间与场景联动。
- **示例语句**：
  1. 把客厅主灯调到 50% 亮度
  2. 客厅现在多少度？湿度怎么样？
  3. 打开客厅空调并设定为 26 度
  4. 把窗帘关上，然后执行回家模式
  5. 统计最近 24 小时客厅的平均温度和湿度

## 整改状态与许可

本次整改已完成：英文改为权威文档语言、新增 7 语言翻译、`SKILL.md` 英文化并保留多语言规则、版本号统一到 **1.1.0**、移除 `author` 对品牌方的署名与登录页内部来源字样、法律文件改为英文权威版、`i18n/` 纳入打包边界。

**已确认/已完成**：品牌/商标授权已获权利人确认，版权主体与维护组织定为 HeimanHome，部署地址、仓库地址、联系邮箱与其余占位均已填写。**仍需人工确认**：`icon.svg` 内嵌美术作品权属或替换、WorkBuddy 规范字段与 `i18n` 扩展对象的披露许可。完整清单见英文 [`../README.md`](../README.md) 的「Remediation status」。

自研内容采用 MIT 许可，见 `LICENSE`；商标、`icon.svg` 与未随包发布的内容不在授权范围内，见 `NOTICE`。
