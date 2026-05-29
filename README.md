# skills

Plugin marketplace of agent skills for Claude Code and OpenAI Codex CLI.

Each skill is packaged as its own plugin under `plugins/<name>/`, so users can install, enable, disable, and remove skills independently.

## Skills

- `create-skill` - scaffold a new skill via prompts and register it in both marketplaces.

## Layout

- `.claude-plugin/marketplace.json` - Claude Code marketplace, lists every plugin.
- `.agents/plugins/marketplace.json` - Codex CLI marketplace, lists every plugin.
- `plugins/<name>/.claude-plugin/plugin.json` - Claude Code plugin manifest.
- `plugins/<name>/.codex-plugin/plugin.json` - Codex CLI plugin manifest.
- `plugins/<name>/skills/<name>/SKILL.md` - shared skill source.

## Install

Claude Code:

```sh
/plugin marketplace add pymmog/skills
/plugin install create-skill@skills
```

Codex CLI:

```sh
/plugin marketplace add pymmog/skills
/plugin add create-skill@skills
```

Manage skills independently with the normal plugin commands, for example `/plugin disable create-skill@skills` in Claude Code or `/plugin remove create-skill` in Codex CLI.

## Contributing

Create one plugin directory per skill:

```text
plugins/my-skill/
  .claude-plugin/plugin.json
  .codex-plugin/plugin.json
  skills/my-skill/SKILL.md
```

Then register the plugin in both marketplace files.
