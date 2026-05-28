---
name: graphite
description: >-
  Use for Graphite CLI stacked PRs workflow in repos with .git/.graphite_repo_config.
  Triggers: graphite, stacked PRs, dependent PRs, chained PRs, PR stack, gt create,
  gt modify, gt submit, gt sync, gt restack, gt log, gt checkout, gt up, gt down,
  rebase my stack, fix stack conflicts, split PR, land my stack, merge stack,
  sync with main/trunk, reorder branches, fold commits, amend stack, move branch
  to different parent, stack out of date, update my stack. For repos WITHOUT
  .git/.graphite_repo_config, use standard git commands instead.
---

# Graphite Stacked PRs Workflow

**IMPORTANT:** This workflow applies ONLY to repositories with `.git/.graphite_repo_config`. For repositories without this file, use standard git commands (`git commit`, `git push`, etc.).

## Detection

Check for `.git/.graphite_repo_config` to determine if a repo uses Graphite:

- **File exists:** Use `gt` commands (this skill applies)
- **File does not exist:** Use standard `git` commands (this skill does NOT apply)

When Graphite is detected, use `gt` commands instead of `git` for all commit and branch operations.

## Planning Stacks (CRITICAL)

**Before writing any code, present the stack structure and ask for confirmation.**

When building a feature as a stack:

1. **Plan first** - break the work into logical, sequential PRs
2. **Use TodoWrite** - each todo item maps to one PR/`gt create`
3. **Present the structure** - show the user the planned stack:
  ```
   PR Stack for [Feature]:
  ```
  1. PR 1: [description] - [what it does]
  2. PR 2: [description] - [what it does]
  3. PR 3: [description] - [what it does]
    `
4. **Ask for confirmation** - "Does this structure look good to proceed?"
5. **Only then start coding**

**IMPORTANT:** Each PR must be atomic and pass CI independently. Verify this before committing.

## Command Mapping

Use these `gt` commands instead of their git equivalents:


| Instead of           | Use                          | Purpose                                 |
| -------------------- | ---------------------------- | --------------------------------------- |
| `git commit` (new branch) | `gt create -m "msg"`    | Create new branch/PR with staged changes |
| `git commit` (same branch) | `gt modify -c -m "msg"` | Add a new commit to the current branch (default for follow-up work) |
| `git commit --amend` | `gt modify` (opt-in)         | Amend the current branch's tip commit — only when explicitly requested or squashing trivial fixups pre-review |
| `git push`           | `gt submit --no-interactive` | Submit current + all downstack branches |
| `git pull`           | `gt sync`                    | Pull trunk, restack, clean merged       |
| `git checkout`       | `gt checkout <branch>`       | Switch branches                         |
| `git rebase`         | `gt restack`                 | Rebase stack (usually via `gt sync`)    |

**Never pass `-a` / `-am` to `gt create` or `gt modify`** — it sweeps untracked files into the commit. Always `git add <paths>` explicitly first, then run the `gt` command without `-a`.


## When to Create, Add a Commit, or Amend

**Default: add a new commit to the current branch** with `gt modify -c -m "msg"`. Stage explicitly with `git add <paths>` first so untracked files aren't swept in. This is the right call for almost all follow-up work, including review feedback — it keeps the review trail legible and avoids force-pushes when reviewers are mid-read.

**Use `gt create -m "message"`** (after `git add <paths>`) when:

- Starting new work (new feature, new fix)
- The change is logically separate from the current PR
- Building the next piece in a stack

**Use `gt modify` to amend** only when:

- The user explicitly asks to amend
- Squashing a trivial fixup (typo, lint) into the tip commit *before* the PR has been reviewed
- Never amend a commit that reviewers have already started reading

## Stack Philosophy

Each PR in a stack must be:

- **Atomic** - passes CI independently, no broken intermediate states
- **Small** - ideally under 250 lines changed
- **Focused** - one logical change per branch
- **Reviewable** - makes sense on its own (even if it depends on others)

Break large features into functional components

For example:

- Database changes first
- Backend logic next
- Frontend last

Or use iterative stacking:

- Basic implementation
- Error handling
- Tests
- Polish

## Navigation

Move through the stack:

```bash
gt log          # View full stack with PR status
gt ls           # Abbreviated stack view
gt up           # Move up one branch (toward tip)
gt down         # Move down one branch (toward trunk)
gt top          # Jump to top of stack
gt bottom       # Jump to bottom of stack
gt checkout X   # Switch to specific branch
```

## Sync Workflow

Run `gt sync` regularly (at least daily) to:

1. Pull latest trunk changes
2. Restack all branches
3. Clean up merged branches

When `gt sync` encounters conflicts, it pauses for resolution. See conflict resolution below.

## Submitting Work

Push changes with:

```bash
gt submit --no-interactive   # Submit current + all downstack branches (recommended)
gt submit --stack            # Submit current + all descendant branches
gt ss                        # Shorthand for --stack
```

Use `--no-interactive` to avoid prompts during submission.

## Conflict Resolution

When `gt sync` or `gt restack` hits conflicts:

1. **Understand what conflicted** - check which branch and what files
2. **Check what each branch does** - use `gt log` and review the changes
3. **Auto-resolve obvious conflicts:**
  - Import order changes
  - Whitespace differences
  - Non-overlapping additions
4. **Ask about ambiguous conflicts:**
  - Same code modified differently
  - Deleted vs modified conflicts
  - Semantic conflicts (logic changes)

After resolving:

```bash
gt continue -a      # Stage all and continue restack
```

If stuck:

```bash
gt abort            # Abandon restack, return to previous state
```

For detailed conflict resolution patterns, see `references/conflict-resolution.md`.

## Reorganizing Stacks

Adjust stack structure when needed:

```bash
gt move --onto <branch>   # Move current branch to new parent
gt fold                   # Merge branch into its parent
gt split                  # Break branch into multiple
gt squash                 # Combine commits in branch to one
gt reorder                # Interactively reorder branches
```

## Collaboration

Work with others' stacks:

```bash
gt get <branch>           # Fetch someone's stack locally
gt track <branch>         # Start tracking existing git branch
```

## Quick Reference

See `references/cheatsheet.md` for a complete command reference.

## Common Workflows

### Building a Feature as a Stack

1. Plan the stack structure first (use TodoWrite)
2. Present to user and get confirmation
3. Implement each PR sequentially:

```bash
gt sync                                    # Get latest
git add <paths>                            # Stage explicitly — don't sweep untracked files
gt create -m "feat: Add avatar upload API"
# ... verify it passes CI ...
git add <paths>
gt create -m "feat: Add avatar display component"
# ... verify it passes CI ...
git add <paths>
gt create -m "feat: Add avatar to user profile"
gt submit --no-interactive                 # Submit entire stack
```

### Addressing Review Feedback

```bash
gt checkout <branch-with-feedback>
# ... make fixes ...
git add <paths>                            # Stage explicitly — don't sweep untracked files
gt modify -c -m "review: address feedback" # Add a new commit (preserves review trail)
gt submit --no-interactive                 # Push updates (auto-restacks dependents)
```

### Daily Sync Routine

```bash
gt sync                                    # Pull, restack, clean
# Resolve any conflicts if prompted
gt continue -a                             # After resolving
```

