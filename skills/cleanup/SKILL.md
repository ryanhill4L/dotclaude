---
name: cleanup
description: Clean up after a merged PR. Use when the user says "cleanup", "clean up", "my PR was merged", "tidy up", or wants to delete a merged branch and get back to main. Checks for the recently merged PR, deletes the local branch, switches to main, and pulls latest changes.
---

# Cleanup After Merged PR

## Workflow

1. **Identify the current branch** and verify its PR was merged:

```bash
BRANCH=$(git branch --show-current)
gh pr view "$BRANCH" --json state,title,number,mergedAt 2>/dev/null
```

2. **If the PR is merged** (state: MERGED), proceed. If not merged or no PR found, confirm with the user before continuing.

3. **Switch to main and pull latest:**

```bash
git checkout main
git pull
```

4. **Delete the local branch:**

```bash
git branch -d "$BRANCH"
```

Use `-D` (force) only if `-d` fails — but first confirm with the user, since that may indicate the branch wasn't fully merged.

## Output

Report:
- The PR title and number that was merged
- That the local branch was deleted
- That main is now up to date
