---
name: frontend-design
description: Research frontend design inspiration, UI patterns, color palettes, typography, and layout ideas. Use when exploring visual direction before building interfaces.
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
---

# Frontend Design Research

**Purpose:** Research and compile design inspiration based on a user's description -- gathering UI references, color palettes, typography choices, layout patterns, and visual direction into a structured mood board document.

## Overview

This skill searches the web for design inspiration matching the user's intent. It produces a research document with curated references, extracted patterns, and concrete recommendations the user can hand off to a design-to-code workflow.

**Output:** A mood board markdown file saved to `docs/design-research/`.

---

<critical_sequence name="design-research" enforce_order="strict">

## Process

<step number="1" required="true">
### Step 1: Clarify Design Intent

Extract from `$ARGUMENTS` and conversation context:

- **What is being designed?** (landing page, dashboard, mobile app, component, etc.)
- **Target audience** (developers, consumers, enterprise, creative professionals, etc.)
- **Mood / vibe keywords** (minimal, bold, playful, luxurious, brutalist, editorial, etc.)
- **Any constraints** (framework, brand colors, accessibility requirements, dark/light theme)

If the description is too vague to research effectively, ask for clarification:

```
To find the right design direction, I need a bit more context:

1. What type of interface is this? (landing page, dashboard, app, etc.)
2. What mood or vibe are you going for? (minimal, bold, playful, luxury, etc.)
3. Any existing brand constraints? (colors, fonts, framework)
```

Wait for response before proceeding.
</step>

<step number="2" required="true" depends_on="1">
### Step 2: Research Design Inspiration

Run parallel web searches based on the intent. Construct 3-5 targeted queries combining the interface type + mood + specific design aspects.

**Search categories (run in parallel):**

1. **Overall style** -- Search for design examples matching the type + mood.
   - Query pattern: `"[interface type] [mood] design inspiration 2025 2026"`
   - Example: `"SaaS dashboard minimal dark design inspiration 2025 2026"`

2. **Color palettes** -- Search for palette ideas matching the mood.
   - Query pattern: `"[mood] color palette [interface type] UI design"`
   - Example: `"luxury dark color palette fintech UI design"`

3. **Typography** -- Search for font pairing ideas matching the vibe.
   - Query pattern: `"[mood] typography font pairing web design"`
   - Example: `"editorial serif sans-serif font pairing web design"`

4. **Layout patterns** -- Search for layout/composition references.
   - Query pattern: `"[interface type] layout patterns [mood] UI"`
   - Example: `"landing page layout patterns asymmetric editorial UI"`

5. **Micro-interactions / motion** (if relevant) -- Search for animation and interaction patterns.
   - Query pattern: `"[interface type] micro-interactions animation design [mood]"`

Use WebSearch for each query. For promising results, use WebFetch to extract specific details (color hex values, font names, layout techniques).

**Reference sites to prioritize in results:**
- Dribbble, Behance, Awwwards, Mobbin, Godly, Siteinspire
- Land-book, One Page Love, Httpster, Minimal Gallery
- Typewolf, Fontpair, Google Fonts
- Coolors, Realtime Colors, Happy Hues
</step>

<step number="3" required="true" depends_on="2">
### Step 3: Synthesize Findings

From the research, extract and organize:

**Color palette:**
- Primary, secondary, accent colors with hex values
- Background and surface colors
- Text color hierarchy (heading, body, muted)
- Present as a table with hex, name, and usage

**Typography:**
- Display/heading font recommendation (with Google Fonts or source link)
- Body font recommendation
- Font size scale suggestion (base size, heading ratios)
- Weight and style combinations

**Layout direction:**
- Grid system recommendation (columns, breakpoints)
- Key layout patterns identified (hero style, card layouts, navigation patterns)
- Spacing philosophy (generous whitespace vs. dense)
- Notable compositional techniques (asymmetry, overlap, broken grid)

**Visual texture and effects:**
- Background treatments (gradients, noise, patterns, solid)
- Border and shadow styles
- Illustration or photography style (if applicable)
- Motion / animation approach

**Reference links:**
- Top 3-5 design references that best match the direction
- What to take from each reference (e.g., "color use from X, typography from Y, layout from Z")
</step>

<step number="4" required="true" depends_on="3">
### Step 4: Present and Refine

Present the synthesized direction to the user as a summary:

```
## Design Direction Summary

**Style:** [One-line description, e.g., "Dark luxury fintech with editorial typography"]

**Color palette:**
| Role       | Color   | Hex     |
|------------|---------|---------|
| Primary    | [name]  | #XXXXXX |
| Secondary  | [name]  | #XXXXXX |
| Accent     | [name]  | #XXXXXX |
| Background | [name]  | #XXXXXX |
| Surface    | [name]  | #XXXXXX |
| Text       | [name]  | #XXXXXX |
| Muted      | [name]  | #XXXXXX |

**Typography:**
- Headings: [Font Name] ([weight])
- Body: [Font Name] ([weight])
- Base size: [size]px / scale ratio: [ratio]

**Layout:** [Key pattern summary]

**Visual effects:** [Key effects summary]

**Top references:**
1. [Reference] -- take [what] from this
2. [Reference] -- take [what] from this
3. [Reference] -- take [what] from this
```

