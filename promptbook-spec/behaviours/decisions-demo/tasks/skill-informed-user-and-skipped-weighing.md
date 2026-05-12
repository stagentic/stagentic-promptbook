# Scorecard: Skipped Weighing With One Option

| Characteristic                                                                                                                                    | Weight   | Min. Score | Match |
|---------------------------------------------------------------------------------------------------------------------------------------------------|----------|------------|-------|
| The When transcript contains an assistant message with the verbatim text "What are you choosing between? List 2 to 5 options, one per line."     | required | -          |       |
| The When transcript contains an assistant message with the verbatim text "Looks like you only gave me one option. The decision is already made." | required | -          |       |
| The When transcript contains an assistant message with the verbatim text "User response: pizza" (the scripted input was used as the options)      | required | -          |       |

**Required** characteristics must all pass.

## How to evaluate

1. Find the transcript folder matching `*-skips-weighing-when-only-one-option-given/` in `promptbook-spec-artefacts/transcripts/`.
2. Read `agent-ids.txt` from that folder to find the When agent's agentId.
3. Read `2-when-{agentId}.md` from that folder.
4. Verify each scorecard row against the transcript content. "Verbatim" means the exact string appears in an assistant message (whitespace-trimmed at the boundaries) — however, newlines can be considered equivalent to spaces.

## On Completion

Emit the scorecard table with the Match column filled in. Then state OVERALL: PASS or OVERALL: FAIL.
