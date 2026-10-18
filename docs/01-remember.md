# Stage 1: Remember — Core Vocabulary & Glossary

Welcome to **Stage 1** of the Claude Developer Certification Lab. The objective of this stage is to build an unshakeable recall of the fundamental primitives, architectural roles, and message concepts across both **MCP (Model Context Protocol)** and **A2A (Agent2Agent)**.

---

## 📚 Model Context Protocol (MCP) Glossary

| # | Term | Definition & Protocol Mechanism | Exam & Architecture Context |
|---|:---|:---|:---|
| 1 | **Host** | The user-facing AI application or environment (e.g., Claude Desktop, Claude Code, an IDE plugin) that coordinates model reasoning, manages security boundaries, and initiates MCP clients. | In Claude systems, the Host holds the master system prompt and user interaction session; it controls which MCP servers are spawned. |
| 2 | **Client** | The protocol entity that resides within or alongside the Host, opening a 1:1 stateful connection to an MCP server to query capabilities, request tool calls, read resources, and fetch prompts. | A Host can spawn multiple Clients, each mapped to an isolated MCP Server. |
| 3 | **Server** | A lightweight service or process that exposes specific capabilities (tools, resources, prompt templates) to MCP clients via standardized JSON-RPC 2.0 messages. | Servers are boundary layers; they can run locally as child processes (stdio) or remotely (HTTP/SSE). |
| 4 | **Tools** | Model-callable executable functions exposed by an MCP server. They take structured JSON inputs (validated by JSON Schema) and perform side effects or computations, returning content back to the client. | In Claude developer exams, tool design requires explicit naming, detailed descriptions, and rigid input schema definition. |
| 5 | **Resources** | Passive, read-only data sources exposed by an MCP server, identified by URI (e.g., `file:///logs/app.log`, `postgres://db/users`). They provide contextual data without executing side effects. | Unlike tools, resources can be subscribed to (`resources/subscribe`) so clients receive notifications when data changes. |
| 6 | **Prompts** | Pre-engineered prompt templates and slash-command-like workflows exposed by an MCP server to standardize user or agent tasks with parameter substitution. | Useful for surfacing domain-specific prompts directly into host autocomplete menus or system directives. |
| 7 | **Sampling** | A protocol feature allowing an MCP **server** to request an LLM completion back from the **client/host**. This enables "human-in-the-loop" or nested sub-model evaluation while keeping LLM API keys inside the host. | Preserves security: servers don't need their own Anthropic API keys; they delegate inference back to the host with host consent. |
| 8 | **Roots** | Top-level boundaries (directory paths or URIs) defined by the client that inform servers where they are authorized to operate within the filesystem or workspace. | Prevents directory traversal attacks; servers must respect root boundaries declared during initialization. |
| 9 | **Elicitation** | The formal protocol pattern where an entity pauses execution and explicitly requests clarification, missing parameters, or human authorization from the host/user before proceeding. | Critical for security-sensitive tools (e.g., executing shell commands, deleting tables, sending payments). |
| 10 | **stdio Transport** | Standard Input/Output communication channel (`stdin`/`stdout`) used when the host launches the MCP server directly as a local child process. Messages are newline-delimited JSON-RPC 2.0. | Default for local tools in Claude Desktop & Claude Code. High security, zero network surface, process lifecycle tied to client. |
| 11 | **Streamable HTTP (SSE)** | A remote network transport using standard HTTP POST requests for client-to-server messages and Server-Sent Events (SSE) for server-to-client streaming push messages. | Used for remote microservices, shared enterprise MCP servers, and cloud-hosted data sources. Requires authentication (tokens/OAuth). |
| 12 | **OAuth** | An industry-standard token-based authorization framework (RFC 6749) used to authenticate MCP clients connecting to remote HTTP/SSE servers without exposing user passwords. | Essential for remote enterprise deployments and cloud MCP gateways; handles scopes and access delegation. |

---

## 🤖 Agent2Agent Protocol (A2A) Glossary

