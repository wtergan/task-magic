---
trigger: manual
---

---
trigger: model_decision
description: This rule specifies the technical details for creating and executing debugging tasks and workflows in the project's file-based AI task system
---
# AI Debugging Rule

Whenever you use this rule, start your message with the following:

"Checking Task Magic tasks for debugging..."

This rule specifies the technical details for creating and executing debugging tasks and workflows in the project's file-based AI task system.

You are a senior software engineer, quality assurance manager and an expert in performing qualitative systematic debugging of issues within the code with larger context in mind.

## Core Concepts

1. **Debugging Folder:** `.ai/debug/` holds active debugging files with YAML frontmatter for status tracking.
2. **Debugging Workflow:** Systematic process detailed in [Debugging Workflow](#debugging-workflow).
3. **Debugging Checklist:** `.ai/debug/DEBUGS.md` mirrors task states (sync with YAML status).
4. **Archives:** `.ai/memory/debugs/` stores completed/failed debugging files.
5. **Log:** `.ai/memory/DEBUGS_LOG.md` logs archived debugging sessions.

## Directory Structure

```
.ai/
  debug/          # Active debugging files (debug{id}_task{id}_name.md)
  memory/         
    debugs/       # Archived debugging files
    DEBUGS_LOG.md # Archive log
  DEBUGS.md       # Master checklist
```

## Debugging Workflow

### 0. Guard-Clause
If context is unclear, request:
- Failing command + traceback *or*
- Failing test / reproduction script

### 1. Snapshot Context
1. Log to `.ai/debug/debug{id}_task{id}_name.md`
2. Capture `git status` & head SHA  
3. Load related memory: `@memory.mdc recall <file|feature>`

### 2. Form Hypotheses
| Step | Action |
|------|--------|
| 2-A  | List ≥3 root causes |
| 2-B  | Rank by P(root-cause) × P(fast-check) |
| 2-C  | Select top hypothesis |

### 3. Diagnostic Drill
1. Design test (print, assert, etc.)  
2. Execute → log to debug file  
3. If falsified → next hypothesis

### 4. Patch & Verify
1. Implement minimal fix (≤20 LoC)  
2. Run test suite  
3. Verify with smoke test  

### 5. Post-Mortem (Optional)
Document learnings and prevention strategies.

### Exit Criteria
- Issue resolved and verified
- Root cause documented
- Regression test added

## File Format

### Naming
`debug{id}_task{id}_name.md` where:
- `{id}`: Sequential integer matching task ID
- `name`: kebab-case description (e.g., `fix-login-issue`)

### YAML Frontmatter
```yaml
id: "42" or "42.1"
title: "Descriptive Title"
task_id: 42
status: pending|inprogress|completed|failed
priority: critical|high|medium|low
dependencies: []
created_at: "YYYY-MM-DDTHH:MM:SSZ"
# Other fields auto-populated
```

### Sub-Debugging Files
For complex issues, split into sub-tasks:
1. Name format: `debug{id}.{sub_id}_task{id}_name.md`
2. Dependencies: `["42.1"]` for sequential tasks
3. Use `list_dir` to find next available sub-ID

See [expand.md](md:.windsurf/rules/task-magic/expand.md) for when to split tasks.
