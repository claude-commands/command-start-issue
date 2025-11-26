---
argument-hint: "<issue-number>"
description: "Create a new git worktree for a GitHub issue"
allowed-tools: ["Bash", "Read"]
---

# Start Issue Worktree

Create a new git worktree for GitHub issue #$ARGUMENTS

## Configuration

Before using, set your project prefix in the WORKTREE_PREFIX variable below.
Default is "project" - change it to match your project name (e.g., "my-app", "api-server").

## Steps

1. **Fetch issue details from GitHub**
   !gh issue view $ARGUMENTS --json title,state,number

2. **Validate issue exists and is open**
   !if ! gh issue view $ARGUMENTS >/dev/null 2>&1; then echo "Error: Issue #$ARGUMENTS not found"; exit 1; fi
   !if [ "$(gh issue view $ARGUMENTS --json state --jq '.state')" = "CLOSED" ]; then echo "Warning: Issue #$ARGUMENTS is already closed"; fi

3. **Create worktree directory name**
   !WORKTREE_PREFIX="${WORKTREE_PREFIX:-project}"
   !ISSUE_TITLE=$(gh issue view $ARGUMENTS --json title --jq '.title' | sed 's/[^a-zA-Z0-9-]/-/g' | tr '[:upper:]' '[:lower:]' | sed 's/--*/-/g' | sed 's/^-\|-$//g')
   !WORKTREE_NAME="${WORKTREE_PREFIX}-issue-$ARGUMENTS-$ISSUE_TITLE"
   !WORKTREE_PATH="../$WORKTREE_NAME"
   !BRANCH_NAME="issue-$ARGUMENTS-$ISSUE_TITLE"

4. **Check if worktree already exists**
   !if [ -d "$WORKTREE_PATH" ]; then echo "Error: Worktree already exists at $WORKTREE_PATH"; exit 1; fi

5. **Fetch latest dev branch**
   !git fetch origin dev

6. **Create worktree from dev branch**
   !git worktree add "$WORKTREE_PATH" origin/dev

7. **Switch to new worktree and create feature branch**
   !cd "$WORKTREE_PATH" && git checkout -b "$BRANCH_NAME"

8. **Copy .claude directory to new worktree**
   !SOURCE_CLAUDE_DIR="$(pwd)/.claude"
   !if [ -d "$SOURCE_CLAUDE_DIR" ]; then cp -r "$SOURCE_CLAUDE_DIR" "$WORKTREE_PATH/"; else echo "Note: No .claude directory found to copy"; fi

9. **Display success message**
   !echo "Created worktree for issue #$ARGUMENTS"
   !echo "Path: $WORKTREE_PATH"
   !echo "Branch: $BRANCH_NAME"
   !echo "To switch: cd $WORKTREE_PATH"

## Next Steps

- Change to the new worktree directory: `cd $WORKTREE_PATH`
- Start working on issue #$ARGUMENTS
- When done, use `/prune-worktree` to clean up
