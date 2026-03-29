# 🦞 OpenClaw Context Monitor

Real-time OpenClaw agent context & model monitoring in your macOS menu bar.

No more worrying about context overflow draining your model quota — glance at the menu bar and know exactly where each agent stands.

![macOS](https://img.shields.io/badge/macOS-only-blue) ![SwiftBar](https://img.shields.io/badge/SwiftBar-v2.0+-green) ![License](https://img.shields.io/badge/license-MIT-green)

[中文说明](#中文说明)

## Features

- **At-a-glance context usage** — menu bar shows the active agent (emoji if set, otherwise agent ID) and its context length, e.g. `🔧 140k`
- **Multi-agent overview** — dropdown lists all agents with context length, model, and status
- **Status indicators** — `▶` running / `—` idle / `✖` failed. Shows `🫠` when context exceeds 100k tokens
- **Agent display** — reads each agent's custom emoji from `IDENTITY.md`; falls back to agent name
- **Model aliases** — shows short names (opus, sonnet, haiku, flash, pro) instead of full model IDs
- **Local + remote** — works whether OpenClaw runs on your Mac or a remote server via SSH

## Requirements

- macOS
- [SwiftBar](https://github.com/swiftbar/SwiftBar) v2.0+
- [OpenClaw](https://github.com/openclaw/openclaw) running locally or on a reachable host
- Python 3 (on the OpenClaw host)

## Install

### Option A: Via OpenClaw skill (recommended)

```bash
openclaw skills install openclaw-context-monitor
```

Then set up via either method:

**A1. Run the installer:**

```bash
bash ~/.openclaw/skills/context-monitor/scripts/install.sh           # local
bash ~/.openclaw/skills/context-monitor/scripts/install.sh --remote user@host  # remote
```

**A2. Or ask your agent**, e.g. *"set up menu bar monitoring"* (any phrasing works).

### Option B: Manual install

```bash
git clone https://github.com/hjklasdfg/openclaw-context-monitor.git
cd openclaw-context-monitor

# Local (OpenClaw on this Mac):
bash scripts/install.sh

# Remote (OpenClaw on another machine):
bash scripts/install.sh --remote user@host
```

> **Remote mode prerequisites**
>
> - **Network**: Your Mac and the OpenClaw host must be reachable. If not on the same LAN, use [Tailscale](https://tailscale.com), [ZeroTier](https://zerotier.com), or [WireGuard](https://wireguard.com).
> - **SSH key auth**: Passwordless SSH required (`BatchMode=yes`):
>   ```bash
>   ssh-keygen -t ed25519 -N ""
>   ssh-copy-id user@host
>   ```
> - **Python 3**: Must be available on the OpenClaw host.

## How it works

```
MacBook (SwiftBar plugin) → SSH → OpenClaw host (status collector) → sessions.json
```

Two components:
1. **`openclaw-status.py`** — Runs on the OpenClaw host, reads agent session data
2. **`swiftbar-plugin.sh`** — Runs on your Mac via SwiftBar, renders the menu bar

## Customization

Plugin file: `~/Library/Application Support/SwiftBar/Plugins/context-monitor.30s.sh`

- **Refresh interval** — rename the file suffix, e.g. `30s` → `10s`, `1m`, `5m`:
  ```bash
  mv ~/Library/Application\ Support/SwiftBar/Plugins/context-monitor.{30s,10s}.sh
  ```
- **Warning threshold** — edit the plugin file, change `WARN_THRESHOLD = 100000` to your preferred token count
- **SSH target** — edit `MINI="user@host"` in the plugin file, or set an env var:
  ```bash
  export OPENCLAW_SSH_TARGET="user@new-host"
  ```

## Troubleshooting

| Menu bar | Meaning |
|---|---|
| `🦞 ❌` | SSH or local connection failed |
| `🦞 ⚠️` | Data parse error |
| Agent missing | Agent has no session yet |

---

## 中文说明

在菜单栏实时监控 OpenClaw agent 的上下文长度和模型状态，不用再担心上下文过长导致模型配额耗尽。

### 功能一览

- **一眼掌握上下文用量** — 菜单栏显示当前活跃 agent（优先显示 emoji，无则显示 agent 名），以及对应的上下文长度，效果：`🔧 140k`
- **多 Agent 总览** — 下拉菜单展示所有 agent 的上下文长度、模型、运行状态
- **状态标识** — `▶` 运行中 / `—` 空闲 / `✖` 失败。上下文超过 100k 时显示 `🫠`
- **Agent 显示** — 从 `IDENTITY.md` 读取自定义 emoji；无 emoji 则显示 agent 名称
- **模型简称** — 显示 opus、sonnet、haiku、flash、pro 而非完整模型 ID
- **本地 + 远程** — 支持 OpenClaw 在本机或远程服务器上运行

### 安装

#### 方式一：通过 OpenClaw skill 安装（推荐）

```bash
openclaw skills install openclaw-context-monitor
```

然后选择以下任一方式完成设置：

**a. 运行安装脚本：**

```bash
bash ~/.openclaw/skills/context-monitor/scripts/install.sh           # 本地模式
bash ~/.openclaw/skills/context-monitor/scripts/install.sh --remote user@host  # 远程模式
```

**b. 或者让 agent 帮你设置**，比如说「帮我设置菜单栏监控」（任意表述均可）。

#### 方式二：手动安装

```bash
git clone https://github.com/hjklasdfg/openclaw-context-monitor.git
cd openclaw-context-monitor

# 本地（OpenClaw 在本机）：
bash scripts/install.sh

# 远程（OpenClaw 在另一台机器上）：
bash scripts/install.sh --remote user@host
```

> **远程模式前置条件**
>
> - **网络** — Mac 和 OpenClaw 主机必须网络互通。不在同一局域网推荐用 [Tailscale](https://tailscale.com)、[ZeroTier](https://zerotier.com) 或 [WireGuard](https://wireguard.com)
> - **SSH 免密登录** — 需要配置密钥认证（`BatchMode=yes`）：
>   ```bash
>   ssh-keygen -t ed25519 -N ""
>   ssh-copy-id user@host
>   ```
> - **Python 3** — OpenClaw 主机上需要安装

### 自定义

插件文件：`~/Library/Application Support/SwiftBar/Plugins/context-monitor.30s.sh`

- **刷新间隔** — 重命名文件后缀，如 `30s` → `10s`、`1m`、`5m`
- **警告阈值** — 编辑插件文件中的 `WARN_THRESHOLD = 100000`
- **SSH 目标** — 编辑插件文件中的 `MINI="user@host"`，或设置环境变量 `OPENCLAW_SSH_TARGET`

### 故障排查

| 菜单栏显示 | 含义 |
|---|---|
| `🦞 ❌` | SSH 或本地连接失败 |
| `🦞 ⚠️` | 数据解析错误 |
| Agent 不显示 | 该 agent 尚未产生 session |

## License

MIT
