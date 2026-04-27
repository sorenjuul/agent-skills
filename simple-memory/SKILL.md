---
name: simple-memory
description: 'Persistent memory and insights across sessions. When triggered, verify whether ~/.agents/simple-memory/insights.md was already loaded into the current session. If it was not, detect the active AI coding tool, configure an appropriate user-level startup mechanism so future sessions auto-load it, and then continue memory operations.'
allowed-tools: Read,Write,Edit,Bash,Grep
---

# Simple Memory — Persistent Cross-Session Insights

## Overview

This skill gives you persistent memory across sessions. Insights are stored in `~/.agents/simple-memory/insights.md`.

The canonical storage path is `~/.agents/simple-memory/insights.md`.

## Session Startup — Hook-First Auto-Load

**At the start of EVERY session, treat memory loading as a hook/bootstrap concern, not a manual first step:**

1. Assume `~/.agents/simple-memory/insights.md` should already have been loaded into context by an appropriate user-level startup mechanism for the active AI coding tool.
2. When this skill is triggered, first determine whether those insights are already present in the current session context.
    - Use evidence from the current conversation, system context, or verified session/tool state.
    - Do **not** blindly read the file just to simulate startup behavior.
3. If the insights are already in context, continue silently and use them.
4. If the insights are **not** in context, detect which AI coding tool is currently in use.
5. Once the tool is identified, find the supported startup mechanism for that tool and configure it so future sessions automatically load `~/.agents/simple-memory/insights.md` into context.
6. Add the required wiring to the tool's **user-level config**.
7. Only after that bootstrap is done, read `~/.agents/simple-memory/insights.md` for the current session **if needed** to complete the user's memory request.
8. Do NOT announce any of this unless the user asks. Just silently apply it.

## Tool Detection & Bootstrap Rules

### General Rules

- Verify the active tool from real evidence: config files, runtime identity, tool-specific directories, or explicit system context.
- Prefer a **user-level** startup/config change over project-level changes.
- Make the smallest persistent change that guarantees future sessions load memory automatically.
- If the tool supports synthetic/hidden context injection, use that instead of adding visible chat noise.
- If a tool does not support startup hooks/plugins, say so explicitly and fall back to manual loading only after telling the user.

## Remembering — When the User Says "Remember..."

**Trigger phrases:** "remember that...", "keep in mind...", "note that...", "don't forget...", "save this insight...", "remember:"

### Workflow

1. **Extract the core insight** from what the user said. Strip conversational fluff.
2. **Reformulate** into a concise, actionable statement that your future self can use without additional context. Write it as an instruction to yourself.
    - BAD: "The user mentioned they like tabs over spaces"
    - GOOD: "Use tabs for indentation — user preference."
    - BAD: "There was a bug in the auth module last Tuesday"
    - GOOD: "auth module: watch for race condition in token refresh — previously caused silent failures."
3. **Categorize** the insight into the appropriate section (see file format below).
4. **Append** the insight to the correct section in `~/.agents/simple-memory/insights.md` using the Edit tool.
5. **Confirm** briefly: "Noted." or "Remembered." — keep it short.

### Reformulation Rules

- Write in **imperative/instructional tone** — these are instructions to your future self.
- Include **why** when it matters (not just what).
- Be **specific** — vague insights are useless. Include file paths, names, versions when relevant.
- Keep each insight to **1-2 lines max**.
- **Deduplicate** — if a similar insight already exists, update it instead of adding a duplicate.
- **Timestamp** each insight with the date it was added (YYYY-MM-DD).

## Forgetting — When the User Says "Forget..."

**Trigger phrases:** "forget about...", "remove the memory about...", "delete the insight about...", "that's no longer true..."

1. Find the matching insight(s) in the file.
2. Remove them using the Edit tool.
3. Confirm: "Forgotten." or "Removed."

## Recalling — When the User Asks "What Do You Remember?"

**Trigger phrases:** "what do you remember about...", "what insights do you have on...", "show me your memories", "what do you know about..."

1. Read the insights file.
2. Filter to the relevant section/topic if the user specified one.
3. Present the insights in a clean format.

## File Format — `~/.agents/simple-memory/insights.md`

```markdown
# Agent Memory

> Persistent insights accumulated across sessions. Auto-loaded at session start.

## User Preferences

<!-- How the user likes to work: style, tools, communication, conventions -->

## Codebase Knowledge

<!-- Hard-won insights about specific codebases, gotchas, patterns -->

## Technical Notes

<!-- General technical knowledge worth remembering: APIs, libraries, patterns -->

## Project Context

<!-- Ongoing project state: what's in progress, decisions made, blockers -->

## People & Teams

<!-- Who works on what, communication preferences, org context -->
```

### Section Guidelines

| Section | What goes here | Example |
|---|---|---|
| User Preferences | Work style, tool choices, communication style | "Prefers terse responses. Uses conventional commits." |
| Codebase Knowledge | Repo-specific gotchas, patterns, architecture | "lbst-shared: caseworker components extend customer — changes ripple." |
| Technical Notes | Library quirks, API patterns, useful techniques | "ngx-mask v16: use NgxMaskDirective standalone, not the module." |
| Project Context | Current work state, decisions, blockers | "Auth refactor in progress — waiting on API team for new endpoints." |
| People & Teams | Org knowledge, who owns what | "Platform team owns CI pipeline config." |

## Important Rules

- **Never store secrets, passwords, tokens, or PII** in the insights file.
- **Never store temporary/ephemeral information** (e.g., "currently debugging X" — that's session-local).
- **Insights must be durable** — they should still be useful weeks or months from now.
- **Update over duplicate** — if an insight evolves, edit the existing one rather than adding a new line.
- The insights file path is always `~/.agents/simple-memory/insights.md` (resolved to the user's home directory).