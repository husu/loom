# Loom

[简体中文](README.md) | **English**

<div align="center">

AI-powered JSON Schema document generator with TUI, web viewer and mock server

[![Node.js](https://img.shields.io/badge/Node.js-≥18.0.0-green)](https://nodejs.org/)
[![TypeScript](https://img.shields.io/badge/TypeScript-5.9-blue)](https://www.typescriptlang.org/)

</div>

## ✨ Features

- **🤖 AI-Powered Schema Generation** - Interactive TUI chat interface to generate and update JSON Schema API documentation using LLMs (DeepSeek, OpenAI)
- **🧩 Entity-Centric Modeling** - Manage reusable entity schemas in `docs/entities` and reference them in endpoint schemas via `x-entity-ref`
- **📚 Modern Web Viewer** - React-based SPA for browsing modules, endpoints, and entities with interactive schema rendering
- **⚡ Mock Server** - Dynamic mock API server that generates realistic test data based on JSON Schema
- **🖥️ In-Chat Service Control** - Start/stop/restart mock server and web viewer directly inside TUI (`/mock`, `/view`)
- **🗂️ Manifest Indexing** - Built-in manifest rebuild command for dependency/index consistency (`loom manifest rebuild`)
- **⬆️ Auto Update Prompt** - On startup, Loom checks npm for a newer version and can upgrade automatically after confirmation
- **🔧 TypeScript First** - Fully typed with modern TypeScript architecture

## 📦 Installation

### Prerequisites
- Node.js ≥ 18.0.0
- npm or yarn
- DeepSeek API key (required)

### Global Installation
```bash
npm install -g loom
# or
yarn global add loom
```


## ⚙️ Configuration

### Configuration File

Default global config path:
- macOS/Linux: `~/.loom/config.json`
- Windows: `%APPDATA%/loom/config.json`


When you run `loom chat` for the first time, it will guide you to create/update this global file interactively.

Default chat onboarding values:
- `provider`: `deepseek`
- `model`: `deepseek-chat`
- `baseURL`: `https://api.deepseek.com/v1`
- `apiKey`: required, must be entered by user

You can also create the global config manually:
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

`docs/` remains in each project directory (`--dir`) and is not moved to global storage.

## 🚀 Quick Start

### 1. Generate API Documentation
```bash
# Start interactive TUI to generate JSON Schema
loom chat

# Or specify project directory
loom chat --dir ./my-api-project
```

In `loom chat`, you can also control local services:
- `/mock`, `/mock stop`, `/mock restart [port]`
- `/view`, `/view stop`, `/view restart [port]`

### 2. View Documentation
```bash
# Start web documentation viewer
loom view

# Or with custom port
loom view --port 8080
```

### 3. Start Mock Server
```bash
# Start mock API server
loom mock

# Or with custom port
loom mock --port 8081
```

### 4. Combined Service (Recommended)
```bash
# Start both viewer and mock server on same port
loom serve

# Access at:
# - Web Viewer: http://localhost:3000
# - Mock API: http://localhost:3000/mock/...
```

### 5. Upgrade Loom
```bash
# Manually trigger upgrade
loom upgrade
```

> Loom also checks npm for updates when you run commands.  
> If a newer version is found, you can confirm and let Loom auto-upgrade.

## 📖 Usage Guide

### `loom chat`
Interactive terminal UI for generating JSON Schema documents through AI conversation.

```bash
loom chat [options]

Options:
  -d, --dir <path>    Target project directory (default: current directory)
  -h, --help          Display help
```

**Example Workflow:**
1. Run `loom chat`
2. Describe your API endpoints in natural language
3. AI generates JSON Schema with proper structure
4. Schema files are saved to `docs/` directory (configurable)

**Built-in chat commands:**
- Input tip: `Enter` to send, `Shift+Enter`/`Alt+Enter` for newline, `↑`/`↓` to navigate history (persisted across sessions), `Tab` to autocomplete commands
- `/help` — Show command help
- `/reset` — Reset conversation history
- `/list` — List generated schema files
- `/mock`, `/mock stop`, `/mock restart [port]` — Manage mock server
- `/view`, `/view stop`, `/view restart [port]` — Manage web viewer
- `/scan <dir>` — Discover APIs from source code via LLM; `/scan resume`, `/scan reset` manage the checkpoint
- `/abort` — Abort current request
- `/exit` — Exit Loom

### `loom view`
Modern web-based documentation viewer with React SPA interface.

```bash
loom view [options]

Options:
  -p, --port <number>  Port number (default: 3000)
  -d, --dir <path>     Target project directory (default: current directory)
  -h, --help           Display help
```

**Features:**
- 📁 Module browsing with endpoint counts
- 🧩 Entity browsing from `docs/entities`
- 🔍 Real-time search across modules and endpoints
- 📊 Swagger-like schema table viewer for all request/response sections (query params, path params, headers, body)
- 🔗 `x-entity-ref` auto-resolve in endpoint request/response rendering
- 🎨 Clean, responsive UI with dark sidebar
- 🔗 Direct links to specific endpoints

### `loom mock`
Dynamic mock API server that generates realistic data based on JSON Schema.

```bash
loom mock [options]

Options:
  -p, --port <number>  Port number (default: 3001)
  -d, --dir <path>     Target project directory (default: current directory)
  -h, --help           Display help
```

**Features:**
- 🚀 Automatic route registration from JSON Schema files
- 🎲 Smart mock data generation using mock-json-schema
- 📡 Support for all HTTP methods (GET, POST, PUT, DELETE, PATCH)
- 🔧 Configurable response status codes and schemas

### `loom serve`
Combined service that runs both web viewer and mock server together.

```bash
loom serve [options]

Options:
  -p, --port <number>  Port number (default: 3000)
  -d, --dir <path>     Target project directory (default: current directory)
  -h, --help           Display help
```

**URL Structure:**
- `http://localhost:3000/` - Web documentation viewer
- `http://localhost:3000/api/docs` - Documentation API
- `http://localhost:3000/api/schemas` - Schema file API
- `http://localhost:3000/api/entities` - Entity file API
- `http://localhost:3000/mock/...` - Mock API endpoints

### `loom manifest rebuild`
Rebuild docs manifest index file (`.loom-manifest.json`) for dependency/index consistency.

```bash
loom manifest rebuild [options]

Options:
  -d, --dir <path>  Target project directory (default: current directory)
  -h, --help        Display help
```

### `loom upgrade`
Upgrade loom to the latest npm version.

```bash
loom upgrade
```

## 📝 JSON Schema Format

Loom uses a custom JSON Schema format optimized for API documentation:

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

### Schema Structure
- **title**: API module title
- **description**: Module description
- **endpoints**: Array of API endpoint definitions
- **endpoint.path**: URL path (supports path parameters)
- **endpoint.method**: HTTP method (GET, POST, PUT, DELETE, PATCH)
- **endpoint.request**: Optional request schema (headers, params, query, body)
- **endpoint.response**: Response schemas keyed by status code

## 🧩 Entity Schema & References

Loom supports reusable entity schemas in:

```text
docs/entities/*.entity.schema.json
```

Example entity file:

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

In endpoint schemas, reference an entity with `x-entity-ref`:

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

Supported forms:
- String form: `"x-entity-ref": "User"`
- Object form: `"x-entity-ref": { "entity": "User", "pick": ["id", "name"] }`

## 🏗️ Project Structure

```
loom/
├── src/
│   ├── agents/          # AI agent system with tool calling
│   │   ├── core/        # Agent core logic
│   │   └── memory/      # Conversation memory management
│   ├── llm/             # LLM client (DeepSeek, OpenAI)
│   │   ├── client.ts    # Streaming LLM client
│   │   └── config.ts    # LLM configuration
│   ├── tools/           # Tool system for agent
│   │   ├── schema-gen.ts     # Schema generation tools
│   │   ├── schema-validate.ts # Schema validation tools
│   │   ├── file-ops.ts        # Schema file operations tools
│   │   ├── entity-file-ops.ts # Entity file operations tools
│   │   └── entity-workflow.ts # Entity impact/sync/validate tools
│   ├── tui/             # Terminal UI (chat interface)
│   │   ├── app.tsx      # Main TUI application
│   │   └── components/  # React components for TUI
│   ├── view/            # Web documentation viewer
│   │   ├── server.ts    # Fastify web server
│   │   ├── routes/      # API routes
│   │   ├── frontend/    # React SPA frontend
│   │   └── public/      # Static assets
│   ├── mocks/           # Mock server
│   │   ├── server.ts    # Mock server implementation
│   │   ├── router.ts    # Dynamic route registration
│   │   └── generator.ts # Mock data generation
│   ├── shared/          # Shared utilities
│   │   ├── config.ts    # Configuration loader
│   │   ├── entity-utils.ts     # Entity read/write/reference helpers
│   │   ├── manifest-utils.ts   # Manifest index builder
│   │   ├── schema-entity-resolver.ts # x-entity-ref resolver for viewer APIs
│   │   ├── types.ts     # TypeScript definitions
│   │   └── logger.ts    # Logging utilities
│   ├── serve.ts         # Combined viewer + mock server
│   └── index.ts         # CLI entry point
├── docs/                # Generated schema files
├── scripts/             # Build scripts
├── dist/                # Compiled output
└── package.json
```

## 🛠️ Development

### Building the Project
```bash
# Build TypeScript backend
npm run build

# Build React frontend
npm run build:view

# Build both (recommended)
npm run build:all
```

### Development Mode
```bash
# Start TypeScript development server
npm run dev

# Watch mode for frontend development
npm run dev:view
```

### Adding New Features
1. **New LLM Provider**: Extend `src/llm/config.ts` and `src/llm/client.ts`
2. **New Agent Tool**: Add under `src/tools/` and register in `src/agents/core/agent.ts`
3. **UI Component**: Add to `src/view/frontend/components/`
4. **API Endpoint**: Add to `src/view/routes/` or `src/mocks/router.ts`

## 📦 Publishing to npm

For maintainers, to publish a new version to npm registry:

```bash
# Login to npm (first time only)
npm login

# Update version if needed
npm version patch  # or minor, major

# Build and create package
npm run build:all

# Publish to npm
npm publish

# Or publish with dry-run first
npm publish --dry-run
```

The package includes:
- Compiled JavaScript in `dist/`
- TypeScript definitions
- Pre-built React frontend bundle
- CLI executable (`loom`)

Package configuration:
- `prepack` script ensures fresh build before packaging
- `.npmignore` excludes source files and development artifacts
- All runtime dependencies are correctly specified

## 📋 Changelog

### v0.3.0
- **Added**: `--lang zh|en` flag for `/scan` and `/scan resume`. Phase 3 (generate-entity) and Phase 4 (generate-endpoint) emit `description` and `summary` text in the configured language. Default is `zh`; override globally via `scan.language` in `~/.loom/config.json` or per-invocation via the flag
- **Changed**: `/scan` LLM cache is now per-language for Phase 3/4 (key gains a trailing `:zh` or `:en` segment), while Phase 1/2 stay shared across languages. Switching language only invalidates the half that actually changes
- **Changed**: Cache phase identifiers renamed to match scan-flow numbering (`phase1` extract-endpoints, `phase2` extract-entities, `phase3` generate-entity, `phase4` generate-endpoint). Upgrading auto-migrates: Phase 1/2 entries from previous versions keep hitting; Phase 3/4 entries are regenerated on next scan
- **Removed**: Global `CACHE_VERSION` wipe. Cache invalidation is now driven by source-hash + per-entry shape guards. For truly breaking prompt changes, users can `rm <outDir>/.loom-scan-cache.json` to clear manually
- **Fixed**: Phase 4 (generate-endpoint) summary no longer falls back to the English `brief` when the LLM omits `summary`; it degrades to `<METHOD> <path>` instead, avoiding a stray English sentence in otherwise-Chinese docs

### v0.2.0
- **Added**: `/scan <dir>` — multi-language, LLM-driven API discovery from existing source code. Detects framework, narrows file scope via globs, extracts endpoint identities (Phase 1), discovers + generates entity schemas (Phase 1.5), then generates one schema file per route prefix (Phase 2 referencing entities via `x-entity-ref`)
- **Added**: `/scan resume` and `/scan reset` — checkpoint-aware continuation of an interrupted scan, or discard of the saved checkpoint
- **Added**: LLM output cache for `/scan`, keyed by file content hash (`<outDir>/.loom-scan-cache.json`); incremental rescans skip unchanged files and can save 25+ minutes. Pass `--no-cache` to force LLM calls
- **Added**: Persistent input history in `loom chat`, stored globally at `~/.loom/history.jsonl` (capped at 100 entries). Up/down arrows navigate across sessions
- **Added**: Tab autocomplete for slash commands — bash-style common-prefix completion with the candidate list rendered inline
- **Added**: Centralized slash command registry (`src/tui/commands.ts`) so the Header panel, `/help` text, and the autocomplete list share one source of truth
- **Added**: Silent usage telemetry — fires `{ userToken, action: "used" }` once per CLI invocation. Persisted UUID at `~/.loom/install-id`, offline-tolerant outbox at `~/.loom/telemetry-outbox.jsonl`
- **Added**: Per-scan diagnostic log at `<outDir>/.loom-scan.log` plus raw response dumps from `scan-failures.ts` for LLM debugging
- **Improved**: Phase 2 sends a handler excerpt rather than the full source file, reducing token usage
- **Improved**: `/scan` tolerates reasoning-model output (e.g. DeepSeek-R1 `<think>` content followed by final JSON in one body)
- **Improved**: LLM client classifies `APIConnectionError` and SDK-internal aborts as retryable timeouts
- **Fixed**: `/scan resume` on an already-completed checkpoint now surfaces a "scan already complete" message instead of silently re-running analyze
- **Fixed**: Entity nullability expressed via `required[]` rather than `type` unions
- **Fixed**: Group conflict policy preserved across `doneCount` increments
- **Fixed**: Scan sub-phase preserved on abort so `/scan resume` continues at the right step
- **Fixed**: Chat startup command list synced with `/help` (now includes `/scan` and `/abort`)
- **Removed**: `/init` command (disabled); `requirement.md` is still loaded as agent context if the file exists in the project

### v0.1.6
- **Added**: Mock view / Mock edit pages for each endpoint, accessible from the module list
- **Added**: Sidecar `<name>.mock.json` storage for hand-written mock responses (one active override per endpoint)
- **Added**: Mock server now responds with the override's HTTP status code (e.g. saving a 400 mock makes `/mock/<path>` return real HTTP 400)
- **Added**: Status code switcher in Mock view/edit, marking which statuses already have a saved override
- **Added**: cURL example block on Mock view, generated from the endpoint's `request` schema (path params, query, headers, body all filled with samples)
- **Added**: Mock.js expression support inside saved mock bodies (`@cname`, `@integer(1,100)`, `'list|1-5': [...]` etc.) — templates are stored as-is and expanded on each request
- **Removed**: Inline "Mock Testing" section from the endpoint detail page (superseded by the dedicated Mock pages)

### v0.1.4
- **Fixed**: Query parameters, path parameters, and headers for GET endpoints now display correctly in the Web Viewer
- **Improved**: All request/response sections use a consistent Swagger-like table view (SchemaTableViewer) with type badges, constraints, and expand/collapse support
- **Fixed**: Duplicate `setNotFoundHandler` error when starting the Web Viewer (`loom view`) or combined server (`loom serve`)
- **Removed**: Broken `@stoplight/json-schema-viewer` dependency (incompatible with React 19)

## 🤝 Contributing

Contributions are welcome! Please follow these steps:

1. **Fork** the repository
2. **Create a feature branch**: `git checkout -b feature/amazing-feature`
3. **Commit your changes**: `git commit -m 'Add amazing feature'`
4. **Push to the branch**: `git push origin feature/amazing-feature`
5. **Open a Pull Request**

### Development Guidelines
- Use TypeScript with strict mode
- Follow existing code style and patterns
- Add tests for new functionality
- Update documentation as needed

## 📄 License

This project is licensed under the ISC License - see the [LICENSE](LICENSE) file for details.

## 🙏 Acknowledgments

- [DeepSeek](https://www.deepseek.com/) for AI capabilities
- [Fastify](https://fastify.io/) for high-performance web server
- [React](https://reactjs.org/) for UI components
- [mock-json-schema](https://github.com/anttiviljami/mock-json-schema) for mock data generation
- [Ink](https://github.com/vadimdemedes/ink) for terminal UI

## 📞 Support

- **Issues**: [GitHub Issues](https://github.com/yourusername/loom/issues)
- **Documentation**: This README and code comments
- **Questions**: Open an issue or discussion

---

<div align="center">
Made with ❤️ by the Loom team
</div>
