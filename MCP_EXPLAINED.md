# MCP, explained properly (and what all the plugin/extension/skill noise actually is)

> **As of 26 Sep 2026.** Current MCP spec: **2026-07-28**. I checked, and it is still the newest one. The docs index lists no later version, and even the draft schema says `LATEST_PROTOCOL_VERSION = "2026-07-28"`. The newest MCP blog post is *"The New MCP Roadmap"* (22 Aug 2026).
>
> **About the sources.** modelcontextprotocol.io was blocked from the machine I worked on. I read the same pages from their source files on GitHub (`modelcontextprotocol/modelcontextprotocol/docs/…`), which is what the site renders.
>
> **Confidence:** Anything stated plainly comes from a primary source (spec text, schema, vendor docs, vendor source code). **"(reported)"** means I only saw it in news or search results, so treat it as likely but unconfirmed.

---

## Contents

0. [TL;DR](#0-tldr)
1. [Your side vs the other side](#1-your-side-vs-the-other-side)
2. [The four layers](#2-the-four-layers)
3. [What the page you linked says](#3-what-the-page-you-linked-actually-says)
4. [MCP in detail (spec 2026-07-28)](#4-mcp-in-detail-spec-2026-07-28)
5. [Every other term, explained](#5-the-rainbow-decoded)
6. [Master comparison table](#6-master-comparison-table)
7. ["I want X, so I use Y"](#7-i-want-x--use-y)
8. [When MCP is the wrong answer](#8-when-mcp-is-the-wrong-answer)
9. [Glossary](#9-glossary)
10. [Sources](#10-sources)

---

## 0. TL;DR

1. **MCP is a wire protocol.** It defines the JSON-RPC messages an AI app sends to a separate program ("server") to discover and call its **tools**, read its **resources**, and fetch its **prompts**. It is plumbing. It is not a product, a file format, or an app store.
2. **Most of the other names you're sick of are a different kind of thing, not competing protocols:**
   - **Plugins / extensions / bundles** are **packaging**: a folder or zip with a manifest. They usually *contain* MCP servers.
   - **Skills, CLAUDE.md, AGENTS.md, GEMINI.md, Cursor rules** are **text**: instructions that get loaded into the model's context.
   - **Hooks, subagents, permissions, tool search** are **harness behaviour**: code inside the agent app itself.
   - **VS Code extensions** are a **separate category**: code that runs inside the editor. They predate all of this.
3. **What sits beside MCP rather than on top of it:**
   - **A2A** is for agent ↔ agent.
   - **ACP** is for editor ↔ coding agent.
   - **LSP** is for editor ↔ language server, and it was MCP's design inspiration.
   - **Function calling** is the model-API format that hosts translate MCP tools *into*.
4. **The big 2026 change: MCP went stateless.**
   - There is no `initialize` handshake and no session ID.
   - Every request carries its own version and capabilities.
   - The server can ask the user for input in the middle of a call via **Multi Round-Trip Requests** (MRTR).
   - Sampling, Roots and Logging are **deprecated**.
5. **The mess is converging.** It isn't finished. The industry is settling on three layers:
   - **MCP** on the wire.
   - **Agent Skills (`SKILL.md`)** for instructions.
   - **Agent Plugins 1.0** for packaging. Codex, Cursor, VS Code/Copilot and ChatGPT read it.
   - Plus **AGENTS.md** as the cross-tool instruction file.

**Quick test for any new buzzword:**

| Question | If yes, it's… |
|---|---|
| Is it bytes going between two running programs? | a **protocol** (MCP, A2A, ACP, LSP) |
| Is it a folder or zip with a manifest that you install? | **packaging** (plugin, extension, `.mcpb`) |
| Is it Markdown the model reads? | **context** (skill, AGENTS.md, rules) |
| Is it code the agent app runs on an event? | **harness** (hook, subagent, permission rule) |
| Does it run inside the IDE process via the IDE's API? | **editor extension** (VS Code `.vsix`) |

---

## 1. Your side vs the other side

### Where you're right
- **The naming really is chaotic.** Some examples:
  - "ChatGPT plugins" means two unrelated things. The 2023 ones were an OpenAPI manifest and are dead. The 2026 ones are MCP + skills bundles (reported: ChatGPT "apps" were renamed "plugins" in July 2026).
  - OpenAI's "connectors" became "apps" and then "plugins".
  - VS Code's `.vscode/mcp.json` uses the key `servers`, while nearly every other tool uses `mcpServers`.
  - Gemini CLI "extensions" are reportedly becoming "Antigravity plugins".
- **Vendors re-brand the same idea.** A "connector" (Claude), an "app" (old ChatGPT) and a "context server" (Zed) are all just an MCP server with a logo on it.
- **MCP itself keeps changing.** There have been five spec versions in about 20 months (2024-11-05 → 2026-07-28). The latest removed the handshake and sessions and deprecated three features (Sampling, Roots, Logging). If you learned MCP in 2025, part of what you learned is now legacy.
- **Even vendors are abandoning parts of it.** OpenAI removed `codex mcp-server` (Codex used to *be* an MCP server). Its replacement, the Codex "app-server", uses its own protocol, not MCP. The removal is confirmed in the source; the release version is reported.

### Where you're wrong (the other side)
- **These aren't "MCP vs plugins vs skills".** They sit at different layers, and each solves a different problem:
  - MCP answers "how does my app talk to GitHub's tool server?"
  - A skill answers "how does the model know *our* release checklist?"
  - A plugin answers "how do I ship both of those to my team in one install?"
  - A hook answers "how do I *guarantee* nobody edits `.env`?"

  None of these replaces another. A typical plugin **contains** an MCP server config *and* skills *and* hooks.
- **Most of the chaos is names, not engineering.** Look inside any vendor's "plugin" or "extension" in 2026 and you find the same three things: an MCP config, a `skills/` folder, and some vendor-specific extras.
- **It's converging, not diverging.** Four standards now sit under neutral homes:
  - MCP, AGENTS.md and A2A are under the Linux Foundation's Agentic AI Foundation.
  - Agent Skills has an open spec.
  - Agent Plugins 1.0 has its own charter.

  Codex's plugin loader literally checks for Agent Plugins first, then Codex's own format, then **Claude's** `.claude-plugin/plugin.json`, then Cursor's.
- **The churn is now bounded.** Since 2026-07-28 there is a formal lifecycle: Active → Deprecated → Removed, with at least 12 months between deprecation and removal. The deprecated features keep working until a revision released on or after 2027-07-28. SDKs speak both old and new versions.
- **The USB-C comparison undersells it.** MCP is closer to "HTTP + OpenAPI + OAuth, specialised for AI tool use" than to a plug shape. See §3.

---

## 2. The four layers

```
┌───────────────────────────────────────────────────────────────────────────┐
│ 4. HARNESS BEHAVIOUR   code inside the agent app, fired by events          │
│    hooks · subagents · permissions · tool search · slash-command dispatch  │
├───────────────────────────────────────────────────────────────────────────┤
│ 3. CONTEXT (TEXT)      Markdown the model reads                            │
│    CLAUDE.md · AGENTS.md · GEMINI.md · Cursor rules · copilot-instructions │
│    SKILL.md (Agent Skills) · output styles · subagent system prompts       │
├───────────────────────────────────────────────────────────────────────────┤
│ 2. PACKAGING           a folder/zip + manifest you install                 │
│    Agent Plugins 1.0 · Claude/Codex/Cursor/Copilot plugins · marketplaces  │
│    Gemini CLI extensions · MCP Bundles (.mcpb, formerly .dxt)              │
├───────────────────────────────────────────────────────────────────────────┤
│ 1. WIRE PROTOCOL       messages between running processes                  │
│    MCP (app ↔ tools/data) · A2A (agent ↔ agent) · ACP (editor ↔ agent)     │
│    LSP (editor ↔ language server) · AG-UI (agent ↔ web frontend)           │
└───────────────────────────────────────────────────────────────────────────┘
   Separate universe: editor extension APIs (VS Code .vsix, JetBrains plugins,
   Zed extensions): code running inside the IDE. These can *ship* MCP servers.
```

- Claude Code's own docs draw this line. MCP is a "Protocol for connecting to external services". Skills are "Knowledge, workflows, and reference material". "Plugins are the packaging layer".
- On hooks vs skills, the docs say: "Claude Code runs a hook at a lifecycle event; it loads a skill into context for Claude to apply."

---

## 3. What the page you linked actually says

This is [*What is the Model Context Protocol (MCP)?*](https://modelcontextprotocol.io/docs/2026-07-28/getting-started/intro), faithfully condensed:

- **Definition:** "MCP (Model Context Protocol) is an open-source standard for connecting AI applications to external systems."
- **What it connects:** AI apps like Claude or ChatGPT can connect to data sources (local files, databases), tools (search engines, calculators) and workflows (specialised prompts).
- **Analogy:** "Think of MCP like a USB-C port for AI applications."
- **Examples it gives:**
  - Agents reading your Google Calendar and Notion.
  - Claude Code generating a web app from a Figma design.
  - Enterprise chatbots querying many databases.
  - AI designing in Blender and sending the result to a 3D printer.
- **Why it matters:**
  - Developers build once and integrate everywhere.
  - AI apps get an ecosystem of tools.
  - End users get assistants that can actually act on their data.
- **Ecosystem it names:** Claude, ChatGPT, VS Code, Cursor, MCPJam, "and many others".
- **Next steps it links:** Build servers, Build clients, **Build MCP Apps** (interactive UIs inside AI clients), Architecture, Security, Contributing.

### Where the USB-C analogy breaks
- **USB-C carries anything.** MCP has typed concepts (tools, resources, prompts) and rules about *who controls* each one (model, app, user).
- **USB-C has no permissions model.** MCP has OAuth 2.1, consent requirements, and a long security best-practices page. Plugging an MCP server in can mean giving an LLM write access to your production database.
- **USB-C devices don't lie to you.** MCP tool descriptions and results go straight into the model's context. A malicious or compromised server can inject instructions. The spec says tool annotations are "untrusted unless the server is trusted".
- **The page doesn't mention it, but MCP deliberately leaves the model side alone.** It "focuses solely on the protocol for context exchange — it does not dictate how AI applications use LLMs or manage the provided context." What your AI *does* with a tool is the host's business.

---

## 4. MCP in detail (spec 2026-07-28)

### 4.1 Who talks to whom

| Role | What it is | Example |
|---|---|---|
| **Host** | The AI application the user sees. It creates clients, enforces consent and security policy, and combines context from all of them. | Claude Code, Claude Desktop, ChatGPT, VS Code, Cursor |
| **Client** | A connection object inside the host. **One client per server** (1:1). It attaches protocol version and capabilities to every request. | The host's "GitHub connection" |
| **Server** | A program that exposes tools, resources and prompts. It can be local (a subprocess) or remote (an HTTPS endpoint). | GitHub MCP server, a filesystem server, Sentry's hosted server |

The docs' example is VS Code connected to Sentry's remote server and a local filesystem server. That setup is **one host with two clients**, and each client talks to exactly one server.

**Design principles** (from the spec):
1. Servers should be extremely easy to build.
2. Servers should be highly composable.
3. Servers "should not be able to read the whole conversation, nor 'see into' other servers." The host is the only one that sees everything.
4. Features can be added progressively.

MCP explicitly "takes some inspiration from the Language Server Protocol". LSP made "one language server works in every editor" normal, and MCP does the same for AI tools.

### 4.2 Two layers inside MCP

- **Data layer:** JSON-RPC 2.0 messages. It covers discovery (`server/discover`), server features (tools, resources, prompts), client features (elicitation), and utilities (progress, cancellation, completion, pagination).
- **Transport layer:** how those messages move.
  - **stdio** is for local subprocesses.
  - **Streamable HTTP** is for remote servers.
  - It also covers framing and authorization. The old HTTP+SSE transport is deprecated.

### 4.3 The primitives: who controls what

| Primitive | Side | Controlled by | Methods | What it's for |
|---|---|---|---|---|
| **Tools** | server | **the model** (it decides to call) | `tools/list`, `tools/call` | Actions: "create issue", "run SQL", "get weather" |
| **Resources** | server | **the application** (the host decides what to attach) | `resources/list`, `resources/read`, `resources/templates/list` | Read-only context: files, DB rows, docs, identified by URI |
| **Prompts** | server | **the user** (e.g. picks a slash command) | `prompts/list`, `prompts/get` | Reusable, parameterised prompt templates |
| **Elicitation** | client | server asks, **user answers** | `elicitation/create` (inside MRTR only) | "I need your GitHub username" (form mode) or "go to this URL to pay or authenticate" (URL mode) |
| Sampling | client | server asks the host's LLM | `sampling/createMessage` | **Deprecated 2026-07-28.** Servers should call LLM APIs directly. |
| Roots | client | host tells the server which folders are in scope | `roots/list` | **Deprecated 2026-07-28.** Pass paths via tool params, resource URIs or config. |
| Logging | server utility | — | `notifications/message` | **Deprecated 2026-07-28.** Use stderr (stdio) or OpenTelemetry. |

**Utilities:**
- `completion/complete`: autocomplete for prompt and resource-template arguments.
- Pagination: an opaque `cursor` / `nextCursor`.
- Progress: `_meta.progressToken` plus `notifications/progress`.
- Cancellation: close the HTTP stream, or send `notifications/cancelled` on stdio.

### 4.4 Complete method inventory (from `schema.ts`)

| Direction | Methods |
|---|---|
| Client → server requests | `server/discover`, `tools/list`, `tools/call`, `resources/list`, `resources/templates/list`, `resources/read`, `prompts/list`, `prompts/get`, `completion/complete`, `subscriptions/listen` |
| Client → server notifications | `notifications/cancelled` |
| Server → client notifications | `notifications/cancelled`, `notifications/progress`, `notifications/message`, `notifications/resources/updated`, `notifications/resources/list_changed`, `notifications/tools/list_changed`, `notifications/prompts/list_changed`, `notifications/subscriptions/acknowledged` |
| Server → client **requests** | **None anymore.** `elicitation/create`, `sampling/createMessage` and `roots/list` now travel only *inside* an `InputRequiredResult` (see MRTR, §4.7). |

**Removed in 2026-07-28:** `initialize`, `notifications/initialized`, `ping`, `logging/setLevel`, `resources/subscribe`, `resources/unsubscribe`, `notifications/roots/list_changed`.

### 4.5 A real exchange, message by message (Streamable HTTP)

**Step 1: discover (optional, but every server must support it).**

```http
POST /mcp HTTP/1.1
Host: mcp.example.com
Content-Type: application/json
Accept: application/json, text/event-stream
MCP-Protocol-Version: 2026-07-28
Mcp-Method: server/discover
```
```json
{"jsonrpc":"2.0","id":1,"method":"server/discover","params":{"_meta":{
  "io.modelcontextprotocol/protocolVersion":"2026-07-28",
  "io.modelcontextprotocol/clientInfo":{"name":"example-client","version":"1.0.0"},
  "io.modelcontextprotocol/clientCapabilities":{"elicitation":{}}}}}
```
```json
{"jsonrpc":"2.0","id":1,"result":{"resultType":"complete",
  "supportedVersions":["2026-07-28"],
  "capabilities":{"tools":{"listChanged":true},"resources":{}},
  "_meta":{"io.modelcontextprotocol/serverInfo":{"name":"example-server","version":"1.0.0"}},
  "ttlMs":3600000,"cacheScope":"public"}}
```

**Step 2: list tools.** Every request repeats the `_meta` block, because there's no session to remember it:

```jsonc
{"jsonrpc":"2.0","id":2,"method":"tools/list","params":{"_meta":{ /* same 3 keys as above */ }}}
```
```json
{"jsonrpc":"2.0","id":2,"result":{"resultType":"complete",
  "tools":[{"name":"weather_current","title":"Current weather",
    "description":"Get current weather for a location",
    "inputSchema":{"type":"object","properties":{"location":{"type":"string"}},"required":["location"]}}],
  "ttlMs":300000,"cacheScope":"public"}}
```

- `ttlMs` / `cacheScope` are new. They let the client, and even a shared gateway, cache the list.
- Servers SHOULD return tools in a deterministic order, so the LLM's prompt cache keeps hitting.

**Step 3: call a tool.** The HTTP headers now include `Mcp-Method: tools/call` and `Mcp-Name: weather_current`, so a load balancer can route on them without parsing the body.

```jsonc
{"jsonrpc":"2.0","id":3,"method":"tools/call","params":{"name":"weather_current",
  "arguments":{"location":"San Francisco"},"_meta":{ /* … */ }}}
```
```json
{"jsonrpc":"2.0","id":3,"result":{"resultType":"complete",
  "content":[{"type":"text","text":"Current weather in San Francisco: 18°C, partly cloudy"}]}}
```

**What a result can contain:**
- `content` items of type `text`, `image`, `audio`, `resource_link` or embedded `resource`.
- `structuredContent`, which is any JSON value matching the tool's `outputSchema`.
- `isError: true` for a *tool* failure, which the model should see so it can self-correct. Protocol failures are JSON-RPC errors instead.

**Step 4: subscribe to changes (optional).**
```jsonc
{"jsonrpc":"2.0","id":4,"method":"subscriptions/listen",
 "params":{"notifications":{"toolsListChanged":true},"_meta":{ /* … */ }}}
```

The server answers on that stream in this order:
1. `notifications/subscriptions/acknowledged`
2. Later, `notifications/tools/list_changed`

Each notification is tagged `_meta["io.modelcontextprotocol/subscriptionId"]: 4`. Notifications are "best effort", so clients should still re-poll.

### 4.6 The big 2026 change: stateless core

| 2025-11-25 and earlier ("legacy era") | 2026-07-28 ("modern era") |
|---|---|
| Client sends `initialize`, server replies, client sends `notifications/initialized` | No handshake. Optional `server/discover`. |
| Server returns an `Mcp-Session-Id`; every later request is pinned to that server instance | No sessions. Any request can hit any instance, so a **plain round-robin load balancer** works. |
| Capabilities negotiated once | Version + capabilities in `_meta` on **every** request |
| A GET endpoint for a server→client stream; `resources/subscribe` | `subscriptions/listen` |
| Server could send requests to the client at any time | Server can only ask for input *inside its reply* (MRTR) |
| SSE resumability via `Last-Event-ID` | Removed. A broken stream means re-issue the request with a new id. |
| Tasks (experimental) in core | Tasks moved to an **extension** |

**"But my tool needs state!"**
- The spec's answer: the server mints a handle and the model passes it back as an ordinary argument.
- Example: `create_basket` returns `basket_id: "bsk_a1b2c3"`, and later `add_item(basket_id=…)`.
- Rules for handles:
  - Make them high-entropy and opaque.
  - Re-check authorization on every call.
  - Document their lifetime.
  - Possession of a handle is **not** authorization. The security guide calls attacks on this "state handle hijacking".

**Version mismatch:** the server returns error `-32022`, and the client retries with a supported version.
```json
{"jsonrpc":"2.0","id":1,"error":{"code":-32022,"message":"Unsupported protocol version",
 "data":{"supported":["2026-07-28","2025-11-25"],"requested":"1900-01-01"}}}
```

**Old and new servers coexisting ("dual-era" implementations):**
- A modern client talking to an old server probes with `server/discover`. On stdio, an unrecognised error or a timeout means "legacy", so it falls back to `initialize`. On HTTP, it inspects the body of the 400 response.
- The official SDK betas say **nothing breaks on July 28**: new clients fall back to the old handshake against old servers.

### 4.7 Multi Round-Trip Requests (MRTR): how a server asks a question now

Old way: in the middle of a tool call, the server sends its own request (`elicitation/create`) to the client over a long-lived stream.

New way: the server **finishes its reply early**, saying "I need more input", and the client **retries the original request** with the answers.

Server reply:
```json
{"jsonrpc":"2.0","id":2,"result":{"resultType":"input_required",
 "inputRequests":{"github_login":{"method":"elicitation/create","params":{"mode":"form",
   "message":"Please provide your GitHub username",
   "requestedSchema":{"type":"object","properties":{"name":{"type":"string"}},"required":["name"]}}}},
 "requestState":"eyJsb2NhdGlvbiI6Ik5ldyBZb3JrIn0..."}}
```

Client retry (note the **new** id):
```json
{"jsonrpc":"2.0","id":3,"method":"tools/call","params":{"name":"get_weather",
 "arguments":{"location":"New York"},
 "inputResponses":{"github_login":{"action":"accept","content":{"name":"octocat"}}},
 "requestState":"eyJsb2NhdGlvbiI6Ik5ldyBZb3JrIn0..."}}
```

**Rules:**
- MRTR is allowed only on `tools/call`, `resources/read` and `prompts/get`.
- The server may only ask for input types the client declared.
- `requestState` travels through the client, so the server **must treat it as attacker-controlled**. If it matters for authorization or business logic, the server must sign or encrypt it (HMAC or AEAD).
- Elicitation answers are `accept`, `decline` or `cancel`.
- **Form mode must never ask for passwords or API keys.** URL mode exists for that: the client shows the domain and asks for consent before opening it.

> Note: the release-candidate blog post shows an older MRTR JSON shape (`"type":"elicitation"`). The final spec uses `{"method":"elicitation/create", …}` as above.

### 4.8 Transports in detail

**stdio (local):**
- The host launches the server as a subprocess.
- Messages are newline-delimited JSON-RPC on stdin/stdout, with no embedded newlines.
- `stderr` is free-form logging.
- The server must never write requests.
- Shutdown: close stdin, then SIGTERM, then SIGKILL.
- If the server crashes, the host restarts it and in-flight requests are lost.
- An open stdio process "is not a conversation or session".

**Streamable HTTP (remote):**
- One endpoint, and every message is a POST.
- The server replies with either plain JSON or a request-scoped SSE stream (notifications first, then the final response).
- Notifications get `202 Accepted`.
- There is no GET endpoint. A GET or DELETE from an old client gets `405`.
- Servers must validate `Origin` (or return 403), and should bind to `127.0.0.1` when running locally.

| Header | When | Purpose |
|---|---|---|
| `MCP-Protocol-Version` | every request | must equal the `_meta` version |
| `Mcp-Method` | every request | routing without parsing JSON |
| `Mcp-Name` | `tools/call`, `resources/read`, `prompts/get` | the tool name or resource URI |
| `Mcp-Param-<X>` | when a tool's `inputSchema` property has `"x-mcp-header":"X"` | e.g. route by `Region` |

| Error | Code | HTTP |
|---|---|---|
| Header doesn't match body | `-32020` HeaderMismatch | 400 |
| Client lacks a required capability | `-32021` MissingRequiredClientCapability | — |
| Unsupported protocol version | `-32022` UnsupportedProtocolVersion | 400 |
| Unknown method | `-32601` | 404 |
| Bad params / resource not found | `-32602` (was `-32002`) | 400 |

### 4.9 Authorization (HTTP only; stdio uses environment credentials)

The MCP server is an OAuth 2.1 **resource server**, and the MCP client is an OAuth **client**. The flow:

1. The client calls the server without a token and gets `401` plus `WWW-Authenticate: Bearer resource_metadata="https://mcp.example.com/.well-known/oauth-protected-resource", scope="files:read"`.
2. The client fetches the **Protected Resource Metadata** (RFC 9728), which names the authorization server.
3. The client fetches the authorization server's metadata (RFC 8414 or OIDC discovery).
4. **Client registration**, in order of preference:
   1. A pre-registered client ID.
   2. **Client ID Metadata Document (CIMD)**. The `client_id` is an HTTPS URL to a JSON document describing the client.
   3. Dynamic Client Registration (RFC 7591), **deprecated as of 2026-07-28**.
   4. Asking the user.
5. Authorization-code flow with **PKCE (mandatory)** and the `resource` parameter (RFC 8707), so the token is bound to *this* server.
6. The client validates `iss` (RFC 9207) to block mix-up attacks. Credentials are keyed per issuer.
7. `Authorization: Bearer …` goes on every request. The server **must** check the audience and **must not** pass the token through to other APIs ("token passthrough" is a named anti-pattern).
8. A `403 insufficient_scope` means step-up: request the union of the old and new scopes.

**Extensions to auth:**
- **OAuth Client Credentials**, for machine-to-machine use.
- **Enterprise-Managed Authorization.** Your company's IdP issues an ID-JAG at SSO, which is exchanged for MCP tokens with no per-server consent screens. It was declared stable 18 Jun 2026, with Anthropic, Microsoft and Okta named as adopters.

### 4.10 Extensions: how MCP grows without bloating the core

- **IDs:** `vendor-prefix/name`, e.g. `io.modelcontextprotocol/ui`.
- **Declaration:** in `capabilities.extensions`. They are disabled by default.
- **Fallback:** if only one side supports an extension, that side falls back to core behaviour or rejects the request.
- **Versioning:** a breaking change means a new ID.

| Official extension | ID / repo | What it does |
|---|---|---|
| **MCP Apps** | `io.modelcontextprotocol/ui`, `ext-apps` | Interactive UI inside the chat (details below). |
| **Tasks** | `io.modelcontextprotocol/tasks`, `ext-tasks` | Long-running work. `tools/call` can return `resultType:"task"` with a `taskId`. The client polls `tasks/get`, answers questions via `tasks/update`, and cancels with `tasks/cancel`. Statuses: working / input_required / completed / failed / cancelled. Contributed by AWS. |
| **Auth** | `ext-auth` | Client credentials; Enterprise-Managed Authorization |
| **Skills over MCP** | `ext-skills` (SEP-2640) | Serve `SKILL.md` workflows and their files *through* MCP resources. This is where the "skills" and "MCP" worlds meet. |

**How MCP Apps works:**
1. A tool declares `_meta.ui.resourceUri: "ui://charts/interactive"`.
2. The host fetches that `ui://` resource. It is HTML with MIME type `text/html;profile=mcp-app`.
3. The host renders it in a **sandboxed iframe**.
4. The UI and host talk JSON-RPC over `postMessage`, using `ui/initialize`, `ui/notifications/tool-result`, `ui/message` and so on.
5. A tool marked `visibility:["app"]` can be called by the UI but is hidden from the model.

**Where MCP Apps came from:**
- It launched 26 Jan 2026 as the first official extension.
- It "unifies the approaches pioneered by MCP-UI and the Apps SDK", meaning OpenAI's Apps SDK was folded into the open standard.
- Hosts listed: Claude, Claude Desktop, VS Code Copilot, Microsoft 365 Copilot, Goose, Postman, MCPJam. ChatGPT is also named.

### 4.11 Version history

| Version | Headline changes |
|---|---|
| 2024-11-05 | First public spec. `initialize` handshake, stdio + HTTP+SSE, tools/resources/prompts, sampling, roots, logging. |
| 2025-03-26 | OAuth 2.1 authorization; **Streamable HTTP** replaces HTTP+SSE; tool annotations (read-only / destructive hints). |
| 2025-06-18 | Structured tool output (`outputSchema`, `structuredContent`); servers become OAuth resource servers (PRM, RFC 8707); **elicitation**; resource links; `MCP-Protocol-Version` header; JSON-RPC batching removed. |
| 2025-11-25 | Icons, URL-mode elicitation, tool calling inside sampling, CIMD, incremental scope consent, experimental Tasks. |
| **2026-07-28** | **Stateless core** (no `initialize`, no sessions, `server/discover`); **MRTR**; `subscriptions/listen`; `resultType`; `Mcp-Method`/`Mcp-Name`/`x-mcp-header`; cache hints; full JSON Schema 2020-12; auth hardening (`iss`, issuer-bound credentials, DCR deprecated); Tasks → extension; formal extensions framework; **Roots, Sampling, Logging deprecated**; 12-month deprecation policy. |

### 4.12 Ecosystem plumbing

- **Governance.**
  - On 9 Dec 2025 Anthropic donated MCP to the **Agentic AI Foundation (AAIF)**, a directed fund under the Linux Foundation.
  - It was co-founded with Block (goose) and OpenAI (AGENTS.md). Google, Microsoft, AWS, Cloudflare and Bloomberg are supporting members.
  - MCP keeps technical autonomy. Lead maintainers are David Soria Parra and Den Delimarsky.
  - Code and specs are licensed Apache-2.0.
- **Changes go through SEPs** (Spec Enhancement Proposals), which are PRs in the spec repo. A standards-track SEP can't be final without a conformance test.
- **SDK tiers.**
  - Tier 1 (100% conformance, ships spec features before release): **TypeScript, Python, Go, C#**.
  - Rust has beta support for 2026-07-28. The **Ruby SDK hit 1.0** on 27 Jul 2026.
  - The release post cites "close to half-a-billion downloads a month" across Tier 1 SDKs.
  - SDK v2 changes: Python's `FastMCP` is renamed `MCPServer`; the TypeScript package splits into `@modelcontextprotocol/server` and `@modelcontextprotocol/client`.
- **MCP Registry** (`registry.modelcontextprotocol.io`, still labelled *preview*):
  - A metadata-only catalogue of `server.json` entries with reverse-DNS names (`io.github.user/server`).
  - It points to npm, PyPI, NuGet, Cargo, Docker/OCI, `.mcpb` packages, or remote URLs.
  - It is meant for aggregators and marketplaces to consume, not for hosts to read directly.
- **MCP Bundles (`.mcpb`):**
  - A zip containing `manifest.json` and a local server, for one-click install.
  - It was Anthropic's `.dxt` "Desktop Extension" format, renamed and moved into the MCP project in Nov 2025.
  - The project itself compares it to Chrome `.crx` and VS Code `.vsix` packages.
- **Roadmap** (22 Aug 2026, the latest post), five priorities:
  1. Agentic messaging: webhooks and channels; bringing Tasks into core.
  2. HTTP-native transport unification, including local servers speaking Streamable HTTP over stdio.
  3. Agent identity and enterprise security: DPoP, workload identity, ID-JAG, token exchange.
  4. Better primitives: one clear tool-result contract; progressive discovery for big tool catalogues.
  5. SDK developer experience.

### 4.13 Security reality check

The official security best-practices page covers:
- **Confused deputy:** proxy servers that use a static client ID plus consent cookies.
- **Token passthrough:** forwarding a client's token to downstream APIs. It is forbidden.
- **SSRF:** including via CIMD metadata fetches.
- **State handle hijacking:** a guessed or stolen handle ≠ authorization.
- **Local server compromise:** a stdio server runs with *your* user permissions.
- **Mix-up attacks, localhost redirect impersonation, scope minimisation.**

Two points the page doesn't emphasise enough:
- **Prompt injection through tool output is a model problem, not something the protocol can fix.** An issue body, a web page or an email returned by a tool can contain "ignore previous instructions…". The host's permission prompts and hooks are your real defence.
- **`npx -y some-mcp-server` runs someone's code with your full user permissions.** Treat MCP servers like any other dependency.

---

## 5. The rainbow, decoded

Each card lists the layer, the format, where it runs, how it relates to MCP, and a tiny example.

### 5.1 Anthropic / Claude

#### MCP servers in Claude Code (protocol, as a client)
- **Config:**

  | Scope | Where it's stored | Shared? |
  |---|---|---|
  | local (default) | `~/.claude.json`, under the project | no |
  | project | `.mcp.json` at the repo root | yes, via git (needs your approval) |
  | user | `~/.claude.json` | no |

- **Add one:**
  ```bash
  claude mcp add --transport http notion https://mcp.notion.com/mcp
  claude mcp add --transport stdio airtable --env AIRTABLE_API_KEY=KEY -- npx -y airtable-mcp-server
  ```
- **Tool names the model sees:** `mcp__<server>__<tool>`, e.g. `mcp__github__search_repositories`.
- **MCP prompts become slash commands:** `/mcp__github__pr_review 456`.
- **Tool search is on by default.** Only tool *names* load at startup, and full schemas are fetched on demand. Anthropic's API docs give the reason: a typical 5-server setup is about 55k tokens of tool definitions, and tool selection degrades past roughly 30–50 tools.
- **`claude mcp serve`** turns Claude Code itself into an MCP server.
- **Claude Code's newer client runtime supports protocol revision 2026-07-28.**

#### Connectors (claude.ai / Desktop): same thing, remote
A connector **is a remote MCP server** (Streamable HTTP + OAuth) that you add under *Customize → Connectors*. Connectors you add in claude.ai also show up in Claude Code when you're logged in.

#### Desktop Extensions: packaging for a local MCP server
A `.mcpb` bundle (formerly `.dxt`) that you install in Claude Desktop. Underneath it's still MCP over stdio.

#### Agent Skills (`SKILL.md`): context, not protocol
- **Format:** a folder with `SKILL.md` (YAML frontmatter + Markdown), plus optional `scripts/`, `references/`, `assets/`.
- **Progressive disclosure** is the whole point:

  | Level | Loaded | Cost |
  |---|---|---|
  | name + description | always, at startup | ~100 tokens per skill |
  | SKILL.md body | when relevant or invoked | < 5k tokens |
  | files and scripts | when read or run | only the *output* of scripts enters context |

- **Open standard** ([agentskills](https://github.com/agentskills/agentskills)). It defines six frontmatter fields: `name` and `description` are required; `license`, `compatibility`, `metadata` and `allowed-tools` are optional.
- **Claude Code adds its own fields** (e.g. `disable-model-invocation`, `context: fork`). **claude.ai rejects those fields on upload.** That's "standard + vendor extras" in miniature.
- **Other vendors that adopted it:** Codex (`.agents/skills`), VS Code/Copilot (`.github/skills`, `.claude/skills`, `.agents/skills`), Cursor, Gemini CLI.
- **Relation to MCP:** none on the wire. A skill can *tell* the model how to use MCP tools well. The Skills-over-MCP extension can *deliver* skills over MCP.

```yaml
---
name: summarize-changes
description: Summarizes uncommitted changes and flags anything risky. Use when the user asks what changed.
---
## Current changes
!`git diff HEAD`
## Instructions
Summarize the changes above…
```

#### Slash commands (now just skills)
Claude Code docs: "Custom commands have been merged into skills." `.claude/commands/deploy.md` and `.claude/skills/deploy/SKILL.md` both give you `/deploy`.

#### Plugins + marketplaces (packaging)
- **What a plugin is:** a folder with `.claude-plugin/plugin.json`.
- **What it can bundle:**
  - `skills/`, `commands/`, `agents/`
  - `hooks/hooks.json`
  - `.mcp.json` (MCP servers, including `.mcpb` bundles)
  - **`.lsp.json`** (language servers)
  - `bin/` executables, themes, output styles, monitors
- **Marketplaces:** a marketplace is just a repo with `.claude-plugin/marketplace.json`, "a catalog, not a hosted store". Install with `/plugin install name@marketplace`.
- **Beyond Claude Code:** plugins also work across claude.ai, Desktop and Cowork.

```json
{ "name": "my-plugin", "mcpServers": "./servers/db.mcpb" }
```

#### Hooks (harness)
- **Where:** `settings.json`, a plugin's `hooks/hooks.json`, or skill/agent frontmatter.
- **Events:** about 30 lifecycle events, including `PreToolUse`, `PostToolUse`, `SessionStart`, `Stop`, `PreCompact`, and MCP `Elicitation`.
- **Handler types:** `command` (shell), `http`, `mcp_tool`, `prompt` (a one-shot LLM decision) or `agent`.
- **Why they matter:** a hook is **deterministic**. The docs put it this way: "never edit .env" in CLAUDE.md "is a request, not a guarantee. A PreToolUse hook that blocks the edit is enforcement."

```json
{"hooks":{"PostToolUse":[{"matcher":"Edit|Write","hooks":[{"type":"command","command":"npx eslint --fix"}]}]}}
```

#### Subagents (harness + context)
- **Format:** `.claude/agents/<name>.md`. The frontmatter holds `name`, `description`, `tools` and `model`. The body is the subagent's system prompt.
- **Runtime:** each subagent runs in **its own context window** and returns a summary.
- **Scoped MCP:** a subagent can have its own `mcpServers` list, visible only to it.

#### CLAUDE.md / AGENTS.md / output styles (context)
- **CLAUDE.md** is loaded every session. It is delivered as a *user message after the system prompt*: context, not configuration.
- Claude Code **also reads `AGENTS.md`** when there's no CLAUDE.md.
- **Output styles** change *how* Claude responds.

#### Claude Agent SDK and the Messages API (developers)
- **Agent SDK:** you define tools with `tool()` / `@tool` and wrap them in `createSdkMcpServer`. It is MCP as an *interface*, running **in-process** with no subprocess.
- **Messages API "MCP connector"** (beta header `mcp-client-2025-11-20`):
  - Anthropic's API itself acts as the MCP client for a **remote** server that you name in `mcp_servers`.
  - Tools only. It can't reach local stdio servers.

```json
"mcp_servers": [{"type":"url","url":"https://mcp.example.com/mcp","name":"example","authorization_token":"TOKEN"}],
"tools": [{"type":"mcp_toolset","mcp_server_name":"example"}]
```

### 5.2 OpenAI

#### Codex: MCP client (protocol)
```toml
# ~/.codex/config.toml
[mcp_servers.context7]
command = "npx"
args = ["-y", "@upstash/context7-mcp"]

[mcp_servers.figma]
url = "https://mcp.figma.com/mcp"
bearer_token_env_var = "FIGMA_TOKEN"
```
- **CLI:** `codex mcp add | list | get | remove | login | logout`.
- **Per-server policy:** `enabled_tools`, `disabled_tools`, timeouts, approval mode.

#### Codex as an MCP server: gone
- `codex mcp-server` is no longer in the CLI source.
- Removal in ~v0.154, Sept 2026 (reported).
- The replacement **app-server speaks its own JSON-RPC protocol, not MCP.** Counterpoint to "MCP is for everything": driving a full coding agent needs a richer protocol, which is also why ACP exists (§5.7).

#### AGENTS.md (context)
- Codex's native instruction file. `AGENTS.override.md` lets you override locally.
- Files are concatenated from `~/.codex` down to the working directory.
- OpenAI contributed the format to AAIF. Claude Code, Copilot, Cursor and Gemini/Antigravity all read it.

#### Codex skills (context)
- Codex reads `SKILL.md` from `.agents/skills`. OpenAI says they "build on the open agent skills standard".
- OpenAI keeps a catalogue at `openai/skills`.

#### Codex plugins (packaging)
- Launched Mar 2026 with 20+ partners (reported).
- **Format** (from OpenAI's own `openai/plugins` repo): `.codex-plugin/plugin.json`, plus optional:
  - `skills/`
  - `.mcp.json`
  - `.app.json` (a pointer to a ChatGPT app/connector)
  - `agents/`, `commands/`, `hooks.json`, `assets/`
- **Marketplace file:** `.agents/plugins/marketplace.json`.
- **Codex's loader checks, in order:**
  1. Agent Plugins 1.0 `plugin.json`
  2. `.codex-plugin/`
  3. **`.claude-plugin/`**
  4. `.cursor-plugin/`

Real Notion plugin, trimmed:
```json
// .mcp.json
{"mcpServers":{"notion":{"type":"http","url":"https://mcp.notion.com/mcp"}}}
```

#### ChatGPT's extension history: same word, different things

| Era | Name | Built on | Status |
|---|---|---|---|
| Mar 2023 | **ChatGPT Plugins** | `ai-plugin.json` + OpenAPI spec | **Dead** (shut down Apr 2024, reported) |
| Nov 2023 | **GPTs + Actions** | OpenAPI | Custom GPTs being retired and migrated to plugins; Enterprise target Dec 2026 (reported) |
| 2025 | **Connectors** → renamed **apps** | MCP | Renamed (reported) |
| Sep 2025 | **Developer mode** | Full MCP client (read + write tools) for remote servers | (reported) |
| Oct 2025 | **Apps SDK / Apps in ChatGPT** | "builds on MCP" + an iframe UI | Folded into the open **MCP Apps** extension (Jan 2026) |
| Jul 2026 | **Plugins** (again) | MCP + skills bundles, one directory shared with Codex | (reported) |

**So when someone says "ChatGPT plugins", ask which year they mean.**

#### Responses API & Agents SDK (developers)
- **Responses API:** has a hosted **remote MCP tool**, so OpenAI's servers act as the MCP client.
  ```json
  {"type":"mcp","server_label":"gitmcp","server_url":"https://gitmcp.io/openai/tiktoken","require_approval":"never"}
  ```
- **Agents SDK (Python):** offers `HostedMCPTool`, `MCPServerStreamableHttp`, `MCPServerSse` and `MCPServerStdio`. With MCP SDK v2 it probes with `server/discover` and falls back to `initialize`.

#### Function calling vs MCP
- **Function calling** is the **model API contract.** Your app sends tool definitions (a JSON Schema) with each request, the model returns arguments, and *your* code runs the call.
- **MCP** is how the app **discovers and calls** tools living in another process.
- Hosts typically turn `tools/list` output into function-calling definitions. **They're complementary layers, not rivals.** Every vendor's function calling (OpenAI, Anthropic, Gemini) sits below MCP in the same way.

### 5.3 Google

#### Gemini CLI extensions (packaging)
- **Manifest:** `gemini-extension.json`, installed to `~/.gemini/extensions`.
- **What it bundles:**
  - `mcpServers`
  - a context file (default `GEMINI.md`)
  - `commands/*.toml`, which become slash commands, e.g. `commands/gcs/sync.toml` is `/gcs:sync`
  - `hooks/hooks.json`
  - `skills/<name>/SKILL.md`
  - `agents/*.md`
  - themes, policies

```json
{"name":"my-extension","version":"1.0.0",
 "mcpServers":{"my-server":{"command":"node","args":["${extensionPath}/my-server.js"]}},
 "contextFileName":"GEMINI.md"}
```

- **Relation to MCP:** it's packaging **around** MCP, the same pattern as everyone else.

#### Antigravity (reported)
- Google announced at I/O 2026 that Gemini CLI is moving into **Antigravity CLI**.
- Extensions become "Antigravity plugins", with an import command, `agy plugin import gemini`.
- None of this is in the Gemini CLI repo docs I read, so verify before relying on it.

#### Gemini API
- The google-genai SDKs can take an MCP session as a tool. It's tools only.
- Managed Agents gained remote MCP in Jul 2026 (reported).

### 5.4 Cursor
- **MCP:** `.cursor/mcp.json` (project) or `~/.cursor/mcp.json` (global), using the `mcpServers` key.
- **Rules (context):** `.cursor/rules/*.mdc`, with frontmatter `description`, `globs`, `alwaysApply`. Cursor also reads AGENTS.md.
- **Plugins (packaging):** `.cursor-plugin/plugin.json`; the marketplace launched Feb 2026 (reported). A plugin bundles rules, skills, agents, commands, hooks and MCP. For example, Cursor's GitHub plugin ships an `mcp.json` pointing at `https://api.githubcopilot.com/mcp/`.

### 5.5 VS Code & GitHub Copilot

#### VS Code extensions (editor extension API, a separate universe)
- **What they are:** TypeScript running in the editor's extension host, declared in `package.json` under `contributes`, shipped as `.vsix`.
- **This predates AI entirely.** It is not MCP.
- **Where it touches AI:** an extension can contribute:
  - in-editor LM tools (`languageModelTools` + `vscode.lm.registerTool`)
  - **MCP servers** (`mcpServerDefinitionProviders`)
  - skills (`chatSkills`)

#### Copilot in VS Code
- **MCP:** `.vscode/mcp.json`, using **`"servers"`** (not `mcpServers`). The portable repo-root `.mcp.json` uses `mcpServers`.
  ```json
  {"servers":{"github":{"type":"http","url":"https://api.githubcopilot.com/mcp"}}}
  ```
- **Instructions:** `.github/copilot-instructions.md`, and `.github/instructions/*.instructions.md` with an `applyTo` glob. It also reads **AGENTS.md** and **CLAUDE.md**.
- **Skills:** `.github/skills/`, `.claude/skills/` or `.agents/skills/`.
- **Agent plugins:** enabled with `chat.plugins.enabled`. VS Code auto-detects Agent Plugins 1.0, Copilot, **Claude** (`.claude-plugin/`) and legacy formats.
  - **Only `skills/` and `mcp.json` are treated as portable.** Everything else is vendor-namespaced.

### 5.6 The cross-vendor standards (the convergence)

| Standard | Layer | What it standardises | Home |
|---|---|---|---|
| **MCP** | protocol | app ↔ tool/data server | AAIF / Linux Foundation |
| **Agent Skills** (`SKILL.md`) | context | folder + frontmatter (`name`, `description`) + progressive disclosure | open spec (originated by Anthropic) |
| **AGENTS.md** | context | "a README for agents": plain Markdown, no schema | AAIF (from OpenAI) |
| **Agent Plugins 1.0** | packaging | "a portable package format for Agent Skills and MCP servers" | its own charter; no vendor may hold a majority of core-maintainer seats |
| **MCP Bundles** (`.mcpb`) | packaging | one local MCP server in a zip | MCP project |

**Agent Plugins 1.0 in detail:**
- 1.0.0 is published and 1.1.0 is a draft.
- The manifest is **closed**. Allowed fields: `$schema`, `name`, `version`, `description`, `author`, `homepage`, `repository`, `license`, `keywords`, `extensions`.
- Components sit at fixed locations: `skills/` and `mcp.json`.
- Anything vendor-specific goes under a reverse-domain namespace.
- Released Aug 2026, initiated by Vercel with AWS, Anysphere (Cursor), GitHub, Microsoft and OpenAI (reported).
- Readers: Codex, Cursor and VS Code/Copilot (confirmed in their code and docs), plus ChatGPT and Kiro (reported).

```json
{"$schema":"https://agent-plugins.org/schemas/1.0.0/plugin.schema.json","name":"hello-plugin"}
```

**The other side of the convergence:**
- Claude Code's docs still describe its own `.claude-plugin/plugin.json` format. I didn't find Anthropic in the reported Agent Plugins launch list.
- In practice other tools read Claude's format anyway (Codex and VS Code do).
- So a bundle of `skills/` + `mcp.json` is portable today, while hooks, agents and commands are still vendor-specific everywhere.

### 5.7 Neighbouring protocols (the ones that really are protocols)

| Protocol | Who ↔ who | Relation to MCP |
|---|---|---|
| **LSP** (Language Server Protocol) | editor ↔ language server | MCP's inspiration. Claude Code plugins can ship LSP servers too (`.lsp.json`). |
| **A2A** (Agent2Agent) | agent ↔ agent, across organisations | Complementary. JSON-RPC 2.0 over HTTPS (plus gRPC/REST bindings); discovery via an **Agent Card** at `/.well-known/agent-card.json`; long-running tasks, streaming, push notifications. Its own docs: "**MCP is vertical.** It deepens a single agent… **A2A is horizontal.** It connects agents across that boundary." v1.0.0 released; joined AAIF Aug 2026 (reported). |
| **ACP** (Agent Client Protocol, from Zed) | editor ↔ coding agent | JSON-RPC over stdio; modelled on LSP; "re-uses the JSON representations used in MCP where possible". Lets Zed or JetBrains host Gemini CLI, Claude Code (via adapter), etc. *Not* IBM's older "Agent Communication Protocol", which merged into A2A. |
| **AG-UI** (CopilotKit) | agent ↔ web frontend | Event stream over HTTP/SSE. Marketed as the third side of a triangle with MCP and A2A (reported). |
| **OpenAPI** | describes REST APIs | Not an agent protocol. It powered the 2023 ChatGPT plugins and GPT Actions. MCP adds discovery, a transport, consent and auth rules on top of the "describe an API" idea. |
| **Function calling** | model API ↔ your code | The layer *below* MCP (see §5.2). |

---

## 6. Master comparison table

| Thing | Layer | Defined in | Runs where | Loaded when | Triggered by | Portable? | Uses MCP? |
|---|---|---|---|---|---|---|---|
| **MCP server** | protocol | `.mcp.json`, `config.toml`, `mcp.json`… | separate process (local) or remote HTTPS | names at start; schemas on demand (with tool search) | model (tools), app (resources), user (prompts) | **yes**: every major host | *is* MCP |
| Claude **connector** | protocol | claude.ai settings | remote | per account | model | yes | *is* MCP |
| **MCPB / Desktop Extension** | packaging | `.mcpb` zip + `manifest.json` | local | on install | user installs | open spec | wraps one MCP server |
| **Agent Skill** | context | `SKILL.md` folder | model context (+ scripts via shell) | description always; body on demand | model or `/name` | **yes**: open standard | no (can be served via ext-skills) |
| **AGENTS.md / CLAUDE.md / GEMINI.md / rules** | context | Markdown in repo/home | model context | every session | always on | AGENTS.md yes; others partly | no |
| **Claude Code plugin** | packaging | `.claude-plugin/plugin.json` | nothing itself | session start if enabled | user installs | Anthropic surfaces; read by Codex & VS Code | usually contains MCP |
| **Codex plugin** | packaging | `.codex-plugin/plugin.json` | nothing itself | — | user installs | Codex + ChatGPT | usually contains MCP |
| **Cursor plugin** | packaging | `.cursor-plugin/plugin.json` | nothing itself | — | user installs | read by Codex | usually contains MCP |
| **Agent Plugins 1.0** | packaging | root `plugin.json` with `$schema` | nothing itself | — | user installs | **yes**: its whole point | `mcp.json` is one of its two components |
| **Gemini CLI extension** | packaging | `gemini-extension.json` | nothing itself | startup | user installs | Gemini/Antigravity only | contains MCP |
| **Hook** | harness | `settings.json` / `hooks.json` | agent app (shell/HTTP/LLM) | on event | harness event | no | can *call* an MCP tool |
| **Subagent** | harness + context | `.claude/agents/*.md` | own context window | when spawned | model or user | no | can have scoped MCP servers |
| **Slash command** | context | now a skill | model context | on `/name` | user | via skills | MCP prompts show up as slash commands |
| **VS Code extension** | editor API | `package.json` + `.vsix` | inside the IDE | IDE start | IDE | VS Code forks only | can *register* MCP servers |
| **2023 ChatGPT plugin** | packaging + OpenAPI | `ai-plugin.json` | OpenAI calls your REST API | — | model | dead | no (predates MCP) |
| **2026 ChatGPT plugin** | packaging | Plugin Directory | remote | — | user installs | Codex + ChatGPT | yes (apps = MCP) |
| **MCP App** | protocol extension | `ui://` resource + `_meta.ui` | sandboxed iframe in the host | when the tool is called | model calls tool; user clicks UI | Claude, ChatGPT, VS Code, M365 Copilot, Goose… | *is* MCP (extension) |
| **A2A agent** | protocol | Agent Card JSON | remote agent | discovery | another agent | yes | no, sibling protocol |
| **ACP agent** | protocol | — | editor subprocess | editor launches | user in editor | Zed, JetBrains, others | no, sibling protocol |
| **Function calling** | model API | JSON Schema in each API request | model provider | every request | model | per provider | MCP tools get *converted* into it |

---

## 7. "I want X → use Y"

| You want to… | Use | Not |
|---|---|---|
| Let the AI read or write a system: GitHub, Postgres, Notion, your internal API | **MCP server** | a skill (skills can't hold credentials or OAuth) |
| Teach the AI *your* procedure: release checklist, code style, "how we write migrations" | **Skill** (or AGENTS.md if it's short and always relevant) | an MCP server (overkill; tool descriptions aren't a manual) |
| Make something **impossible**: block `rm -rf`, force lint after every edit | **Hook** | CLAUDE.md ("a request, not a guarantee") |
| Give a big side-task its own clean context | **Subagent** | stuffing it into the main thread |
| Ship all of the above to your team in one install | **Plugin** (Agent Plugins 1.0 if you want cross-vendor) | a wiki page of setup steps |
| Ship one local MCP server to non-technical users | **`.mcpb` bundle** | "run `npx` in your terminal" |
| Show an interactive chart or form inside the chat | **MCP App** (extension) | a plain text tool result |
| Let *your* agent hand work to *someone else's* agent | **A2A** | MCP (an MCP server is a tool, not a peer) |
| Plug a coding agent into an editor | **ACP** | MCP |
| Call one function from your own backend, with no other host | **Plain function calling** | a full MCP server (see §8) |

---

## 8. When MCP is the wrong answer

**The case against MCP. Take it seriously:**
1. **Context cost.** Every connected tool's name, description and schema competes for the model's attention.
   - Anthropic's own docs: about 55k tokens for 5 typical servers, and selection degrades past roughly 30–50 tools.
   - Tool search / deferred loading helps (by more than 85%, per Anthropic's API docs), but it's a patch on a real problem.
2. **For coding agents with a shell, a CLI + a skill often wins.** The model already knows `gh`, `git`, `psql` and `kubectl`. A skill that says "use `gh pr view --json` like this" costs about 100 tokens until it's needed. A GitHub MCP server can put dozens of tool definitions into context.
3. **Security surface.**
   - Every server is third-party code (stdio) or a third party holding your OAuth token (remote).
   - Tool outputs are untrusted text that goes straight into the model.
4. **Churn.** Five revisions in about 20 months, and the newest rewrote the core model: stateful to stateless, server-initiated requests to MRTR. The 12-month deprecation policy is new and hasn't been tested yet.
5. **It's not the answer for agent-to-agent or editor-to-agent communication.** A2A and ACP exist for those, and OpenAI moved Codex's control surface *off* MCP.
6. **If there's only one app and one API, MCP adds a process and a protocol you don't need.** Plain function calling is simpler.

**The case for MCP, which is why it won anyway:**
1. **Hosts without a shell have no other option.** claude.ai, ChatGPT, M365 Copilot and phone apps can't run `gh`. Remote MCP + OAuth is how they reach your data.
2. **Build once, run everywhere.** One Notion server works in Claude, ChatGPT, Codex, Cursor, VS Code and Gemini. Before MCP, that was N×M custom integrations.
3. **Auth done properly.** PRM discovery, PKCE, audience-bound tokens and enterprise SSO (EMA) are hard to get right in a CLI wrapper.
4. **The 2026 stateless core fixed the ops pain.** It runs behind a round-robin load balancer and on serverless platforms, with cacheable lists and header-based routing.
5. **Neutral governance.** It's under the Linux Foundation now, and OpenAI, Google, Microsoft and AWS all ship it.

**Verdict:**
- Use MCP for **reaching external systems**, especially remote ones with auth.
- Use skills for **knowledge**, hooks for **guarantees**, and plugins for **distribution**.
- If you're a coding agent with a terminal and a good CLI exists, try CLI + skill first.

---

## 9. Glossary

- **A2A:** Agent2Agent protocol; agents talking to other agents.
- **ACP:** Agent Client Protocol (Zed); editors hosting coding agents.
- **AAIF:** Agentic AI Foundation (Linux Foundation). Home of MCP, AGENTS.md, goose and A2A.
- **Agent Plugins 1.0:** cross-vendor plugin manifest for skills + MCP servers.
- **Agent Skills / SKILL.md:** open format for on-demand instruction folders.
- **AGENTS.md:** cross-tool project instruction file.
- **CIMD:** Client ID Metadata Document; the client's `client_id` is a URL to its metadata. Replaces DCR.
- **Client (MCP):** one connection inside a host, 1:1 with a server.
- **Connector:** Claude/OpenAI product name for a remote MCP server.
- **DCR:** Dynamic Client Registration (RFC 7591). Deprecated in MCP 2026-07-28.
- **Elicitation:** a server asking the user for input (a form, or a URL to visit).
- **Extension (MCP):** an optional add-on with its own ID, e.g. MCP Apps or Tasks.
- **Hook:** deterministic code the agent app runs on lifecycle events.
- **Host:** the AI application containing MCP clients.
- **MCP Apps:** extension for interactive HTML UIs (`ui://`) rendered in sandboxed iframes.
- **MCPB:** MCP Bundle (`.mcpb`, formerly `.dxt`); a zip with a local server.
- **MRTR:** Multi Round-Trip Request; server replies `input_required`, client retries with answers.
- **PRM:** Protected Resource Metadata (RFC 9728); how clients find the authorization server.
- **Prompt (MCP):** a user-invoked template from a server.
- **Resource (MCP):** app-controlled, URI-addressed read-only context.
- **Roots / Sampling / Logging:** client/server features deprecated in 2026-07-28.
- **SEP:** Spec Enhancement Proposal; how MCP changes.
- **Server (MCP):** a program exposing tools, resources and prompts.
- **Streamable HTTP:** MCP's remote transport; one endpoint, POST per message, optional SSE response.
- **Subagent:** a separate context window with its own prompt and tools.
- **Tasks:** extension for long-running tool calls (poll `tasks/get`).
- **Tool (MCP):** a model-invoked action with a JSON Schema.
- **Tool search:** loading tool schemas on demand to save context.

---

## 10. Sources

**MCP (read from the GitHub source of modelcontextprotocol.io, `main` branch):**
- Intro page you linked: https://modelcontextprotocol.io/docs/2026-07-28/getting-started/intro (source: `docs/docs/2026-07-28/getting-started/intro.mdx`)
- Architecture: https://modelcontextprotocol.io/docs/2026-07-28/learn/architecture
- Spec index & changelog: https://modelcontextprotocol.io/specification/2026-07-28 · https://modelcontextprotocol.io/specification/2026-07-28/changelog
- Deprecated-features registry: https://modelcontextprotocol.io/specification/2026-07-28/deprecated
- Transports, authorization, MRTR, subscriptions, caching, tools/resources/prompts, elicitation pages under `/specification/2026-07-28/…`
- Schema: https://raw.githubusercontent.com/modelcontextprotocol/modelcontextprotocol/main/schema/2026-07-28/schema.ts
- Security best practices: https://modelcontextprotocol.io/docs/2026-07-28/tutorials/security/security_best_practices
- Extensions overview, MCP Apps, Tasks: https://modelcontextprotocol.io/extensions/overview · https://modelcontextprotocol.io/extensions/apps/overview
- Registry: https://modelcontextprotocol.io/registry/about · Governance & SDK tiers: https://modelcontextprotocol.io/community/governance · https://modelcontextprotocol.io/community/sdk-tiers
- Blog: [2026-07-28 spec](https://blog.modelcontextprotocol.io/posts/2026-07-28/) · [Release candidate](https://blog.modelcontextprotocol.io/posts/2026-07-28-release-candidate/) · [SDK betas](https://blog.modelcontextprotocol.io/posts/sdk-betas-2026-07-28/) · [New roadmap](https://blog.modelcontextprotocol.io/posts/mcp-roadmap/) · [MCP Apps launch](https://blog.modelcontextprotocol.io/posts/2026-01-26-mcp-apps/) · [Joins AAIF](https://blog.modelcontextprotocol.io/posts/2025-12-09-mcp-joins-agentic-ai-foundation/) · [Adopting MCPB](https://blog.modelcontextprotocol.io/posts/2025-11-20-adopting-mcpb/)
- MCPB: https://github.com/modelcontextprotocol/mcpb

**Anthropic / Claude:**
- https://code.claude.com/docs/en/features-overview · /mcp · /skills · /plugins/overview · /plugins/components · /hooks · /sub-agents · /memory · /output-styles · /agent-sdk/custom-tools
- https://claude.com/docs/connectors/overview · https://claude.com/docs/plugins/overview
- https://platform.claude.com/docs/en/agents-and-tools/agent-skills/overview · /mcp-connector · /tool-use/tool-search-tool
- Agent Skills spec: https://github.com/agentskills/agentskills

**OpenAI:**
- Codex source: https://github.com/openai/codex (`codex-rs/core/config.schema.json`, `codex-rs/cli/src/mcp_cmd.rs`, `codex-rs/cli/src/main.rs`)
- Codex plugins: https://github.com/openai/plugins · https://developers.openai.com/codex/mcp · https://developers.openai.com/codex/skills
- Agents SDK MCP: https://github.com/openai/openai-agents-python/blob/main/docs/mcp.md
- Responses API MCP: https://developers.openai.com/api/docs/guides/tools-connectors-mcp
- Reported: [Apps in ChatGPT](https://openai.com/index/introducing-apps-in-chatgpt/) · [Plugins in ChatGPT and Codex](https://help.openai.com/en/articles/20001256-plugins-in-chatgpt-and-codex) · [Custom GPT retirement FAQ](https://help.openai.com/en/articles/20001519-custom-gpt-retirement-and-migration-faq) · [Codex plugins launch](https://siliconangle.com/2026/03/27/openai-introduces-plugins-codex-programming-assistant/) · [`codex mcp-server` removal PR](https://github.com/openai/codex/pull/42993)

**Google, Cursor, Microsoft:**
- Gemini CLI extensions: https://github.com/google-gemini/gemini-cli/blob/main/docs/extensions/index.md · /reference.md
- Reported: [Gemini CLI → Antigravity CLI](https://developers.googleblog.com/an-important-update-transitioning-gemini-cli-to-antigravity-cli/)
- Cursor: https://cursor.com/docs/mcp · https://cursor.com/docs/context/rules · https://github.com/cursor/plugin-template · (reported) https://cursor.com/blog/marketplace
- VS Code: https://github.com/microsoft/vscode-docs (`docs/agent-customization/mcp-servers.md`, `custom-instructions.md`, `agent-plugins.md`, `api/extension-guides/ai/mcp.md`, `tools.md`)

**Cross-vendor & adjacent protocols:**
- Agent Plugins: https://github.com/agentplugins/agent-plugins-spec · (reported) https://vercel.com/blog/introducing-agent-plugins
- AGENTS.md: https://github.com/agentsmd/agents.md
- A2A: https://github.com/a2aproject/A2A (`docs/specification.md`, `docs/topics/a2a-and-mcp.md`) · (reported) [A2A joins AAIF](https://www.axios.com/2026/08/17/a2a-agentic-ai-foundation-open-ai-standards)
- ACP: https://github.com/agentclientprotocol/agent-client-protocol
- AAIF: https://www.linuxfoundation.org/press/linux-foundation-announces-the-formation-of-the-agentic-ai-foundation

### Not independently verified
- ChatGPT "apps → plugins" rename date; custom-GPT retirement dates; the exact release that removed `codex mcp-server`.
- The Antigravity transition details; the Agent Plugins release date and full launch-client list; the date A2A joined AAIF.
- Cursor marketplace dates; whether `.mdc` is still Cursor's only rules format.
- Whether the MCP Registry has left preview (the docs still say preview).
