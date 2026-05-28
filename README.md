# Loom

**简体中文** | [English](README.en.md)

<div align="center">

基于 AI 的 JSON Schema 文档生成器，集成 TUI 交互、Web 浏览器与 Mock 服务

[![Node.js](https://img.shields.io/badge/Node.js-≥18.0.0-green)](https://nodejs.org/)
[![TypeScript](https://img.shields.io/badge/TypeScript-5.9-blue)](https://www.typescriptlang.org/)

</div>

## ✨ 特性

- **🤖 AI 驱动的 Schema 生成** —— 通过 TUI 聊天界面与大模型（DeepSeek、OpenAI）对话生成和更新 JSON Schema API 文档
- **🧩 实体（Entity）建模** —— 在 `docs/entities` 中维护可复用的实体 Schema，通过 `x-entity-ref` 在接口 Schema 中引用
- **📚 现代 Web 浏览器** —— 基于 React 的 SPA，用于浏览模块、接口和实体，支持交互式 Schema 渲染
- **⚡ Mock 服务** —— 基于 JSON Schema 动态生成贴近真实的 Mock 数据
- **🖥️ TUI 内服务控制** —— 直接在 TUI 中启动/停止/重启 Mock 与 Web Viewer（`/mock`、`/view`）
- **🗂️ Manifest 索引** —— 内置 `loom manifest rebuild` 命令重建依赖/索引一致性
- **⬆️ 自动升级提示** —— 启动时检查 npm 上的新版本，确认后可自动升级
- **🔧 TypeScript 优先** —— 完整类型定义，现代化的 TypeScript 架构

## 📦 安装

### 环境要求
- Node.js ≥ 18.0.0
- npm 或 yarn
- DeepSeek API Key（必需）

### 全局安装
```bash
npm install -g @vegamo/loom
# 或
yarn global add @vegamo/loom
```


## ⚙️ 配置

### 配置文件

默认全局配置路径：
- macOS/Linux: `~/.loom/config.json`
- Windows: `%APPDATA%/loom/config.json`

首次运行 `loom chat` 时，会以交互向导的形式引导你创建/更新该全局配置文件。

聊天首次引导的默认值：
- `provider`：`deepseek`
- `model`：`deepseek-chat`
- `baseURL`：`https://api.deepseek.com/v1`
- `apiKey`：必填，需由用户输入

你也可以手动创建全局配置：
```json
{
  "outDir": "docs",
  "llm": {
    "provider": "deepseek",
    "model": "deepseek-chat",
    "baseURL": "https://api.deepseek.com/v1",
    "apiKey": "your_deepseek_api_key",
    "temperature": 0.7,
    "maxTokens": 2000
  },
  "serve": {
    "port": 3000,
    "host": "0.0.0.0"
  },
  "mock": {
    "port": 3001,
    "host": "0.0.0.0"
  }
}
```

`docs/` 目录仍保留在各项目目录中（通过 `--dir` 指定），不会被移动到全局存储。

## 🚀 快速开始

### 1. 生成 API 文档
```bash
# 启动交互式 TUI 来生成 JSON Schema
loom chat

# 或指定项目目录
loom chat --dir ./my-api-project
```

在 `loom chat` 中，你也可以控制本地服务：
- `/mock`、`/mock stop`、`/mock restart [port]`
- `/view`、`/view stop`、`/view restart [port]`

### 2. 浏览文档
```bash
# 启动 Web 文档浏览器
loom view

# 或指定端口
loom view --port 8080
```

### 3. 启动 Mock 服务
```bash
# 启动 Mock API 服务
loom mock

# 或指定端口
loom mock --port 8081
```

### 4. 组合服务（推荐）
```bash
# 在同一端口同时启动 Viewer 和 Mock 服务
loom serve

# 访问入口：
# - Web Viewer: http://localhost:3000
# - Mock API:   http://localhost:3000/mock/...
```

### 5. 升级 Loom
```bash
# 手动触发升级
loom upgrade
```

> 执行其它命令时，Loom 也会检查 npm 是否有新版本。  
> 发现新版本时，确认即可自动升级。

## 📖 使用指南

### `loom chat`
通过 AI 对话生成 JSON Schema 文档的交互式终端 UI。

```bash
loom chat [options]

Options:
  -d, --dir <path>    目标项目目录（默认：当前目录）
  -h, --help          显示帮助
```

**典型流程：**
1. 运行 `loom chat`
2. 用自然语言描述你的 API 接口
3. AI 生成结构合理的 JSON Schema
4. Schema 文件被保存到 `docs/` 目录（可配置）

**内置聊天命令：**
- 输入提示：`Enter` 发送，`Shift+Enter`/`Alt+Enter` 换行，`↑`/`↓` 浏览历史记录（跨会话持久化），`Tab` 自动补全命令
- `/help` —— 显示命令帮助
- `/reset` —— 重置对话历史
- `/list` —— 列出已生成的 Schema 文件
- `/mock`、`/mock stop`、`/mock restart [port]` —— 管理 Mock 服务
- `/view`、`/view stop`、`/view restart [port]` —— 管理 Web Viewer
- `/scan <dir>` —— 通过 LLM 从源码中识别 API；`/scan resume`、`/scan reset` 管理断点
- `/abort` —— 中止当前请求
- `/exit` —— 退出 Loom

### `loom view`
基于 React SPA 的现代化 Web 文档浏览器。

```bash
loom view [options]

Options:
  -p, --port <number>  端口号（默认：3000）
  -d, --dir <path>     目标项目目录（默认：当前目录）
  -h, --help           显示帮助
```

**特性：**
- 📁 按模块浏览，显示接口数量
- 🧩 浏览 `docs/entities` 中的实体
- 🔍 在模块与接口范围内实时搜索
- 📊 类 Swagger 的 Schema 表格视图，覆盖所有请求/响应区段（query、path、headers、body）
- 🔗 渲染接口请求/响应时自动解析 `x-entity-ref`
- 🎨 简洁的响应式 UI，深色侧边栏
- 🔗 支持接口的直链访问

### `loom mock`
基于 JSON Schema 动态生成数据的 Mock API 服务。

```bash
loom mock [options]

Options:
  -p, --port <number>  端口号（默认：3001）
  -d, --dir <path>     目标项目目录（默认：当前目录）
  -h, --help           显示帮助
```

**特性：**
- 🚀 自动从 JSON Schema 文件注册路由
- 🎲 基于 mock-json-schema 智能生成 Mock 数据
- 📡 支持所有 HTTP 方法（GET、POST、PUT、DELETE、PATCH）
- 🔧 可配置响应状态码与 Schema

### `loom serve`
组合服务，同时运行 Web Viewer 与 Mock 服务。

```bash
loom serve [options]

Options:
  -p, --port <number>  端口号（默认：3000）
  -d, --dir <path>     目标项目目录（默认：当前目录）
  -h, --help           显示帮助
```

**URL 结构：**
- `http://localhost:3000/` —— Web 文档浏览器
- `http://localhost:3000/api/docs` —— 文档 API
- `http://localhost:3000/api/schemas` —— Schema 文件 API
- `http://localhost:3000/api/entities` —— 实体文件 API
- `http://localhost:3000/mock/...` —— Mock API 路由

### `loom manifest rebuild`
重建文档清单索引文件 `.loom-manifest.json`，确保依赖/索引一致。

```bash
loom manifest rebuild [options]

Options:
  -d, --dir <path>  目标项目目录（默认：当前目录）
  -h, --help        显示帮助
```

### `loom upgrade`
将 loom 升级到 npm 上的最新版本。

```bash
loom upgrade
```

## 📝 JSON Schema 格式

Loom 使用一套为 API 文档优化的自定义 JSON Schema 格式：

```json
{
  "$schema": "http://json-schema.org/draft-07/schema#",
  "title": "Authentication API",
  "description": "User authentication endpoints",
  "version": "1.0.0",
  "endpoints": [
    {
      "path": "/api/auth/login",
      "method": "POST",
      "summary": "User login",
      "description": "Authenticate user with credentials",
      "tags": ["auth"],
      "request": {
        "headers": {
          "Content-Type": { "type": "string", "enum": ["application/json"] }
        },
        "body": {
          "type": "object",
          "properties": {
            "username": { "type": "string" },
            "password": { "type": "string" }
          },
          "required": ["username", "password"]
        }
      },
      "response": {
        "200": {
          "type": "object",
          "properties": {
            "token": { "type": "string" },
            "user": { "type": "object" }
          }
        },
        "400": {
          "type": "object",
          "properties": {
            "error": { "type": "string" }
          }
        }
      }
    }
  ]
}
```

### Schema 结构说明
- **title**：API 模块标题
- **description**：模块描述
- **endpoints**：API 接口定义数组
- **endpoint.path**：URL 路径（支持路径参数）
- **endpoint.method**：HTTP 方法（GET、POST、PUT、DELETE、PATCH）
- **endpoint.request**：可选的请求 Schema（headers、params、query、body）
- **endpoint.response**：按状态码索引的响应 Schema

## 🧩 实体（Entity）Schema 与引用

Loom 支持以下位置的可复用实体 Schema：

```text
docs/entities/*.entity.schema.json
```

实体文件示例：

```json
{
  "$schema": "http://json-schema.org/draft-07/schema#",
  "title": "User",
  "description": "Reusable user entity",
  "type": "object",
  "properties": {
    "id": { "type": "integer" },
    "name": { "type": "string" },
    "email": { "type": "string", "format": "email" }
  },
  "required": ["id", "name", "email"]
}
```

在接口 Schema 中通过 `x-entity-ref` 引用实体：

```json
{
  "user": {
    "x-entity-ref": {
      "entity": "User",
      "pick": ["id", "name", "email"]
    }
  }
}
```

支持的形式：
- 字符串形式：`"x-entity-ref": "User"`
- 对象形式：`"x-entity-ref": { "entity": "User", "pick": ["id", "name"] }`




## 📋 更新日志

### v0.3.0
- **Added**：`/scan` 与 `/scan resume` 新增 `--lang zh|en` 参数。Phase 3（generate-entity）和 Phase 4（generate-endpoint）按所选语言输出 `description` 与 `summary` 文本。默认 `zh`；可通过 `~/.loom/config.json` 中的 `scan.language` 全局覆盖，或在执行命令时使用该参数
- **Changed**：`/scan` 的 LLM 缓存对 Phase 3/4 改为按语言区分（键末尾追加 `:zh` 或 `:en`），Phase 1/2 仍跨语言共享。切换语言只会失效真正会变的那一半
- **Changed**：缓存的阶段标识符改名以匹配扫描流程的编号（`phase1` extract-endpoints、`phase2` extract-entities、`phase3` generate-entity、`phase4` generate-endpoint）。升级自动迁移：旧版本的 Phase 1/2 记录继续命中；Phase 3/4 记录会在下次扫描时重新生成
- **Removed**：去掉全局 `CACHE_VERSION` 一刀切清空机制。缓存失效改由 source-hash + 每条记录的形状校验驱动。如有 prompt 重大变更，用户可手动 `rm <outDir>/.loom-scan-cache.json` 清空
- **Fixed**：Phase 4 当 LLM 漏掉 `summary` 时，不再回退到英文 `brief`，而是退化为 `<METHOD> <path>`，避免一句英文混在中文文档里

### v0.2.0
- **Added**：`/scan <dir>` —— 多语言、LLM 驱动的源码 API 识别。识别框架、用 glob 收紧文件范围、提取接口标识（Phase 1）、识别并生成实体 Schema（Phase 1.5），然后按路由前缀生成单文件 Schema（Phase 2，通过 `x-entity-ref` 引用实体）
- **Added**：`/scan resume` 和 `/scan reset` —— 基于断点继续被中断的扫描，或丢弃已保存的断点
- **Added**：`/scan` 的 LLM 输出缓存，按文件内容哈希索引（`<outDir>/.loom-scan-cache.json`）；增量扫描会跳过未变更的文件，可节省 25 分钟以上。可使用 `--no-cache` 强制调用 LLM
- **Added**：`loom chat` 的输入历史持久化在全局 `~/.loom/history.jsonl`（上限 100 条），上下方向键跨会话浏览
- **Added**：斜杠命令 Tab 自动补全 —— bash 风格的公共前缀补全，候选列表内联展示
- **Added**：集中式的斜杠命令注册表（`src/tui/commands.ts`），让 Header 面板、`/help` 文案与自动补全列表共享一份数据源
- **Added**：静默使用埋点 —— 每次 CLI 调用上报一次 `{ userToken, action: "used" }`。UUID 持久化于 `~/.loom/install-id`，离线时落到 `~/.loom/telemetry-outbox.jsonl`
- **Added**：每次扫描的诊断日志 `<outDir>/.loom-scan.log`，以及来自 `scan-failures.ts` 的原始响应快照，便于排查 LLM 问题
- **Improved**：Phase 2 改为发送 handler 片段而非整文件，降低 token 消耗
- **Improved**：`/scan` 容忍推理模型的输出格式（例如 DeepSeek-R1 在同一响应中先输出 `<think>` 再给出最终 JSON）
- **Improved**：LLM 客户端将 `APIConnectionError` 与 SDK 内部 abort 归为可重试的 timeout
- **Fixed**：对已经扫描完成的断点执行 `/scan resume`，现在会提示 "scan already complete" 而不是默默重跑 analyze
- **Fixed**：实体的可空性改用 `required[]` 表达，而不是 `type` 联合
- **Fixed**：在 `doneCount` 自增过程中保留 group 冲突策略
- **Fixed**：abort 时保留扫描子阶段，`/scan resume` 能从正确步骤继续
- **Fixed**：聊天启动时的命令列表与 `/help` 同步（现在包含 `/scan` 与 `/abort`）
- **Removed**：禁用了 `/init` 命令；若项目里存在 `requirement.md`，仍会作为 agent 上下文加载

### v0.1.6
- **Added**：每个接口都有 Mock view / Mock edit 页面，可从模块列表进入
- **Added**：手写的 Mock 响应以同名 sidecar 文件 `<name>.mock.json` 存储（每个接口同时只有一个生效 override）
- **Added**：Mock 服务会使用 override 的 HTTP 状态码响应（例如保存 400 的 mock 后，`/mock/<path>` 会真的返回 HTTP 400）
- **Added**：Mock view/edit 中的状态码切换器，并标记哪些状态码已经存在 override
- **Added**：Mock view 提供 cURL 示例区块，基于接口的 `request` schema 生成（path、query、headers、body 全部填充示例值）
- **Added**：Mock body 内支持 Mock.js 表达式（`@cname`、`@integer(1,100)`、`'list|1-5': [...]` 等）—— 模板原样存储，每次请求时再展开
- **Removed**：接口详情页内嵌的 "Mock Testing" 区块（已由独立的 Mock 页面替代）

### v0.1.4
- **Fixed**：Web Viewer 中 GET 接口的 query 参数、path 参数和 headers 现在能正确显示
- **Improved**：所有请求/响应区段统一使用类 Swagger 的表格视图（SchemaTableViewer），含类型标签、约束、展开/折叠
- **Fixed**：启动 Web Viewer（`loom view`）或组合服务（`loom serve`）时的 `setNotFoundHandler` 重复注册错误
- **Removed**：移除与 React 19 不兼容的 `@stoplight/json-schema-viewer` 依赖




## 🙏 致谢

- [DeepSeek](https://www.deepseek.com/) —— 提供 AI 能力
- [Fastify](https://fastify.io/) —— 高性能 Web 服务
- [React](https://reactjs.org/) —— UI 组件
- [mock-json-schema](https://github.com/anttiviljami/mock-json-schema) —— Mock 数据生成
- [Ink](https://github.com/vadimdemedes/ink) —— 终端 UI

## 📞 支持

- **提问**：欢迎在 Issues 或 Discussions 中提出

---

<div align="center">
Made with ❤️ by the Loom team
</div>
