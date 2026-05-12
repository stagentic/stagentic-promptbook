**Directives:**
- Follow the workflow precisely emitting what you would even if the user was present.
- Stop when the workflow reaches `stop` or `end`
- Do not ask clarifying questions.

**TASK:**
- Use the Skill tool to load the `stagentic-promptbook:decisions-demo` skill. Then follow the workflow at `/workspace/stagentic-promptbook/skills/decisions-demo/decisions-demo.puml` (the workspace source the plugin is built from). Do not search for the skill files elsewhere and do not read them from the plugin cache.
- You will play the Session Agent in the workflow. 
- The User is not present in this session, so:
  - When the workflow's `**Input**:` step expects a user response, use the next entry from
    the scripted user responses below (in order) omitting its entry number e.g. "1. ". 
  - Treat that entry as if the User had just typed it.
  - Emit "User response: {exact user response used}"

**Scripted user responses (in order):**
1. `pizza`
