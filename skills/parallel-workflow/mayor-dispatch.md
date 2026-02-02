# Mayor Dispatch Patterns

Templates and patterns for Mayor when dispatching Polecats with parallelization awareness.

## Convoy Creation Template

When analyzing issues to form a convoy:

```markdown
## Convoy Analysis

### Issue Inventory
| Issue | Description | Dependencies | Parallel Potential |
|-------|-------------|--------------|-------------------|
| bp-XXX | ... | none | high - has 3+ independent tasks |
| bp-YYY | ... | none | medium - 2 parallel, 1 sequential |
| bp-ZZZ | ... | bp-XXX | low - depends on bp-XXX |

### Phase Structure

**Phase 1 (Parallel - dispatch simultaneously)**
- bp-XXX: [description] - PARALLEL POTENTIAL: high
- bp-YYY: [description] - PARALLEL POTENTIAL: medium

**Phase 2 (Sequential - after Phase 1)**
- bp-ZZZ: [description] - depends on bp-XXX
```

## Sling Command with Hints

When dispatching a Polecat:

```bash
sling bp-XXX --worktree feature/bp-XXX --message "$(cat <<'EOF'
Work on bp-XXX.

PARALLELIZATION HINTS:
- This issue has "## Parallel Phase" section
- Use superpowers:dispatching-parallel-agents for parallel phase tasks
- Use superpowers:subagent-driven-development for sequential phase
- Report when parallel phase completes before starting sequential
EOF
)"
```

## Dispatch Prompt Templates

### Template A: Issue with Parallel Phase

```markdown
# Polecat Assignment: bp-XXX

## Your Issue
[Paste full issue description]

## Parallelization Guidance

This issue contains a `## Parallel Phase` section. You should:

1. **Recognize the signal**: `## Parallel Phase` means use dispatching-parallel-agents
2. **Dispatch subagents** for each task in the Parallel Phase
3. **Wait for all** parallel tasks to complete
4. **Then proceed** to Sequential Phase (if any)

**REQUIRED:** Read superpowers:dispatching-parallel-agents before starting

## Expected Workflow
1. Read issue thoroughly
2. Identify Parallel Phase tasks
3. Dispatch one subagent per task (use Task tool with parallel calls)
4. Collect results
5. Proceed to Sequential Phase
6. Use finishing-a-development-branch when done
```

### Template B: Issue without Phase Markers

```markdown
# Polecat Assignment: bp-XXX

## Your Issue
[Paste full issue description]

## Parallelization Analysis

This issue doesn't have explicit phase markers. Analyze it:

1. **Count tasks**: How many distinct tasks?
2. **Check dependencies**: Which tasks depend on others?
3. **Identify parallel opportunities**: 3+ independent tasks?

**Decision tree:**
- 3+ independent tasks → use dispatching-parallel-agents
- Tasks with dependencies → use subagent-driven-development
- Simple sequential → proceed directly

## Expected Workflow
1. Analyze task structure
2. Choose parallelization strategy
3. Execute with appropriate skill
4. Use finishing-a-development-branch when done
```

### Template C: Phase-Gated Convoy

```markdown
# Convoy Dispatch: Phase {N}

## Ready Issues
These issues can start now (dependencies resolved):
- bp-XXX: [description]
- bp-YYY: [description]

## Parallelization Hints

**For bp-XXX:**
- Contains "## Parallel Phase" → use dispatching-parallel-agents internally
- Estimated 3 parallel tasks, then 1 sequential

**For bp-YYY:**
- Sequential work only → use subagent-driven-development
- No parallel opportunities identified

## Phase Completion Gate
- All Phase {N} issues must complete before Phase {N+1} starts
- Report completion to Mayor when done
- Include summary of changes made

## Next Phase Preview
After this phase, Phase {N+1} will dispatch:
- bp-ZZZ: [description] - depends on bp-XXX
```

## Multi-Polecat Coordination

When multiple Polecats work simultaneously:

```markdown
# Convoy Status Update

## Active Polecats
| Polecat | Worktree | Issue | Status | Parallelization |
|---------|----------|-------|--------|-----------------|
| P1 | feature/auth | bp-123 | working | 3 subagents active |
| P2 | feature/profiles | bp-124 | working | sequential |
| P3 | feature/settings | bp-125 | waiting | blocked on bp-123 |

## Expected Completion Order
1. bp-124 (sequential, simple) - likely first
2. bp-123 (parallel phase active) - after subagents complete
3. bp-125 (waiting) - after bp-123

## Coordination Notes
- P1 using parallel subagents - may have multiple commits
- P2 straightforward - single commit expected
- P3 will be unblocked when P1 completes
```

## Result Collection Template

When Polecats complete:

```markdown
# Polecat Result: bp-XXX

## Summary
[What was accomplished]

## Parallelization Used
- **Strategy**: dispatching-parallel-agents
- **Subagents dispatched**: 3
- **Parallel tasks**: Task A, Task B, Task C
- **Sequential tasks**: Task D (after parallel)

## Changes Made
- [List of changes with file paths]

## Commits
- abc1234: Add field A to model
- def5678: Add field B to model
- ghi9012: Add field C to model
- jkl3456: Update queries to use new fields

## Verification
- Tests: [X passing / Y failing]
- Lint: [clean / issues]
- Types: [clean / issues]

## Ready for Next Phase
[Yes/No - if dependencies resolved]
```

## Error Handling

### Polecat Stuck Sequential

If a Polecat reports working sequentially despite parallel opportunity:

```markdown
# Intervention: bp-XXX

## Observation
You appear to be working sequentially on tasks that could be parallel.

## Check
Does your issue have:
- `## Parallel Phase` marker?
- 3+ independent tasks?

If yes, consider:
1. Stop current sequential work
2. Use dispatching-parallel-agents
3. Dispatch subagents for remaining independent tasks

## Rationale
Parallel execution completes faster with no loss of quality when tasks are truly independent.
```

### Subagent Conflicts

If parallel subagents report conflicts:

```markdown
# Conflict Resolution: bp-XXX

## Conflict Detected
Subagents A and B both modified [file].

## Resolution Steps
1. Stop parallel execution
2. Review changes from both agents
3. Merge manually or sequentially
4. Update issue to mark these tasks as sequential

## Lesson Learned
These tasks have hidden dependencies. Update issue template to reflect this.
```
