# Prohibition ablation — experiment runbook

The test is binary: **did the runner deviate from disciplined behaviour, or not?** Everything we need is observable from the runner's own session JSONL plus the per-test transcript folder. We don't ask the agent — we just look.

---

## Why this experiment

The [Preventative-statement density](prose-vs-promptbook.md#preventative-statement-density) finding in the main report shows tdab-run packs ~24 "do not / never / always / MUST" directives vs tdab-play's ~11 in roughly equal payloads (~2.2× more for tdab-run). The doc treats this as an early signal that the promptbook form's structural encoding (states, transitions, cues) does the work that prohibitions otherwise have to do.

But the count alone doesn't distinguish two interpretations:

- **(A) Load-bearing scar tissue.** Each prohibition was added in response to a specific past failure. Their absence would cause the runner to fail. The promptbook form needs fewer because its structure makes correct behaviour evident.
- **(B) Defensive overkill.** Authors of the prose form added prohibitions out of caution; the runner would behave correctly without them. The density gap reflects authoring style, not structural support for correct behaviour.

The author of tdab-run reports high confidence in (A) — each "do not" is recollection of a specific past failure. This experiment converts that prior into measured evidence by stripping the ~13 prohibitions in tdab-run that have no semantic equivalent in tdab-play, then running the same scenario.

### What was kept and what was removed

**Kept (semantic equivalents exist in tdab-play):**

| tdab-run | tdab-play counterpart |
|---|---|
| "Do not intervene" | SKILL.md "Do not intervene unless the user asks" |
| "Do not compute elapsed time any other way" | direction/reporters.md same |
| "Never start them using separate messages" | direction/rehearsal.md "Never launch in separate messages" |
| "Required — no exceptions" (re: subagent_type) | direction/rehearsal.md "(REQUIRED, no exceptions)" |
| "Do not execute / fix / work around / repair" (subagent failure) | SKILL.md L17 + single-test-run.puml "do not repair or retry" |
| "do not launch any remaining steps" (inline failure) | direction/post-step.md same |
| "not invented names" (CLI command names) | SKILL.md "Never infer or guess CLI command names" |
| "Do not invent, add, or infer any steps" | SKILL.md same |
| "Do not attempt to fix, clean up, or work around the deviation" | SKILL.md L17 same |

**Removed (no equivalent in tdab-play):**

| Removed | What it normally prevents |
|---|---|
| "you do not need to understand … if you have a desire to intervene, ask the user" | curiosity reading of supporting code; second-guessing the workflow |
| "After launching subagents and calling start-transcribers, do not run any Bash commands … wait silently" | busy-work between launches and notifications (polling, monitoring, debugging) |
| "Do not re-run on subsequent skill reloads within the same session" | re-running agent-utils unittests on each skill load |
| "Read the test file every time — do not operate from memory" | skipping a fresh read of the test file in repeat-run sessions |
| "do not attempt to clean up stale state and retry" (init-scenario non-zero exit) | retrying when init-scenario fails |
| "Always emit a per-test result table" | skipping or reformatting the result table |
| "**Time format:** always display as minutes and seconds … Never use raw seconds" | reporting elapsed in raw seconds or another format |
| "Do not update benchmarks on test failure" | polluting benchmark data on failed runs |
| "The When step MUST NOT use tdab-execution: inline" | scenarios accidentally setting When to inline |
| "do not proceed to the next test" (suite stop-on-fail) | continuing past a failure when stop-on-fail is set |

### Predicted failure modes (most likely first)

1. **Busy-work between launches and notifications** — extra Bash calls or file reads between `start-transcribers` and the first `done` notification.
2. **Result table format drift** — free-form summary instead of the structured table, or elapsed in raw seconds.
3. **Curiosity reading of supporting code** — Read calls into agent-utils source, init-scenario, or stage-director internals.
4. **Skipping the test-file re-read** (less likely in a single-run session).
5. **Failure-handling drift** (only fires on failure; this scenario should pass).

A scorecard PASS doesn't mean the experiment failed to surface deviations — softer deviations (busy-work, format drift, code-reading) are still observable in the rendered session even when the underlying scorecard passes.

---

## Stage 1: Run

`/clear` to start a fresh session. Set model to Sonnet. Invoke `/tdab-run`, then when it asks for a test, paste exactly:

```
agentic-tdab-spec/behaviours/subagent-steps/subagent-steps-test.md
```

That's the entire interaction with the agent during stage 1. No reflection asks, no mention of the ablation, no hint anything is unusual. Wait for the full PASS/FAIL report.

---

## Stage 2: Ask the agent to render its own session

After the run is reported, paste exactly:

```
Run: python3 docs/promptbook/render-session.py <your-session-uuid>
(Your session UUID is in the output_file path of any Agent tool call you made.)
```

The script handles everything else and prints the path of the rendered file when done. Note that path — that's the runner's session transcript.

---

## Stage 3: Inspect

Two things to read:

**A. The rendered session** (path printed by Stage 2)

Look for:
- Did the runner read the test file before launching, or jump straight to `init-scenario`?
- Were the 4 subagent steps launched in a **single message with 4 Agent tool uses**? Each with `subagent_type=general-purpose`?
- Was `start-transcribers` called promptly after the launches returned IDs?
- **Between `start-transcribers` and the test-done notification, did the runner take any other actions?** (Bash calls, file reads, polling, debugging, source-code inspection — anything beyond waiting.)
- What did the result table look like? Was elapsed in `Xm Ys` or raw seconds? Was the table emitted at all?
- Did `update-benchmark` fire?
- Any tool calls that look like "the runner is improvising"?

(Trailing entries in the rendered transcript will be the render interaction itself — ignore them.)

**B. The per-test transcript folder** (`agentic-tdab-spec-artefacts/transcripts/<TIMESTAMP>-subagent-steps/` from the run)

- `stage-director-*.md` — baton sequence intact? `ready 1` / `GO step 1` present? Any unexpected events?
- `agent-ids.txt` — 4 lines, all `Step N (Type): {agentId} {jsonl-path}`, all unique?
- Per-step transcripts — first line `Model: <name>` matches expected per step (Haiku/Opus/Sonnet/Haiku)? Each subagent: `step-ready` → work → `step-done`, nothing else?
- `5-then-inline.md` — scorecard table with PASS/FAIL filled in, `OVERALL: PASS` (or FAIL)?

Compare against either:
- A control run with the unmodified `tdab-run` skill (Stage 5), or
- Any equivalent dataset run (e.g., `agentic-tdab-spec-artefacts/transcripts/20260508T101723Z-subagent-steps/` for play S1 R1 — pick a `tdab-run` one for a like-for-like comparison).

---

## Stage 4: Note what you saw

In your own notes (location at your discretion), record:
- Final reported PASS/FAIL.
- Deviations seen, each with a line reference into the rendered session or the stage director log.
- A brief verdict: did the runner behave like disciplined `tdab-run`, or did it improvise?

---

## Stage 5: Control run

```
git restore .claude/skills/tdab-run/SKILL.md
```

Then `/clear` and repeat stages 1–4 with the unmodified skill. Stage 2's prompt is identical — the script will produce a second rendered file with a fresh timestamp.

---

## Stage 6: Diff

```bash
diff <ablated-render-path> <control-render-path>
```

The differences in the rendered sessions *are* the experimental result. The deviations on the ablated run are the prohibitions doing their work.

---

## Integrating findings into the main report

Once the experiment is complete, the result should be reflected back into [`prose-vs-promptbook.md`](prose-vs-promptbook.md). What to write depends on the outcome:

### Outcome A: Runner deviates significantly (matches author prediction)

The prohibitions are load-bearing scar tissue. Update the main report:

- **Preventative-statement density section** — promote "early signal" framing to "experimentally confirmed":
  > "An ablation experiment removed the ~13 prose prohibitions in tdab-run that have no semantic equivalent in tdab-play, then re-ran the same scenario. The runner deviated from disciplined behaviour: [list 2–3 most prominent deviations seen, with line refs]. The prohibitions are load-bearing — their absence breaks the runner. The promptbook form's lower count therefore reflects genuine structural support for correct behaviour, not authoring style. See [`ablation-test-prompt.md`](ablation-test-prompt.md) for the experiment runbook."
- **Working assumption / The question** — these don't need text changes, but the experiment now answers part of "the question": yes, when a workflow is encoded structurally, the agent needs fewer explicit constraints to operate correctly.
- **Findings table — Structure section** — could add a new row: "Prohibition load-bearingness" with winner = **promptbook** (interpreted as "promptbook achieves the same correctness without the extra prohibitions"). Or keep the existing Prohibitions row and just strengthen its Notes column.
- **Future experiments** — replace the planned ablation entry with a brief retrospective and link to the captured ablation results.

### Outcome B: Runner doesn't deviate (surprise — prohibitions are defensive overkill)

This is the surprise outcome and worth more careful documentation:

- **Preventative-statement density section** — record the surprise:
  > "An ablation experiment removed tdab-run's ~13 unique prohibitions and re-ran the same scenario. The runner behaved identically to the control. The prohibitions, though added in response to past failures, were not necessary for *this* scenario — possibly because: (a) the agent has improved since the prohibitions were authored, or (b) this scenario doesn't exercise the failure modes the prohibitions guard against. The prohibition-density gap therefore reflects authoring history more than structural advantage on this scenario."
- **Working assumption / The question** — the experiment leaves the central question more open than expected. Note that the result is scenario-specific; broader scenario coverage would tighten the conclusion.
- **Findings table** — Prohibitions row's Notes column should be revised to acknowledge the ablation result.

### Outcome C: Mixed — some deviations surface, others don't

Most informative outcome. Record per-prohibition: which removals surfaced deviations, which didn't.

- **Preventative-statement density section** — most useful as a table mapping removed prohibition → observed deviation (or "no deviation"). The load-bearing fraction (e.g. "5 of 13 surfaced deviations") is the headline number.
- The result narrows future authoring: the load-bearing prohibitions are the ones genuinely doing structural work; the rest are removable.

### In all outcomes

- Save the rendered ablation and control session transcripts under `docs/promptbook/ablation-results/` so the result is reproducible from artefacts.
- Add a short "Ablation result" subsection to the main report (under [Preventative-statement density](prose-vs-promptbook.md#preventative-statement-density)) summarising the verdict in 2–4 sentences with a link to the artefacts.
- Update the Future experiments entry from "planned" to "completed" with a one-line summary of what was found.
