# MiMo Free API MCP🚀 (V2.5 + V2.6 Series)

[![CI](https://github.com/Golden0Voyager/mimo-free-api-mcp/actions/workflows/ci.yml/badge.svg)](https://github.com/Golden0Voyager/mimo-free-api-mcp/actions/workflows/ci.yml) [![License: GPL-3.0](https://img.shields.io/badge/License-GPL--3.0-blue.svg)](./LICENSE) [![Node](https://img.shields.io/badge/node-22%2B-green.svg)](https://nodejs.org)

English | [简体中文](./README.md)


> An advanced OpenAI-compatible gateway + native MCP plugin integration based on the reverse engineering of the official Xiaomi Large Model (MiMo) website ([aistudio.xiaomimimo.com](https://aistudio.xiaomimimo.com)). Now fully adapted to the **MiMo V2.5 Multimodal** series, with newly added support for the **MiMo V2.6** series, supporting the **Thinking (Chain of Thought) protocol** and **Omni (Multimodal) interaction**.

---

## 🏗️ Core Features (V2.5 + V2.6 Features)

1.  **V2.5 Multimodal Adaptation**: Deep integration of `mimo-v2.5` and `mimo-v2.5-pro` models, supporting complex analysis of native images, videos, and audio.
2.  **New V2.6 Support**: Adds the `mimo-v2.6-pro` (flagship reasoning) and `mimo-v2.6-flash` (ultra-fast lightweight) model IDs, ready to use directly; V2.5 remains the default model.
3.  **Thinking Protocol Alignment**: Perfectly supports the latest official Chain of Thought (Reasoning) protocol. Stream output automatically includes `reasoning_content`, authentically restoring the AI's thinking process.
4.  **Transparent Upgrade Routing**: Maintains compatibility with the V2 era. Requests for `mimo-v2-omni` are smoothly routed to `mimo-v2.5`, and `mimo-v2-pro` to `mimo-v2.5-pro`.
5.  **Environmental Token Configuration**: Supports one-click deployment via the `token` environment variable (format: `ph.uid.token`) in `.env`.
6.  **Native MCP Server**: Integrated with the latest MCP standards. Empowers clients like Claude and Cursor with native **web search (`search`)** and **visual analysis (`vision`)** capabilities.

---

## 📊 Model Matrix

| Model ID | Base Capability | Thinking (Reasoning) | Core Advantages |
| :--- | :--- | :--- | :--- |
| `mimo-v2.6-pro` | **V2.6 Flagship Reasoning** 🆕 | ✅ Enabled by default | Trillion-parameter flagship, strongest reasoning and agent capabilities |
| `mimo-v2.6-flash` | **V2.6 Ultra-fast Lightweight** 🆕 | 🔁 Native reasoning (always outputs `reasoning_content`) | Fast response, hybrid reasoning architecture |
| `mimo-v2.5` | **Multimodal Flagship** (default model) | ✅ Enabled by default | Best for visual, audio, and multimodal understanding |
| `mimo-v2.5-pro` | **Inference Enhanced** | ✅ Enabled by default | Rigorous logic, strongest search, and long-text analysis |
| `mimo-v2-flash` | **Ultra-fast Lightweight** | Optional (via suffix) | Millisecond response, ideal for simple dialogue and translation |
| `mimo-v2-omni` | (Compatibility ID) | ✅ (Routed to 2.5) | Compatible with legacy V2-Omni clients |
| `mimo-v2-pro` | (Compatibility ID) | ✅ (Routed to 2.5-pro) | Compatible with legacy V2-Pro clients |

> [!TIP]
> **Using V2.6**: Simply pass `model: "mimo-v2.6-pro"` or `"mimo-v2.6-flash"` in your request — vision and web search are verified working; requests explicitly specifying V2.6 keep V2.6 in vision/tool scenarios instead of being downgraded, while requests without a model still auto-upgrade to `mimo-v2.5` (identical behavior to previous versions).
> **MCP Tool Model Configuration**: You can set `MCP_SEARCH_MODEL` / `MCP_VISION_MODEL` in `.env` to choose the models used by the `search` / `vision` tools (defaults: `mimo-v2.5-pro` / `mimo-v2.5`).

> [!WARNING]
> **Tool Calling Limitation**: Native Function Calling is currently extremely unstable and **cannot be reliably used in Agents (e.g., AutoGPT, LangChain Agents)**. It is recommended for use only in dialogue, visual analysis, or via the MCP plugin in supported clients (e.g., Claude/Cursor).

---

## 🔑 Credential Configuration

The project supports passing credentials via environment variables or API headers.

### Method A: One-click .env Deployment (Recommended)
Create a `.env` file in the root directory (refer to `.env.example`) and fill in the three-part Token obtained from the official website:
```env
# Format: ph.userId.serviceToken
token=xxxxxxxx.yyyyyyyy.zzzzzzzz
```

### Method B: OpenAI Header Transmission
Use the Bearer Token directly during API calls:
```bash
Authorization: Bearer YOUR_MIMO_TOKEN
```

### 📖 Beginner's Guide: Get a Token in 3 Minutes

> Prerequisite: registered Xiaomi account, and [aistudio.xiaomimimo.com](https://aistudio.xiaomimimo.com) opens normally.

1. **Log in** to [aistudio.xiaomimimo.com](https://aistudio.xiaomimimo.com).
2. **Open DevTools**: press `F12` (macOS: `⌥ + ⌘ + I`), or right-click the page → **Inspect**.
3. **Open the Cookies panel**: click the **Application** tab → expand **Cookies** in the sidebar → click `https://aistudio.xiaomimimo.com`.
   > Firefox: the tab is **Storage** → **Cookies** in the sidebar.
4. **Copy three values** — find these rows in the cookie list and double-click the **Value** column to copy:
   | Name | Description |
   | :--- | :--- |
   | `xiaomichatbot_ph` | Part 1 (short string ending with `==`) |
   | `userId` | Part 2 (plain digits) |
   | `serviceToken` | Part 3 (very long string) |
5. **Join them** with dots in order:
   ```text
   <xiaomichatbot_ph>.<userId>.<serviceToken>
   ```
6. **Write to `.env`**: paste the joined string after `token=` in the project's `.env`, then restart the service (`docker compose up -d` or `npm start`).

<details>
<summary>🔎 Can't find serviceToken in the Application panel? Grab it from the Network panel</summary>

1. Switch DevTools to the **Network** tab and press `F5` to reload.
2. Click any request starting with `open-apis`.
3. In **Request Headers**, find the `Cookie:` line and copy the values after `xiaomichatbot_ph=`, `userId=`, and `serviceToken=` (each value ends at the next `;`).

</details>

> [!IMPORTANT]
> - **Tokens expire** (usually lasting months). If all models suddenly return **empty replies**, it's 99% an expired token — re-capture and update `.env`.
> - **Never share your Token**: it is equivalent to your account session. `.env` is excluded by `.gitignore`; do not commit or share it.
> - The third part, `serviceToken`, is an `HttpOnly` cookie — invisible to `document.cookie`; use the panels above.

---

## 📂 Local File Support (Docker Mode)

When running in Docker, the service cannot access host files by default due to container isolation.

### Core Workflow:
1. **Multi-Mode Support**: This project supports **URLs**, **Base64 Data**, and **Local Filenames**.
2. **Mount & Map (Local)**: To use local files, place them in a host directory and map it to `/app/media` in your `docker-compose.yml`:
   ```yaml
   volumes:
     - /your/host/path:/app/media:ro
   ```
3. **Simple Access**: Once mounted, just tell the AI the filename (e.g., "Analyze `test.mp4`"). The system will automatically look in `/app/media`.

### 💡 Usage Tips & Prompting Guide
- **Filename (Recommended)**: "Analyze this local video: `demo.mp4`"
- **URL Address**: "Check this online image: `https://example.com/cat.jpg`"
- **Base64**: Simply paste the Base64 Data URI to the AI.
- **Comparison**: "Compare `local_image.png` with this web image `https://.../2.jpg`"

> **Note**: Ensure the AI has access to the `vision` tool. This project informs the AI in the tool schema about these multiple sources.

---

## 🤖 MCP Plugin Integration (Cursor / Claude)

The MCP service in this project is integrated on port `8001` and has been fully upgraded to the **2025 Streamable HTTP** standard.

### Client Configuration Example:
Please add the following configuration to `claude_desktop_config.json`:

```json
{
  "mcpServers": {
    "mimo": {
      "url": "http://localhost:8001/mcp",
      "type": "http",
      "headers": {
        "Authorization": "Bearer YOUR_TOKEN"
      }
    }
  }
}
```

### Provided Tools
- **`search(query)`**: AI automatically calls MiMo's web search.
- **`vision(query, image)`**: AI analyzes images, videos, audio, or local resources (supports local absolute paths, Base64, or URLs).

---

## 🐳 Quick Deployment

```bash
# 1. Ensure .env is configured with the token
# 2. Start the container
docker compose up -d --build
```

---

## 🤖 One-shot Deployment Prompt (for AI Agents)

Copy the prompt below to any AI Agent with **terminal execution** (optionally browser control) — Cursor, Claude Code, Cline, etc. It will autonomously complete the full flow: clone, token capture, configuration, build, startup, and verification:

````text
You are the automated deployment assistant for the mimo-free-api-mcp project. Execute the following phases in order; each phase has explicit completion criteria. On failure, follow the troubleshooting note and retry (max 2 attempts), then report the blocker to the user and stop.

[Phase 0: Environment check]
- Verify git, docker, and docker compose are available (run docker compose version).
- If Docker is unavailable, fall back to Node.js: requires Node.js 22+; run npm install and npm run build for the Phase 3 build step. Note bare Node does NOT auto-load .env — export its variables (e.g. export $(grep -v "^#" .env | xargs)) before npm start.
- Service port: container listens on 8001; host mapping is in docker-compose.yml (default 127.0.0.1:8002->8001). If the port is taken, change the left-side port to a free one.

[Phase 1: Get the code]
- git clone https://github.com/Golden0Voyager/mimo-free-api-mcp.git and cd into it.
- Completion criteria: README.md and docker-compose.yml exist.

[Phase 2: Obtain credentials (critical)]
- Goal: get three cookies of a LOGGED-IN session for domain aistudio.xiaomimimo.com: xiaomichatbot_ph, userId, serviceToken. Note serviceToken is an HttpOnly cookie.
- Path A (you have browser control, e.g. Playwright/CDP/browser extension): open https://aistudio.xiaomimimo.com (prefer reusing the user's logged-in browser session); if the page shows logged-out, stop and ask the user to sign in with their Xiaomi account in that browser first. Then read the three cookie values via the CDP/Playwright cookie API (which can read HttpOnly).
- Path B (no browser control): ask the user to follow the "Beginner's Guide: Get a Token in 3 Minutes" section of the README and paste the three cookie values to you.
- Join the values as <ph>.<userId>.<serviceToken> and write them into .env in the project root: token=<joined> (single line; .env is git-ignored, do not commit it).
- Security: the token is equivalent to the user's account session; never print it fully in logs or chat.
- Completion criteria: .env exists with a token= line in three-part format (two dot separators).

[Phase 3: Build and start]
- Run docker compose up -d --build, wait ~5s, then docker compose ps must show the container Up.

[Phase 4: Verify]
- GET http://127.0.0.1:<host-port>/v1/models — the returned data list must contain mimo-v2.6-pro.
- POST http://127.0.0.1:<host-port>/v1/chat/completions with body: {"model":"mimo-v2.6-pro","stream":false,"messages":[{"role":"user","content":"reply ok only"}]}
- Success criteria: HTTP 200 and non-empty choices[0].message.content.
- Troubleshooting: empty content or 401 in container logs → token expired or wrong, return to Phase 2; connection refused → check port mapping and container status; backend replies "model name error" → make sure the requested model ID matches /v1/models.

[Phase 5: Report]
- Report to the user: service URL, model list from /v1/models, the test reply content, and how to refresh the token when it expires (repeat Phase 2, then restart the container).
- Bonus: if the user wants MCP integration, the MCP endpoint is http://127.0.0.1:<host-port>/mcp with header Authorization: Bearer <token>.
````

> [!TIP]
> Phase 2 depends on **the user's logged-in Xiaomi browser session**. Agents without browser control will ask you for the three cookie values here — follow the "Beginner's Guide" above to copy them.

---

## ⚖️ Disclaimer

Please read and understand the following terms before using this project:

1. **Not an official project**: This project has **no affiliation** with Xiaomi Inc. or its MiMo products. The "MiMo" name and trademarks belong to Xiaomi. It is implemented by reverse-engineering the official web interface, for technical learning and research purposes only.
2. **Learning & research only**: The project (including all source code) is for personal learning, technical research, and academic exchange. **Commercial use** and any illegal use are strictly prohibited.
3. **No stability guarantee**: The official interface may change or be hardened at any time, breaking part or all of this project's functionality; the author and contributors make no promises regarding availability or accuracy.
4. **Use at your own risk**: Any consequences of using this project (including capturing and using Tokens) — including but not limited to account restrictions, risk control, or bans — are **borne by the user**.
5. **Compliance**: Please comply with the official Xiaomi MiMo user agreement and applicable laws and regulations; upon official request, this project will cease maintenance.
6. **License**: The code is open-sourced under [GPL-3.0](./LICENSE); the project comes with no warranty, express or implied.

---

## 🙏 Acknowledgements

This project is built on top of [Fu-Jie/mimo-free-api-mcp](https://github.com/Fu-Jie/mimo-free-api-mcp) — credits to the original author for the core reverse engineering and implementation. This repository adapts the MiMo V2.6 series, fixes V2.6 Flash model routing, and adds CI quality gates as community maintenance.
