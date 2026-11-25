# ◇ CARDINAL
Local-first personal companion for SAO Minecraft. Unofficial and independent.

**Status:** Specification baseline. Application implementation is not yet complete. These documents are the source of truth for Codex.

| Document | Purpose |
|---|---|
| [PRD.md](PRD.md) | Product scope, requirements, milestones |
| [Architecture.md](Architecture.md) | Rust daemon, storage, synchronization, native HUD, security |
| [API.md](API.md) | Official upstream and proposed local contracts |
| [Design_Topography.md](Design_Topography.md) | Aincrad Glass design system, typography, layout, motion |
| [Dashboard.md](Dashboard.md) | React routes, gameplay knowledge, HUD widgets and states |
| [AGENT.md](AGENT.md) | Codex execution runbook |
| [AGENTS.md](AGENTS.md) | Automatically discovered Codex instructions |

Target: React + TypeScript + Vite, Rust + Axum + Tokio, SQLite, Tauri 2, localhost at 127.0.0.1:7410. Optional Ollama. Windows first, Linux browser fallback. No client injection, hidden telemetry, gameplay automation or required cloud. Official API is read-only and snapshot-based. Live overlay use requires confirmation of server operator permission.

Start Codex in the repository root and ask it to implement the milestones in AGENT.md. The planned run commands in Architecture.md are not available until scaffolding is implemented.
