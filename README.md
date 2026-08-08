# ZuseCode

ZuseCode 是一个用 Python 编写的终端 AI 编程助手。它驱动 LLM 在项目上执行工具调用循环，提供 Textual TUI、非交互式 `-p` 模式、WebSocket `--remote` 服务器，并支持多 Agent 团队、技能（skills）、钩子（hooks）、记忆（memory）和 git worktree 隔离。

## 特性

- **三种前端，同一核心**：TUI（Textual）、非交互式命令行（`-p`）、远程浏览器 UI（`--remote`），共享 `zusecode.agent.Agent` 主循环。
- **工具调用循环**：流式工具执行、权限确认（HITL）、自动压缩（auto-compact）、大结果落盘溢写。
- **多 Agent 协作**：团队（teams）模式、fork 子 Agent、任务板与文件邮箱通信。
- **上下文管理**：会话 JSONL 持久化、`/session resume` 恢复、RecoveryState 快照。
- **可扩展**：技能（skills）、钩子（hooks）、自定义工具、MCP 服务器、多 Provider（Anthropic / OpenAI / OpenAI-compat）。

## 环境要求

- Python ≥ 3.11
- 推荐使用 [uv](https://docs.astral.sh/uv/) 管理依赖

## 安装

```bash
uv sync --group dev
```

`--group dev` 会安装 pytest 与 pytest-asyncio（测试用）。

## 使用

```bash
# 启动 TUI
uv run zusecode

# 非交互式单次 prompt
uv run zusecode -p "你的 prompt"

# NDJSON 事件流输出
uv run zusecode -p "你的 prompt" --output-format stream-json

# 远程浏览器 UI（http://localhost:18888）
uv run zusecode --remote
```

## 配置

配置按优先级合并（后者覆盖前者）：`~/.zusecode/config.yaml` → `<work_dir>/.zusecode/config.yaml` → `<work_dir>/.zusecode/config.local.yaml`。

关键配置项包括 `providers`、`permission_mode`、`mcp_servers`、`hooks`、`worktree`、`sandbox` 等，可参考 `.zusecode/config.yaml.example`。

## 测试

```bash
uv run pytest                # 运行全部测试
uv run python -m compileall zusecode tests   # 语法检查
```

## 架构速览

- `zusecode/agent.py` — Agent 主循环，产出类型化事件，前端只负责渲染
- `zusecode/context/` — 上下文管理（落盘溢写 + 自动压缩）
- `zusecode/tools/` — 文件/命令核心工具与 ToolRegistry
- `zusecode/permissions/` — 权限分层检查
- `zusecode/memory/` — 长期记忆、会话、召回
- `zusecode/teams/` — 多 Agent 协作后端
- `zusecode/skills/` — 技能系统
- `zusecode/hooks/` — 生命周期钩子
