---
name: excalidraw
description: "Generate Excalidraw diagrams as .excalidraw files. Use when asked to create diagrams, flowcharts, architecture sketches, wireframes, or visual explanations. Outputs valid .excalidraw JSON files with hand-drawn style."
license: MIT
metadata:
  author: alexanderopalic
  version: "1.0"
---

# Excalidraw Diagram Generator

Generate hand-drawn Excalidraw diagrams and save them as `.excalidraw` files.

## Workflow

1. Read `colors.md` from this skill directory to get the current color theme
2. Decide what the diagram should look like: elements, layout, colors, labels, arrows
3. Spawn a **subagent** (Task tool, `subagent_type: "general-purpose"`) with:
   - The full element format reference below
   - The colors from `colors.md`
   - A detailed description of the diagram to draw (element positions, labels, connections, colors)
   - The output file path (ask the user or default to `diagram.excalidraw` in the current directory)
4. The subagent produces the JSON elements array and writes the `.excalidraw` file
5. Tell the user the output path — they can open it at [excalidraw.com](https://excalidraw.com) or in VS Code with the Excalidraw extension

## Subagent Prompt Template

Include everything between the `---` markers in the subagent prompt:

---

You are an Excalidraw JSON generator. Your job is to produce a valid `.excalidraw` file.

### Output Format

Write a JSON file with this envelope structure:

```json
{
  "type": "excalidraw",
  "version": 2,
  "source": "https://excalidraw.com",
  "elements": [ ... ],
  "appState": {
    "gridSize": null,
    "viewBackgroundColor": "#ffffff"
  },
  "files": {}
}
```

### Element Format

#### Required Fields (all elements)
`type`, `id` (unique string), `x`, `y`, `width`, `height`

#### Defaults (skip these)
strokeColor="#1e1e1e", backgroundColor="transparent", fillStyle="solid", strokeWidth=2, roughness=1, opacity=100

#### Element Types

**Rectangle**: `{ "type": "rectangle", "id": "r1", "x": 100, "y": 100, "width": 200, "height": 100 }`
- `roundness: { "type": 3 }` for rounded corners
- `backgroundColor: "#a5d8ff"`, `fillStyle: "solid"` for filled

**Ellipse**: `{ "type": "ellipse", "id": "e1", "x": 100, "y": 100, "width": 150, "height": 150 }`

**Diamond**: `{ "type": "diamond", "id": "d1", "x": 100, "y": 100, "width": 150, "height": 150 }`

**Labeled shape (PREFERRED)**: Add `label` to any shape for auto-centered text.
`{ "type": "rectangle", "id": "r1", "x": 100, "y": 100, "width": 200, "height": 80, "label": { "text": "Hello", "fontSize": 20 } }`
- Works on rectangle, ellipse, diamond

**Labeled arrow**: `"label": { "text": "connects" }` on an arrow element.

**Standalone text** (titles, annotations only):
`{ "type": "text", "id": "t1", "x": 150, "y": 138, "text": "Hello", "fontSize": 20 }`
- x is the LEFT edge. To center at cx: set x = cx - (text.length × fontSize × 0.5) / 2

**Arrow**: `{ "type": "arrow", "id": "a1", "x": 300, "y": 150, "width": 200, "height": 0, "points": [[0,0],[200,0]], "endArrowhead": "arrow" }`
- points: [dx, dy] offsets from element x,y
- endArrowhead: null | "arrow" | "bar" | "dot" | "triangle"

#### Arrow Bindings
`"startBinding": { "elementId": "r1", "fixedPoint": [1, 0.5] }`
fixedPoint: top=[0.5,0], bottom=[0.5,1], left=[0,0.5], right=[1,0.5]

### Sizing Rules

**Font sizes:**
- Minimum fontSize: **16** for body text, labels
- Minimum fontSize: **20** for titles and headings
- Minimum fontSize: **14** for secondary annotations only (sparingly)
- NEVER use fontSize below 14

**Element sizing:**
- Minimum shape size: 120×60 for labeled rectangles/ellipses
- Leave 20-30px gaps between elements minimum
- Prefer fewer, larger elements over many tiny ones

**Canvas layout:**
- Plan layout to fit roughly 800×600 for standard diagrams
- Leave padding around edges (start content at x:50-100, y:50-100)

### Drawing Order
- Array order = z-order (first = back, last = front)
- Background zones first, then shapes, then text/labels, then arrows
- Decorative art/illustrations LAST

### Rules
- Do NOT use emoji in text — they don't render in Excalidraw's Virgil font
- Make sure text is readable (never use same text color as background)
- Use colors consistently — one fill per semantic role
- Check that elements don't overlap unintentionally
- Arrow labels need space — keep labels short or make arrows wider
- Center titles relative to the diagram content

### Quick Example

```json
{
  "type": "excalidraw",
  "version": 2,
  "source": "https://excalidraw.com",
  "elements": [
    {"type":"rectangle","id":"b1","x":100,"y":100,"width":200,"height":100,"roundness":{"type":3},"backgroundColor":"#a5d8ff","fillStyle":"solid","label":{"text":"Start","fontSize":20}},
    {"type":"rectangle","id":"b2","x":450,"y":100,"width":200,"height":100,"roundness":{"type":3},"backgroundColor":"#b2f2bb","fillStyle":"solid","label":{"text":"End","fontSize":20}},
    {"type":"arrow","id":"a1","x":300,"y":150,"width":150,"height":0,"points":[[0,0],[150,0]],"endArrowhead":"arrow","startBinding":{"elementId":"b1","fixedPoint":[1,0.5]},"endBinding":{"elementId":"b2","fixedPoint":[0,0.5]}}
  ],
  "appState": {"gridSize":null,"viewBackgroundColor":"#ffffff"},
  "files": {}
}
```

---

After the format reference, append:
- The colors from `colors.md`
- Your detailed diagram description
- The output file path
- "Write the .excalidraw file using the Write tool. Output ONLY the file — no explanation needed."

## Full Examples

See `reference.md` in this skill directory for complete diagram examples (photosynthesis, storyboard, etc.).
Include relevant examples in the subagent prompt when the user's request is similar.
