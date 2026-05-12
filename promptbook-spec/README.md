# Promptbook Spec

> **Status: prototype — not yet runnable publicly.** These scenarios are executed by a private prototype of a Test-Driven Agentic Behaviours (TDAB) runner. The public, open-source equivalent — `stagentic-flow` — is in development (see the [main Roadmap](../README.md#roadmap)). Until it ships, this folder is illustrative for anyone outside the project.

Scenarios under `behaviours/` characterise the expected behaviour of `stagentic-promptbook` skills when traversed by the interpreter. Each scenario follows the TDAB pattern:

- a **test** file linking one or more **When** and **Then** task files
- a **task** file per step containing directives for the subagent that plays the Session Agent
- a **scorecard** that the Then-step subagent uses to grade the When-step transcript

## Why this isn't yet runnable for everyone

The runner that drives these scenarios — wiring up subagents, coordinating cues, capturing transcripts — is a private prototype. The work to make the equivalent capability open-source is happening in [`stagentic-flow`](../README.md#the-stagentic-family).

This is a temporary phase. Once `stagentic-flow` ships, the scenarios here will run unchanged against it, and this README will be updated with run instructions.

## Test results

While the runner is private, the most recent passing transcripts are kept under [`behaviours/transcripts/`](behaviours/transcripts/) as evidence. Each folder is one full test run, named by timestamp and scenario. Inside:

- `1-when-<agentId>.md` — the When-step subagent's transcript (the agent under test)
- `2-then-<agentId>.md` — the Then-step subagent's scorecard verdict
- `agent-ids.txt` — maps step numbers to agent IDs
- `stage-director-*.md` — the coordinator's view

Transcripts are refreshed on demand; the timestamp in the folder name shows how fresh they are.

## Layout

```
promptbook-spec/
└── behaviours/
    ├── <skill-name>/
    │   ├── <scenario>-test.md       # links the When + Then task files
    │   └── tasks/
    │       ├── <when-task>.md       # what the agent under test should do
    │       └── <then-task>.md       # scorecard for grading the When transcript
    └── transcripts/
        └── <timestamp>-<scenario>/  # one folder per captured passing run
```

## See also

- `stagentic-promptbook` itself: [../README.md](../README.md)
- The interpreter these scenarios exercise: [../skills/interpreter/SKILL.md](../skills/interpreter/SKILL.md)
- The demo skill these scenarios cover: [../skills/decisions-demo/SKILL.md](../skills/decisions-demo/SKILL.md)
