# Git Standards

## Commit Format

All commits follow Conventional Commits:

```
<type>[optional scope]: <description>
```

**Types:**
- `feat` — new feature
- `fix` — bug fix
- `docs` — documentation only
- `refactor` — restructuring, no behavior change
- `test` — adding or correcting tests
- `chore` — maintenance, dependencies
- `ci` — CI/CD changes

**Examples:**
```
feat(auth): add JWT refresh token support
fix(api): handle null response from property search
test(listings): add edge case for zero results
```

Breaking changes append `!` and include a footer:
```
feat(api)!: replace paginated endpoint with cursor-based

BREAKING CHANGE: pagination params replaced with cursor/limit
```

## Branch Naming

```
feature/<short-description>     # New features
fix/<issue-number>-<description> # Bug fixes
docs/<component>                 # Documentation
refactor/<component>             # Refactoring
```

## PR Workflow

1. Branch from `main` (or the designated base)
2. Commit with conventional format
3. Create PR: `gh pr create --title "<type>(<scope>): <description>"`
4. PR body must include:
   - Summary (what changed and why)
   - Test plan (how to verify)
   - Any breaking changes

## Rules

- Never commit directly to `main` — always use PRs
- Never force push to shared branches
- Never commit secrets, `.env` files, or credentials
- Delete branches after merge
- One logical change per commit — don't mix unrelated changes
