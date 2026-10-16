# TRACEA — Master Plan
### Trace anything running. The dynamic half of reverse engineering, built for agents.

**Prepared:** 2026-10-16 · **Owner:** GitHub `Hisernberg` / HF `Nabidnur` · **Status:** v1.0 — repos created, build starting
**Primary repo:** `github.com/Hisernberg/tracea` · **HF suite:** `tracea-demo` (Space) + `tracea-bench` (Dataset) + `tracea-explainer` (Model)

---

## 0. TL;DR

**TRACEA** is a Python MCP server + CLI that gives coding agents (Claude Code, Codex, Cursor, Gemini CLI) **dynamic analysis superpowers**: spawn/attach to a running process or Android app, hook APIs, trace syscalls, capture network traffic, inspect memory — and return every finding as a structured **Evidence Record** with confidence and limitations, in the same philosophy that made `morluto/rea` (283.9k★) explode.

- **Why this wins:** REA owns *static* analysis. The *dynamic/runtime* niche has **zero dominant player** (biggest existing `frida-mcp`: **18★**). The gap is verified, quantified, and perfectly rides REA's own virality ("the missing dynamic half of REA" is a one-sentence pitch anyone on Hacker News already understands).
- **Stack:** Python 3.10+, distributed as `uvx tracea setup` (PyPI), MCP over stdio, Frida + adb + scapy + mitmproxy as providers.
- **HF flywheel:** a working demo Space (likes), an annotated trace dataset **TraceBench** (downloads), and a small explainer model (stars) — all cross-linked back to GitHub.
- **Timeline:** 4 weeks, agent-driven (you steer, coding agents write ~80%). Launch window: **Nov 10–12, 2026** (Tue–Thu HN sweet spot).

---

## 1. What the 2026 Top-Star Data Actually Says

Scan method: GitHub Search API, repos created ≥ 2026-01-01 sorted by stars (retrieved 2026-10-16). Top of the table:

| # | Repo | Stars | What it is |
|---|------|-------|-----------|
| 1 | mattpocock/skills | 293.6k | "Skills for Real Engineers" — agent skill packs |
| 2 | **morluto/rea** | 284.0k | Reverse engineer anything with agents (MCP) |
| 3 | affaan-m/ECC | 281.0k | Agent harness: skills, instincts, memory |
| 4 | deepseek-ai/deepseek-harness | 251.0k | "Everything is a Plugin" agent harness |
| 5 | multica-ai/andrej-karpathy-skills | 220.3k | CLAUDE.md derived from Karpathy's takes |
| 6 | ultraworkers/claw-code | 194.9k | Agent-managed project in Rust |
| 7 | DietrichGebert/ponytail | 164.9k | "Makes your agent think like the laziest senior dev" |
| 8 | garrytan/gstack | 136.6k | Garry Tan's exact Claude Code setup |
| 9 | Graphify-Labs/graphify | 127.1k | Codebase → queryable knowledge graph |
| 10 | VoltAgent/awesome-design-md | 121.7k | DESIGN.md files for agents |
| … | JuliusBrussee/caveman | 113.0k | Cuts agent token use 65% |
| … | addyosmani/agent-skills | 107.2k | Production-grade agent skills |
| … | Panniantong/Agent-Reach | 98.0k | "Give your agent eyes on the entire internet" |
| … | stablyai/orca | 94.1k | ADE for fleets of parallel agents |
| … | rtk-ai/rtk | 83.6k | Token-cutting CLI proxy (60–90%) |

### The 7 extracted patterns (what every winner does)

