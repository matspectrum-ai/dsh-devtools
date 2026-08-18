# DSH DevTools — Engineering Baseline

Status: planning / audit only. No implementation has started.

Audited DeepSeek Harness baseline:

- Repository: `deepseek-ai/deepseek-harness`
- Branch: `master`
- Commit: `99f6f02fecdb7dff40c3fbc9470f5907c29f74ca`
- Release line: `dsh@0.1.0-rc.7`
- Audit date: 2026-08-18

## Objective

Build an IDE-grade developer workflow for DSH with Source Control, Git worktrees, diff/review, GitHub Pull Requests and an interactive terminal, while keeping DSH core unmodified unless an audited extension seam is demonstrably insufficient.

## Current decision

Composition-first. Do not implement a monolithic replacement and do not fork DSH core for the MVP.

Candidate composition:

- `dsh-better-sidebar`: visual workbench, file/editor/diff/interactive terminal shell.
- `dsh-git-nexus`: candidate SCM + GitHub backend/UI source.
- `dsh-git-worktree` by wloops: candidate isolated worktree engine and human-gated delivery state machine.
- `dsh-devtools`: integration/adaptor layer, contracts, additional PR/review/check capabilities, i18n and policy.

Compatibility of the community plugins with DSH `rc.7` is NOT yet proven. That is the next gate.

## Repository map

- `docs/audit/` — audited facts and ecosystem/compatibility findings.
- `specs/` — behavior specifications before implementation.
- `contracts/` — cross-module and host/client boundary contracts.
- `decisions/` — ADRs.
- `tests/` — fail-first verification plans and, later, executable tests.
- `AGENTS.md` — rules for coding agents working in this repository.

## Compatibility gate

Static rc.7 screening is recorded in `docs/audit/M1-rc7-compatibility-screening.md`. Runtime/build verification remains mandatory. Do not downgrade the project baseline to rc.6 just to make a candidate dependency pass.
