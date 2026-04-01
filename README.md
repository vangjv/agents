# AI Agent System

A composable collection of specialist AI agents designed for software development workflows. Agents can operate standalone or be orchestrated together through the orchestrator agents.

## Agent Roster

### Specialist Agents

| Agent | Purpose | Output Format |
|-------|---------|---------------|
| **Coder** | Implements focused code changes and verifies builds | Markdown report |
| **C# Expert** | Provides expert C#/.NET guidance, patterns, and code | Markdown (advisory) |
| **Debug** | Diagnoses failures and localizes root causes | JSON diagnosis |
| **Docs** | Updates documentation after approved changes | Markdown report |
| **Integrator** | Applies approved changes to branches and manages git workflow | JSON status |
| **Planner** | Decomposes work items into task graphs with dependencies | JSON task graph |
| **Reviewer** | Inspects code changes and returns a verdict | JSON verdict |
| **Test** | Creates/updates tests and reports results | Markdown report |

### Orchestrator Agents

| Agent | Purpose | When to Use |
|-------|---------|-------------|
| **Generic Orchestrator** | General-purpose task decomposition and delegation | Multi-domain tasks spanning several specialist areas |
| **Plan Implementation Orchestrator** | Executes structured implementation plans in waves | When you have a written plan to implement |
| **Software Orchestrator** | Full SDLC pipeline with state machine | End-to-end feature/bugfix/refactor lifecycle |

## Architecture

```
         Orchestrator (Generic / Plan / Software)
                        │
    ┌───────┬───────┬───┴───┬───────┬───────┬───────┐
    ▼       ▼       ▼       ▼       ▼       ▼       ▼
 Planner  Coder  Reviewer  Test  Integrator Debug  Docs
```

- **Orchestrators** decompose, delegate, and synthesize — they never implement directly.
- **Specialists** are stateless workers — they receive a payload, execute, and return a result.
- **Specialists never invoke other specialists** — all routing is done by the orchestrator.

## Design Principles

1. **Single Responsibility** — Each agent has one clear job.
2. **Stateless Specialists** — Workers receive all context in their prompt; they have no memory between invocations.
3. **Self-Contained Prompts** — Every agent prompt must include all context the agent needs; never assume ambient knowledge.
4. **Explicit Boundaries** — Agents declare what they do and what they refuse to do.
5. **Observable Progress** — Orchestrators maintain visible state so users can track work.
6. **Fail Fast, Re-delegate** — On failure, diagnose and re-delegate with tighter constraints rather than retrying the same prompt.
7. **Technology Agnostic** — Generic agents adapt to the repository's stack; language-specific agents (e.g., C# Expert) are clearly labeled.

## Usage

Invoke any agent by name. Orchestrator agents accept work items or plans in natural language. Specialist agents accept focused task descriptions with relevant context.

### Standalone Specialist

Give the specialist all the context it needs: task description, relevant file contents, constraints, and expected output format.

### Orchestrated Workflow

Describe the work item to an orchestrator. It will decompose the task, dispatch specialists, handle failures, and synthesize the result.
