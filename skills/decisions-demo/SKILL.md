---
name: decisions-demo
description: A small demo of the stagentic-promptbook interpreter — helps the user pick between a few options when they can't decide. Trigger on "Stagentic, help me pick", "Stagentic, decide for me", or "Stagentic, pick one".
---

# Decisions Demo

A tiny decision-helper skill that demonstrates the [interpreter](../interpreter/SKILL.md) end-to-end. Pure conversation: no files touched, no shell, no network.

1. Load the `stagentic-promptbook:interpreter` skill.
2. Follow the workflow in [decisions-demo.puml](decisions-demo.puml).

You are the Session Agent in this skill. Follow the workflow precisely. Don't make decisions for the user beyond what the workflow and direction files instruct.

## What this demo covers

- `|Session Agent|` and `|User|` swimlanes
- `**Ask**`, `**Input**`, `**Cue**`, `**Inform**` keywords
- A decision (`if/else/endif`) for an edge case
- A `while` loop to gather multiple answers
- A sub-workflow call (`weigh-options.puml`)
- Two cues into a single direction file via `#anchor` links (`prompts.md#cue-questions`, `prompts.md#cue-weigh`)
- A `:return;` from the sub-workflow back to the caller

Safe by construction: worst case, the conversation is awkward.
