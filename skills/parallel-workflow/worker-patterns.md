# Worker Recognition Patterns

Patterns and checklists for Polecats (workers) to recognize and act on parallelization signals.

## Signal Detection Checklist

Run through this checklist when starting any issue:

### Step 1: Scan for Explicit Markers

```
[ ] Does issue have "## Parallel Phase" section?
    → YES: Use dispatching-parallel-agents for that section

[ ] Does issue have "## Sequential Phase" section?
    → YES: Use subagent-driven-development, wait for parallel

[ ] Are there "PARALLELIZATION HINTS" from Mayor?
    → YES: Follow the hints explicitly
```

### Step 2: Analyze Task Structure

```
[ ] List all tasks in the issue
[ ] For each task, note dependencies:
    - "depends on X" → sequential after X
    - "independent" or no deps → candidate for parallel
    - "can be done simultaneously" → parallel

[ ] Count independent tasks:
    - 3+ independent → strong candidate for dispatching-parallel-agents
    - 2 independent → marginal (consider overhead)
    - 0-1 independent → sequential or subagent-driven-development
```

### Step 3: Check for Hidden Dependencies

```
[ ] Will tasks edit the same files?
    → YES: Sequential (would conflict)

[ ] Does one task's output feed another's input?
    → YES: Sequential (dependency)

[ ] Do tasks need shared context to be correct?
    → YES: Sequential or single-agent

[ ] Are tasks in different subsystems?
    → YES: Good parallel candidate
```

## Strategy Decision Matrix

| Parallel Phase Marker | Independent Tasks | Strategy |
|-----------------------|-------------------|----------|
| Yes | 3+ | dispatching-parallel-agents |
| Yes | 2 | dispatching-parallel-agents (if overhead acceptable) |
| Yes | 1 | subagent-driven-development |
| No | 3+ | dispatching-parallel-agents |
| No | 2 | subagent-driven-development |
| No | 0-1 | Direct execution or single subagent |

## Execution Patterns

### Pattern A: Recognized Parallel Phase

```
1. Read issue
2. Identify "## Parallel Phase" section
3. List tasks in that section
4. For each task, prepare subagent prompt:
   - Task description
   - Constraints (files to touch, approach)
   - Expected output
5. Dispatch all subagents in single message (parallel)
6. Wait for all results
7. Proceed to "## Sequential Phase" if present
8. Complete issue
```

### Pattern B: Inferred Parallelization (No Markers)

```
1. Read issue
2. List all tasks
3. Build dependency graph:
   - Task A: no deps
   - Task B: no deps
   - Task C: depends on A, B
4. Group independent tasks (A, B)
5. If 3+ independent: use dispatching-parallel-agents
6. If 2 independent: evaluate overhead vs benefit
7. Execute parallel group
8. Execute dependent tasks sequentially
9. Complete issue
```

### Pattern C: Sequential with Review

```
1. Read issue
2. Tasks have dependencies or need shared context
3. Use subagent-driven-development:
   - Dispatch implementer for Task 1
   - Review Task 1
   - Dispatch implementer for Task 2
   - Review Task 2
   - ...
4. Complete issue
```

## Anti-Pattern Recognition

### Anti-Pattern: Ignoring Parallel Marker

```
BAD:
> I see "## Parallel Phase" but let me start with Task A first
> to understand the codebase...

GOOD:
> I see "## Parallel Phase" with 3 tasks.
> Using dispatching-parallel-agents immediately.
```

### Anti-Pattern: Serial by Default

```
BAD:
> I'll do these tasks one by one for safety.
[No analysis of whether tasks are independent]

GOOD:
> Let me analyze task dependencies first.
> Tasks A, B, C are independent (no shared state).
> Using dispatching-parallel-agents.
```

### Anti-Pattern: Sunk Cost Continuation

```
BAD:
> I already started Task A sequentially.
> Might as well continue with B and C the same way.

GOOD:
> I started Task A but realize B and C are independent.
> Dispatching parallel subagents for B and C while A completes.
```

## Result Reporting Format

When completing parallel work:

```markdown
## Parallel Phase Complete

### Subagents Dispatched
- Agent 1: Task A → SUCCESS
- Agent 2: Task B → SUCCESS
- Agent 3: Task C → SUCCESS

### Results Summary
| Task | Status | Changes | Notes |
|------|--------|---------|-------|
| A | Done | model.ts:+15,-3 | Added field |
| B | Done | schema.ts:+8,-0 | Added validation |
| C | Done | api.ts:+22,-5 | New endpoint |

### Conflicts
None - tasks were truly independent.

### Proceeding to Sequential Phase
[Yes/No]
```

## Integration Checkpoints

### Before Starting Issue

```
[ ] Read issue completely
[ ] Ran signal detection checklist
[ ] Identified parallelization strategy
[ ] Prepared subagent prompts (if parallel)
```

### During Parallel Execution

```
[ ] All subagents dispatched in single message
[ ] Monitoring for conflicts
[ ] Ready to intervene if issues arise
```

### After Parallel Completion

```
[ ] All subagents returned results
[ ] Checked for merge conflicts
[ ] Verified changes don't conflict
[ ] Proceeding to sequential phase (if any)
```

### Before Completing Issue

```
[ ] All phases complete
[ ] Tests passing
[ ] Ready for finishing-a-development-branch
```

## Emergency Procedures

### Subagent Timeout

```
1. Note which subagent(s) timed out
2. Collect completed results
3. Dispatch replacement for timed out task
4. Or complete manually if simpler
```

### Unexpected Conflict

```
1. Stop parallel execution
2. Identify conflicting changes
3. Determine which change is correct
4. Apply correct change, discard other
5. Update issue to note hidden dependency
6. Continue sequentially for affected tasks
```

### Wrong Strategy Detected Mid-Work

```
1. Complete current in-flight work
2. Re-evaluate remaining tasks
3. Switch strategy for remaining work
4. Document the pivot
```
