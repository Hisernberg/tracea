<div align="center">

# tracea

### Trace anything running — hooks, calls, memory, network — with evidence, through your agent.

**The dynamic half of reverse engineering, built for AI coding agents.**

[![PyPI](https://img.shields.io/badge/PyPI-tracea-blue)](https://pypi.org/project/tracea/)
[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)
[![CI](https://github.com/Hisernberg/tracea/actions/workflows/ci.yml/badge.svg)](../../actions)
[![MCP](https://img.shields.io/badge/protocol-MCP-black)](https://modelcontextprotocol.io)
[![Hugging Face](https://img.shields.io/badge/%F0%9F%A4%97-Space-yellow)](https://huggingface.co/spaces/Nabidnur/tracea-demo)

</div>

> 🚧 **Status: under active development.** v0.1 (network evidence engine) ships this week; **v1.0 launches November 2026.** See the [roadmap](docs/ROADMAP.md). Stars watch the build. 📈

---

Static-analysis MCPs tell your agent what software *looks like*.
**tracea shows your agent what software *does*** — spawn it, hook it, trace it, capture its traffic — and returns every finding as a structured **Evidence Record**, so the agent cites proof instead of guessing.

- **Runtime, not guesses** — API hooks, syscall summaries, memory inspection, HTTP/TLS/DNS reconstruction from live traffic or pcaps
- **Evidence over assertion** — every result carries observation, artifacts, confidence, and explicit limitations
- **Token-efficient by design** — deduplicated summaries + artifact references; never a 10 MB strace dump in your context window
- **Works where you already work** — Claude Code, Codex, Cursor, Gemini CLI via MCP; or standalone CLI

## The one-liner (shipping with v0.1)

```bash
uvx tracea setup
```

Detects your agents, backs up configs, registers the MCP server, installs matching workflow instructions. Restart your agent and ask:

```
Trace what this app actually does at startup, hook its crypto calls,
and show me the evidence.
```

Or use it straight from the terminal:

```bash
tracea net-pcap-summary capture.pcap --json
tracea run-diff run-a run-b        # what changed between two runs?
tracea evidence-export --html      # standalone, shareable report
```

## What you can trace (v1 — 24 tools)

| Domain | Tools | Provider |
|---|---|---|
| **Network & protocols** | `net_pcap_summary` · `net_flow_graph` · `net_http_reconstruct` · `net_tls_inventory` · `net_dns_timeline` · `net_live_capture` | scapy / dpkt / mitmproxy |
| **Linux & macOS processes** | `proc_list` · `proc_trace_syscalls` · `proc_hook_function` · `proc_memory_map` · `proc_memory_scan` · `proc_watch_files` · `proc_stalker` | Frida + /proc |
| **Android** | `android_list_apps` · `android_spawn_app` · `android_hook_java` · `android_hook_native` · `android_trace_api` · `android_dump_ui` · `android_crypto_scan` · `android_capture_traffic` | Frida + adb |
| **Cross-cutting** | `evidence_query` · `evidence_export` · `run_diff` | built-in |

## How tracea works

```
Agent (Claude Code / Codex / Cursor / Gemini CLI)
        │  MCP (stdio)
┌───────▼────────────────────────────────────────┐
│  tracea MCP server (Python, FastMCP)            │
│  • 24 tools   • session manager   • evidence DB │
└───────┬────────────────────────────────────────┘
        │ provider adapters
   ┌────┴─────┬──────────┬───────────┬──────────┐
   ▼          ▼          ▼           ▼          ▼
 Frida      adb        /proc       mitmproxy   dpkt
```

The agent calls a tracea tool → tracea drives the mature instrumentation stack → findings come back as **Evidence Records**, not raw dumps:

```json
{
  "id": "ev_01HZX...",
  "tool": "android_hook_java",
  "provider": "frida",
  "target": "com.example.app",
  "observation": "SharedPreferences.getString('api_key') from AntiTamper.check()",
  "confidence": "observed",
  "artifacts": [{"type": "stacktrace", "ref": "artifacts/0001.txt"}],
  "limitations": ["string value not captured (hook depth 1)"]
}
```

`observed` outranks `inferred`. Every record states what it *couldn't* see. That's the whole philosophy.

## The Hugging Face suite

| Asset | What | Why |
|---|---|---|
| 🤗 [**tracea-demo**](https://huggingface.co/spaces/Nabidnur/tracea-demo) | Live Space: pcap explorer, APK static teaser, evidence viewer | Try it in the browser, zero install |
| 🤗 [**tracea-bench**](https://huggingface.co/datasets/Nabidnur/tracea-bench) | **TraceBench** — annotated runtime traces + hook-script corpus with ground truth | Train/eval dynamic-analysis agents |
| 🤗 [**tracea-explainer**](https://huggingface.co/Nabidnur/tracea-explainer) | Small model fine-tuned on TraceBench: trace → plain-English report | Proof the data works |

## Who is this for

- **Security researchers** — triage, malware lab analysis, pinning checks, protocol documentation
- **Developers** — "why does our release build phone home?" answered with evidence, not vibes
- **Agent builders** — give your agent runtime perception; eval against TraceBench
- **Software preservationists** — understand abandoned binaries by watching them run

## Ethics

tracea is for **authorized analysis only**: your apps, your devices, your captures, your labs. No DRM circumvention, no piracy tooling, no malware development. Evidence exports redact high-entropy secrets by default. Full policy in [docs](docs/ARCHITECTURE.md#ethics).

## Roadmap

`v0.1` network engine → `v0.3` native processes + setup wizard → `v0.5` Android + Space demo → **`v1.0` launch (Nov 2026)**. Details in [docs/ROADMAP.md](docs/ROADMAP.md).

## Credits & ecosystem

- Built with [Frida](https://frida.re), [scapy](https://scapy.net), [FastMCP](https://github.com/jlowin/fastmcp)
- Evidence philosophy inspired by [morluto/rea](https://github.com/morluto/rea) — the static half. tracea is the dynamic half.
- Ideas from the whole agentic-RE community: [ida-pro-mcp](https://github.com/mrexodia/ida-pro-mcp), [GhidraMCP](https://github.com/LaurieWired/GhidraMCP), [hooker](https://github.com/CreditTone/hooker)

## License

MIT © 2026 [Nabid Nur](https://github.com/Hisernberg)
