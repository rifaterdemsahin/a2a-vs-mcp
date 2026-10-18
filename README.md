# a2a-vs-mcp (a2a-mcp-lab) 🧪

> **Mastering MCP (Model Context Protocol) and A2A (Agent2Agent) for Claude Developer Certification**

This laboratory repository guides you step-by-step through six pedagogical learning stages (Bloom's Taxonomy: **Remember**, **Understand**, **Apply**, **Analyse**, **Evaluate**, and **Create**) to master tool-context protocols and agent-to-agent collaboration protocols.

---

## 🌐 Live GitHub Pages & Interactive Dashboard

- **Live GitHub Pages URL**: [https://rifaterdemsahin.github.io/a2a-vs-mcp/](https://rifaterdemsahin.github.io/a2a-vs-mcp/)
- **Local Interactive Dashboard**: `http://localhost:30080` (Run `python3 -m http.server 30080`)
- **Interactive Documentation**: [`index.html`](file:///Users/rifaterdemsahin/projects/a2a-vs-mcp/index.html)

---

## 📋 Protocol Specifications & SDK Versions Used

| Protocol / Component | Specification Version | Reference Implementation |
| :--- | :--- | :--- |
| **Model Context Protocol (MCP)** | Spec Release `2026-07-28` (JSON-RPC 2.0 / SSE / Stdio) | Official Python SDK `v2.3.0` (`mcp>=1.0.0`) |
| **Agent2Agent Protocol (A2A)** | Spec `v1.0` (Canonical Protobuf `specification/a2a.proto`) | Python 3.11+ async implementation |
| **Exam Standard** | Claude Certified Developer – Foundations (`CCDV-F`) | Anthropic Agent SDK & Tool Design |

> [!NOTE]
> **Exam Transparency Notice**: Anthropic's official certification blueprint (`CCDV-F`) covers the Messages API, tool-use loops, MCP server architecture, prompt caching, and Claude Code environments. A2A (Agent2Agent) is an open cross-agent orchestration standard. We explore both to demonstrate how agents connect to context (MCP) versus how agents collaborate with other autonomous agents (A2A).

---

## 🗺️ Learning Roadmap (6 Stages)

1. [**Stage 1: Remember**](docs/01-remember.md) — Recall core vocabulary across MCP and A2A. Interactive glossary, flashcards, and quiz.
2. **Stage 2: Understand** — Explain how pieces relate: architecture flows, comparison matrix, and colleague briefing.
3. **Stage 3: Apply** — Build minimal working MCP server and A2A agent with streaming and Claude Code connection.
4. **Stage 4: Analyse** — Inspect raw wire messages, compare stdio vs streamable HTTP, threat modeling, and failure modes.
5. **Stage 5: Evaluate** — 1-5 quality rubric, automated schema validation & injection tests, and CI/CD pipelines.
6. **Stage 6: Create** — Capstone architecture combining coordinator agent, A2A delegation, and MCP tool reach with Docker.

Track overall progress in [`docs/progress.md`](docs/progress.md).

---

## 🚀 Quickstart

```bash
# 1. Clone repository
git clone https://github.com/rifaterdemsahin/a2a-vs-mcp.git
cd a2a-vs-mcp

# 2. Setup environment
cp .env.example .env

# 3. Serve the interactive learning dashboard on port 30080
python3 -m http.server 30080
```
Open in Google Chrome:
```bash
open -a "Google Chrome" http://localhost:30080
```