1. **Give agents a new superpower, not users a new app.** REA (RE powers), Agent-Reach (internet eyes), graphify (memory). The buyer is the agent, the voter is the developer.
2. **One-sentence pitch with a body metaphor.** "Eyes to see the internet." "The missing dynamic half of REA." If it can't be said in one line, it won't trend.
3. **Skill/MCP/plugin packaging = zero-friction adoption.** The mega-repos are *additive* to tools people already run. Nothing new to learn → instant star conversion.
4. **One-liner install is non-negotiable.** `npx rea-agents setup` → ours: `uvx tracea setup`. Every extra setup step cuts conversion massively.
5. **Personality & authority sell.** Karpathy's takes, Garry Tan's setup, "laziest senior dev." TRACEA's personality: *"Trust nothing the agent asserts. Demand evidence."* — a memorable, quotable ethos.
6. **Token/data efficiency is a currency.** caveman/rtk/headroom win by shrinking context. TRACEA inherits this: evidence records are compact, structured, and de-duplicated (never dump 10MB of strace into context).
7. **Fast release cadence feeds trending algorithms.** REA shipped 8 releases in 9 days; each release re-triggers trending. Plan for 2 releases/week minimum.

**Implication for TRACEA:** we are not "a Frida wrapper." We are the **evidence-grade runtime perception layer for coding agents** — the thing an agent needs when it must *prove* what software actually does, not guess from screenshots.

---

## 2. Competitive Map — Reverse Engineering for Agents (verified 2026-10-16)

| Niche | Best incumbent | Stars | Status |
|---|---|---|---|
| All-in-one RE suite (MCP) | morluto/rea | 283,899 | Dominated — partner, not rival |
| IDA Pro bridge | mrexodia/ida-pro-mcp | 12,941 | Crowded, commercial tool required |
| Ghidra bridges | LaurieWired/GhidraMCP · bethington/ghidra-mcp | 11,305 · 5,676 | Crowded, static-only |
| Android device farm | firerpa/lamda | 8,611 | Adjacent (device control ≠ analysis) |
| Android hooking CLI (human) | CreditTone/hooker | 5,327 | Human-first, no MCP, no evidence system |
| **Dynamic/runtime MCP for agents** | Gindhar2112/frida-mcp · Nihility-Protoss/frida-mcp | **18 · 18** | 🔥 **WIDE OPEN** |
| **Protocol/network RE MCP** | — | **0** | 🔥 **EMPTY** |
| RE platform (multi-tool) | LING71671/open-reverselab | 1,265 | Early, platform-heavy |
| Game modding via agents | rehan-remade/universal-modder | 8,020 | Validates "agents+RE" appetite |

**Read of the map:** static analysis is a knife-fight between REA and 5 bridges. Dynamic analysis for agents has *two* 18-star repos and nothing else. This is the single largest unclaimed territory adjacent to the year's #2 repo.

### Positioning statement

> **TRACEA = the dynamic half of reverse engineering for agents.**
> REA tells your agent what software *looks like*. TRACEA shows your agent what software *does* — live, hooked, traced, captured — with evidence for every claim.

We explicitly complement REA (their README already mentions "runtime behavior" — we make it deep). Community cross-references are free marketing.

---

## 3. Product Spec

### 3.1 Identity

| Item | Value |
|---|---|
| Name | **tracea** (lowercase) |
| Reads as | "trace-anything" |
| Backronym | **T**racing **R**untime **A**ctions, **C**alls & **E**vidence — for **A**gents |
| PyPI | `tracea` — ✅ verified FREE |
| npm | `tracea` — ✅ verified FREE (reserve day one to squat copycats) |
| GitHub | `Hisernberg/tracea` |
| Tagline | *Trace anything running — hooks, calls, memory, network — with evidence, through your agent.* |
| Runner-up names (checked) | `tappa`, `traka` (both fully free) |

**Rejected:** `spoor` (PyPI/npm taken), `spydra` (Spotify owns it on PyPI), `drea` (npm taken + too close to REA → impersonation risk).

### 3.2 v1 Feature Set — 24 tools, 4 domains

**Domain A — Android (Frida + adb)** — the most requested, highest wow-factor
1. `android_list_apps` — installed packages, versions, permissions
2. `android_spawn_app` — spawn/attach Frida session, return session handle
3. `android_hook_java` — hook Java methods → evidence records on invoke/return
4. `android_hook_native` — hook native lib functions → args/retval traces
5. `android_trace_api` — bulk API tracing (classes/methods matching regex)
6. `android_dump_ui` — UI hierarchy snapshot with view IDs
7. `android_crypto_scan` — auto-hook crypto/SSL APIs → key usage & pinning detection
8. `android_capture_traffic` — device traffic → pcap for Domain C tools

