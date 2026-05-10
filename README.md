# Stagentic Promptbook

> **Status: prototype.** This is the experimental version of stagentic-promptbook.
>
> Keyword semantics may change before a stable release. Feedback via Issues is gold, pull-requests are platinum!

A Claude Code plugin that brings [PlantUML activity diagrams](https://plantuml.com/activity-diagram-beta) to skill workflows. Part of the [Stagentic](#the-stagentic-family) family.

**Stagentic** — *stage + agentic* — is a family of tools by [Antony Marcano](#maintained-by) for auditioning agentic skills with automated rehearsals ([read the backstory here](https://open.substack.com/pub/antonymarcano/p/taming-claude-code-one-agentic-test)).

In theatre, a **promptbook** is the stage manager's master copy of a play: the full script alongside every cue (light, sound, scene-change, actor entrance), plus blocking, props lists, and timings. It's the operational source of truth for putting on the show — anyone who can read a promptbook can run the production from it.

`stagentic-promptbook` brings the same idea to Claude Code skills:
- A skill's workflow is expressed as a PlantUML activity diagram infused with a lightweight DSL.
- An interpreter skill allows Claude Code to follow the Promptbook-infused PlantUML the way a stage manager runs a show — calling each cue in turn, branching where the diagram branches.

## What's in the box

Two skills, both namespaced under `/stagentic-promptbook:`.

### `interpreter`

The skill that lets Claude Code follow a PlantUML activity diagram that contains Promptbook keywords, as if it were the body of a skill.

Keyword vocabulary includes (`**Cue**:`, `**Run**:`, `**Await**:`, `**Inform**:`, `**Ask**:`, `**Input**:`, `**Find**:`) and the diagram constructs (swimlanes, decisions, loops, forks, sub-diagram calls, terminators).

For more, [see the interpreter here](skills/interpreter/SKILL.md).

### `decisions-demo`

A small, working example skill that uses the interpreter end-to-end.

See [decisions-demo walkthrough](docs/decisions-demo.md) for more.

## Tested with

| Model | ID | Consistency |
|---|---|---|
| Opus 4.7 | `claude-opus-4-7` | ●●●○ |
| Sonnet 4.6 | `claude-sonnet-4-6` | ●●●● |
| Haiku 4.5 | `claude-haiku-4-5-20251001` | ●○○○ |

## Tokens and speed

This is a v0 prototype — no optimisation has been attempted. Overhead scales with the number of files a Promptbook skill fans out across; more `.puml` and `direction` files will carry a higher cold-run (pre-cached) premium.

Once cached, speed is equivalent to prose — with signs it can be marginally faster.

For more detail, see: [Prose vs Promptbook — preliminary analysis](docs/performance/prose-vs-promptbook-v0-2-1.md)

## Install

In Claude Code, if the Stagentic marketplace isn't yet added, add it first:

```
/plugin marketplace add stagentic/stagentic-cc-marketplace
```
That is a one-time setup. Any plugin from the [Stagentic family](#the-stagentic-family) can then be installed without re-adding the marketplace.

Now, install the plugin:
```
/plugin install stagentic-promptbook@stagentic
```

From here, [write your own Promptbook workflow](#writing-your-own-promptbook-based-skill) or try the demo first.

## Upgrading

To update to a newer version:

1. Open the plugin manager with `/plugin`.
2. Navigate to the **Installed** tab.
3. Select `stagentic-promptbook`.
4. Select **Update now**.
5. Run `/reload-plugins` to apply the update to the current session.

## Bundled skill: `decisions-demo`

A small, working example skill that uses the interpreter end-to-end — helps the user pick between options when they can't decide.

See [decisions-demo walkthrough](docs/decisions-demo.md) for triggers, a feature tour, and the diagram source.

## Writing your own Promptbook-based skill

Minimum layout:

```
your-skill/
├── SKILL.md            # frontmatter + directive to load the interpreter
├── your-skill.puml     # the activity diagram
└── direction/          # optional — direction files cued from the diagram
    └── ...
```

In `SKILL.md`:

```markdown
---
name: your-skill
description: ...
---

# Your Skill

1. Load the `stagentic-promptbook:interpreter` skill.
2. Follow the workflow in [your-skill.puml](your-skill.puml).
```

The body instruction `Load the stagentic-promptbook:interpreter skill` is the runtime hook — it tells Claude Code to load the interpreter before traversing the workflow. Use the fully qualified name so the plugin's interpreter is unambiguously identified, even if other PlantUML interpreters are present.

The diagram is the workflow. Free-text works in activities, but Promptbook's DSL produces more concise, reliable workflows (see the [interpreter skill file](skills/interpreter/SKILL.md) for keywords and what they do).

Promptbook workflows can **Cue** other Promptbook workflows, or prose-based direction files.

Direction files hold any prose that the diagram cues into. See [`skills/decisions-demo/`](skills/decisions-demo/) for a complete working example.

## Roadmap

- Acceptance tests for the interpreter constructs (TDAB-style scenarios), runnable once `stagentic-flow` ships.
- TypeScript utilities for validation, scaffolding, and linting of `.puml`-based skills.
- More example skills demonstrating different construct combinations.

## License

MIT — see [LICENSE](LICENSE).

## Maintained by

**Antony Marcano** has over 30 years in software engineering, 25 of them guiding teams in eXtreme Programming. Over the last decade he's helped companies grow development teams that ship multiple times per day, achieving the ['Elite' DORA software delivery performance](https://dora.dev/quickcheck/?v=2025&leadtime=6&deployfreq=6&failurerecovery=6&rework=1&changefailure=1&industry=all) benchmark. His current work focuses on Test-Driven Agentic Behaviours and the discipline of working with AI coding agents. Available for consulting, training, and speaking engagements.

Find Antony here: **[Substack](https://antonymarcano.substack.com) · [LinkedIn](https://www.linkedin.com/in/antonymarcano/) · [Bluesky](https://bsky.app/profile/antonymarcano.bsky.social) · [Mastodon](https://mastodon.social/@antonymarcano)**

## Support this work

The Stagentic family of free and open-source tools is a labour of love. Two ways to help it grow:

- A paid subscription to [Antony's Substack](https://antonymarcano.substack.com) — supports the writing and thinking that feeds the tools.
- Sponsorship enquiries via [LinkedIn](https://www.linkedin.com/in/antonymarcano/).

## Acknowledgements

> "Some of the inspiration for this work came from my brother, **[Raymond Rodriguez](https://www.linkedin.com/in/raymond-rodriguez-3042a532/)**. In early 2025 he built a programming-language-style DSL for his ChatGPT custom GPTs and showed me how much more reliably they ran when their instructions could be treated as code rather than prose. We talked it over many times after that. I couldn't see how to make this accessible to a wider audience, until one day I was making PlantUML diagrams to explain how my tooling worked. That's when those conversations clicked — leading to stagentic-promptbook."
>
> *— Antony Marcano*

## The Stagentic family

- `stagentic-promptbook` — this plugin.
- `stagentic-flow` — TDAB runner framework (in development).
- `stagentic-tdd` — TDD support skill (in development).
