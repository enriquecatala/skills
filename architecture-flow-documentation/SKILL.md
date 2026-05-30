---
name: architecture-flow-documentation
description: "Generate durable architecture flow documentation: a paired Markdown narrative and a polished single-file HTML architecture explorer with a static Canvas overview, animated step-by-step flow, sidebar timeline, playback controls, and a technical diagram/source view. Use this whenever the user asks for architecture documentation, system walkthroughs, deployment topology visualizations, interactive HTML architecture pages, or self-contained visual documentation for software, cloud, data, AI-agent, pipeline, or infrastructure workflows. Prefer a simpler diagram skill instead when the user only wants a standalone draw.io, Mermaid, or static flowchart."
license: MIT
compatibility: Works with filesystem editing tools. No build step required.
metadata:
  author: Enrique Catalá Bañuls
  version: "1.0"
---

# Architecture Flow Documentation

Create durable architecture flow documentation with two synchronized outputs:

- A readable Markdown architecture spec.
- A standalone HTML page that opens directly in a browser.
- A visual Canvas architecture overview.
- A step-by-step animated flow mode.
- A sidebar timeline that doubles as navigation.
- A technical view containing Mermaid source or an equivalent text diagram.

This skill is for durable architecture memory, not a landing page. The output should help an engineer understand the system, replay the runtime flow, and inspect the technical sequence without needing a dev server.

## First Pass

1. Inspect existing architecture docs if present, especially files under `docs/architecture/`.
2. Identify the system boundary, phases, components, protocols, data stores, control plane, runtime path, and security boundaries.
3. Extract the canonical flow as ordered steps. Each step needs:
   - `from`: source component id
   - `to`: target component id
   - `label`: short action label
   - `desc`: one concise explanation
   - `phase`: optional grouping such as `indexing`, `runtime`, `deployment`, `security`, `data`
4. Produce or update both files together:
   - `docs/architecture/<name>.md`
   - `docs/architecture/<name>.html`

When modifying a repo that already has an architecture documentation rule, honor it: update the architecture docs whenever infrastructure, workflow, or component boundaries change.

## Markdown Output

Write Markdown as the stable technical reference. Use this structure unless the local docs use a stronger pattern:

````markdown
# <System Name>: Architecture Flow

## Purpose
## Architecture Diagram
```mermaid
sequenceDiagram
...
```
## Detailed Flow
### 1. <Phase or Step>
## Components
## Runtime / Deployment Notes
## Security and Governance
## Operational Checks
## Key Benefits
````

Keep the Markdown factual and implementation-oriented. Include concrete ports, endpoints, cloud resources, protocols, tool names, and feature flags when known.

## HTML Output Contract

Generate a single `.html` file that is self-contained:

- Inline CSS.
- Inline JavaScript.
- No required local assets.
- No build tools.
- No server requirement.
- Avoid external CDNs when the user asks for offline/self-contained output. If a library would normally be used, provide a native fallback instead.

The page should preserve this look and interaction model:

- Dark slate app shell with a fixed left sidebar.
- Sidebar header with title and compact subtitle.
- Timeline list populated from `sequenceSteps`.
- Optional phase dividers when steps have phases.
- Main area with two tabs:
  - `Beautiful Flow`: Canvas diagram.
  - `Technical Spec`: Mermaid source, sequence diagram text, or a generated static technical view.
- Bottom control bar:
  - Static View button.
  - Previous, Play/Pause, Next controls.
  - Current step description panel.
- Canvas diagram:
  - Logical coordinate system such as `1500 x 850`.
  - Zones as dashed rounded rectangles for boundaries/platforms/phases.
  - Nodes as compact rounded cards with title, subtitle, border color, and text color.
  - Curved directed connections with labels in static mode.
  - Animated glowing route and packet in sequence mode.
  - Focused nodes highlighted and non-focused nodes dimmed during playback.
  - Responsive scaling to fit viewport.

Use plain text icon labels or simple Unicode only if the file already uses that style. Do not depend on Font Awesome or Tailwind for a self-contained artifact; recreate the needed button and layout styles in inline CSS.

## Data Model for HTML

Define the visualization from data objects near the top of the script. Keep content out of drawing functions so future updates are easy.

```javascript
const diagram = {
  title: "Cube Semantic Layer",
  accent: "#3b82f6",
  logicalWidth: 1500,
  logicalHeight: 850,
  zones: [
    { id: "gcp", title: "Google Cloud Platform", x: 260, y: 50, w: 960, h: 740, color: "rgba(59,130,246,0.08)", border: "#3b82f6" }
  ],
  nodes: {
    user: { x: 50, y: 380, w: 140, h: 90, title: "User", subtitle: "Browser", color: "#1e293b", border: "#6366f1", textColor: "#a5b4fc" }
  },
  staticConnections: [
    ["user", "chatbot", "HTTPS"]
  ],
  sequenceSteps: [
    { from: "user", to: "chatbot", label: "1. User Question", desc: "User asks a question in the chat UI.", phase: "runtime" }
  ]
};
```

Use stable, short component ids. Validate every `from` and `to` id exists in `nodes` before considering the document complete.

## Visual Design Rules

- Prefer dense, engineering-friendly layout over marketing composition.
- Keep cards compact and readable at desktop and laptop sizes.
- Use restrained color accents by role:
  - Blue: runtime/control flow.
  - Purple: offline indexing/build stages.
  - Green: data/context readiness or successful governed path.
  - Amber: routing, policy, gateways, or decision points.
  - Red: graph databases, alerts, or critical stores.
  - Cyan: warehouses or analytical engines.
- Use zones to explain ownership boundaries, cloud boundaries, phases, or trust boundaries.
- Use the sidebar as the narrative spine: every animation step should appear there.
- Keep the main canvas uncluttered. Put detailed explanations in the sidebar and bottom description panel.
- Ensure all text fits inside node cards; shorten titles before shrinking the whole diagram.

## Interaction Requirements

Implement these interactions in vanilla JavaScript:

1. Static View shows all major connections with protocol labels.
2. Play starts the ordered sequence from the first step if static mode is active.
3. Pause freezes playback.
4. Previous and Next jump one step and animate that edge.
5. Clicking a sidebar item jumps to that step.
6. Technical Spec tab shows the sequence diagram/source and does not require Mermaid to render.
7. Resizing the browser recalculates canvas dimensions and redraws.

Use `requestAnimationFrame` for playback. If animation helpers are useful, write small native functions; do not require GSAP in self-contained/offline mode.

## Implementation Checklist

Before finishing:

- Confirm the Markdown and HTML describe the same architecture and ordered flow.
- Confirm every sequence step references real node ids.
- Confirm the HTML opens directly from disk.
- Confirm there are no required external scripts, stylesheets, fonts, or images when self-contained output is requested.
- Confirm the static view still communicates the architecture if JavaScript animation is not playing.
- Confirm the technical view contains a copyable diagram/source representation.
- Confirm the page title, sidebar title, and H1 reflect the architecture being documented.

## Reference

For a reusable skeleton, read `references/self-contained-canvas-template.md`. Adapt its structure to the target architecture instead of copying project-specific Cube content.
