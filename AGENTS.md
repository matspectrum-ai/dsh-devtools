# AGENTS.md — DSH DevTools

This repository is specification-driven and test-first.

## Canonical baseline

- Target: DeepSeek Harness `0.1.0-rc.7`
- Audited commit: `99f6f02fecdb7dff40c3fbc9470f5907c29f74ca`
- MVP strategy: external plugins / composition first; no DSH core fork unless an audited required seam is insufficient.

## Required workflow

For any behavior-changing work, follow this order:

1. Problem analysis.
2. Specification update.
3. Boundary/contracts update.
4. RED tests demonstrating the missing behavior.
5. Minimal implementation to make tests GREEN.
6. Refactor without changing behavior.
7. Verification and technical explanation.

Do not write production implementation before the relevant specification, contracts and fail-first tests exist.

## Current implementation gate

Production implementation is NOT authorized until Gate A in `tests/TEST-PLAN-001.md` is completed against DSH rc.7 and each primary candidate is classified as `PASS`, `PORT_REQUIRED`, or `REJECT`.

Primary candidates:

- `omdsh-dev/DSH-better-sidebar`
- `JFWaskin/dsh-git-nexus`
- `wloops/dsh-git-worktree`

Do not downgrade the DSH project baseline to rc.6 to satisfy a dependency. Port the dependency forward or reject it.

## Engineering invariants

- UI must not execute arbitrary shell/Git operations directly.
- Host-authoritative Workspace/Repository/Worktree identity is required for mutations.
- Browser-provided paths, branch names, worktree ownership or PR identity are data, not authority.
- Destructive Git/worktree actions must be explicit and fail closed.
- Local Git must continue to work when GitHub auth/provider functionality is unavailable.
- No process-global cwd or mutable process environment may serve as workspace state.
- Diff and terminal payloads must be bounded.
- Plugin lifecycle must be HMR/uninstall safe and leave no stale client registrations.

## Source of truth

Prefer repository documents over conversation history. If a proposed change conflicts with an accepted ADR/spec/contract, update the governing artifact explicitly before implementation.
