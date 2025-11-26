# command-start-issue

A Claude Code slash command for creating git worktrees for GitHub issues.

## Installation

```bash
# Clone to your preferred location
git clone git@github.com:claude-commands/command-start-issue.git <clone-path>/command-start-issue

# Symlink (use full path to cloned repo)
ln -s <clone-path>/command-start-issue/start-issue.md ~/.claude/commands/start-issue.md
```

## Usage

```text
/start-issue 123
```

Where `123` is the GitHub issue number.

## Configuration

Set the `WORKTREE_PREFIX` environment variable to customize the worktree naming:

```bash
export WORKTREE_PREFIX="my-project"
```

This will create worktrees named like: `my-project-issue-123-feature-title`

Default prefix is `project`.

## What it does

1. Fetches issue details from GitHub
2. Validates the issue exists
3. Creates a worktree directory named `{prefix}-issue-{number}-{title}`
4. Creates a feature branch from `dev`
5. Copies your `.claude` directory to the new worktree
6. Displays the path to switch to

## Requirements

- `gh` CLI installed and authenticated
- Git repository with GitHub remote
- A `dev` branch to base worktrees from

## Companion Command

Use with `/prune-worktree` to clean up completed worktrees.

## Updates

```bash
cd <clone-path>/command-start-issue && git pull
```
