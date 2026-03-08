---
name: roundtable-design-review
description: This skill should be used when the user wants to design a system with multiple models, run a structured design review, revise a proposal across multiple review rounds, or create a new design session for a follow-up redesign. It orchestrates a session-based Author-Reviewer workflow where one model drafts a proposal and other models review it.
version: "1.0"
---

# LeslieMiau Roundtable Design Review v1.0

Multi-model collaborative design workflow. One model (Author) drafts a technical proposal, other models (Reviewers) critique it, and the human arbitrates conflicts. Each design run lives in its own session directory, so the skill can be reused across many designs without mixing artifacts into the business codebase.

## Core Model

- A **session** is one isolated design workflow for one design question.
- Each session has its own `SESSION_ROOT` directory.
- The recommended root is `AI_SESSIONS_HOME/roundtable-design-review/<workspace-slug>/<session-slug>/`.
- Checked-in repository samples live under `examples/sessions/` and are never the live working directory.

Default `AI_SESSIONS_HOME`:

- macOS: `~/Library/Application Support/ai-sessions`
- Linux: `${XDG_DATA_HOME:-~/.local/share}/ai-sessions`
- Windows: `%APPDATA%/ai-sessions`

Examples:

```text
AI_SESSIONS_HOME/
  roundtable-design-review/
    payments-service/
      v1/
      observability-followup/
    checkout/
      redesign-2026-03/
```

## Roles

- **Author** — Drafts proposals, asks clarifying questions, revises based on feedback. Only the Author writes `*-proposal.md`.
- **Reviewer** — Reviews proposals across multiple dimensions and writes `*-review-*.md` or `*-final-review-*.md`.

## Session Layout

All live workflow artifacts for one design session live in `SESSION_ROOT/`.

```text
SESSION_ROOT/
  1-proposal.md
  2-review-{model}.md
  3-proposal.md
  4-final-review-{model}.md
  5-proposal.md
```

`{model}` = lowercase model identifier such as `claude`, `gpt`, or `gemini`.

Rules:

- **One design question per session.** If the user starts a materially new design or wants a second design cycle after completion, create a new session instead of reusing the old one.
- **Session isolation is mandatory.** Never store live workflow files in the repository skill directory. Never use `examples/sessions/` as the live workspace.
- **Recommended location**: `AI_SESSIONS_HOME/roundtable-design-review/<workspace-slug>/<session-slug>/` so artifacts stay out of the main business tree and stay platform-neutral.
- **Sequence prefix is mandatory.** It makes session state visible from a directory listing.
- **Each role writes only its own file type.** Never cross-write.
- **Files are append-only.** To redo a step, the user deletes the target file inside that session and reruns the step.

## Session Resolution Protocol (CRITICAL)

Every model must resolve the target session before taking action.

1. Determine whether the user wants to continue an existing design or start a new one.
2. Resolve `SESSION_ROOT`:
   - If the user provided a session path or session name, use it.
   - Otherwise, prefer creating a new directory under `AI_SESSIONS_HOME/roundtable-design-review/<workspace-slug>/<session-slug>/`.
   - If the user clearly asked to continue a prior design, identify the existing session they mean before proceeding.
3. Confirm the chosen `SESSION_ROOT` is not inside `examples/sessions/`.
4. Do all state detection and file reads only inside that `SESSION_ROOT`.
5. Never infer state by mixing files from multiple sessions.

If the user asks for another design round after a prior session is complete, treat that as a **new session**. The old session may be read as context, but it must not be reopened.

## Bootstrap Protocol (CRITICAL)

After resolving `SESSION_ROOT`, every model must execute this protocol before taking any action:

1. List files in `SESSION_ROOT/`.
2. If any `4-final-review-*.md` files exist, read each one and inspect the first line.
3. Determine the current state using the State Detection Table below. Match top-to-bottom; first match wins.
4. Determine what action the user is requesting.
5. Verify the requested action matches the current state. If not, warn the user and suggest the correct next step.
6. Only then proceed.

