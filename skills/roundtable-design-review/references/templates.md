# Templates

Input templates for each workflow step and output format specifications.

All live outputs are saved inside a chosen `SESSION_ROOT/` directory. The recommended location is `AI_SESSIONS_HOME/roundtable-design-review/<workspace-slug>/<session-slug>/`.

Default `AI_SESSIONS_HOME`:

- macOS: `~/Library/Application Support/ai-sessions`
- Linux: `${XDG_DATA_HOME:-~/.local/share}/ai-sessions`
- Windows: `%APPDATA%/ai-sessions`

Repository note:

- Checked-in examples for this skill live under `../examples/sessions/`.
- Do not write live workflow runs into `../examples/sessions/`.
- Do not use a top-level `rounds/` directory shared across multiple designs.

## Session Selection Template

Use this before any step if the session is not already clear.

```text
TASK = choose-session

REQUEST:
(what the user wants to design or continue)

KNOWN_CONTEXT: (optional)
- existing sessions under AI_SESSIONS_HOME/roundtable-design-review/<workspace-slug>/
- prior final designs to reference

INSTRUCTION:
1. Decide whether this is a new design session or a continuation of an existing one.
2. Resolve SESSION_ROOT.
3. If starting fresh, prefer `AI_SESSIONS_HOME/roundtable-design-review/<workspace-slug>/<session-slug>/`.
4. If the target session is ambiguous, ask the user to identify it before doing any design work.
5. Never use `examples/sessions/` as the live session root.
```

## Step 1: Kickoff — Author Input Template

```text
STEP = 1 (kickoff)
ROLE = Author
SESSION_ROOT = AI_SESSIONS_HOME/roundtable-design-review/<workspace-slug>/<session-slug>/

IDEA:
(user's idea description)

USER_CONTEXT: (optional)
(constraints, preferences, existing stack, prior design links, etc.)

INSTRUCTION:
Act as the proposal Author.

1. Execute the Session Resolution Protocol and confirm SESSION_ROOT.
2. List files in SESSION_ROOT/.
3. Verify SESSION_ROOT/ is empty or missing.
4. Assess whether the idea has critical ambiguities that would significantly
   affect the design direction.
5. IF AMBIGUITIES EXIST: output ONLY your clarifying questions (3-5 max),
   then STOP. Do NOT generate V1 in the same response.
6. IF THE REQUIREMENT IS CLEAR ENOUGH: generate V1.
7. Begin V1 with a Project Brief.
8. Save the complete V1 output to SESSION_ROOT/1-proposal.md.
```

## Step 2: Review — Reviewer Input Template

```text
STEP = 2 (review)
ROLE = Reviewer
MODEL = (your lowercase model identifier, e.g., claude, gpt, gemini)
SESSION_ROOT = AI_SESSIONS_HOME/roundtable-design-review/<workspace-slug>/<session-slug>/

INSTRUCTION:
Act as proposal Reviewer.

1. Execute the Session Resolution Protocol and confirm SESSION_ROOT.
2. List files in SESSION_ROOT/.
3. Verify SESSION_ROOT/1-proposal.md exists.
4. Verify SESSION_ROOT/2-review-{MODEL}.md does NOT exist yet.
5. Read SESSION_ROOT/1-proposal.md.
6. If other SESSION_ROOT/2-review-*.md files exist, read those too.
7. Review across 4 mandatory dimensions:
   - Architecture soundness
   - Technical feasibility
   - Missed risks
   - Alternative approaches
8. Provide specific suggestions tagged high, medium, or low priority.
9. Mark conflicts with other reviewers using `⚠️ conflict`.
10. Save output to SESSION_ROOT/2-review-{MODEL}.md.
```

## Human Decision (between Step 2 and Step 3)

No template needed. The user reviews all `2-review-*.md` files within the same session, arbitrates conflicts, confirms all expected reviewers have submitted, and tells the Author to continue in that `SESSION_ROOT`.

## Step 3: Revise — Author Input Template

```text
STEP = 3 (revise)
ROLE = Author
SESSION_ROOT = AI_SESSIONS_HOME/roundtable-design-review/<workspace-slug>/<session-slug>/

HUMAN_DECISIONS: (optional)
(conflict arbitration decisions and any supplementary requirements)

INSTRUCTION:
Act as the proposal Author.

1. Execute the Session Resolution Protocol and confirm SESSION_ROOT.
2. List files in SESSION_ROOT/.
3. Verify SESSION_ROOT/1-proposal.md exists.
4. Verify at least one SESSION_ROOT/2-review-*.md exists.
5. Verify SESSION_ROOT/3-proposal.md does NOT exist yet.
6. List all SESSION_ROOT/2-review-*.md files and acknowledge the count.
7. Read SESSION_ROOT/1-proposal.md.
8. Read ALL SESSION_ROOT/2-review-*.md files.
9. Evaluate each suggestion individually.
10. Incorporate HUMAN_DECISIONS where provided.
11. Generate V2 as a complete proposal with a changelog.
12. Save output to SESSION_ROOT/3-proposal.md.
```

