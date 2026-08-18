# M1 — DSH rc.7 Compatibility Screening

## Status

Static screening completed. Runtime/build smoke is **BLOCKED IN THIS EXECUTION ENVIRONMENT**, not passed.

Environment observed:

- Node.js: v22.16.0
- npm: 10.9.2
- Git: 2.47.3
- pnpm: unavailable
- direct network/DNS from execution container to github.com: unavailable

Because source cloning/package installation cannot be performed here, no candidate receives a runtime PASS.

## DSH rc.6 → rc.7 source delta screening

Pinned release commits:

- rc.6 merge: `fb82698709c39f1860b0ab0ed147e1fa30c1d5d0`
- rc.7 merge: `99f6f02fecdb7dff40c3fbc9470f5907c29f74ca`

Relevant findings from the upstream compare:

- `packages/client/ui-slots`: version bump observed; no source change found in the compare result.
- `packages/api/gateway`: version bump observed; no source change found in the compare result.
- `packages/api/remotes/src`: no source change found.
- `packages/terminal/terminal/src`: no source change found.
- `packages/workspace/workspace/src`: no source change found.
- `packages/client/ui-conversation/src`: source changed in the composer/InputBar for Safari-specific textarea behavior. No contract break was demonstrated by static inspection.

Absence from the compare output is a static signal only; it is not a runtime compatibility proof.

## Candidate classification

| Candidate | Package compatibility signal | Static classification | Runtime gate |
|---|---|---|---|
| Better Sidebar 0.13.0 | DSH peers declared with rc.6-centered ranges; uses public client plugin/slots; external-tab API is documented | `STATICALLY_LOW_RISK` | REQUIRED |
| Git Nexus 0.1.1 | Typert protocol explicitly `>=0.1.0-rc.6 <0.2.0`; uses API Remotes | `STATICALLY_LOW_RISK` | REQUIRED |
| wloops dsh-git-worktree 0.3.0 | multiple DSH dependencies pinned exactly to `0.1.0-rc.6`; exports Typert Remote/client contracts | `PORT_REQUIRED_OR_VENDOR_LOCK` | REQUIRED after dependency update |

## Required local/CI compatibility command sequence

This sequence must be executed in a networked disposable environment before implementation begins:

```text
1. install/pin DSH 0.1.0-rc.7
2. install candidate plugin
3. run candidate typecheck
4. run candidate test suite
5. run candidate build/publish check
6. compose plugin into DSH web profile
7. run profile config validation
8. start dsh web against disposable Git fixture
9. execute candidate-specific browser/host smoke
10. classify PASS / PORT_REQUIRED / REJECT
```

Do not downgrade the project baseline to rc.6 merely to make a dependency pass. Candidate code is adapted to the pinned DSH baseline, not vice versa.
