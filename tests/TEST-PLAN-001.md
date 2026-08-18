# TEST-PLAN-001 — RED First

No implementation is authorized until the compatibility tests below and the contract tests are written and observed failing for missing DSH DevTools behavior.

## Gate A — rc.7 compatibility

RED/verification targets:

1. Better Sidebar installs and client bundle mounts under DSH rc.7.
2. Better Sidebar `ctx.betterSidebar.registerTab` registration and disposer work under rc.7.
3. Better Sidebar interactive terminal opens, accepts raw keyboard input and resizes under rc.7.
4. Git Nexus installs under rc.7 and its Typert Remote endpoints resolve.
5. Git Nexus status/stage/unstage/diff/commit operate against a disposable repository fixture.
6. wloops worktree plugin builds/installs under rc.7 or fails with a precisely classified incompatibility.
7. Worktree create/list/review/discard leaves the Local checkout unchanged when delivery is not finalized.

Gate result must be one of: PASS, PORT_REQUIRED, REJECT.

## Gate B — repository contract RED tests

- staged/unstaged/untracked classification is deterministic.
- rename/delete/binary paths are represented without ambiguity.
- destructive discard requires an explicit confirmation token/intention.
- external mutation invalidates stale repository revision.
- operations outside the resolved repository boundary are rejected.
- local Git works with no GitHub credentials.

## Gate C — worktree RED tests

- same branch cannot be silently owned by two worktrees.
- dirty cleanup fails closed.
- stale review revision cannot finalize.
- caller cannot manage a checkout from another canonical project.
- browser-provided managedRoot is never accepted as authority.
- failed cleanup remains observable/retryable and is never reported as success.

## Gate D — terminal RED tests

- terminal cwd is exactly the selected Workspace/Worktree boundary.
- terminal A cannot receive input/output from terminal B.
- resize changes the PTY dimensions.
- reconnect resumes from a bounded sequence/scrollback point.
- process-tree close does not claim success before quiescence.
- missing node-pty degrades to an explicit unavailable state, not a host crash.

## Gate E — GitHub RED tests

- expired/no auth is an explicit provider state.
- non-GitHub remote does not render a false GitHub association.
- PR identity is repository+number, never title/branch text alone.
- checks/reviews/comments are bound to the requested PR.
- PR write operations require explicit user intent.

## Gate F — integration RED tests

- UI and agent-facing Git tools report the same repository revision.
- switching Session/Workspace cannot leak Source Control state.
- switching Worktree changes terminal cwd and SCM source atomically from the user's perspective.
- disabling DSH DevTools unregisters all Better Sidebar contributions.