### State Detection Table

| # | Condition in `SESSION_ROOT/` | State | Valid next actions |
|---|---|---|---|
| 1 | `5-proposal.md` exists | **Done (V3 hard stop)** | None. Session complete. Start a new session for further design work. |
| 2 | Any `4-final-review-*.md` exists, AND `3-proposal.md` does not exist | Invalid state (final review without V2) | Warn the user to remove stray final reviews or restore `3-proposal.md`. |
| 3 | `3-proposal.md` exists, AND at least one `4-final-review-*.md` exists, AND the set of models in `2-review-*.md` exactly matches the set of models in `4-final-review-*.md`, AND all verdicts are `✅` | **Done (V2 approved)** | None. Session complete. Start a new session for any follow-up design. |
| 4 | `3-proposal.md` exists, AND at least one `4-final-review-*.md` exists, AND the set of models in `2-review-*.md` exactly matches the set of models in `4-final-review-*.md`, AND any verdict is `⚠️` | V2 needs fix | User arbitrates issues → Author writes `5-proposal.md`. |
| 5 | `3-proposal.md` exists, AND any `4-final-review-{model}.md` exists without a corresponding `2-review-{model}.md` | Invalid state (unmatched final reviews) | Warn the user to remove unmatched final reviews or restore the missing Step 2 reviews. |
| 6 | `3-proposal.md` exists, AND no `2-review-*.md` exist | Invalid state (V2 without prior reviews) | Warn the user to restore missing Step 2 review files or restart in a new session. |
| 7 | One or more `2-review-*.md` exist, AND `1-proposal.md` does not exist | Invalid state (reviews without V1) | Warn the user to restore `1-proposal.md` or start a new session. |
| 8 | `3-proposal.md` exists, AND one or more `4-final-review-{model}.md` are missing for existing `2-review-{model}.md` | Final reviews pending | Only participating Reviewers missing a final review may write `4-final-review-{model}.md`. |
| 9 | One or more `2-review-*.md` exist | Reviews available | User confirms all reviews are in → Author writes `3-proposal.md`. |
| 10 | `1-proposal.md` exists | V1 drafted | Reviewer writes `2-review-{model}.md`. |
| 11 | `SESSION_ROOT/` empty or missing | Not started | Author writes `1-proposal.md`. |

### Enforcement Rules

- If the workflow is complete (rows 1 or 3), refuse to generate new files in that session.
- If the state is invalid, do not write new files until the inconsistency is resolved or a new session is chosen.
- If a Reviewer is asked to act but the state expects an Author action, refuse and explain which role should act next.
- If a file required for the current step is missing, warn the user before proceeding.

## Workflow

```text
New design question
  -> choose/create SESSION_ROOT
  -> Author writes 1-proposal.md
  -> Reviewers write 2-review-{model}.md
  -> Human arbitrates
  -> Author writes 3-proposal.md
  -> Reviewers write 4-final-review-{model}.md
  -> all pass: session complete
  -> any blocker: Author writes 5-proposal.md, session complete
```

## Interaction Rules

1. **Session first** — always identify the correct `SESSION_ROOT` before reasoning about state.
2. **Use a fresh session for a fresh design** — do not recycle a completed session for another design round.
3. **Absorb user context at any time** — additional requirements may be incorporated into the current step.
4. **Act autonomously first, ask only when truly uncertain** — but do ask if the intended session is ambiguous.

## Step 1: Kickoff — Author Drafts V1

**Precondition**: `SESSION_ROOT/` is empty or does not exist.
**Output**: `SESSION_ROOT/1-proposal.md`

Process:

1. Assess whether the user's idea has critical ambiguities that would significantly affect design direction.
2. If ambiguities exist, output only 3-5 clarifying questions, then stop. Do not generate V1 in the same turn.
3. If the requirement is clear enough, proceed directly to generate V1.
4. Save the complete V1 to `SESSION_ROOT/1-proposal.md`.