**Domain B — Linux/macOS native processes (Frida + /proc)**
9. `proc_list` — processes, loaded modules, exposed symbols
10. `proc_trace_syscalls` — syscall stream → summarized evidence (deduped, not raw dumps)
11. `proc_hook_function` — on-the-fly native function hooks
12. `proc_memory_map` — memory regions + strings
13. `proc_memory_scan` — pattern/budget scan across regions
14. `proc_watch_files` — file I/O interception
15. `proc_stalker` — code-coverage trace of a thread (Frida Stalker)

**Domain C — Network & protocol evidence (scapy/dpkt/mitmproxy)**
16. `net_pcap_summary` — pcap → structured protocol inventory with counts
17. `net_flow_graph` — L3/L4 conversation graph (edges = conversations)
18. `net_http_reconstruct` — full HTTP request/response sessions from pcap
19. `net_tls_inventory` — SNI, JA3, cert chains, anomalies
20. `net_dns_timeline` — DNS query timeline + suspicious patterns
21. `net_live_capture` — timed live capture (N seconds) → pcap handoff

**Domain D — Cross-cutting**
22. `evidence_query` — filter/search session evidence (by tool, target, confidence, time)
23. `evidence_export` — export bundle: JSONL / SARIF / standalone HTML report
24. `run_diff` — diff two runs: which behaviors appeared/disappeared (regression + malware triage killer feature)

### 3.3 Evidence Record v1 — the trust moat

REA's Evidence v2 is what separates it from hallucination generators. TRACEA adopts the same philosophy (credited in docs), optimized for runtime:

```json
{
  "id": "ev_01HZX...",
  "tool": "android_hook_java",
  "provider": "frida",
  "target": "com.example.app",
  "observation": "SharedPreferences.getString('api_key') called from AntiTamper.check()",
  "confidence": "observed",
  "artifacts": [{"type": "stacktrace", "ref": "artifacts/0001.txt"}],
  "limitations": ["string value not captured (hook depth 1)"],
  "timestamp": "2026-11-08T14:22:31Z"
}
```

Rules: `observed` > `inferred`; every record carries limitations; raw dumps never enter agent context (summaries + artifact refs only — this is also our token-efficiency story).

### 3.4 Architecture

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
 Frida      adb/scapy  /proc+ptrace  mitmproxy  dpkt
(Android)  (Android)  (Linux/mac)   (live net) (pcap)
        │
   Evidence Store (SQLite + JSONL + HTML report)
