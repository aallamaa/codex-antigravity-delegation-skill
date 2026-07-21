# Antigravity Delegation skill for Codex

A Codex skill that delegates substantial, well-bounded engineering work to the
local Antigravity CLI (`agy`) while keeping architecture, integration review,
and final verification in Codex.

It includes:

- delegation guidance and safety boundaries;
- a launcher that selects a model and timeout, serializes Antigravity jobs, and
  supports read-only or write-enabled missions;
- a helper that reads the interactive Antigravity quota panel.

## Prerequisites

- Codex with the built-in `skill-installer` skill;
- the `agy` CLI installed, authenticated, and available on `PATH`;
- `bash` for the delegation launcher;
- `expect`, `script`, Perl, and Python 3 for the optional quota helper.

The launcher defaults to Gemini 3.6 Flash (High), using the canonical
`gemini-3.6-flash-high` model ID reported by `agy models`. Otherwise, pass an
available model explicitly.

## Install in Codex

Paste this prompt into Codex:

```text
Use the built-in skill-installer skill to install the public GitHub skill from:
https://github.com/jhiver/codex-antigravity-delegation-skill/tree/main/skills/antigravity-delegation

Verify that SKILL.md is valid, install the complete skill under ~/.codex/skills,
including its bundled scripts, and tell me when it is available.
```

Alternatively, run the bundled installer directly:

```bash
python3 ~/.codex/skills/.system/skill-installer/scripts/install-skill-from-github.py \
  --repo jhiver/codex-antigravity-delegation-skill \
  --path skills/antigravity-delegation

chmod +x ~/.codex/skills/antigravity-delegation/scripts/agy-delegate \
  ~/.codex/skills/antigravity-delegation/scripts/agy-usage
```

The skill becomes available to Codex on the next turn. If a directory already
exists at `~/.codex/skills/antigravity-delegation`, move or remove it before
installing; the installer intentionally refuses to overwrite existing skills.

## Use

Ask Codex explicitly:

```text
Use $antigravity-delegation to delegate this bounded engineering task.
```

Codex can also invoke the skill automatically for broad repository exploration,
implementation from an agreed design, test writing, repetitive refactoring,
documentation synchronization, and bounded review work.

The included launcher can be called directly:

```bash
bash ~/.codex/skills/antigravity-delegation/scripts/agy-delegate \
  --kind implement \
  --complexity medium \
  --mission /absolute/path/to/mission.md \
  --cwd /absolute/path/to/repository \
  --write
```

Inspect quota availability with:

```bash
bash ~/.codex/skills/antigravity-delegation/scripts/agy-usage
```

## Safety

The launcher auto-approves Antigravity tool prompts so unattended jobs do not
hang. In read-only mode, the mission instructions are the primary guardrail; in
write mode, Antigravity is additionally allowed to edit the workspace. Review
the mission carefully, keep it narrowly scoped, inspect every diff, and run the
verification commands independently before accepting the result.

The skill instructs Codex not to delegate secrets, production operations,
destructive actions, releases, commits, or pushes unless the user explicitly
authorizes that exact action.