## Step 4: Final Review — Reviewer Input Template

```text
STEP = 4 (final-review)
ROLE = Reviewer
MODEL = (your lowercase model identifier, e.g., claude, gpt, gemini)
SESSION_ROOT = AI_SESSIONS_HOME/roundtable-design-review/<workspace-slug>/<session-slug>/

INSTRUCTION:
Act as proposal Reviewer for the final review.

1. Execute the Session Resolution Protocol and confirm SESSION_ROOT.
2. List files in SESSION_ROOT/.
3. If SESSION_ROOT/5-proposal.md exists, STOP - the session is already complete.
4. Verify SESSION_ROOT/3-proposal.md exists.
5. Verify SESSION_ROOT/2-review-{MODEL}.md exists.
6. Verify SESSION_ROOT/4-final-review-{MODEL}.md does NOT exist yet.
7. Read SESSION_ROOT/3-proposal.md.
8. Read your own SESSION_ROOT/2-review-{MODEL}.md.
9. Verify prior suggestions were handled reasonably.
10. Check whether V2 introduced new blockers.
11. Write a verdict file whose first line is exactly one of:
    # Verdict: ✅ pass
    # Verdict: ⚠️ remaining issues
12. Save output to SESSION_ROOT/4-final-review-{MODEL}.md.
```

## V3 Fix — Author Input Template

```text
STEP = V3 fix
ROLE = Author
SESSION_ROOT = AI_SESSIONS_HOME/roundtable-design-review/<workspace-slug>/<session-slug>/

HUMAN_DECISIONS: (optional)
(arbitration regarding remaining issues)

INSTRUCTION:
Act as the proposal Author for targeted V3 fixes.

1. Execute the Session Resolution Protocol and confirm SESSION_ROOT.
2. List files in SESSION_ROOT/.
3. Verify SESSION_ROOT/3-proposal.md exists.
4. Verify at least one SESSION_ROOT/4-final-review-*.md contains a `⚠️` verdict.
5. Verify SESSION_ROOT/5-proposal.md does NOT exist yet.
6. Read SESSION_ROOT/3-proposal.md.
7. Read ALL SESSION_ROOT/4-final-review-*.md files.
8. Apply targeted fixes ONLY for valid remaining issues.
9. Generate V3 as a complete proposal with the new changelog placed first.
10. Save output to SESSION_ROOT/5-proposal.md.
11. Treat V3 as the hard stop for that session.
```

## Follow-Up Design Template

Use this when the user wants another design round after one session has already completed.

```text
TASK = follow-up-design
NEW_SESSION_ROOT = AI_SESSIONS_HOME/roundtable-design-review/<workspace-slug>/<new-session-slug>/
PREVIOUS_SESSION_ROOT = AI_SESSIONS_HOME/roundtable-design-review/<workspace-slug>/<old-session-slug>/

REQUEST:
(what changed and why a new design is needed)

INSTRUCTION:
1. Read the final proposal from PREVIOUS_SESSION_ROOT as context.
2. Do not modify files in PREVIOUS_SESSION_ROOT.
3. Start a fresh workflow in NEW_SESSION_ROOT beginning at Step 1.
4. Carry forward only the decisions still relevant to the new design question.
```

## Review Output Format

```markdown
# Review - {Model Name} (Reviewer)

## Review Summary
One-sentence overall assessment of the proposal.

## Dimension Reviews
### Architecture Soundness
...
### Technical Feasibility
...
### Missed Risks
...
### Alternative Approaches
...

## Specific Suggestions
### Suggestion 1: [title]
- **Priority**: high / medium / low
- **Issue**: ...
- **Suggestion**: ...
- **Rationale**: ...

## Conflicts With Other Reviewers (if applicable)
(mark specific points of disagreement)
```

## Final Review Output Format

The first line must be the verdict heading. This enables automated state detection inside one session.

```markdown
# Verdict: ✅ pass

## Review of Changes
(brief assessment of how prior suggestions were handled)
```

Or:

```markdown
# Verdict: ⚠️ remaining issues

## Findings
### 1. [Priority] Issue title
- **Location**: (section or line reference)
- **Issue**: ...
- **Suggestion**: ...

## Handled Well
(brief acknowledgment of suggestions that were properly addressed)
```

## Changelog Format

Append to the end of V2:

```markdown
## Changelog (V1 -> V2)
| # | Reviewer Suggestion | Disposition | Rationale |
|---|---|---|---|
| 1 | Claude: Use PostgreSQL instead of MongoDB | ✅ accepted | Relational data better fits transactional workload |
| 2 | GPT: Add message queue | ❌ rejected | Current scale does not require async decoupling |
| 3 | User decision: Deploy with Docker | ✅ accepted | Explicit user requirement |
```

If V3 is needed, place the V3 changelog above the V1 -> V2 changelog:

```markdown
## Changelog (V2 -> V3)
| # | Final Review Issue | Disposition | Change Location |
|---|---|---|---|
| F1 | GPT: Settlement cycle incorrectly modeled as sell restriction | ✅ fixed | Section 4 MarketRules |
```
