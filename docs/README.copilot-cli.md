# Superpowers for GitHub Copilot CLI

Guide for using Superpowers with GitHub Copilot CLI via the Open Plugin spec.

## Prerequisites

- GitHub Copilot CLI **v1.0.6** or later (Open Plugin spec support)
- Git

## Quick Install

Install directly from the GitHub repository:

```bash
/plugin install https://github.com/dlivxpr/superpowers
```

## Install via Self-hosted Marketplace

The repository includes a `.plugin/marketplace.json`, so you can also register it as a local marketplace source and install from it:

```bash
copilot plugin marketplace add dlivxpr/superpowers
copilot plugin install superpowers@superpowers
```

> **Note:** This registers `dlivxpr/superpowers` itself as the marketplace index. Copilot CLI reads `.plugin/marketplace.json` from the repository to discover the plugin. If this command fails, use the direct GitHub URL method above.

## Install from Local Path

Clone the repository and point Copilot CLI to it:

```bash
git clone https://github.com/dlivxpr/superpowers.git ~/superpowers
copilot --plugin-dir ~/superpowers
```

## Verify Installation

Start a new Copilot CLI session and ask for something that should trigger a skill:

```
help me plan this feature
```

or:

```
let's debug this issue
```

The agent should automatically invoke the relevant superpowers skill. You can also list available skills:

```
/skills list
```

## Update

If installed via marketplace:

```bash
copilot plugin update superpowers
```

If installed from a local clone:

```bash
cd ~/superpowers
git pull
```

## How It Works

Copilot CLI discovers Superpowers through the `.plugin/` directory (Open Plugin spec) or the `.claude-plugin/` directory (backward-compatible). Both contain a `plugin.json` manifest that declares:

- **skills/** — composable skills (TDD, debugging, collaboration patterns, etc.)
- **agents/** — specialized agent definitions
- **commands/** — slash commands (brainstorm, write-plan, execute-plan)
- **hooks/** — session lifecycle hooks (SessionStart injects context automatically)

### Session Start Hook

When Copilot CLI loads the plugin, the `SessionStart` hook runs automatically. It reads the root skill file and injects it as additional context, giving your agent immediate access to all superpowers capabilities.

### Tool Name Mapping

Copilot CLI uses different tool names than Claude Code. The skills reference these mappings automatically. See [copilot-tools.md](../skills/using-superpowers/references/copilot-tools.md) for the full mapping table.

Key differences:

| Claude Code | Copilot CLI |
| ----------- | ----------- |
| Read        | view        |
| Write       | create      |
| Edit        | edit        |
| Bash        | bash        |
| Task        | task        |

## Troubleshooting

### Plugin not loading

1. Verify Copilot CLI version: `copilot --version` (requires v1.0.6+)
2. Check that `.plugin/plugin.json` exists in the plugin directory
3. Try loading explicitly: `copilot --plugin-dir /path/to/superpowers`

### Skills not triggering

1. Ensure the SessionStart hook ran successfully — you should see superpowers context in the session
2. Try explicitly requesting a skill: "use the test-driven-development skill"
3. Check `/skills list` output for available skills

### Windows

The hook system uses a polyglot wrapper (`hooks/run-hook.cmd`) that works on both Windows and Unix. It requires Git for Windows (which provides bash). If hooks fail:

1. Verify Git for Windows is installed
2. Check that bash is available at one of:
   - `C:\Program Files\Git\bin\bash.exe`
   - `C:\Program Files (x86)\Git\bin\bash.exe`
   - Or in your PATH
