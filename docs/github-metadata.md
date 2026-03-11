# GitHub Metadata And Rollout

Use this file as the source of truth for repository-facing GitHub settings that cannot live directly in git.

## Canonical Brand

- Display brand: `AI Coding Skills`
- Subtitle: `for Claude Code and Codex`
- Current repo slug: `ai-productivity-skills`
- Recommended future slug: `ai-coding-skills`

If the slug cannot be changed yet, keep the visible brand aligned everywhere else and defer the rename.

## About Description

Use this exact text in the GitHub repository About field:

```text
Curated AI coding skills for Claude Code and Codex: stronger prompts, safer design reviews, and token-aware execution.
```

## Topics

Apply these GitHub topics:

```text
ai-coding
coding-agents
claude-code
codex
ai-skills
developer-productivity
prompt-engineering
design-review
token-budget
agent-workflows
```

## Profile And Pinning

- Pin this repository on the GitHub profile.
- Keep the profile bio aligned with the repo description.

Suggested profile bio:

```text
Building AI coding skills for Claude Code and Codex. Stronger prompts, safer design reviews, token-aware execution.
```

## Social Preview

- Source asset: [`docs/assets/social-preview.svg`](assets/social-preview.svg)
- Preferred upload asset: `docs/assets/social-preview.png`
- Visual message only: brand, two flagship skills, and the two main benefits.

Upload checklist:

1. Open the repository settings page on GitHub.
2. Upload `docs/assets/social-preview.png` as the social preview image.
3. Verify that link previews show the `AI Coding Skills` title, the About description, and the same visual.

## Release `v0.1.0`

- Tag name: `v0.1.0`
- Release title: `AI Coding Skills v0.1.0`
- Release body source: [`docs/releases/v0.1.0.md`](releases/v0.1.0.md)

Manual sequence:

1. Commit the branding and launch assets.
2. Create tag `v0.1.0` from that commit.
3. Create the GitHub release using the release notes file.
4. Publish one English launch post and one Chinese launch post from [`docs/launch.md`](launch.md).

## Traffic Baseline

Record the baseline on the day the launch ships, then compare again 14 days later.

| Date | Unique visitors | Total views | Stars | Clones |
| --- | --- | --- | --- | --- |
| Launch day baseline |  |  |  |  |
| +14 days |  |  |  |  |

## Quick Verification

- The repo title and README H1 both say `AI Coding Skills`.
- `Claude Code`, `Codex`, `design review`, and `token` are all visible in the About field and README first screen.
- README first-screen links reach setup docs and both flagship skills.
- The social preview image matches the same brand and benefit statement.
