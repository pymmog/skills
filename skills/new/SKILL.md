---
name: new
description: Scaffold a new skill in this dual-ecosystem marketplace (Claude Code + Codex CLI) by prompting the developer for required fields, creating the skill directory, writing SKILL.md with valid frontmatter, registering it in .claude-plugin/marketplace.json, and bumping the version in .codex-plugin/plugin.json. Use when the user says "add a skill", "new skill", "create a skill", "scaffold a skill", "/new", or asks how to contribute a skill to this repo.
---

# new

Conversational scaffolder for new skills in this repo. Collects fields via prompts, writes `SKILL.md`, registers the plugin in the Claude marketplace, bumps Codex plugin version, validates.

`SKILL.md` is shared across both ecosystems. Claude side requires a per-plugin entry in `marketplace.json`; Codex side auto-discovers via the `skills:` glob in `plugin.json` but benefits from a version bump so devs get the update on `/plugins update`.

## When to use

- User wants to add a new skill to this repo.
- User asks "how do I add a skill" or "/new".
- Triggered from inside this repo (working dir contains both `.claude-plugin/marketplace.json` and `.codex-plugin/plugin.json`).

If working dir is not this repo, stop and tell the user to `cd` into it first.

## Steps

1. **Sanity-check location.** Confirm both `.claude-plugin/marketplace.json` and `.codex-plugin/plugin.json` exist in CWD. If either is missing, abort with a clear message.

2. **Collect fields via `AskUserQuestion`** (one tool call, multiple questions). Required:
   - `name` — kebab-case, unique. Validate: `^[a-z][a-z0-9-]*$`. Reject if dir already exists or name already in `marketplace.json`.
   - `short_description` — one-line summary for `marketplace.json` (≤120 chars).
   - `long_description` — full description for `SKILL.md` frontmatter. Must explain *what*, *when to auto-invoke*, and list example trigger phrases. This is what Claude reads to decide when to fire — be specific.
   - `trigger_phrases` — comma-separated examples (e.g. `"run lint", "check style", "/lint-check"`). Will be folded into the description if not already present.
   - `steps` — short bullet list of what the skill does (free text; user can say "I'll write it later" to get a stub).

   Use free-text via the "Other" option for description/steps fields since they're long.

3. **Validate name.**
   - Regex `^[a-z][a-z0-9-]*$`.
   - `test ! -d skills/<name>` — abort if dir exists.
   - `grep -q "\"name\": \"<name>\"" .claude-plugin/marketplace.json` — abort if registered.

4. **Create skill directory.** `mkdir skills/<name>`.

5. **Write `skills/<name>/SKILL.md`.** Template:

   ```markdown
   ---
   name: <name>
   description: <long_description — include trigger phrases inline>
   ---

   # <Title-cased name>

   ## When to use
   - <trigger phrase 1>
   - <trigger phrase 2>
   - User runs `/<name>`

   ## Steps
   1. <step 1>
   2. <step 2>
   3. <step 3>

   ## Output
   <what the skill produces — file written, summary, side effect>
   ```

   If user gave free-form steps, format them as numbered list. If they punted, leave `TODO: fill in` placeholders so the file still parses.

6. **Register in `.claude-plugin/marketplace.json`** (Claude side). Read file, parse JSON, append:

   ```json
   {
     "name": "<name>",
     "source": "./skills/<name>",
     "description": "<short_description>"
   }
   ```

   Preserve existing formatting (2-space indent, trailing newline). Write back with `Edit` (locate the closing `]` of `plugins` and insert before it) — do not rewrite the whole file unless necessary.

7. **Bump version in `.codex-plugin/plugin.json`** (Codex side). Codex auto-discovers any new dir under `skills/` via the `skills: "./skills/"` glob, so no per-skill entry needed — but a version bump signals devs to update.

   - Read `version` (semver `MAJOR.MINOR.PATCH`).
   - Increment `MINOR` (a new skill is an additive feature). E.g. `0.1.0` → `0.2.0`.
   - Edit just that line; preserve all other fields and formatting.

8. **Validate.** Run `claude plugin validate .` if the binary is available (`command -v claude`). Report output. If unavailable, skip and note it.

9. **Print next steps to the user:**

   ```
   Skill scaffolded: skills/<name>/SKILL.md
   Registered in .claude-plugin/marketplace.json (Claude Code)
   Bumped version in .codex-plugin/plugin.json (Codex CLI) — devs auto-discover on /plugins update

   Next:
     1. Edit skills/<name>/SKILL.md — refine description and steps
     2. Test locally:
          Claude:  /plugin marketplace add <absolute repo path>
                   /plugin install <name>@skills
          Codex:   /plugins install <absolute repo path>
     3. git checkout -b skill/<name> && git add skills/<name> .claude-plugin/marketplace.json .codex-plugin/plugin.json
     4. Open PR against master
   ```

## Output

- New directory `skills/<name>/` with `SKILL.md`.
- Updated `.claude-plugin/marketplace.json` with new plugin entry.
- Bumped `version` in `.codex-plugin/plugin.json`.
- Chat message with next steps.

## Guardrails

- Never overwrite an existing skill directory or marketplace entry — abort instead.
- Never commit or push. Leave git state for the dev to review.
- If JSON parse of `marketplace.json` or `plugin.json` fails, abort and surface the error — do not attempt repair.
- Keep frontmatter `description` on a single logical line (YAML scalar). Newlines inside break skill discovery in both Claude Code and Codex.
- Only bump `MINOR` for new skills. Reserve `MAJOR` for breaking changes (skill removal/rename) and `PATCH` for fixes within an existing skill.
