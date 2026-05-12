# 0. Use Architecture Decision Records

Date: 2026-05-12
Status: Accepted

## Context

`stagentic-promptbook` is one of several plugins in the Stagentic family. Decisions here ripple across sibling projects (e.g. `stagentic-flow` / `stagentic-play`) and into users' Claude Code setups. Choices about the interpreter's keyword semantics, the plugin layout, and how the plugin is exercised in tests carry trade-offs that are not obvious from the code alone.

Without explicit records, the rationale behind such decisions tends to be lost in commit messages, chat history, and code comments — making it difficult to:

- Understand why approaches were chosen
- Evaluate whether to reverse or modify decisions later
- Onboard new contributors (human or AI)
- Avoid revisiting already-settled debates

This matters more here than in many projects because AI agents are active contributors. Agents benefit from explicit, structured context far more than from implicit knowledge buried elsewhere.

## Decision

Use Architecture Decision Records (ADRs) to document significant technical decisions for `stagentic-promptbook`.

ADRs are stored in `docs/architecture/decisions/`, follow a consistent [template](template.md), and are numbered sequentially with a 4-digit prefix (e.g. `0001-title.md`).

Each ADR includes: Context, Decision, Implementation, Consequences (with metrics where applicable), Alternatives Considered, and Related Decisions.

## Implementation

Established the ADR practice by creating a directory structure, a reusable template, and an index for discoverability.

## Consequences

### Benefits

- Decisions and their rationale are visible to all contributors
- Future maintainers understand why things are the way they are
- Settled decisions don't need to be re-litigated
- AI agents can quickly understand architectural choices from structured records
- Lightweight: simple Markdown files, no special tooling required

### Drawbacks

- Writing ADRs takes time
- ADRs need to be updated if decisions are reversed or superseded
- Requires discipline to write ADRs for significant decisions

### Metrics

N/A (process decision)

## Alternatives Considered

1. **No formal documentation** - Rejected: decisions would be lost in commit messages, PRs, or chat history
2. **Wiki or separate documentation site** - Rejected: adds complexity, can drift from codebase
3. **Inline code comments** - Rejected: doesn't capture high-level architectural decisions or trade-offs
4. **Heavyweight design documents** - Rejected: too much overhead, harder to maintain, less discoverable

## Related Decisions

All subsequent ADRs follow this practice.
