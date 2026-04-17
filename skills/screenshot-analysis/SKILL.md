---
name: screenshot-analysis
description:
  Use when any agent needs to capture, analyze, or understand visual page state
  — including taking screenshots, checking what a page looks like, verifying
  visual layout, diagnosing UI failures, or comparing before/after states.
  Triggers include "take a screenshot", "what does the page look like",
  "check the page state", "analyze the screenshot", "verify the UI",
  "what's on screen", or any task requiring visual page understanding.
---

# Screenshot Analysis Skill

**Do NOT capture or analyze screenshots yourself.** Delegate all screenshot and
visual analysis work to the **`screenshot-analyzer`** subagent.

## Why

Screenshots as raw images consume ~5,000–20,000+ tokens per image in your
context window. The `screenshot-analyzer` subagent captures the screenshot, saves
it to disk, analyzes the page structure, and returns a **~200 token text
summary** — saving 25–100x the token cost.

## How to Use

Call `runSubagent` with `agentName: "screenshot-analyzer"` and a prompt
describing what you need:

### Full Page Analysis

```
runSubagent("screenshot-analyzer")
Prompt: "Analyze the current page state.
  URL: current
  Context: {why you need this — e.g., 'Just navigated to the dashboard, need to confirm layout'}"
```

### Element-Focused Analysis

```
runSubagent("screenshot-analyzer")
Prompt: "Analyze a specific area of the page.
  Element: {description or CSS selector}
  Context: {what you need to know about this element}"
```

### Before/After Comparison

```
runSubagent("screenshot-analyzer")
Prompt: "Compare current state with expected state.
  Expected: {what should be on screen}
  Context: {what action was just performed}"
```

### Failure Diagnosis

```
runSubagent("screenshot-analyzer")
Prompt: "Analyze why a test action may have failed.
  Expected: {what the test expected}
  Actual error: {error message}
  URL: {page URL}"
```

## What You Receive Back

A structured text summary (~100–300 tokens) containing:

- **Page identity** — URL, title, loaded/loading/error state
- **Layout** — Major sections visible on screen
- **Key content** — Headings, labels, data values, counts
- **Interactive elements** — Form state, button state, with `@e{n}` refs
- **Errors/Alerts** — Validation messages, error banners, toasts
- **Notable** — Direct answer to your specific question

The screenshot image file is saved to disk — the file path is included in the
summary for reference in reports.

## Rules

1. **NEVER** use `agent-browser screenshot` and then try to view the image
   yourself — always delegate to `screenshot-analyzer`
2. **NEVER** ask for raw image data or base64 — the subagent only returns text
3. **DO** include context about what you're trying to understand — the subagent
   tailors its analysis to your question
4. **DO** include the text summary in your reports/artifacts — it's compact
   enough to forward to downstream agents
