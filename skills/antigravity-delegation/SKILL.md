---
name: antigravity-delegation
description: Delegate substantial bounded engineering work to the local Antigravity CLI (`agy`) to conserve Codex tokens and exploit the user's Antigravity Ultra quota. Use for repository exploration, inventory, mechanical analysis, implementation from a clear architecture/specification, test writing, documentation, and bounded review work. Also use when choosing an Antigravity model, checking Antigravity quota, preparing a mission file, or recovering from an Antigravity timeout or partial delivery.
---

# Delegate to Antigravity

Keep architectural judgment, task decomposition, acceptance criteria, integration review, and final verification in Codex. Delegate the largest well-bounded execution units to `agy`.

## Decide whether to delegate

Delegate when a unit is expected to take more than about five minutes, requires broad repository reading, produces several files/tests, or can run from an explicit plan. Prefer delegation for:

- repository exploration and inventories;
- implementing a specified design;
- writing or expanding tests;
- repetitive refactors and migrations;
- documentation synchronization;
- a second review pass.

Work locally when the change is trivial, the task is mostly ambiguous product/architecture judgment, the mission cannot be bounded safely, or delegating would cost more context than doing it. Do not delegate secret handling, production operations, destructive actions, releases, commits, pushes, or final acceptance unless the user explicitly authorizes that exact action.

## Grade the unit

- `low`: mechanical, localized, obvious acceptance criteria.
- `medium`: several files or nontrivial tests, but architecture is already decided.
- `high`: broad implementation or difficult investigation with a precise boundary. Split first if independent pieces exist.

Antigravity processes may run concurrently when they operate in distinct
worktrees or read-only scopes. Never run concurrent write missions in the same
worktree; serialize dependent missions whose output feeds the next mission.

## Prepare the mission

Create a mission Markdown file inside the target repository, normally under `.codex/agy-missions/`. Include:

1. objective and non-goals;
2. exact working directory and relevant files/lines;
3. architecture and constraints already decided by Codex;
4. required edits or read-only scope;
5. tests and verification commands;
6. forbidden actions, including no commit/push by default;
7. requested final report: files changed, commands run, failures, uncertainties.

Keep the CLI prompt short; pass only the mission path. Long inline prompts have caused silent failures.

## Run

Use the bundled launcher:

```bash
bash ~/.codex/skills/antigravity-delegation/scripts/agy-delegate \
  --kind explore|implement|tests|review|docs \
  --complexity low|medium|high \
  --mission /absolute/path/to/mission.md \
  --cwd /absolute/path/to/repository \
  [--write] [--model "exact model ID"] [--timeout 60m]
```

Omit `--write` for analysis, exploration, and review. The launcher auto-approves Antigravity's tool prompts in print mode to prevent unattended hangs; the mission's read-only scope is therefore the guardrail. With `--write`, it additionally uses Antigravity's `accept-edits` mode. Only use `--write` when the requested repository edits are already authorized.

The launcher resolves the preferred Gemini Flash high-effort model from `agy models` at runtime and passes its model ID (currently preferring Gemini 3.6 Flash (High), then Gemini 3.5 Flash (High)). An explicit `--model` may be either the advertised ID or its exact display name. Complexity still controls task splitting and timeout. Verify current availability with `agy models`.

## Inspect quota

Run:

```bash
bash ~/.codex/skills/antigravity-delegation/scripts/agy-usage
```

Quota is exposed only through the interactive `/usage` panel in the installed CLI, not a documented non-interactive subcommand. The helper drives that panel in a pseudo-terminal and cleans the output. Treat parsing failure as non-blocking; fall back to opening `agy` and entering `/usage`.

## Integrate and verify

After every run:

1. inspect exit status and the complete report;
2. inspect `git status` and the diff, including pre-existing user changes;
3. assume a timeout may have left partial files;
4. run the relevant verification independently in Codex;
5. fix or re-delegate a narrowly rewritten mission;
6. never accept Antigravity's claim of success without evidence.

Codex remains responsible for the final answer and for explaining what was delegated and verified.