V1 must begin with a Project Brief:

```markdown
## Project Brief
- **Original requirement**: (user's idea)
- **Key constraints**: (confirmed during Q&A, or inferred)
- **Excluded approaches**: (ruled out, with reasons)
- **Core decisions**: (key choices made, with rationale)
```

Suggested sections when useful:

- Technology stack selection
- System architecture
- Module decomposition
- Data model or API design
- Security and access control
- Performance and scalability
- Risks and open questions
- Implementation roadmap

## Step 2: Review — Reviewers Critique V1

**Precondition**: `SESSION_ROOT/1-proposal.md` exists and this Reviewer's `2-review-{model}.md` does not.
**Input**: Read `SESSION_ROOT/1-proposal.md`. If other `2-review-*.md` exist, read those too.
**Output**: `SESSION_ROOT/2-review-{model}.md`

Mandatory review dimensions:

1. Architecture soundness
2. Technical feasibility
3. Missed risks
4. Alternative approaches

Mark disagreements with other Reviewers using `⚠️ conflict`.

Output format: see `references/templates.md` section "Review Output Format".

## Human Decision (between Step 2 and Step 3)

The user reviews all `2-review-*.md` files, arbitrates conflict points, confirms all expected reviews are in, and then tells the Author to continue in the same `SESSION_ROOT`.

## Step 3: Revise — Author Generates V2

**Precondition**: `SESSION_ROOT/1-proposal.md` and at least one `2-review-*.md` exist; `3-proposal.md` does not.
**Input**: Read `SESSION_ROOT/1-proposal.md`, all `SESSION_ROOT/2-review-*.md`, and any user decisions.
**Output**: `SESSION_ROOT/3-proposal.md`

Before writing, list all `2-review-*.md` files and report how many reviews were found. Evaluate each suggestion. Accept or reject with explicit rationale in the changelog.

Output format: see `references/templates.md` section "Changelog Format".

## Step 4: Final Review — Reviewers Approve V2

**Precondition**: `SESSION_ROOT/3-proposal.md` exists; this Reviewer's `2-review-{model}.md` exists; this Reviewer's `4-final-review-{model}.md` does not; `5-proposal.md` does not.
**Input**: Read `SESSION_ROOT/3-proposal.md` and the Reviewer's own `SESSION_ROOT/2-review-{model}.md`.
**Output**: `SESSION_ROOT/4-final-review-{model}.md`

The first line must be one of:

- `# Verdict: ✅ pass`
- `# Verdict: ⚠️ remaining issues`

Remaining issues must be genuine blockers, not nice-to-haves.

## V3 Fix — Author Applies Targeted Fixes

**Precondition**: `SESSION_ROOT/3-proposal.md` exists and at least one `4-final-review-*.md` contains `⚠️`; `5-proposal.md` does not.
**Input**: Read `SESSION_ROOT/3-proposal.md`, all `SESSION_ROOT/4-final-review-*.md`, and any `HUMAN_DECISIONS`.
**Output**: `SESSION_ROOT/5-proposal.md`

Rules:

- Apply targeted fixes only for valid remaining issues.
- Place the V2 -> V3 changelog above the existing V1 -> V2 changelog.
- `5-proposal.md` is the hard stop for that session.

## Starting Another Design Round

If the user wants another design after a session is complete:

1. Create a new sibling session under `AI_SESSIONS_HOME/roundtable-design-review/<workspace-slug>/`.
2. Optionally read the final proposal from the previous session as context.
3. Treat the new session as independent state with its own `1-proposal.md` through `5-proposal.md`.

Do not append new work into a completed session.

## Additional Resources

- `references/templates.md` — Prompt templates and output formats.
- `examples/sessions/` — Sample repository sessions that show the file layout and progression.
