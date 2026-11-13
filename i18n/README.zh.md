# Heiman Smart Home MCP — 使用指南（简体中文）

让 AI 助手用自然语言查询与控制智能家居设备：灯光、空调、窗帘、温湿度、设备日志、房间、场景联动与安防。

| 项目 | 值 |
| --- | --- |
| 服务端点 | `https://spmcp.heimanhome.com/mcp` |
| 传输协议 | MCP Streamable HTTP |
| 授权方式 | OAuth 2.1 + PKCE（使用智能家居账号登录） |
| 仓库 | `https://github.com/heimanhome/heimanhome-mcp.git` |
| 支持邮箱 | `help@heimanhome.com` |

> 英文 [`../README.md`](../README.md) 为权威文档；本页为翻译，如有冲突以英文为准。

## 1. 连接与授权

1. 在 MCP 客户端中按下述配置填入服务端点。
2. 客户端会打开授权页，使用你的智能家居账号登录。
3. 勾选所需权限：默认只授只读权限；需要控制设备时再勾选写权限。
4. 完成授权。令牌由客户端保存，上游账号凭据不会写入本仓库。

## 2. 客户端配置

**通用 `mcp.json`**（Claude Desktop、Cursor、Cline、Windsurf、Zed、VS Code + GitHub Copilot、MCP Inspector）：

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

**Claude Code**：

```bash
claude mcp add --transport http heiman-smart-home https://spmcp.heimanhome.com/mcp
```

**仅支持 stdio 的客户端**：用 `mcp-remote` 桥接：

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

**托管客户端**（ChatGPT / Claude.ai / Copilot Studio / Gemini CLI / WorkBuddy / CodeBuddy）：在各自的「添加远程 MCP 服务」界面填入同一 URL 即可，无需本仓库文件。

## 3. 权限与写操作

| 权限 | 作用 |
| --- | --- |
| `device:read` | 设备列表、状态、能力、固件、日志、传感器读数 |
| `device:control` | 开关、亮度、设定温度、窗帘 |
| `scene:read` | 场景、房间、家庭、自动化列表 |
| `scene:execute` | 执行、创建、修改、删除场景与自动化 |
| `security:control` | 布防 / 撤防、告警确认 |

- 未指定权限时只授只读权限。
- 高风险操作需 `confirm=true`，建议先用 `dry_run=true` 预览。
- 重试控制指令时带 `idempotent_key`，再用 `get_command_status(trace_id)` 查询结果；返回成功只表示指令已受理，不代表设备已执行。
- `whoami` 可查看当前身份与已授权限。调用被拒绝时请修正权限 / 参数 / 确认项，不要重复提交相同请求。

## 4. 常用指令

```
把客厅主灯调到 50% 亮度
客厅现在多少度？湿度怎么样？
打开客厅空调并设定为 26 度
把窗帘关上，然后执行回家模式
统计最近 24 小时客厅的平均温度和湿度
每天 23 点自动关客厅灯
```

助手会用你使用的语言回答；8 语言示例见 `connector-meta.json` → `i18n.locales`。

## 5. 常见问题

| 现象 | 处理 |
| --- | --- |
| `401` / 授权过期 | 在客户端重新连接并授权 |
| 提示权限不足 | 重新授权并勾选所需权限 |
| 指令已受理但设备未变化 | 用 `get_command_status(trace_id)` 查询，等待设备回报 |
| 请求过多 | 稍后重试，不要循环提交相同指令 |
| 无法连接 | 确认客户端支持 Streamable HTTP + OAuth，且端点可通过 HTTPS 访问 |

## 6. 安全与隐私

- 控制类操作会改变真实设备状态，请先看 `dry_run` 预览并遵循二次确认。
- 令牌与上游凭据只保存在服务端；切勿在公开 Issue 中粘贴令牌 / 口令，安全上报至 `help@heimanhome.com`。
- 远端服务运营方是家庭 / 设备数据的控制者，详见 `OPEN-SOURCE-STATEMENT.md`。

## 许可与更多资料

MIT 许可见 `LICENSE`，权属与商标边界见 `NOTICE`；完整工具清单与参数见 `skills/heimanhome-skill/SKILL.md`。
