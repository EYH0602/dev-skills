# My Dev Workflow

## Code Review Skills

Agent-agnostic skills for automated code review, following the [Agent Skills specification](https://agentskills.io/specification).

### Why

Existing code review tools are tightly coupled to specific agent platforms. Claude Code ships two review plugins — [code-review](https://github.com/anthropics/claude-code/tree/main/plugins/code-review) and [pr-review-toolkit](https://github.com/anthropics/claude-code/tree/main/plugins/pr-review-toolkit) — but they only work within Claude Code's plugin system (its agent definitions, model routing, and GitHub CLI integration).

We want code review that works across any AI agent: Claude Code, Codex, Cursor, or custom setups. Skills that describe *what to check and how to score* rather than *which tool to call*.

### Design Principles

#### Parallel specialized reviewers

Instead of one reviewer trying to catch everything, we dispatch parallel subagents each focused on one aspect. Aspects are selectively included based on what actually changed.

#### Confidence-based filtering

Every finding gets a 0-100 confidence score. Only findings above threshold are reported. This eliminates the noise that makes automated reviews useless: pre-existing issues, nitpicks, things linters catch, and "maybe" concerns.

#### Progressive disclosure

Skills use the agentskills progressive disclosure pattern:

1. **Metadata** (~100 tokens) — `name` and `description` loaded at startup for skill matching
2. **SKILL.md** (~200 lines) — orchestration workflow, loaded when the skill activates
3. **references/** (~60 lines each) — detailed reviewer prompts, loaded only when dispatching a specific reviewer

#### Agent-agnostic

Skills describe review methodology in plain language — no platform-specific APIs, tool names, or model choices. Any agent that can read a diff, dispatch subagents, and post results can follow them.

---

## Skills

### pr-code-review

Comprehensive pull request review using 6 parallel specialized reviewers with confidence threshold 75.

| Reviewer | Focus |
|----------|-------|
| Bug Detection | Logic errors, security, race conditions |
| Error Handling | Silent failures, catch blocks, fallbacks |
| Type Design | Invariants, encapsulation, illegal states |
| Test Coverage | Missing tests, critical path gaps |
| Comment Quality | Accuracy, staleness, misleading docs |
| Guidelines Compliance | Project-specific rules (CLAUDE.md, etc.) |

```
pr-code-review/
├── SKILL.md                          # Orchestration workflow
└── references/
    ├── bug-detection.md              # Bug/security reviewer prompt
    ├── error-handling.md             # Silent failure hunter prompt
    ├── type-design.md                # Type invariant analyzer prompt
    ├── test-coverage.md              # Test gap analyzer prompt
    ├── comment-quality.md            # Comment accuracy reviewer prompt
    └── guidelines-compliance.md      # Project rules compliance prompt
```

`pr-code-review/` works great with [receiving-code-review](https://github.com/obra/superpowers/tree/main/skills/receiving-code-review) from superpowers — it teaches the agent how to critically evaluate review feedback before blindly applying suggestions.

## Installation

Install with [skillshub](https://github.com/EYH0602/skillshub), which syncs skills across all your agents (Claude Code, Codex, Cursor, etc.):

```bash
skillshub tap add -i https://github.com/EYH0602/dev-skills
```

Skills activate automatically based on their description triggers.
