# 1. Characterise Promptbook Behaviour with TDAB Scenarios

Date: 2026-05-12
Status: Accepted

## Context

`stagentic-promptbook` is intended to be the foundation for skills shipped by the broader Stagentic family — most immediately the open-source TDAB runner (working title: `stagentic-flow` or `stagentic-play`). For that to work safely, the promptbook interpreter and its conventions need to support the new use cases without regression in existing behaviour.

To extend the interpreter with confidence, we need to know how it currently behaves — particularly the parts that are sensitive to agent interpretation (keyword semantics, multi-line activity text, scope of file reads, etc.). The behaviour today is what users already rely on, even where it is not fully deterministic.

The TDAB runner that drives such scenarios is itself a private prototype. The public, open-source equivalent (`stagentic-flow` / `stagentic-play`) is still in development. External contributors cannot yet run these scenarios themselves.

## Decision

Write Test-Driven Agentic Behaviours (TDAB) scenarios that **characterise** the current behaviour of `stagentic-promptbook` in its prototype form. As it is used in earnest, those scenarios act as regression tests. New capabilities will be added test-first.

Scenarios live under `promptbook-spec/behaviours/`.

Because the runner is private for now, transcripts of passing test-runs will be included under `promptbook-spec/behaviours/transcripts/`. Each folder is one full captured run, named by timestamp.

When the open-source TDAB runner ships, it will be packaged as a plugin that can replay these scenarios. Whether to keep, regenerate, or drop the transcripts will be revisited then.

## Implementation

- Created `promptbook-spec/` with `behaviours/` for scenarios and `behaviours/transcripts/` for evidence.
- Added a README in `promptbook-spec/` explaining status, layout, and where to find test results.

## Consequences

### Benefits

- Future interpreter changes can be evaluated against captured behaviour.
- Readers without the private runner can still see what passing looks like via the committed transcripts.
- The scenarios are human-readable, executable specifications of promptbook's behaviour.

### Drawbacks

- Transcripts can drift from current behaviour if not refreshed when relevant changes land.
- Characterisation accepts current behaviour, including any rough edges (e.g. non-deterministic whitespace handling), and so codifies it for now.
- The folder convention is provisional and may shift once `stagentic-flow` ships.

### Metrics

N/A

## Alternatives Considered

1. **Wait for `stagentic-flow` to ship before writing tests** - Rejected: changes to the interpreter made in the interim would have no safety net.
2. **Keep scenarios in the private TDAB runner repo** - Rejected: the behaviour being characterised belongs to this plugin, so the spec should live alongside the code.
3. **Enforce deterministic scorecards** - Rejected: the goal is to characterise current behaviour, not constrain it prematurely. Where behaviour is variable (e.g. newline preservation in multi-line activity strings), the scorecard accepts either rendering.
4. **Omit committed transcripts** - Rejected: without them, readers outside the project have no way to see whether the spec is current or stale.

## Related Decisions

None yet.
