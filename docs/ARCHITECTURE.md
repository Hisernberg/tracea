# tracea — Architecture

## Overview

tracea is a Python MCP server + CLI that turns mature instrumentation tooling into **evidence-grade runtime perception** for AI coding agents. It is an orchestration layer: the heavy lifting stays with Frida, adb, scapy, dpkt and mitmproxy; tracea adds session management, structured findings, and token-efficient summaries.

```
Agent (Claude Code / Codex / Cursor / Gemini CLI)
        │  MCP (stdio)
┌───────▼────────────────────────────────────────┐
│  tracea MCP server (FastMCP)                    │
│  • 24 tools      • session manager              │
│  • evidence DB   • redaction engine             │
└───────┬────────────────────────────────────────┘
        │ provider adapters
   ┌────┴─────┬──────────┬───────────┬──────────┐
   ▼          ▼          ▼           ▼          ▼
 Frida      adbutils   /proc        mitmproxy   dpkt/scapy
```

## Components

| Component | Module | Responsibility |
|---|---|---|
| MCP server | `src/tracea/server.py` | Registers 24 tools over stdio; thin, stateless handlers |
| Evidence store | `src/tracea/evidence.py` | SQLite + JSONL append-only log; Evidence Record v1 schema |
| Session manager | `src/tracea/sessions.py` | One session per target/run; enables `run_diff` |
| Network tools | `src/tracea/tools/network.py` | pcap parsing, flow graph, HTTP reconstruction, TLS/DNS inventory |
| Native tools | `src/tracea/tools/native.py` | Frida attachment, syscall summarization, memory ops; /proc fallback |
| Android tools | `src/tracea/tools/android.py` | adb device control, Java/native hooks, crypto-scan rules |
| Setup wizard | `src/tracea/setup.py` | Agent detection, config backup, MCP registration, workflow instructions |
| CLI | `src/tracea/cli.py` | typer app mirroring all MCP tools with `--json` |
| Redaction | `src/tracea/redact.py` | High-entropy string + secret-pattern redaction before export |

## Evidence Record v1

```json
{
  "id": "ev_01HZX...",
  "tool": "android_hook_java",
  "provider": "frida",
  "target": "com.example.app",
  "observation": "string, one sentence, evidence-first",
  "confidence": "observed | inferred",
  "artifacts": [{"type": "stacktrace|pcap|screenshot|hexdump", "ref": "artifacts/0001.txt"}],
  "limitations": ["explicit statements of what was NOT captured"],
  "session_id": "ses_...",
  "timestamp": "2026-11-08T14:22:31Z"
}
```

Design rules:
1. `observed` outranks `inferred`; inference must cite the observation it derives from.
2. Raw dumps never enter agent context — records carry summaries + artifact refs (token efficiency).
3. Every record lists limitations. No record, no claim.
4. Evidence is append-only; `run_diff` compares sessions, never mutates them.

## Distribution

- PyPI: `tracea` (extras: `network`, `native`, `android`, `all`)
- One-liner: `uvx tracea setup` registers the MCP server with detected agents
- Registries: Smithery, PulseMCP, mcp.so, Glama
- Companion assets: HF Space `Nabidnur/tracea-demo`, Dataset `Nabidnur/tracea-bench`, Model `Nabidnur/tracea-explainer`

## Ethics

- **Authorized analysis only** — your apps, your devices, your captures, your labs. First-run banner requires acknowledgment.
- No DRM circumvention, no piracy tooling, no malware development. Malware triage supported in offline-lab mode only.
- Privacy: exports redact high-entropy strings and known secret patterns by default.
- tracea orchestrates upstream tools (Frida — MIT/Apache ecosystem); it never forks or redistributes them.
