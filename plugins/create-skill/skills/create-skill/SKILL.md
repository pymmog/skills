---
name: create-skill
description: Scaffold a new skill in this dual-ecosystem marketplace for Claude Code and Codex CLI by prompting the developer for required fields, creating a per-skill plugin directory with both manifests, writing SKILL.md, and registering the plugin in both marketplaces. Use when the user says "add a skill", "new skill", "create a skill", "scaffold a skill", "/new", "/create-skill", or asks how to contribute a skill to this repo.
---

# create-skill

Conversational scaffolder for new skills in this repo. Each skill is its own plugin so users can toggle them independently.

Layout: every skill lives under `plugins/<name>/` with its own `.claude-plugin/plugin.json`, `.codex-plugin/plugin.json`, and `skills/<name>/SKILL.md`. The two marketplace files (`.claude-plugin/marketplace.json` for Claude Code and `.agents/plugins/marketplace.json` for Codex CLI) list every plugin.

## When to use

- User wants to add a new skill to this repo.
- User asks "how do I add a skill", "/new", or "/create-skill".
- Triggered from inside this repo, where CWD contains `.claude-plugin/marketplace.json` and `.agents/plugins/marketplace.json`.

If working dir is not this repo, stop and tell the user to `cd` into it first.

## Steps

1. Sanity-check location. Confirm `.claude-plugin/marketplace.json` and `.agents/plugins/marketplace.json` exist in CWD. If either is missing, abort with a clear message.
2. Collect fields from the user: `name`, `long_description`, `short_description`, `trigger_phrases`, and `steps`.
3. Validate `name` as kebab-case with `^[a-z][a-z0-9-]*$`. Reject if `plugins/<name>` already exists.
4. Create `plugins/<name>/.claude-plugin`, `plugins/<name>/.codex-plugin`, and `plugins/<name>/skills/<name>`.
5. Write `plugins/<name>/skills/<name>/SKILL.md` with only `name` and `description` in YAML frontmatter.
6. Write matching `plugins/<name>/.claude-plugin/plugin.json` and `plugins/<name>/.codex-plugin/plugin.json` manifests using `skills: "./skills/"`.
7. Register the plugin in `.claude-plugin/marketplace.json` with `source: "./plugins/<name>"`.
8. Register the plugin in `.agents/plugins/marketplace.json` with local source path `./plugins/<name>`, policy `AVAILABLE` and `ON_INSTALL`, and category `Productivity`.
9. Validate JSON formatting and the skill frontmatter before reporting completion.

## Output

- New `plugins/<name>/` tree with `SKILL.md` and both ecosystem manifests.
- Updated `.claude-plugin/marketplace.json`.
- Updated `.agents/plugins/marketplace.json`.
- A short summary with install and test commands.

## Guardrails

- Never overwrite an existing `plugins/<name>` directory.
- Never commit or push.
- If JSON parse of a marketplace file fails, abort and surface the error.
- Keep frontmatter `description` on a single logical line.
- Each plugin owns its own `version`; do not bump unrelated plugin versions.
