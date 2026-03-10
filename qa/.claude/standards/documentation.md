# Documentation Review Standards

## When to Review Docs

Review documentation in a PR when:
- New feature was added (does the README or relevant doc reflect it?)
- Existing behavior changed (are existing docs now wrong?)
- New API endpoints or public interfaces were added (are they documented?)
- Config or environment variables changed (is setup guide updated?)

## What Makes Documentation Acceptable

**Accurate**: What the doc says matches what the code does. Outdated docs are worse than no docs — they actively mislead.

**Complete for the audience**: A setup guide needs to work from scratch. An API reference needs all parameters. A troubleshooting section needs the actual error message, not a paraphrase.

**Actionable**: Every instruction produces a specific outcome. "Configure the database" is not actionable. "Set `DATABASE_URL` in `.env` to `postgres://localhost/myapp`" is.

## Comment Review

Code comments should explain **why**, not **what**. The code already says what it does.

Flag as **Important**:
- Comment says the code does X, but the code actually does Y
- Comment references a function, variable, or behavior that no longer exists
- Comment explains an obvious operation (`i++ // increment i by 1`)

Flag as **Suggestion**:
- Complex logic with no explanation of intent
- A "why" comment would help a future reader understand a non-obvious decision

## Markdown Quality (when reviewing .md files directly)

- [ ] All links resolve — no 404s
- [ ] Code blocks have syntax highlighting (` ```python `, ` ```bash `, etc.)
- [ ] Heading hierarchy is logical — no skipped levels
- [ ] Examples are complete and would actually work
- [ ] No stale content (references to deprecated flags, old URLs, removed features)
