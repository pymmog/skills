<img width="788" height="531" alt="Image" src="https://github.com/user-attachments/assets/1f779dc8-6774-48eb-969c-cd148a325755" />

# skills

Plugin marketplace of **CLANKER** skills for Claude and Codex.

Each skill is packaged as its own plugin under `plugins/<name>/`, so users can install, enable, disable, and remove skills independently.

## Skills

- `create-skill` - scaffold a new skill via prompts and register it in both marketplaces.

## Layout

- `.claude-plugin/marketplace.json` - Claude marketplace, lists every plugin.
- `.agents/plugins/marketplace.json` - Codex marketplace, lists every plugin.
- `plugins/<name>/.claude-plugin/plugin.json` - Claude plugin manifest.
- `plugins/<name>/.codex-plugin/plugin.json` - Codex plugin manifest.
- `plugins/<name>/skills/<name>/SKILL.md` - shared skill source.

## Install

Claude:

```sh
/plugin marketplace add pymmog/skills
/plugin install create-skill@skills
```

Codex:

```sh
/plugin marketplace add pymmog/skills
/plugin add create-skill@skills
```

Manage skills independently with the normal plugin commands, for example `/plugin disable create-skill@skills` in Claude or `/plugin remove create-skill` in Codex.

## Contributing

Create one plugin directory per skill:

```text
plugins/my-skill/
  .claude-plugin/plugin.json
  .codex-plugin/plugin.json
  skills/my-skill/SKILL.md
```

Then register the plugin in both marketplace files.