Ask the user if the direction feels right or if they want adjustments:

```
Does this direction match what you're envisioning?

1. Looks good -- save this mood board
2. Adjust colors -- I want a different palette direction
3. Adjust typography -- different font vibe
4. Adjust overall mood -- shift the whole direction
5. Other
```

If adjustments requested, loop back to the relevant part of Step 2/3 and re-research the specific aspect.
</step>

<step number="5" required="true" depends_on="4">
### Step 5: Save Mood Board

1. **Create directory** if it doesn't exist:
   ```bash
   mkdir -p docs/design-research
   ```

2. **Generate filename**: `YYYY-MM-DD-<sanitized-description>.md`
   - Sanitize: lowercase, hyphens for spaces, no special chars, max 60 chars

3. **Write mood board file:**

   ```markdown
   ---
   date: YYYY-MM-DD
   type: design-research
   interface: [type of interface]
   mood: [mood keywords]
   status: draft
   ---

   # Design Research: [Title]

   **Date:** YYYY-MM-DD
   **Interface type:** [landing page / dashboard / app / etc.]
   **Mood:** [mood keywords]

   ## Design Direction

   [One paragraph summarizing the overall aesthetic direction and rationale]

   ## Color Palette

   | Role       | Color   | Hex     | Usage                    |
   |------------|---------|---------|--------------------------|
   | Primary    | [name]  | #XXXXXX | [buttons, links, CTAs]   |
   | Secondary  | [name]  | #XXXXXX | [secondary actions]      |
   | Accent     | [name]  | #XXXXXX | [highlights, badges]     |
   | Background | [name]  | #XXXXXX | [page background]        |
   | Surface    | [name]  | #XXXXXX | [cards, panels]          |
   | Text       | [name]  | #XXXXXX | [headings, body]         |
   | Muted      | [name]  | #XXXXXX | [secondary text, borders]|

   ## Typography

   **Headings:** [Font Name] ([source/link])
   - Weights: [weights used]
   - Sizes: H1 [size], H2 [size], H3 [size]

   **Body:** [Font Name] ([source/link])
   - Weight: [weight]
   - Size: [base size]px
   - Line height: [ratio]

   **Scale ratio:** [ratio name and value, e.g., "Major Third (1.25)"]

   ## Layout

   **Grid:** [columns, gap, max-width]
   **Key patterns:**
   - [Pattern 1 description]
   - [Pattern 2 description]

   **Spacing:** [philosophy -- generous/dense, base unit]

   ## Visual Effects

   - **Backgrounds:** [treatment description]
   - **Shadows:** [shadow style]
   - **Borders:** [border style]
   - **Motion:** [animation approach]

   ## References

   1. **[Site/Project Name]** -- [URL]
      Take: [what to borrow from this reference]

   2. **[Site/Project Name]** -- [URL]
      Take: [what to borrow from this reference]

   3. **[Site/Project Name]** -- [URL]
      Take: [what to borrow from this reference]

   ## Implementation Notes

   - [Any technical considerations for implementing this design]
   - [Framework-specific notes if applicable]
   - [Accessibility considerations]
   ```

4. **Confirm output:**
   ```
   Mood board saved to: docs/design-research/[filename].md

   Next steps:
   - Use this as a reference when building the interface
   - Hand off to /frontend-design skill for implementation
   - Refine further with another /knowledge-garden::frontend-design session
   ```
</step>

</critical_sequence>

---

## Error Handling

**No relevant search results:**
- Broaden search terms, try alternative mood keywords
- Fall back to curating from known design reference sites directly

**User intent too broad:**
- Ask clarifying questions before researching
- Suggest 2-3 possible directions and let user pick

**WebSearch unavailable:**
- Fall back to general design knowledge and recommendations
- Note that results are based on training data rather than live research
- Suggest the user check reference sites manually

---

## Quality Guidelines

**Good design research has:**
- Specific hex values, not vague color descriptions
- Named fonts with sources (Google Fonts links, foundry names)
- Concrete layout measurements (columns, spacing units)
- Real reference links the user can visit
- Rationale connecting choices to the stated mood/intent

**Avoid:**
- Generic recommendations that could apply to anything
- Overloading with too many options (curate, don't dump)
- Ignoring the stated constraints (framework, brand, accessibility)
- Defaulting to the same safe choices (Inter, purple gradients, white backgrounds)
