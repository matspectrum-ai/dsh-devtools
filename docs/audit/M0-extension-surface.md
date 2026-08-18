# M0 — DSH Extension Surface Audit

## Baseline

This audit is pinned to DeepSeek Harness commit `99f6f02fecdb7dff40c3fbc9470f5907c29f74ca` (`dsh@0.1.0-rc.7`).

## Result

M0 result: **PASS WITH CONSTRAINTS**.

An external plugin suite can implement the planned product without a DSH core fork for the MVP. The two important constraints are:

1. DSH's public `ctx.terminals` contract is agent/operation-oriented, not a full raw xterm contract. It lacks raw byte input and terminal resize in the published backend session interface.
2. Existing community plugins were primarily authored or verified against `0.1.0-rc.6`; compatibility with `rc.7` must be tested before reuse.

## Capability matrix

| Capability | Verified DSH seam at rc.7 | Status | Decision |
|---|---|---:|---|
| External Web plugin loading | `dsh.client`, `ClientModuleRegistry`, `./client` bundle | VERIFIED | Native plugin |
| Additive shell UI | `shell.overlay`, `sidebar.footer.action`; other typed slots available | VERIFIED | Native plugin |
| Right-side contextual UI | `details` slot exists, but is single-owner and replacing it replaces its subtree | VERIFIED / destructive seat | Prefer Better Sidebar or inner/additive surfaces |
| Workspace identity | `ctx.workspaceRegistry` with canonical paths and durable records | VERIFIED | Reuse |
| Host→browser unary business API | Typert Remote: `TypertRemoteService`, `@Remote`, `ctx.remote` | VERIFIED | Canonical transport |
| Custom HTTP/WS routes | `ctx.webServer.register()` / `registerUpgrade()` | VERIFIED | Use only when unary Typert is insufficient |
| Tool edit diff presentation | `DiffCallView` / `DiffResultView` | VERIFIED | Reuse for agent-edit presentation |
| Git SCM service | No canonical core Git service identified | ABSENT IN AUDITED CORE | Plugin-owned service |
| Git worktree manager | No canonical core worktree lifecycle manager identified | ABSENT IN AUDITED CORE | Reuse/port community engine |
| Persistent agent PTY | `ctx.terminals` | VERIFIED | Reuse for model-oriented terminal |
| Raw interactive IDE terminal | Raw input/resize not present in public `TerminalBackendSession` contract | GAP | Plugin-owned PTY for MVP; upstream seam later if justified |
| GitHub PR provider | No canonical GitHub provider seam identified in core | ABSENT IN AUDITED CORE | Plugin-owned provider |

## UI seams

### Client plugin loading

`ClientModuleRegistry` discovers packages with a `dsh.client` declaration and a `./client` export, composes their boot graph and serves their browser bundles. External Web UI is therefore an intended extension mechanism, not a source patch technique.

### Layout

The rc.7 layout declares:

- `sidebar` — single owner, whole left column;
- `conversation` — single owner, whole center column;
- `details` — single owner, right details column;
- `shell.overlay` — list/additive root overlay.

The sidebar additionally exposes `sidebar.footer.action` as an additive list slot.

Directly taking `details`, `sidebar`, or `conversation` replaces the existing owner. DSH DevTools therefore MUST NOT claim those single-owner slots merely to add a tab unless replacement is an explicit product decision.

### Better Sidebar extension surface

`dsh-better-sidebar` exposes the browser-side `ctx.betterSidebar` registry. External plugins can register tabs and file viewers using the same registration path as its built-ins. Its built-ins include Explorer, Git, Terminal, Editor and Diff. This gives DSH DevTools an additive workbench seam without taking over DSH's own single-owner layout slots.

## Host/client transport

For ordinary business operations, DSH DevTools MUST use Typert Remote rather than maintaining a second hand-written RPC table.

Target pattern:

```text
Client tab
  -> ctx.remote.devtoolsGit.*
  -> DSH Connection / API Gateway
  -> Typert Remote service
  -> Git service
```

Use a plugin WebSocket only for a genuinely streaming protocol that cannot be expressed as unary Remote calls, notably raw interactive terminal I/O and unsolicited high-frequency events.

## Workspace semantics

`ctx.workspaceRegistry` is a durable registry of canonical directories and their associated sessions. It is not a general-purpose per-workspace dependency-injection/runtime container.

DSH DevTools therefore owns explicit runtime state keyed by stable identity:

```text
WorkspaceId -> RepositoryId -> WorktreeId -> Runtime resources
```

No runtime state may be inferred from process-global cwd or mutable process environment.

## Terminal finding

The core Terminal service is robust for owner-scoped persistent PTY operations, but its public backend session currently exposes line/text send, paged scrollback, signals, status and close. It does not expose the raw input and resize contract expected by xterm.js.

MVP decision: do not patch core. Reuse the plugin-owned xterm.js/node-pty implementation already proven by Better Sidebar if its rc.7 compatibility gate passes.

Potential later upstream proposal: extend the canonical terminal seam with explicit raw-write, resize and stream subscription contracts, only after our implementation demonstrates a reusable shape.

## Security constraints

- Third-party DSH plugins execute with the user's host permissions; installation is a trust boundary.
- Mutating Git operations MUST be explicit and contract-classified.
- Destructive operations MUST fail closed and require an explicit user action/approval path.
- Browser-provided paths, branch names, worktree ownership or PR identity MUST NOT be treated as authority.
- Remote methods MUST resolve canonical workspace/session identity on the host.
- Binding DSH Web beyond loopback materially changes risk; no assumption of TLS/auth/origin protection may be made from the base webserver.
