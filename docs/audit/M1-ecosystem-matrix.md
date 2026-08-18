# M1 — Ecosystem Candidate Matrix

Status: discovery complete enough to select candidates; rc.7 compatibility testing remains open.

| Candidate | Proven capability from project docs | License | Declared/known DSH line | Preliminary decision |
|---|---|---|---|---|
| `omdsh-dev/DSH-better-sidebar` v0.13.0 | Extensible right/bottom workbench, Explorer, editor, xterm.js+node-pty terminal, Git panel, VS Code-style diff, `ctx.betterSidebar.registerTab` | MIT | peer range centered on rc.6 | PRIMARY UI SHELL; VERIFY rc.7 |
| `JFWaskin/dsh-git-nexus` v0.1.1 | SCM status/diff/stage/unstage/discard/commit, branches, fetch/pull/push/sync, log, GitHub OAuth, PR creation | MIT | Typert protocol `>=rc.6 <0.2.0`; README targets rc.6 | SCM/GITHUB SOURCE; VERIFY rc.7; likely refactor backend from UI |
| `wloops/dsh-git-worktree` v0.3.0 | Real isolated worktrees as Workspaces/Sessions, review lifecycle, Local Preview, rollback/finalize/discard, fail-closed cleanup, strict Typert Remote | MIT | exact rc.6 package dependencies | WORKTREE ENGINE; PORT/VERIFY rc.7 |
| `kaziii/dsh-github-connector` | Device Flow, create/read PR workflow, AI review/merge UI, GitHub model tools | MIT | not yet audited here | SECONDARY GITHUB REFERENCE |
| `BrambleXu/dsh-revdiff` | Interactive Git diff review with structured annotations returned to agent | community listing | not yet audited | REVIEW UX REFERENCE |
| `No-PRM/dsh-explorer` | Git-first file tree, status decorations, HEAD-vs-worktree diff preview | community listing | not yet audited | EXPLORER UX REFERENCE |
| `ccq1/dsh-side-panel` | Git review + terminal + files | BSD-3-Clause | archived | REFERENCE ONLY; upstream recommends Better Sidebar |

## Composition hypothesis

```text
dsh-devtools (integration/bundle)
  |
  +-- dsh-better-sidebar
  |     +-- DevTools tabs
  |     +-- terminal/editor/diff shell
  |
  +-- devtools-git
  |     +-- adapted/extracted Git Nexus logic
  |     +-- Typert Remote
  |
  +-- devtools-worktree
  |     +-- port/adapt wloops state machine
  |     +-- Typert Remote
  |
  +-- devtools-github
        +-- OAuth/gh provider
        +-- PR list/detail/reviews/checks/comments/create
```

The product MUST not couple its UI directly to shell commands. UI talks to typed service contracts; services own process execution and validation.

## Known gaps after ecosystem discovery

- Full PR list/detail/checks/review-thread UX is not yet proven by the selected primary candidates.
- rc.7 compatibility is unverified for all selected third-party code.
- Portuguese (`pt-BR`) UI is not proven by the selected candidates; Better Sidebar documents zh/en.
- A stable cross-plugin Git service contract does not yet exist; DSH DevTools must define one before extracting/reusing implementations.
