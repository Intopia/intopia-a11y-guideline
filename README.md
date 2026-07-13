# Intopia Figma accessibility skill

A skill that helps agents build accessible web UI in **Figma Make**.

## What the skill covers

- Semantic HTML and landmarks, logical heading order
- Accessible names, roles and states for every interactive element
- Keyboard operability and visible focus indicators
- Form structure, labels, grouping and real error handling
- Colour contrast; checked with an embedded script
- Focus management for modals and menus, and reflow down to 320px
- Charts/data visualisations, tables, and drag-and-drop patterns

## How it works

The skill drives a repeatable five-step workflow before and during code generation:

1. **Analyse the request** against the core accessibility principles and sort the work into categories (contrast, interactive elements, forms, semantics, focus, screen-reader support).
2. **Write a plan** to `guidelines/accessibility.md`, a persistent task list plus the reference material, created before any UI code is written so it survives across iterations.
3. **Build the colour palette and run the contrast check first.** Every colour pair is composited (including `rgba`/opacity) and run through the embedded script against the 4.5:1 / 3:1 thresholds.
4. **Work through the plan in order**, ticking tasks off as each requirement is met.
5. **Add the Intopia accessibility toolbar** and report every accessibility decision that was made, along with the manual checks the user should complete (headings, keyboard, reflow, etc.).

## The colour-contrast script

`SKILL.md` embeds a self-contained Node script (`check-colour-contrast.cjs`) that the workflow writes out and runs. It takes a palette JSON of colour pairs and reports pass/fail against WCAG thresholds, compositing any transparent colours against their background first.