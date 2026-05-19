# Claude Code Graphite

Claude Code plugins for [Graphite](https://graphite.dev) stacked PRs workflow.

## Installation

```bash
claude plugin marketplace add georgeguimaraes/claude-code-graphite
```

Install all plugins:

```bash
claude plugin install graphite@claude-code-graphite
```

## Prerequisites


| Platform | Command                                            |
| -------- | -------------------------------------------------- |
| macOS    | `brew install withgraphite/tap/graphite`           |
| npm      | `npm install -g @withgraphite/graphite-cli@stable` |


---

### graphite plugin

Skill and hooks for Graphite CLI (`gt`) stacked PR workflows.


| Feature             | Description                                                  |
| ------------------- | ------------------------------------------------------------ |
| Detection           | Auto-detects Graphite repos via `.git/.graphite_repo_config` |
| Commands            | Uses `gt` commands instead of `git` for commits/branches     |
| Stack planning      | Break features into atomic, reviewable PRs                   |
| Conflict resolution | Guidance for handling restack conflicts                      |
| SessionStart hook   | Context when entering a Graphite repo                        |


**Commit style:** Conventional commits (feat:, fix:, etc.), casual and concise, no LLM fluff.

**PR bodies:** What changed, why, and the benefit.

---

## License

This project is a fork of the original work by George Guimarães.

Original work:
Copyright (c) 2026 George Guimarães

Modifications:
Copyright (c) 2026 Benjamin Norquist

Licensed under the Apache License, Version 2.0. You may use, modify, and redistribute this project under the terms of that license.

This fork includes modifications from the original project. See the repository history for details.