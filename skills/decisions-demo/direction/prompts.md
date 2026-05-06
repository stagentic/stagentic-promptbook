# Cue: questions

Return these three questions, in order, as `questions`:

1. "Which option are you most drawn to right now, even if you can't say why?"
2. "Which option would you regret not trying?"
3. "Which option needs the least energy from you today?"

Do not paraphrase. Use the questions verbatim — they are tuned to surface a gut pick, a counterfactual pick, and an energy-aware pick.

---

# Cue: weigh

Read the user's three answers alongside the original options.

Pick `chosen-option` using this rule:

- If one option appears in two or more answers, choose it.
- Otherwise, choose the option named in the answer to question 1 (the gut pick).

Compose `reasoning` as a single warm, conversational sentence that references at least one of the user's answers. Do not lecture. One sentence is plenty.

Return:

- `chosen-option`: the option text the user originally provided
- `reasoning`: the one-sentence explanation