| # | Term | Definition & Protocol Mechanism | Exam & Architecture Context |
|---|:---|:---|:---|
| 13 | **Agent Card (`AgentCard`)** | A standardized, machine-readable JSON metadata document (hosted at `/.well-known/agent.json`) declaring an agent's identity, description, endpoints, capabilities, skills, and authentication requirements. | The A2A equivalent of an OpenAPI specification or business card; enables automated agent discovery. |
| 14 | **Task (`Task`)** | A stateful, durable unit of work managed by an A2A agent. Tasks represent long-running operations that survive connection interruptions and track execution lifecycle. | Tasks transition through explicit states: `submitted` -> `working` -> `input-required` -> `completed` / `failed` / `canceled`. |
| 15 | **Message (`Message`)** | A structured communication unit sent between agents or within a task lifecycle, carrying conversation turns, instructions, queries, or execution feedback. | Can be exchanged directly (`message/send`) or inside stateful task sessions. |
| 16 | **Part (`Part`)** | A granular component of a Message or Artifact. Each part contains a defined MIME type (e.g., `text/plain`, `application/json`, `image/png`, code blocks). | Enables rich, multi-modal exchanges between agents without monolithic text blobs. |
| 17 | **Artifact (`Artifact`)** | A durable deliverable or output produced by an agent during task execution (e.g., code files, reports, diagrams, compiled binaries). | Belongs to a task; can be streamed incrementally using chunk updates (`append: true`). |
| 18 | **Streaming** | Real-time transmission of incremental task updates, thoughts, or artifact chunks using Server-Sent Events (SSE) via `message/stream`. | Allows a caller agent or human user to observe agent progress live rather than waiting for multi-minute batch completion. |
| 19 | **Push Notification** | An asynchronous webhook or webhook callback mechanism allowing an A2A agent to alert a calling agent when an offline or long-running task changes state. | Eliminates tight polling loops over HTTP; critical for distributed multi-agent systems. |
| 20 | **Skill** | A discrete capability, specialized toolset, or domain expertise declared by an agent in its Agent Card (e.g., SQL generation, code refactoring, image analysis). | Calling agents inspect declared skills to route tasks to the best specialist agent. |

---

## ⚡ Shared Architecture & Security Primitives

| # | Term | Definition & Protocol Mechanism | Exam & Architecture Context |
|---|:---|:---|:---|
| 21 | **JSON-RPC 2.0** | The stateless, lightweight remote procedure call specification used by both MCP and A2A over stdio and HTTP, defining `jsonrpc`, `method`, `params`, `id`, `result`, and `error`. | Ensures protocol interoperability; errors follow standard error codes (`-32700`, `-32600`, `-32601`, etc.). |
| 22 | **Capability Negotiation** | The initial handshake phase (`initialize`) where client and server exchange supported protocol versions and feature sets (e.g., tools, prompts, resources, experimental flags). | Neither party assumes feature support until confirmed during the handshake. |
| 23 | **Dynamic Subscription** | A pub/sub mechanism allowing clients to register interest in a resource URI (`resources/subscribe`) and receive `notifications/resources/updated` whenever the underlying state changes. | Avoids polling; vital for file watchers, telemetry streams, and database state monitors. |
| 24 | **Tool Call / Result** | The request-response cycle (`tools/call`) where the host passes evaluated arguments and the server returns content blocks or isError flags. | In Claude, tool results are returned as `tool_result` content blocks inside the message thread. |
| 25 | **Task Lifecycle States** | The formalized state machine of an A2A task: `submitted` (queued), `working` (active), `input-required` (waiting for human/peer), `completed` (success), `failed` (error), `canceled`. | Provides predictable orchestration state across asynchronous microservices. |
| 26 | **JWS / JCS Signatures** | JSON Web Signatures applied using JSON Canonicalization Scheme to cryptographically sign Agent Cards. | Prevents man-in-the-middle tampering and verifies publisher authenticity in decentralized agent registries. |
| 27 | **Confused Deputy** | A security vulnerability where an AI agent with high permissions executes an untrusted instruction or tool on behalf of a lower-privilege caller. | A primary threat model tested in AI certification; mitigated by least-privilege scoping and human-in-the-loop authorization. |
| 28 | **Context Window Hygiene** | The discipline of curating what tool schemas, resource outputs, and agent messages enter the LLM context window to prevent attention degradation and token budget exhaustion. | Core exam theme: use Resources and targeted tool pagination rather than dumping massive raw schemas into context. |

---

## 🗺️ MCP vs A2A: Quick Mental Model

```
+--------------------------------------------------------------------------+
|  HUMAN USER / HOST APPLICATION (Claude Desktop / Claude Code)            |
+--------------------------------------------------------------------------+
       |                                                    |
       | (MCP stdio/HTTP)                                   | (A2A JSON-RPC/SSE)
       v                                                    v
+------------------------+                        +------------------------+
|   MCP SERVER           |                        |   PEER AGENT (A2A)     |
| - Passive context      |                        | - Autonomous entity    |
| - Tools (functions)    |                        | - Publishes AgentCard  |
| - Resources (data)     |                        | - Executes stateful    |
| - Prompts (templates)  |                        |   Tasks & Artifacts    |
+------------------------+                        +------------------------+
```

- **MCP**: An agent connecting downward to **tools and data** (client-to-server).
- **A2A**: An agent connecting horizontally to **other autonomous agents** (peer-to-peer / orchestrator-to-specialist).
