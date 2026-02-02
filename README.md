# gastown-parallel-workflow

A Claude Code plugin that bridges [Gastown](https://github.com/peterwarnock/gastown-demo)'s cross-session orchestration with in-session subagent parallelization.

## The Two-Level Model

**Gastown** distributes work across sessions:
- Mayor coordinates from main worktree
- Polecats work in isolated git worktrees
- Communication via beads issues and git

**Task subagents** parallelize within sessions:
- Multiple agents run simultaneously
- Share same worktree context
- Coordinate via Task tool

This skill teaches agents to leverage **both levels** for maximum throughput.

## Prerequisites

This plugin requires the following to be installed:

- **[beads](https://github.com/anthropics/beads)** - Git-backed issue tracking (provides `bd` CLI commands)
- **[superpowers](https://github.com/anthropics/superpowers)** - Agent workflow skills (provides `dispatching-parallel-agents`, `subagent-driven-development`)

Install beads:
```bash
/plugin install beads@beads-marketplace
```

## Installation

```bash
# Clone the plugin
git clone https://github.com/peterwarnock/gastown-parallel-workflow.git ~/github/gastown-parallel-workflow

# Install in Claude Code
cd ~/github/gastown-parallel-workflow
claude plugins install .
```

## Usage

The skill activates when:
- Coordinating multi-session work via Gastown
- Dispatching convoys with multiple issues
- Recognizing parallelization signals in beads issues

### Issue Templating

Use phase markers in issue descriptions:

```markdown
## Parallel Phase (dispatch subagents)
- [ ] Task A (independent)
- [ ] Task B (independent)

## Sequential Phase (after parallel completes)
- [ ] Task C (depends on A, B)
```

Workers recognize `## Parallel Phase` and use `dispatching-parallel-agents` skill.

### Mayor Dispatch

Include parallelization hints when dispatching Polecats:

```
PARALLELIZATION HINTS:
- Phase 1 issues are independent -> use superpowers:dispatching-parallel-agents
- Phase 2 issues have dependencies -> use superpowers:subagent-driven-development
```

## Integration

- **Gastown**: Convoy metadata, Mayor dispatch, Polecat work
- **Superpowers**: `dispatching-parallel-agents`, `subagent-driven-development`
- **Beads**: Issue descriptions, dependencies, labels

## License

MIT
