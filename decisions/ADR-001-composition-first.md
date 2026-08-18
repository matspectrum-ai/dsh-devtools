# ADR-001 — Composition First, No DSH Core Fork for MVP

Status: Accepted after M0 audit.

## Context

The intended product requires IDE-like Git, worktree, PR, diff and terminal capabilities. DSH rc.7 exposes external Web plugin loading, typed UI slots, host Web routes, Typert Remote business APIs and a durable Workspace registry. The community already contains mature partial implementations.

## Decision

Build DSH DevTools as an external integration/bundle and compose audited community components behind our own contracts.

Preferred initial candidates:

- Better Sidebar for the extensible workbench and interactive terminal/editor/diff surface.
- Git Nexus as a source for SCM and GitHub behavior.
- wloops dsh-git-worktree as a source for the isolated worktree lifecycle and safety state machine.

Do not fork DeepSeek Harness core for the MVP.

## Why

A core fork would increase upgrade cost while solving problems for which extension seams already exist. A fresh monolith would duplicate terminal, diff, SCM and worktree engineering already present in the ecosystem.

The one audited core terminal gap (no raw input/resize contract in `TerminalBackendSession`) does not force a core patch because Better Sidebar already owns an interactive node-pty/xterm transport as a plugin.

## Consequences

Positive:

- lower permanent divergence from DSH upstream;
- faster path to an integrated product;
- clearer replacement boundaries;
- individual community components can be replaced without rewriting the whole suite.

Costs/risks:

- rc.7 compatibility must be established explicitly;
- community plugin APIs may not be designed as stable public dependencies;
- selected code may require extraction/refactoring behind DSH DevTools contracts;
- PT-BR requires additional localization work.

## Revisit trigger

Reconsider a DSH upstream patch only if a required behavior cannot be implemented without overriding private client internals, or if multiple independent plugins need the same missing raw terminal/workspace lifecycle seam.
