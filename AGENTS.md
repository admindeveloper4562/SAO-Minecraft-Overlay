# Repository instructions for Codex
Read [AGENT.md](AGENT.md) for the complete implementation runbook before editing code.
Read [PRD.md](PRD.md), [Architecture.md](Architecture.md), [API.md](API.md), [Design_Topography.md](Design_Topography.md), and [Dashboard.md](Dashboard.md).
- Implement local-first Rust/Axum/SQLite + React/TypeScript/Vite + Tauri companion, not a Minecraft mod.
- Start with runnable offline demo; use only official read-only SAO API for live account data.
- Keep credentials backend-only; loopback default; no gameplay automation or unauthorized game observation. Live overlay requires operator permission.
- Maintain source/age/status for snapshots, explicit demo labeling, shared UI tokens and accessible state variants.
- Follow milestone gates, verify meaningful changes, report actual results and blockers. Do not force-push, deploy, or delete user data without authorization.