```

- `tracea` (PyPI) → `uvx tracea setup` = detect agents, write MCP config (with backups), install workflow instructions — mirroring the proven `npx rea-agents setup` UX.
- CLI mirror: `tracea android-hook-java com.example.app --json` (same engine; agentless users included).
-_deps:_ `fastmcp`, `frida`, `frida-tools`, `adbutils`, `scapy`, `dpkt`, `rich`. Python ≥ 3.10.

### 3.5 Distribution surfaces
PyPI (`pip install tracea` / `uvx tracea`), GitHub Releases, MCP registries: **Smithery, PulseMCP, mcp.so, Glama**, npm name-squat `tracea` (points to PyPI instructions), docs on GitHub Pages (Wk 3).

---

## 4. Hugging Face Asset Suite (the likes/downloads engine)

### 4.1 Space — `Nabidnur/tracea-demo` 🔥 primary likes driver
Gradio app, runs **fully server-side** (no device needed — that's the demo-able subset):
- **Pcap Explorer**: upload pcap → protocol inventory, HTTP reconstruction, TLS/DNS timelines (tools 16–20 live in-browser)
- **APK Static Teaser**: upload APK → permissions, activities, crypto-import map (teaser for Domain A)
- **Evidence Report Viewer**: paste/`tracea evidence_export` JSONL → interactive evidence table
Purpose: every HN/Reddit/X click lands on a *working* artifact. Spaces are HF's highest-like-per-visitor surface.

### 4.2 Dataset — `Nabidnur/tracea-bench` (TraceBench)
**1,000+ annotated runtime traces**: (target, scenario, Frida hook script, syscall/HTTP trace, evidence JSONL, plain-English ground truth) across 30+ open-source Android apps & Linux utilities (OWASP UnCrackable, DIVA, MPAndroid samples, coreutils, curl...). Includes the **hook-script corpus** — genuinely novel; nobody publishes paired trace+groundtruth data. Splits: train/val/test + eval suite `tracea-eval` for agent benchmarks.
### 4.3 Model — `Nabidnur/tracea-explainer`
Small fine-tune (Qwen3-4B-Instruct LoRA — same base you used for ARC) on TraceBench: *trace in → plain-English behavior report out*. Marketing artifact proving the data's value; runs in the Space's "Auto-explain" tab.

### 4.4 Cross-linking flywheel
GitHub README → Space demo · Space → `pip install tracea` · Dataset card → paper-style model card → back to GitHub. Each asset's "Cite/linked assets" links the other two. One launch, four indexed artifacts.

---

## 5. Four-Week Agent-Driven Execution Roadmap

You steer; coding agents (Claude Code/Codex) write ~80%. Each task below has a **copy-paste agent prompt**. Cadence: 2 releases/week (`v0.1` → `v0.8`), each tagged & released to PyPI to feed trending.

### Week 1 (Oct 16–23) — Skeleton + Domain C (network; zero-device, testable in CI)
| # | Task | Agent prompt (paste into your coding agent) |
|---|---|---|
| 1.1 | Core skeleton | "In this repo, implement a FastMCP server in `src/tracea/server.py` registering tool stubs for the 24 tools listed in docs/ROADMAP.md. Implement session management (SQLite evidence store in `src/tracea/evidence.py`) with the Evidence Record v1 schema. All tools return evidence records. Follow the architecture in docs/ARCHITECTURE.md." |
| 1.2 | pcap engine | "Implement `net_pcap_summary`, `net_flow_graph`, `net_http_reconstruct`, `net_tls_inventory`, `net_dns_timeline` using dpkt+scapy in `src/tracea/tools/network.py`. Input: pcap path. Output: evidence records with artifacts. Include unit tests against the pcaps in tests/fixtures/ (generate 5 small synthetic pcaps with scapy: HTTP, TLS, DNS, mixed, malformed)." |
| 1.3 | CLI + PyPI | "Implement `src/tracea/cli.py` (typer): `tracea net-pcap-summary file.pcap --json`. Make `pyproject.toml` publishable with entry points `tracea` and `tracea-mcp`. Add GitHub Actions: lint (ruff), test (pytest), build (python -m build), publish on tag." |
| 1.4 | Release v0.1 | Tag `v0.1.0`, push to PyPI. Post 1st build-in-public X thread. |

### Week 2 (Oct 23–30) — Domain B (native) + setup wizard
| # | Task | Agent prompt |
|---|---|---|
| 2.1 | Native tools | "Implement `proc_list`, `proc_trace_syscalls`, `proc_hook_function`, `proc_memory_map`, `proc_memory_scan`, `proc_watch_files`, `proc_stalker` with Frida in `src/tracea/tools/native.py`, plus a /proc fallback layer that works without Frida. Test on `/bin/cat`, `/usr/bin/curl`, and a sample C program in tests/targets/. Summarize syscalls (dedupe by (name,count,args-hash)) — never raw dumps." |
| 2.2 | Setup wizard | "Implement `tracea setup` (mirroring npx rea-agents setup): detect installed agents (Claude Code, Codex, Cursor, Gemini CLI), back up existing MCP configs, register tracea MCP server + write workflow instructions into AGENTS.md/CLAUDE.md sections. Must be idempotent." |
| 2.3 | run_diff + evidence_query/export | "Implement `run_diff` (set-diff of evidence records between two session IDs, with a unified narrative summary) and `evidence_export` (JSONL/SARIF/standalone HTML report via jinja2 template)." |
| 2.4 | Release v0.3 | Tag + PyPI + changelog. |

### Week 3 (Oct 30–Nov 6) — Domain A (Android) + HF Space + TraceBench v0.1
| # | Task | Agent prompt |
|---|---|---|
| 3.1 | Android tools | "Implement the 8 Android tools in `src/tracea/tools/android.py` using adbutils + Frida gadget/USB. Include `android_crypto_scan` with a rules file (crypto hooks: javax.crypto, Conscrypt, OkHttp CertificatePinner) and `android_capture_traffic` writing pcaps for Domain C tools. Test against OWASP UnCrackable-Level1 APK on an emulator." |
| 3.2 | HF Space | "Build the Gradio Space app in hf-space/: Pcap Explorer (upload → summary/graphs/HTTP view), APK Static Teaser (permissions/activities/crypto imports via androguard), Evidence Viewer (JSONL upload → table). Deploy." |
| 3.3 | TraceBench v0.1 | "Generate 200 annotated traces: run tools 16–20 against fixtures + 20 OSS Android apps; write scenario YAMLs + ground-truth narratives; publish dataset to HF with a full dataset card." |
| 3.4 | Docs site | "MkDocs Material in docs/ with quickstart, per-tool reference auto-generated from docstrings, evidence philosophy page, ethics page. Deploy to GitHub Pages." |
| 3.5 | Release v0.5 | Beta tag. Invite 5–10 trusted testers (Discord/DMs). |

### Week 4 (Nov 6–13) — Polish, model, LAUNCH
| # | Task | Agent prompt |
|---|---|---|
| 4.1 | README trailer | "Record asciinema/GIF set: (1) `tracea net-pcap-summary`, (2) agent hooking an app end-to-end, (3) run_diff narrative. Embed in README top." |
| 4.2 | Explainer model | "LoRA fine-tune Qwen3-4B-Instruct on TraceBench train split (trace → narrative). Publish model card linking dataset + repo. Wire into Space 'Auto-explain' tab." |
| 4.3 | Hardening | "Fuzz every tool with malformed inputs; ensure zero unhandled exceptions leak into MCP; add `--dry-run` everywhere; security review of setup wizard (no config clobbering)." |
| 4.4 | Launch assets | Draft Show HN post, 4 Reddit posts, X thread, directory listings (§6). |
| 4.5 | **RELEASE v1.0 + LAUNCH Nov 10–12** | HN Tue/Wed 07:00–09:00 ET → Reddit +30 min → X thread → directories same day. |

---

## 6. Launch Playbook

### 6.1 Pre-launch checklist (gate)
- [ ] `uvx tracea setup` works on clean macOS + Ubuntu VMs
- [ ] README has GIF trailer + working quickstart (nothing broken on day one)
- [ ] Demo Space loads < 30s and works without auth
- [ ] TraceBench v0.1 published (200+ traces), model card live
- [ ] npm `tracea` name reserved, PyPI project links wired (repo/issues URL)
- [ ] Discord/Community tab enabled; CONTRIBUTING + SECURITY + ethics section live

### 6.2 Show HN (draft — Tue/Wed, 07:00–09:00 ET)
> **Title:** `Show HN: Tracea – agents can now prove what software actually does at runtime`
> **Body:** We built TRACEA, an MCP server that gives coding agents dynamic analysis: spawn/attach processes and Android apps, hook APIs, trace syscalls, capture network traffic — every finding returns as an Evidence Record (observation + artifacts + limitations), so the agent cites proof instead of guessing. It complements the static-analysis wave (REA, GhidraMCP): REA shows what code looks like, TRACEA shows what it does. Python, `uvx tracea setup`, works with Claude Code/Codex/Cursor/Gemini CLI. Demo Space with pcap explorer: [link]. Repo: [link]. We also released TraceBench (1k+ annotated traces) + a small explainer model. Ethics: authorized testing only, built-in.

### 6.3 Reddit sweep (staggered +30–90 min after HN)
| Sub | Angle |
|---|---|
| r/ClaudeAI, r/ChatGPTCoding | "I gave Claude Code Frida superpowers — it hooks APIs and shows evidence" + GIF |
| r/LocalLLaMA | Open-source MCP + local explainer model angle, TraceBench for evals |
| r/reverseengineering | Pure-technical: syscall dedup, crypto-scan rules, run_diff for malware triage; no hype |
| r/androiddev | Legit uses: debugging release builds, verifying SDK behavior, pinning issues |

### 6.4 X/Twitter thread (5 tweets)
1. Hook: "Your agent guesses what an app does. TRACEA makes it *prove* it. 🧵"
2. 20-sec GIF: hook → evidence table → agent narrative
3. Evidence Record explainer (why hallucination dies here)
4. "The missing dynamic half of REA" positioning + ecosystem thanks
5. `uvx tracea setup` + links (repo/Space/dataset)

### 6.5 Directories & lists (same day)
Smithery · PulseMCP · mcp.so · Glama · awesome-mcp-servers (PR) · **morluto/awesome-reverse-engineering (PR — 595★, the REA author's own list; a "Dynamic Analysis / MCP" section entry is the single highest-leverage link)** · PyPI trending takes care of the rest.

### 6.6 Cadence after launch
2 releases/week for 6 weeks; reply-to-every-comment for 48h; weekly showcase thread ("what did your agent trace this week?"); feature Friday: one public trace teardown post (OSS apps only).

---

## 7. Metrics & Iteration Loop

| Metric | Source | W1 baseline | 30-day target |
|---|---|---|---|
| GitHub stars | repo insights | 0 | 500+ (viral gate: ≥100/day on launch) |
| PyPI downloads | pypistats | 0 | 1,500+ |
| Space likes/visits | HF API | 0 | 150 / 3,000 |
| Dataset downloads | HF API | 0 | 400 |
| Setup completions | telemetry opt-in | 0 | 500 |
| MCP registry inbound | Smithery/Glama dashboards | 0 | top-20 in category |

Weekly loop: metrics review → top user complaint → ship fix in next release → post the fix (build-in-public content = free marketing).

---

## 8. Ethics, Safety & Legal Guardrails (non-negotiable)

1. **Authorized targets only** — README + `tracea setup` + first-run banner require explicit acknowledgment; tool refuses `--i-own-this-target=false` flag absence in corporate mode.
2. No DRM circumvention, no piracy tooling, no malware development; malware-triage use cases supported only in "offline lab" mode.
3. Docs adopt REA's framing: analysis, interoperability, preservation, security research.
4. Privacy: traces may contain secrets — `evidence_export` redacts high-entropy strings by default.
5. Frida/adb/etc. are MIT/Apache-licensed; we orchestrate, we don't fork.

## 9. Risks & Mitigations

| Risk | Likelihood | Mitigation |
|---|---|---|
| REA ships deep runtime support | Medium | Speed + specialize (Android depth, protocol RE, run_diff); ally via their awesome-list |
| Frida on-device friction kills conversion | High | Domain C (pcap) needs zero devices — lead demos with it; emulator quickstart |
| HF fine-grained token lacks write scope | Low | Verified at repo-creation time; fallback: new token with write role |
| Launch day silence | Medium | 4-channel same-day playbook + pre-seeded tester feedback quotes |
| Legal heat from a vendor | Low | Ethics guardrails + MIT + takedown-ready stance |

## 10. Execution Log

- **2026-10-16** — Recon: REA/competitive landscape quantified; name `tracea` verified free (PyPI/npm/GitHub); GitHub repo `Hisernberg/tracea` created + scaffold pushed (README, MIT LICENSE, pyproject, roadmap, architecture, CI stub); HF assets created: Space `tracea-demo`, Dataset `tracea-bench`, Model `tracea-explainer` (all with starter cards); npm name reserved as redirect stub where applicable.
- **Next owner action:** run Week-1 agent prompts (§5) in Claude Code/Codex against the new repo.
