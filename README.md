# Excalidraw Skill for Claude Code

A [Claude Code skill](https://docs.anthropic.com/en/docs/claude-code/skills) that generates hand-drawn Excalidraw diagrams as `.excalidraw` files.

## Install

```bash
npx skills add alexanderop/excalidraw-skill
```

## Usage

Invoke via slash command:

```
/excalidraw a flowchart showing user authentication
```

Or just ask naturally — the skill triggers on requests to create diagrams, flowcharts, architecture sketches, wireframes, or visual explanations.

## What it does

1. Reads a customizable color theme (`colors.md`)
2. Spawns a subagent with the full Excalidraw element format reference
3. The subagent generates valid `.excalidraw` JSON and writes the file
4. Open the output at [excalidraw.com](https://excalidraw.com) or in VS Code with the [Excalidraw extension](https://marketplace.visualstudio.com/items?itemName=pomdtr.excalidraw-editor)

## Customization

Edit `colors.md` to change the color palette used for all generated diagrams.

## License

MIT
