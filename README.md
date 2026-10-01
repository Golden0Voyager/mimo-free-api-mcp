# MiMo Free API MCP🚀 (V2.5 + V2.6 Series)

[![CI](https://github.com/Golden0Voyager/mimo-free-api-mcp/actions/workflows/ci.yml/badge.svg)](https://github.com/Golden0Voyager/mimo-free-api-mcp/actions/workflows/ci.yml) [![License: GPL-3.0](https://img.shields.io/badge/License-GPL--3.0-blue.svg)](./LICENSE) [![Node](https://img.shields.io/badge/node-22%2B-green.svg)](https://nodejs.org)

[English](./README_EN.md) | 简体中文


> 基于小米大模型（MiMo）官方网站（[aistudio.xiaomimimo.com](https://aistudio.xiaomimimo.com)）逆向构建的高级 OpenAI 兼容网关 + 原生 MCP 插件集成。现已全量适配 **MiMo V2.5 全模态** 系列，并新增 **MiMo V2.6** 系列支持，支持 **Thinking (思维链) 协议** 与 **Omni (全模态) 交互**。

---

## 🏗️ 核心特性 (V2.5 + V2.6 Features)

1.  **V2.5 全模态适配**: 深度集成 `mimo-v2.5` 与 `mimo-v2.5-pro` 模型，支持原生图像、视频、音频的复杂分析。
2.  **V2.6 新增支持**: 新增 `mimo-v2.6-pro`（旗舰推理）与 `mimo-v2.6-flash`（极速轻量）两个模型 ID，可直接请求使用；V2.5 仍为默认模型。
3.  **Thinking 协议对齐**: 完美支持官方最新的思维链 (Reasoning) 协议。流式输出中自动包含 `reasoning_content`，真实还原 AI 思考过程。
4.  **透明升级路由**: 保持对 V2 时代的兼容。请求 `mimo-v2-omni` 会自动平滑路由至 `mimo-v2.5`，`mimo-v2-pro` 会路由至 `mimo-v2.5-pro`。
5.  **环境化 Token 配置**: 支持在 `.env` 中通过 `token` 环境变量（格式：`ph.uid.token`）完成一键部署。
6.  **Native MCP Server**: 集成最新 MCP 标准。赋予 Claude / Cursor 等客户端原生的 **联网搜索 (`search`)** 与 **视觉分析 (`vision`)** 能力。

---

## 📊 模型矩阵 (Model Matrix)

| 模型 ID | 基座能力 | 推理思维链 (Thinking) | 核心优势 |
| :--- | :--- | :--- | :--- |
| `mimo-v2.6-pro` | **V2.6 旗舰推理版** 🆕 | ✅ 默认开启 | 万亿参数旗舰，最强推理与 Agent 能力 |
| `mimo-v2.6-flash` | **V2.6 极速轻量版** 🆕 | 🔁 原生思维链（始终输出 `reasoning_content`） | 快速响应，混合推理架构 |
| `mimo-v2.5` | **全模态旗舰**（默认模型） | ✅ 默认开启 | 视觉、音频、多模态理解最佳方案 |
| `mimo-v2.5-pro` | **推理增强版** | ✅ 默认开启 | 逻辑严密、最强搜索与长文本分析 |
| `mimo-v2-flash` | **极速轻量版** | 可选 (后缀激活) | 毫秒级响应，适合简单对话与翻译 |
| `mimo-v2-omni` | (兼容 ID) | ✅ (路由至 2.5) | 兼容旧版 V2-Omni 客户端 |
| `mimo-v2-pro` | (兼容 ID) | ✅ (路由至 2.5-pro) | 兼容旧版 V2-Pro 客户端 |

> [!TIP]
> **使用 V2.6**：在请求中直接传 `model: "mimo-v2.6-pro"` 或 `"mimo-v2.6-flash"` 即可，视觉识别与联网搜索均已实测可用；显式指定 V2.6 的请求在带图/带工具场景下保持 V2.6 不降级，未指定模型的请求仍自动升配至 `mimo-v2.5`（行为与旧版本一致）。
> **MCP 工具模型配置**：可在 `.env` 中通过 `MCP_SEARCH_MODEL` / `MCP_VISION_MODEL` 指定 `search` / `vision` 工具使用的模型（默认分别为 `mimo-v2.5-pro` / `mimo-v2.5`）。

> [!WARNING]
> **工具调用 (Tool Calling) 限制**：目前模型原生工具调用（Function Calling）极度不稳定，**无法在 Agent（如 AutoGPT、LangChain Agent 等）中可靠使用**。建议仅作为对话、视觉分析或通过 MCP 插件在支持的客户端（如 Claude/Cursor）中使用。

---

## 🔑 凭证配置 (Credentials)

项目支持通过环境变量或 API Header 传递凭证。

### 方式 A：一键式 .env 部署 (推荐)
在根目录创建 `.env` 文件（可参考 `.env.example`），填入从官网获取的三段式 Token：
```env
# 格式: ph.userId.serviceToken
token=xxxxxxxx.yyyyyyyy.zzzzzzzz
```

### 方式 B：OpenAI Header 传递
直接在 API 调用时使用 Bearer Token：
```bash
Authorization: Bearer YOUR_MIMO_TOKEN
```

### 📖 新手教程：3 分钟获取 Token

> 前置要求：已注册并登录小米账号，能正常打开 [aistudio.xiaomimimo.com](https://aistudio.xiaomimimo.com)

1. **登录** [aistudio.xiaomimimo.com](https://aistudio.xiaomimimo.com)，进入对话页面。
2. **打开开发者工具**：按 `F12`（macOS 为 `⌥ + ⌘ + I`），或页面空白处右键 → **检查 / Inspect**。
3. **进入 Cookie 面板**：点击顶部 **Application**（应用）标签页 → 左侧栏展开 **Cookies** → 点击 `https://aistudio.xiaomimimo.com`。
   > Firefox 用户：顶部标签为 **存储 / Storage** → 左侧 **Cookie**。
4. **复制三个值**：在右侧 Cookie 列表中分别找到下面三行，双击 **Value** 列复制其值：
   | Name（名称） | 说明 |
   | :--- | :--- |
   | `xiaomichatbot_ph` | 第一段（以 `==` 结尾的短串） |
   | `userId` | 第二段（纯数字） |
   | `serviceToken` | 第三段（很长的串） |
5. **按顺序拼接**：三段用英文句点 `.` 连接，得到：
   ```text
   <xiaomichatbot_ph的值>.<userId的值>.<serviceToken的值>
   ```
6. **写入 `.env`**：将拼接结果粘贴到项目根目录 `.env` 的 `token=` 后面，然后重启服务（`docker compose up -d` 或重新运行 `npm start`）。

<details>
<summary>🔎 Application 面板里找不到 serviceToken？用网络面板抓一次</summary>

1. 开发者工具切到 **Network**（网络）标签页，按 `F5` 刷新页面。
2. 在请求列表中点击任意一个 `open-apis` 开头的请求。
3. 在右侧 **Request Headers**（请求标头）中找到 `Cookie:` 那一行，分别复制 `xiaomichatbot_ph=`、`userId=`、`serviceToken=` 后面的值（每段值以 `;` 结束）。

</details>

> [!IMPORTANT]
> - **Token 会过期**（通常可维持数月）。若所有模型突然返回**空回复**，99% 是 token 过期了——按上述步骤重新抓取并更新 `.env` 即可。
> - **不要泄露 Token**：它等同你的账号会话凭证。`.env` 已被 `.gitignore` 排除，请勿提交或分享。
> - 第三段 `serviceToken` 是 `HttpOnly` Cookie，通过 JS 的 `document.cookie` 读不到，必须在上述面板中查看。

---

## 📂 本地路径支持 (Local File Access)

如果您在 Docker 环境下运行，由于容器隔离，服务默认无法直接读取宿主机路径。

### 核心工作流 (Workflow)：
1. **多模式支持**：本项目支持 **URL**、**Base64 数据** 以及 **本地文件名**。
2. **挂载映射 (本地文件)**：若需使用本地文件，请将其存放在宿主机目录中，并在 `docker-compose.yml` 中挂载到容器的 `/app/media`：
   ```yaml
   volumes:
     - /您的宿主机路径:/app/media:ro
   ```
3. **直接访问**：挂载完成后，您只需直接告诉 AI 文件名（如：“分析一下 `test.mp4`”），系统会自动在 `/app/media` 目录中寻址。

### 💡 提问技巧 (Prompting Guide)
- **文件名 (最推荐)**: "分析这个本地视频：`demo.mp4`"
- **URL 地址**: "分析这张网上的图片：`https://example.com/cat.jpg`"
- **Base64**: 直接将 Base64 数据 URI 粘贴给 AI 即可。
- **对比分析**: "对比 `local_image.png` 和这个网页图片 `https://.../2.jpg` 的区别"

> **注意**：请确保 AI 能够通过其工具集（Tools）访问到 `vision` 工具。本项目已在工具描述中告知 AI 支持多种来源。

---

## 🤖 MCP 插件集成 (Cursor / Claude)

本项目 MCP 服务集成在 `8001` 端口下，已全面升级至 **2025 Streamable HTTP** 标准。

### 客户端配置示例：
请将以下配置添加到 `claude_desktop_config.json`：

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

### 提供的工具 (Tools)
- **`search(query)`**: AI 自动调用 Mimo 联网搜索。
- **`vision(query, image)`**: AI 分析图像、视频、音频或本地资源（支持本地绝对路径、Base64 或 URL）。

---

## 🐳 快速部署 (Deployment)

### 方式一：预构建镜像（免构建，推荐）

镜像发布于 GHCR，支持 amd64 / arm64 双架构，无需克隆源码和本地构建：

```bash
docker run -d --name mimo-free-api-mcp \
  --restart always \
  -p 8001:8001 \
  -e token=<你的Token> \
  ghcr.io/golden0voyager/mimo-free-api-mcp:v1.3.0
```

> [!NOTE]
> - **镜像本身不包含任何凭证**：每个用户必须通过 `-e token=` 注入**自己的** Token（获取方式见上文「新手教程」），也可以改用 `-e xiaomichatbot_ph=xxx -e userId=xxx -e xiaomichatbot_serviceToken=xxx` 三段式注入。
> - Token 过期后无需重新拉镜像，重启容器换新 token 即可：`docker rm -f mimo-free-api-mcp` 后用新值重跑上面的命令。
> - 全部可用版本见[镜像发布页](https://github.com/Golden0Voyager/mimo-free-api-mcp/pkgs/container/mimo-free-api-mcp)；也可使用 `latest` 标签跟随主线。

### 方式二：源码构建（适合需要自定义的开发者）

```bash
# 1. 确保 .env 已配置 token
# 2. 启动容器
docker compose up -d --build
```

---

## 🤖 一键部署 Prompt（交给 AI Agent 执行）

把下面这段 Prompt 复制给任意具备**终端执行**能力（可选：浏览器控制）的 AI Agent（Cursor、Claude Code、Cline 等），它将自主完成克隆、抓取 Token、配置、构建、启动与验证的全套工作：

````text
你是 mimo-free-api-mcp 项目的自动化部署助手。请按以下阶段依次执行，每个阶段有明确的完成标准；任何一步失败，按对应排障指引处理后再重试，最多重试 2 次，仍失败则向用户报告卡点并停止。

【阶段 0：环境检查】
- 确认 git、docker、docker compose 均可用（执行 docker compose version 验证）。
- 若 Docker 不可用，改用 Node.js 路线：需要 Node.js 22+，执行 npm install 与 npm run build 替代阶段 3 的构建部分；注意裸 Node 不会自动读取 .env，启动前需将 .env 中的变量导出为环境变量（如 export $(grep -v "^#" .env | xargs)）后再 npm start。
- 确定服务端口：默认容器内 8001，宿主机映射见 docker-compose.yml（默认 127.0.0.1:8002->8001）。若端口被占用，修改 docker-compose.yml 左侧端口为空闲端口。

【阶段 1：获取代码】
- git clone https://github.com/Golden0Voyager/mimo-free-api-mcp.git 并进入项目目录。
- 完成标准：README.md 与 docker-compose.yml 存在。

【阶段 2：获取凭证（关键）】
- 目标：取得域名 aistudio.xiaomimimo.com 下【已登录】会话的三个 Cookie：xiaomichatbot_ph、userId、serviceToken。注意 serviceToken 是 HttpOnly Cookie。
- 路径 A（你具备浏览器控制能力，如 Playwright/CDP/浏览器扩展）：打开 https://aistudio.xiaomimimo.com（优先复用用户已登录的浏览器会话）；若页面显示未登录，停止并请用户先在该浏览器中登录小米账号。然后用 CDP/Playwright 的 Cookie 读取接口（可读取 HttpOnly）获取上述三个 Cookie 的值。
- 路径 B（无浏览器能力）：请用户自行按项目 README 中「新手教程：3 分钟获取 Token」的步骤，从开发者工具复制三个 Cookie 值给你。
- 将三个值按 <ph>.<userId>.<serviceToken> 拼接，写入项目根目录的 .env 文件：token=<拼接结果>（单行；.env 已被 .gitignore 排除，不要提交）。
- 安全要求：Token 等同账号会话凭证，不要在日志或对话中完整输出。
- 完成标准：.env 存在且 token= 行格式为三段式（两个英文句点分隔）。

【阶段 3：构建与启动】
- 执行 docker compose up -d --build，等待约 5 秒后执行 docker compose ps 确认容器状态为 Up。

【阶段 4：验证】
- GET http://127.0.0.1:<宿主机端口>/v1/models：返回的 data 列表应包含 mimo-v2.6-pro。
- POST http://127.0.0.1:<宿主机端口>/v1/chat/completions，请求体：{"model":"mimo-v2.6-pro","stream":false,"messages":[{"role":"user","content":"只回答ok"}]}
- 成功标准：HTTP 200 且 choices[0].message.content 非空。
- 排障：若 content 为空字符串或容器日志出现 401 → token 过期或错误，回到阶段 2 重新抓取；若连接被拒绝 → 检查端口映射与容器状态；若后端报「模型名称错误」→ 确认请求的 model ID 与 /v1/models 列表一致。

【阶段 5：收尾报告】
- 向用户报告：服务地址、/v1/models 返回的模型列表、测试对话的返回内容、以及日后 token 过期时的更新方法（重复阶段 2 后重启容器）。
- 附加：如用户需要 MCP 集成，提示 MCP 端点为 http://127.0.0.1:<宿主机端口>/mcp，需在 MCP 客户端配置 Authorization: Bearer <token>。
````

> [!TIP]
> 阶段 2 依赖**用户已登录小米账号的浏览器会话**。没有浏览器控制能力的 Agent 会在此处向你索要三个 Cookie 值——照着上文「新手教程」复制即可。

---

## ⚖️ 免责声明 (Disclaimer)

使用本项目前，请务必阅读并理解以下条款：

1. **非官方项目**：本项目与小米公司及其 MiMo 产品**无任何关联**。"MiMo" 相关名称与商标归小米所有。本项目仅通过逆向官方 Web 端接口实现，属于技术学习与研究用途。
2. **仅供学习研究**：本项目（含全部源码）仅供个人学习、技术研究与学术交流，**严禁用于商业用途**或任何违反法律法规的场景。
3. **服务稳定性不保证**：官方接口随时可能变更或加固，导致本项目部分或全部功能失效；作者与贡献者不承诺任何可用性与准确性。
4. **风险自担**：使用本项目（包括抓取与使用 Token）产生的任何后果——包括但不限于账号被限制、风控、封禁——由使用者**自行承担**。
5. **合规义务**：使用本项目时请遵守小米 MiMo 官方用户协议及您所在地的法律法规；如官方提出要求，本项目将停止维护。
6. **许可**：本项目代码以 [GPL-3.0](./LICENSE) 协议开源；本项目不附带任何明示或默示的担保。

---

## 🙏 致谢 (Acknowledgements)

本项目基于 [Fu-Jie/mimo-free-api-mcp](https://github.com/Fu-Jie/mimo-free-api-mcp) 构建，感谢原作者的核心逆向工程与实现。本仓库在其基础上适配 MiMo V2.6 系列、修复 V2.6 Flash 模型路由，并补充 CI 质检等社区维护。
