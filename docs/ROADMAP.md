# tracea — Roadmap

Cadence: **2 releases/week**. Each tagged release publishes to PyPI. Owner: @Hisernberg. Execution model: agent-driven (Claude Code / Codex write ~80% against the task prompts in the master plan).

## v0.1 — Network evidence engine (week of Oct 20)
- [ ] FastMCP server skeleton + session manager + SQLite evidence store (Evidence Record v1 schema)
- [ ] `net_pcap_summary`, `net_flow_graph`, `net_http_reconstruct`, `net_tls_inventory`, `net_dns_timeline` (dpkt + scapy)
- [ ] `tracea` CLI (typer) with `--json` everywhere
- [ ] CI: ruff + pytest + build; synthetic pcap fixtures
- [ ] PyPI publish

## v0.3 — Native processes + setup wizard (week of Oct 27)
- [ ] `proc_list`, `proc_trace_syscalls`, `proc_hook_function`, `proc_memory_map`, `proc_memory_scan`, `proc_watch_files`, `proc_stalker`
- [ ] /proc fallback layer (works without Frida)
- [ ] `tracea setup` — agent detection, config backup, MCP registration (Claude Code, Codex, Cursor, Gemini CLI), idempotent
- [ ] `evidence_query`, `evidence_export` (JSONL/SARIF/HTML), `run_diff`

## v0.5 — Android + HF Space + TraceBench v0.1 (week of Nov 3)
- [ ] 8 Android tools (Frida + adbutils), crypto-scan rules file, traffic capture → pcap
- [ ] Gradio Space: Pcap Explorer + APK static teaser + evidence viewer
- [ ] TraceBench v0.1: 200+ annotated traces with ground-truth narratives
- [ ] MkDocs Material site on GitHub Pages

## v1.0 — Launch (week of Nov 10)
- [ ] Trailer GIFs in README (asciinema)
- [ ] `tracea-explainer` LoRA fine-tune on TraceBench (Qwen3-4B) + model card
- [ ] Hardening: fuzz all tools, `--dry-run` everywhere, setup-wizard security review
- [ ] Launch: Show HN → Reddit sweep → X thread → MCP directories + awesome-list PRs (Nov 10–12)

## Post-launch (6 weeks)
- 2 releases/week, weekly trace-teardown posts, TraceBench growth to 1k+ traces, community hook-script registry

## Guardrails
Authorized analysis only; no DRM circumvention / piracy / malware-dev tooling; secrets redaction on by default; Frida/scapy/adb orchestrated, never forked.
