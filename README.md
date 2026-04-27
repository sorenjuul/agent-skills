# agent-skills

This repository contains custom agent skills.

## Contents

- `simple-memory/` - a skill for persistent cross-session memory and insight storage.
- `simple-memory/SKILL.md` - the skill definition and operating instructions for the `simple-memory` skill.

## Purpose

The repo is intended to hold reusable skill definitions that can be loaded by an AI coding agent. Each skill lives in its own directory and provides its own `SKILL.md` file describing behavior, triggers, and constraints.

## Install

The recommended install method is the one described on [skills.sh](https://skills.sh):

```bash
npx skills add sorenjuul/agent-skills
```

This installs the skills from this repository for supported agents.

To install only the `simple-memory` skill:

```bash
npx skills add sorenjuul/agent-skills --skill simple-memory
```

## Local development install

For local development, you can also link a skill directory directly into your user skills directory:

```bash
ln -s "$(pwd)/simple-memory" ~/.agents/skills/simple-memory
```

Or copy it instead:

```bash
cp -r ./simple-memory ~/.agents/skills/
```

Installed skills are discovered from `~/.agents/skills/`.

## Usage

After installation, invoke a skill by name from your agent environment.

Current skill in this repository:

- `simple-memory` - persistent cross-session memory and insight storage.

### `simple-memory`

Use `simple-memory` when you want the agent to retain durable, cross-session notes such as user preferences, codebase gotchas, project context, or team ownership.

For initial setup, use a capable model such as GPT 5.4 or Claude Opus 4.6.

After installing the skill, save an initial memory and then trigger the skill:

```text
Remember that I prefer concise commit messages
```

```text
/simple-memory
```

This performs the setup so future sessions automatically load memories into context at startup.

After setup, use natural phrasing such as:

- `Remember that ...`
- `Keep in mind that ...`
- `Note that ...`
- `Don't forget that ...`
- `What do you remember about ...?`
- `Show me your memories`
- `Forget ...`
- `Remove the memory about ...`

The skill stores insights in `~/.agents/simple-memory/insights.md` and is designed for durable notes that remain useful over time.

Do not use it for secrets, tokens, passwords, or short-lived session-only details.

See `simple-memory/SKILL.md` for the full trigger phrases, workflow, and constraints.
