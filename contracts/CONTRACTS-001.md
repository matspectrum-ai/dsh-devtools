# CONTRACTS-001 — Boundary Contracts

Status: architectural contract draft. Types are conceptual; implementation syntax is intentionally deferred until RED tests exist.

## Identity hierarchy

```text
WorkspaceId
  -> RepositoryId
      -> WorktreeId?
          -> SessionId?
          -> PullRequestRef?
          -> TerminalSessionId*
```

Every mutation must be resolved from host-authoritative identity. Paths are data, not authority.

## Repository service

Read operations:

- resolve repository for Workspace/Worktree
- status
- diff
- branches
- log
- remotes

Mutation operations:

- stage paths
- unstage paths
- discard paths (explicit confirmation)
- commit
- checkout/switch branch
- create/delete branch
- fetch/pull/push

Every mutation returns a fresh repository revision/snapshot token. The client must not optimistically invent durable state.

## Worktree service

Must preserve the stricter semantics demonstrated by the audited worktree engine:

- isolated target has distinct canonical checkout identity;
- ownership is tied to host-resolved Session/Workspace identity;
- review/finalize/discard uses revision/CAS validation;
- dirty or ambiguous cleanup fails closed;
- destructive finalize/discard is user-gated, not model-autonomous.

## GitHub provider

Provider boundary is separate from local Git:

```text
GitHostingProvider
  authenticate/status
  repository metadata
  pullRequests.list/get/create
  pullRequests.checks
  pullRequests.reviews
  pullRequests.comments
```

Local Git MUST continue functioning when the provider is absent or authentication expires.

## Terminal service

The user-facing terminal contract requires:

- spawn(cwd, dimensions, shell/profile)
- rawWrite(bytes/text)
- resize(cols, rows)
- subscribeOutput(sequence cursor)
- signal/kill
- status
- close

The existing DSH `ctx.terminals` seam does not currently satisfy rawWrite+resize, so the MVP may use Better Sidebar's plugin-owned PTY implementation behind this contract.

## Client workbench

The DSH DevTools client registers its views with `ctx.betterSidebar` when available. Registration/disposal is lifecycle-owned and HMR-safe. The integration layer MUST NOT claim DSH's single-owner `sidebar`, `conversation`, or `details` slots just to add a DevTools page.
