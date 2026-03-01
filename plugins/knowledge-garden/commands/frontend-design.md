---
name: knowledge-garden::frontend-design
description: Research frontend design inspiration, UI patterns, color palettes, typography, and layout ideas
argument-hint: "[design description, e.g. 'dark luxury fintech dashboard' or 'playful landing page for a kids app']"
allowed-tools:
  - Read
  - Write
  - Bash
  - Glob
  - Grep
  - WebSearch
  - WebFetch
  - AskUserQuestion
  - Task
  - Edit
---

# Frontend Design Research

Research design inspiration based on a description and compile findings into a structured mood board with color palettes, typography, layout patterns, and reference links.

<instructions>

Execute the full frontend-design skill workflow:

1. **Clarify intent** from `$ARGUMENTS` -- determine what is being designed, the target mood/vibe, audience, and any constraints.

2. **Research in parallel** -- run 3-5 targeted web searches covering overall style, color palettes, typography, layout patterns, and motion/interactions. Prioritize design reference sites (Dribbble, Awwwards, Mobbin, Typewolf, Coolors, etc.).

3. **Synthesize** -- extract specific color hex values, font recommendations, layout patterns, and visual effects from search results into a cohesive direction.

4. **Present and refine** -- show the design direction summary to the user. Iterate on any aspects they want adjusted.

5. **Save mood board** -- write a structured markdown file to `docs/design-research/YYYY-MM-DD-<topic>.md` with frontmatter, color table, typography specs, layout patterns, references, and implementation notes.

## Arguments

- `$ARGUMENTS` (required): Description of what to design and the desired mood/vibe. The more specific, the better the research.

  Examples:
  - `dark luxury fintech dashboard with editorial typography`
  - `playful colorful landing page for a kids educational app`
  - `minimal brutalist portfolio site with monospace fonts`
  - `warm organic e-commerce product page for a coffee brand`

</instructions>
