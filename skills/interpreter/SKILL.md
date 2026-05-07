---
name: interpreter
description: PlantUML activity diagram interpreter for stagentic-promptbook skills. Loaded by skills that include `Load the stagentic-promptbook:interpreter skill` in their body. Not a general PlantUML interpreter — defines the stagentic-promptbook keyword set.
---

# Interpreter

## Diagram constructs

- **Swimlanes** — `|Lane|` markers. Lanes: `Session Agent` (execute), `User` (pause for input or output).
- **Activities** — `:text;` form. Text may span multiple lines. `↳ name` captures a named value from the activity's result for use in later steps. Multiple captures: `↳ a, ↳ b`. For long capture lists, each `↳` may sit on its own line for readability. In a sub-diagram call, `↳ name` captures the value returned by that diagram's `:return;`.
- **Links** — `[[file.md slug]]` or `[[file.md#anchor slug]]` inside an activity. A `.puml` link is a sub-diagram call. Link paths are relative to the skill's base directory and are authoritative — read the file directly at that path. Do not search for it.
- **Control flow** — `start`, `stop`, `end`, `if/else/endif`, `while/endwhile`, `break`, `fork/end fork`, `detach`.
- **Notes** and **stereotypes** (`<<...>>`) — visual only. Ignore them.

## Executing activities

For each activity in the `Session Agent` swimlane:

- **Delegated** (references external files via a hyperlink prefixed with "**Cue**:") — follow the link and execute what it says. Box text is a named label for the delegation, not an instruction. `#anchor` links point to a heading — read to the next same-level heading or end of file. If the file was already read this session, navigate to the relevant section without re-reading.
- **Inline** (has no link) — box text is the complete instruction. Execute it directly according to the [Keyword](#keywords) used.

For each activity in the `User` swimlane — pause for input or present output as appropriate.

### Keywords

| Keyword | Meaning |
|---|---|
| `**Cue**:` | Delegate to a direction file or sub-diagram via the link that follows |
| `**Run**:` | Execute the shell command provided between backticks. Commands are literal and authoritative — run exactly as written once any substitutions have taken place (e.g. if placeholder `<command>` has been assigned the value `foo`, `` `which <command>` `` executes as `which foo`). If a command is unknown or fails, that is an authorship error — report it and stop. |
| `**Find**:` | Locate the named resource (file, path) and capture the result |
| `**Inform**:` | Emit output to the user; no response expected. When followed by a backtick command, the command runs per `**Run**:` and the Session Agent emits the captured stdout to the user as its own message text — the tool's own output rendering does not count, since the host UI may collapse or truncate it. |
| `**Ask**:` | Emit output to the user and wait for their response |
| `**Input**:` | Capture a named value from user input |
| `**Await**:` | Wait for all named background agents to complete |

## Terminators

- **`stop`** — halt immediately. Do not return to any call site.
- **`end`** — complete this diagram. If called as a sub-diagram, return to the call site.
- **`:return;`** — placed before `end` to declare return value(s) to the caller.
- **`detach`** — terminate this branch without joining.

## Sub-diagrams

A `[[*.puml slug]]` link is a sub-diagram call. Terminator semantics apply at any nesting depth.

## Decisions, loops, and forks

- **Decisions** — evaluate the condition from activity results or skill state; follow the matching branch.
- **Loops** — repeat the body until the exit condition; `break` exits immediately.
- **Forks** — `fork/end fork` runs branches concurrently. Branches with `detach` terminate independently. An empty branch rejoins at `end fork` and the main flow continues from there.

## Pause gates

Apply standard pause gates from the host project's `AGENTS.md` / `CLAUDE.md` unless the skill overrides them.

## On failure

Stop and report. Any failure is an error in the diagram or its referenced files — do not repair, retry, or work around unless the linked direction file explicitly directs you to.
