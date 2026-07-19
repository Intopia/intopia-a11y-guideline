---
name: intopia-a11y-guideline
description: Build accessible web user interfaces that meet WCAG 2.2 Level AA. Use whenever generating HTML, CSS, JSX, TSX, React components, pages, forms.
---

## Overview

Apply these accessibility principles whenever generating or modifying UI code. The goal is interfaces usable by people of all abilities, without workarounds.

## Instructions

Follow this workflow for every UI request, whether the input is a text prompt, a visual design (mockup, wireframe, screenshot, or design system), or both.

### 1. Analyse the request

Review the request against the Core Accessibility Principles below and identify what needs to be addressed, categorised as:

- **Colour contrast:**text, borders, focus indicators, UI components.
- **Interactive elements:** buttons, links, labels, states, keyboard navigation.
- **Form structure:** labels, fieldsets, error handling, required fields.
- **Semantic HTML:** headings, landmarks, lists, tables.
- **Focus management:** modals, menus, dynamic content.
- **Screen reader support:** alt text, ARIA, live regions.

### 2. Create the guidelines folder

Before writing any UI code, check there is a `guidelines/` folder in the project root. If the folder does not exist, create a `guidelines/` folder in the project root containing two files.

**guidelines/Guidelines.md** — write the following content verbatim, if the file already exists append this to the System Guidelines:

```
# System Guidelines  Read guidelines/accessibility.md at the start of every turn. Before writing any UI code, your accessibility plan must be documented in the Task List section of guidelines/accessibility.md. When implementing any component or pattern, read the corresponding guidance in the Accessibility Guidance section of guidelines/accessibility.md before writing code. Track the status of every task using [ ] (to do), [~] (in progress), and [x] (complete). Mark each task complete as soon as it is implemented. If a turn ends before all tasks are done, pick up from the first incomplete task on the next turn. Do not add the Intopia accessibility toolbar until all tasks are marked complete.
```

**guidelines/accessibility.md** — this file has two parts: the task list and the full accessibility guidance.

Part 1: derive tasks from your analysis in step 1. Use this structure:

```
# Accessibility Plan  ## Task List  ### Colour contrast - [ ] Build colour palette and run contrast check  ### [Component or pattern, e.g. Navigation] - [ ] [Specific task, e.g. Add aria-current="page" to active link]  ### [Next component or concern] - [ ] [Specific task]  ### Toolbar - [ ] Add Intopia accessibility toolbar
```

Be specific. Write "Add aria-expanded to accordion trigger" not "Fix ARIA". One section per component or concern area. The toolbar must always be the final task.

Part 2: after the task list, append the full contents of the Core Accessibility Principles section from this skill file, under the heading `## Accessibility Guidance`. This gives the agent direct access to the rules without relying on the skill being in context on subsequent turns.

### 3. Build the colour palette and run the contrast check

Before implementing anything else:

1. Identify every colour pair in scope: text vs background, input borders vs adjacent background, focus rings vs adjacent background, icons vs background.
2. Composite any `rgba`, `hsla`, or `opacity` values against their backgrounds first (see Colour Contrast Workflow).
3. Create a palette JSON and run the embedded contrast script.
4. Mark the colour contrast task complete in guidelines/accessibility.md.

If any checks fail, use `mcp__plugin_conversation_ask_user_question__ask_user_question` to present all failing pairs. For each failure include the current colour, the contrast ratio, the required ratio, and two or three accessible alternatives as selectable options. Each failure should be presented as a question. Do not ask any other accessibility questions — resolve all other concerns by applying the Core Accessibility Principles directly.

Wait for the user's answers, then proceed to step 4.

### 4. Work through the accessibility plan

With colour resolved, work through every remaining task in guidelines/accessibility.md in order. After completing each task, mark it `[x]` in guidelines/accessibility.md and continue without pausing. Do not ask the user questions about non-colour accessibility decisions.

### 5. Add the toolbar and report

When every task except the toolbar is marked `[x]`, add the Intopia accessibility toolbar (see Accessibility Toolbar section) and mark it complete in guidelines/accessibility.md.

Then report:

- Each accessibility decision made — one line per component or pattern (e.g. "Modal: focus moves to heading on open, trapped while open, returns to trigger on close").
- The following manual verifications for the user to complete. The toolbar provides tools for headings, accessible names, images, colour contrast, target size, keyboard, and responsive layout. Work through each verification below using the matching tool.
  - **Headings:** Use the toolbar to review the heading structure. Confirm the hierarchy is logical, no levels are skipped, and headings accurately describe the content that follows.
  - **Accessible names:** Use the toolbar to confirm all interactive elements and landmark regions have an accessible name that accurately reflects their purpose.
  - **Images:** Use the toolbar to review alt text for all images. Confirm informative images have meaningful descriptions, decorative images have empty alt text, and functional images describe the action or destination.
  - **Colour contrast:** Use the toolbar to check text and UI component contrast against the rendered page. Do not rely on design token values alone.
  - **Target size:** Use the toolbar to check all interactive targets meet 24×24px minimum.
  - **Keyboard:** Tab through all interactive elements. Confirm focus order is logical, focus indicator is always visible, and no keyboard trap exists outside of intentional modal containment.
  - **Responsive:** Click the Mobile preview button at the top of the Make preview window to switch to the mobile viewport. Confirm content reflows to fit with no horizontal scroll, no loss of functionality (every control still reachable and usable), nothing cut off, hidden or truncated, nothing overlapping, and nothing displayed outside the viewport edges.
- A short note about the toolbar: it appears as a floating bar on every page and highlights headings, images, and accessible names of interactive elements, flagging anything that needs review. The user can drag it, switch its orientation, and click the help (?) button for full instructions. Tell the user to let you know when they want the toolbar removed and you will strip it from the project.

---

## Accessibility Toolbar

Include the Intopia accessibility toolbar on every generated page. Always load from the jsDelivr CDN — never copy the widget source into the project. Place the script tag in `index.html` before `</body>` (for both plain HTML and React/Vite, the Figma Make default) so the widget loads once before React mounts. Do not import the widget into the React tree. This is a Figma Make environment with an auto-generated figma\_\_entrypoint.ts. Inject the script from the root component on mount via `useEffect`, appending a `<script>` element with the same URL.

<script src="https://cdn.jsdelivr.net/gh/Intopia/intopia-a11y-guideline-toolbar@main/intopia-accessibility-toolbar.js"></script>

---

## Core Accessibility Principles

### Foundations

- Use native HTML elements for their intended purpose.
- Use ARIA to enhance elements where native HTML lacks support (like aria-expanded).
- Use ARIA to build custom components (like tabs or combobox) that HTML doesn't support natively.
- Never override native semantics using ARIA roles unless absolutely necessary.

### Page Level

- `<title>`: Unique and descriptive for every page.
- `lang` attribute: Declared on `<html>`; matches page content language.
- Landmarks: Use core landmark elements (`<header>`, `<nav>`, `<main>`, `<footer>`, `<aside>`). Don’t overuse.
- Sections: Use `<section>` only for important, high-priority areas users need to navigate to. Must have an accessible label.
- Headings: Follow strict hierarchical order (`<h1>`–`<h6>`) without skipping levels. Keep visible by default.
- Skip Link: Provide a `"Skip to Main Content"` link, especially if more than 5 focusable items precede the main content but apply your own judgement.

### Semantic Markup

#### Lists

Use the correct list type:

- `<ul>`: Order doesn't matter (navigation, features).
- `<ol>`: Order matters (steps, rankings, instructions).
- `<dl>`: Key-value pairs (glossaries, metadata).

Do not use list markup for visual indentation. Avoid suppressing list semantics with `list-style: none`; some screen readers remove list semantics when this CSS is applied.

#### Tables

- Use `<table>` only for data with row/column relationships, never for layout.
  - Rows are `<tr>`, data cells `<td>`, header cells `<th>`.
  - Name the table with `<caption>` as the first child of `<table>`; if the name is an existing element like a heading, use `aria-labelledby` instead.
  - Headers spanning columns/rows: `scope="colgroup"` or `scope="rowgroup"`. If `scope` can't express the structure, give each `<th>` an `id` and list applicable ids in a `headers` attribute on each cell, or split into simpler tables.
  - Sortable column: a `<button>` inside the `<th>`, with `aria-sort` (`ascending`/`descending`) on that `<th>`, removed from all others.
  - Layout tables (legacy only): `role="presentation"` on `<table>`.

#### Buttons and Links

- `<button>`: Triggers actions (submit, modal, toggle).
- `<a href>`: Navigates to a URL/anchor.

Never use `<div>`, `<span>`, or other non-interactive elements as buttons or links. If the design requires custom styling, style a native element rather than using ARIA to patch an incorrect one. If a link performs an action rather than navigating, use `<button>`.

#### Accessible Names

Every interactive element must have an accessible name. Apply in this order of preference:

1. **Native label:** `<label>`, `<caption>`, `<figcaption>`, button text, link text.
2. `aria-labelledby`**:** references visible text already on the page by `id`. Preferred when visible text exists.
3. `aria-label`**:** use only when no visible text is available (e.g. an icon-only button). The value must match or begin with any visible text on the element (label-in-name requirement).

Do not use `aria-label` to override or contradict visible text.

```
<!-- Icon-only button: use aria-label --> <button type="button" aria-label="Close dialog">  <svg aria-hidden="true" focusable="false">...</svg> </button> <!-- Label via aria-labelledby (preferred when visible text exists) --> <h2 id="billing-heading">Billing address</h2> <form aria-labelledby="billing-heading">...</form>
```

#### Roles

Prefer native HTML elements over ARIA roles. When a custom component is unavoidable:

- Assign the correct ARIA role (e.g. `role="dialog"`, `role="tab"`, `role="combobox"`).
- Do not add roles to native elements that already carry the correct role (e.g. do not add `role="button"` to `<button>`).
- Ensure required owned elements and required context are present (e.g. `role="option"` must be inside `role="listbox"`).

#### State

Interactive elements must expose their current state programmatically.

| Pattern                                    | Attribute                                     |
| :----------------------------------------- | :-------------------------------------------- |
| Expandable control (accordion, disclosure) | `aria-expanded="true"` / `"false"`            |
| Toggle button                              | `aria-pressed="true"` / `"false"`             |
| Tab / option / treeitem                    | `aria-selected="true"` / `"false"`            |
| Checkbox or switch                         | `aria-checked="true"` / `"false"` / `"mixed"` |
| Functionally disabled (still focusable)    | `aria-disabled="true"`                        |
| Current page in navigation                 | `aria-current="page"`                         |

**Rule (prose):**

`aria-pressed` **requires a true toggle.** Only use `aria-pressed` when pressing the same button switches it between an active and inactive state.

Use `aria-disabled="true"` instead of the `disabled` attribute when the element should remain focusable, so keyboard users can discover it and understand why it is unavailable. Pair it with a visible explanation where possible.

#### Hiding Content

- `aria-hidden="true"`: removes an element and its children from the accessibility tree. Use for decorative icons, duplicate text, and visually redundant content. Never apply to focusable elements.
- `hidden` or `display: none`: removes content from both visual rendering and the accessibility tree. Use when content is not present.
- Visually hidden but available to screen readers: use a `.visually-hidden` / `.sr-only` CSS class that clips the element without using `display: none` or `visibility: hidden`.

```
.visually-hidden {  position: absolute;  width: 1px;  height: 1px;  padding: 0;  margin: -1px;  overflow: hidden;  clip: rect(0, 0, 0, 0);  white-space: nowrap;  border: 0; }
```

### Keyboard Navigation

- Tab order follows logical visual reading order (top to bottom, left to right).
- Use `tabindex="0"` to include custom elements in the tab order.
- Use `tabindex="-1"` only for programmatic focus management.
- Never use `tabindex` values greater than 0.
- Focus must never leave the visible viewport or be lost.

### Focus Indicators

- All interactive elements must have a visible focus indicator.
- Use `outline: 2px solid` with `outline-offset: 2px` for focus states. Do not rely on the default browser focus ring in environments that apply `outline: 0` or `outline: none`.
- Minimum 3:1 contrast ratio between the focus indicator and the adjacent background.
- Use `:focus-visible` to show focus rings for keyboard users without affecting mouse users.
- Never remove `outline` without an equally visible replacement.

### Focus Management on Dynamic Content

| Pattern           | Required behaviour                                                                |
| :---------------- | :-------------------------------------------------------------------------------- |
| Modal opens       | Move focus to modal heading. Trap focus inside. Return focus to trigger on close. |
| Menu opens        | Move focus to first item. Return focus to menu button on close.                   |
| Toast / alert     | Use `role="status"` or `role="alert"` with `aria-live`. Do not move focus.        |
| Accordion expands | Focus stays on the header trigger. Do not move it.                                |
| SPA navigation    | Move focus to new page heading or `<main>`.                                       |

### Live Regions

Use `aria-live` only when all three conditions are true:

1. Content changes dynamically after page load.
2. The change does not result from explicit user navigation.
3. Focus management is not already handling the announcement.

- `role="status"` (`aria-live="polite"`): non-urgent updates such as search result counts and filter feedback.
- `role="alert"` (`aria-live="assertive"`): critical, time-sensitive errors only.
- Set `aria-atomic="true"` only when the whole region should be read as a unit.

### Forms

#### Structure and Labels

- Use `<label>` for every input. Associate explicitly with `for`/`id`; do not rely on wrapping alone.
- Use `<fieldset>` and `<legend>` for grouped inputs (radio groups, checkboxes, date field sets, address field sets).
- Never use `placeholder` as a substitute for a `<label>`. Placeholder text disappears on input, has insufficient contrast by default, and is not reliably announced by screen readers. **Exception:** A Search icon is a valid visual label for a search field providing the field has an accessible name e.g. Search.

#### Input Types

- Use the correct `type` attribute: `email`, `tel`, `number`, `date`, `password`, `search`, `url`. This provides appropriate keyboards on mobile and semantic meaning.
- Use `autocomplete` attributes for personal data fields (e.g. `autocomplete="given-name"`, `autocomplete="email"`, `autocomplete="current-password"`). Required by WCAG 1.3.5.

#### Required Fields

- Mark required fields both visually and programmatically.
- Use `required` and `aria-required="true"` on the input.

```
<label for="name">Full name <span aria-hidden="true">*</span></label> <input type="text" id="name" name="name" required aria-required="true" autocomplete="name">
```

#### Hints and Descriptions

- Use `aria-describedby` to associate hints, constraints, or instructions with an input.
- Hints must be persistent visible text, not placeholder or tooltip content.

#### Form Error Handling

- Do not rely on browser-native constraint validation (e.g. `required`, `type="email"` default popups). Always provide custom inline error messages.
- Error messages must include the field name and a resolution instruction (e.g. "Enter your first name", not "This field is required").
- Use `aria-invalid="true"` on inputs with validation errors.
- Use `aria-describedby` to associate the error message with the input.
- Error messages must be visible text, not colour or icon alone.
- On submission failure, move focus to the first error or an error summary at the top of the form.
- Do not use `role="alert"` or `aria-live="assertive"` on error containers when focus is already being moved to the error.

```
<label for="email">Email address</label> <input  type="email"  id="email"  name="email"  required  aria-required="true"  aria-invalid="true"  aria-describedby="email-error email-hint" > <p id="email-hint">We'll never share your email.</p> <p id="email-error">Enter a valid email address.</p>
```

#### Disabled and Read-only States

- `disabled`: removes the element from the tab order and prevents interaction. Use only when the field genuinely cannot be used.
- `readonly`: keeps the field focusable and its value submittable. Use when the value is visible but should not be changed.
- Do not mark a field as both `disabled` and `required`.
- Avoid disabling the submit button to signal an incomplete form. Keep it enabled and surface errors on submit instead.

### Drag and Drop

Any drag-and-drop interaction (reorderable lists, kanban boards, file sorting, repositioning items) must be fully operable without a pointer drag gesture. Pointer dragging may remain as an enhancement, but it must never be the only way to perform the action.

#### Keyboard operation

Provide a keyboard-operable mechanism using one of the following patterns. Either pattern must use native `<button>` elements, follow a logical tab order, and have visible focus indicators meeting 3:1 contrast.

- **Drag handle with keyboard controls:** Provide a permanently visible drag handle. Make the handle a focusable native `<button>`. On activation (Enter or Space) the item enters a "grabbed" state where arrow keys move it between valid positions, Enter or Space drops it, and Escape cancels and returns it to its original position.
- **Move menu / move controls:** Provide a `<button>` (e.g. "Move item") that opens a menu or exposes controls letting the user choose a new position directly (move up, move down, move to top, move to a named target). This is often simpler and more robust than a grabbed state, particularly when moving items between containers.

#### Screen reader feedback

The interaction must expose its state and outcome to screen reader users:

- Give each draggable item and each drop target an accessible name describing what it is and where it is (e.g. "Task A, item 2 of 5, To Do column").
- Describe the available keys in help text associated with the handle via `aria-describedby` (e.g. "Press Enter to pick up, arrow keys to move, Escape to cancel").
- Announce every meaningful change through a polite live region (`role="status"` / `aria-live="polite"`): pick-up ("Grabbed Task A, position 2 of 5"), each move ("Task A, now position 3 of 5"), drop ("Dropped Task A at position 3 of 5"), and cancel ("Cancelled, Task A returned to position 2 of 5"). Do not use `aria-live="assertive"` for reorder status; assertive is reserved for critical, time-sensitive content.
- Do not move focus to make an announcement; use the live region and keep focus on the item or handle being moved. Do not use `aria-pressed` to convey the grabbed state; communicate it through the live region announcement and `aria-describedby` help text instead.

### Colour Contrast (WCAG 2.2 AA)

| Content type                                    | Minimum ratio |
| :---------------------------------------------- | :------------ |
| Normal text (below 24px regular / 18.66px bold) | 4.5:1         |
| Large text (24px+ regular / 18.66px+ bold)      | 3:1           |
| UI components (borders, focus indicators)       | 3:1           |
| Meaningful icons and graphical objects          | 3:1           |

Form field borders and focus states are frequent failure points. Always check both explicitly. Verify every pair using the Colour Contrast Workflow below.

Be careful. A decorative border on a non-interactive elements container doesn’t need to meet the 3:1 non-text contrast ratio. This is a common over reach. You must determine if the border is for an interactive element, for example a search field, in which case it must meet the requirement, or is surrounding an area of content, such as a card in which case it is for decoration and doesn’t need to meet the requirement. This requires reasoning to make an accurate judgment.

### Alternative Text

| Image type                         | Requirement                                                           |
| :--------------------------------- | :-------------------------------------------------------------------- |
| Decorative                         | `alt=""` (empty string; do not omit the attribute)                    |
| Informative                        | Describe the information conveyed, not the image literally            |
| Functional (inside link or button) | Describe the action or destination                                    |
| Complex (chart, diagram)           | Use `aria-describedby` pointing to a nearby detailed text description |

### Charts and Data Visualisations

A chart is a complex image; the visual alone is never sufficient.

#### Default behaviour

Unless an interactive chart is explicitly requested, do not rely on the chart itself as the accessible representation. Instead:

- Give the chart (`<svg>`, `<canvas>`, or wrapper element) aria-hidden="true" and a visible `<figcaption>` or heading for sighted context.
- Provide the data through an accessible alternative: a `<table>` for exact values (see Tables), or a plain-text summary stating the takeaway when the trend matters more than the individual values (e.g. "Revenue rose steadily from Q1 to Q4, peaking at $1.2M").

#### Placement

- If the design already includes an accessible alternative (a table, list, or text description adjacent to the chart), follow the design. The accessible name lives on the table `<caption>` or the summary text, not on the chart element.
- If the design does not include an accessible alternative, add one using one of these approaches:
  - Place it visibly adjacent to the chart.
  - Place it in a `<details>`/`<summary>` disclosure directly after the chart.
  - Hide it visually using the .visually-hidden CSS utility (see Hiding Content section) so it is available to assistive technology without affecting the visual layout.

The alternative must be readable on its own. Do not gate it behind hover, mouse-only controls, or JavaScript-dependent interactions.

#### Interactive charts

If an interactive chart is explicitly requested, expose it to assistive technology rather than hiding it. Provide keyboard access to data points, use ARIA live regions for dynamic updates, and still include a non-interactive fallback (table or summary) as a complement and respect `prefers-reduced-motion`.

### Responsive Layout

Content must reflow without loss of information or functionality at a viewport width of 320 CSS pixels (vertical scrolling) or a height of 256 CSS pixels (horizontal scrolling). Users must not be required to scroll in two dimensions to read or interact with content. Tables, complex data visualisations, maps, and toolbars are exempt where 2D layout is essential to usage or meaning.

#### Layout rules

- Set `<meta name="viewport" content="width=device-width, initial-scale=1">` in `<head>`.
- Use relative units (`%`, `rem`, `em`, `vw`, `ch`) for widths, padding, and font sizes. Avoid fixed pixel widths on layout containers.
- Build layouts with Flexbox or CSS Grid. Use `flex-wrap: wrap`, or Grid with `repeat(auto-fit, minmax(<min>, 1fr))`, so columns collapse at narrow widths.
- Stack horizontal navigation, toolbars, button groups, and multi-column forms vertically at narrow widths (use a media query around 600–768px).
- Allow long strings (URLs, IDs, codes, email addresses) to break with `overflow-wrap: anywhere` or `word-break: break-word`. Long unbreakable strings are a common cause of 320px overflow.
- Avoid horizontal-only scrolling regions for primary content. If a data table or diagram genuinely requires 2D layout, wrap it in a focusable scroll container so keyboard users can reach it.
- Hide nothing at small widths that is available at large widths. Collapsing into a menu is acceptable; removing content is not.

## Colour Contrast Workflow

This step is mandatory whenever colour values are produced or changed. Common failure points: low-contrast body text, placeholder and helper text, disabled or secondary button text, input/select/textarea borders, focus indicators, and any colour using `rgba`/`hsla`/`opacity`.

### Process

1. Identify every colour pair in scope: text vs background, input borders vs adjacent background, focus rings vs adjacent background, icons vs background.
2. For any colour using `rgba`, `hsla`, or `opacity`, composite it against its background first (see formula below) and put the resulting opaque hex in the palette. Do not test the pre-opacity value.
3. Create a palette JSON file with the schema below.
4. This is a Figma Make environment. Save the embedded script (further below) as `check-colour-contrast.cjs` and run: `node check-colour-contrast.cjs <palette.json>`.
5. Fix any failures and re-run until all checks pass.
6. Only then present the implementation.

### Compositing transparent colours

Per channel: `rendered = foreground × alpha + background × (1 − alpha)`

Worked example — `rgba(29, 78, 216, 0.3)` over `#FFFFFF`:

```
R: 29  × 0.3 + 255 × 0.7 = 187  → BB G: 78  × 0.3 + 255 × 0.7 = 202  → CA B: 216 × 0.3 + 255 × 0.7 = 243  → F3 → composited colour: #BBCAF3
```

Testing `#1D4ED8` (6.70:1, passes) instead of `#BBCAF3` (1.63:1, fails) is a critical error. Watch for `box-shadow` focus rings with `rgba`, overlay backdrops, hover/active states with `opacity`, `rgba` borders, and CSS custom properties carrying alpha.

### Palette JSON schema

Object with a `checks` array. Each check requires `id`, `foreground`, `background`, and `type` (`text-normal` | `text-large` | `ui` | `graphic`). Optional: `state`, `notes`. Colour values may be hex, rgb/rgba, hsl/hsla, or named (black, white, red, green, blue, gray/grey, transparent).

```
{  "checks": [   { "id": "body-text", "foreground": "#1f2937", "background": "#ffffff", "type": "text-normal" },   { "id": "input-border", "foreground": "#6b7280", "background": "#ffffff", "type": "ui", "state": "default" },   { "id": "focus-ring", "foreground": "#1d4ed8", "background": "#ffffff", "type": "ui", "state": "focus" }  ] }
```

### Embedded script — `check-colour-contrast.cjs`

Write this verbatim to a file and run with Node. Exit code is `0` on all-pass, `1` otherwise.

`"use strict"; const fs = require("fs"); const path = require("path"); const THRESHOLDS = { "text-normal": 4.5, "text-large": 3.0, ui: 3.0, graphic: 3.0 }; const NAMED = { black:[0,0,0], white:[255,255,255], red:[255,0,0], green:[0,128,0], blue:[0,0,255], gray:[128,128,128], grey:[128,128,128], transparent:[0,0,0,0] }; function toLinear(c) {  const s = c / 255;  return s <= 0.03928 ? s / 12.92 : Math.pow((s + 0.055) / 1.055, 2.4); } function luminance([r, g, b]) {  const [R, G, B] = [r, g, b].map(toLinear);  return 0.2126 * R + 0.7152 * G + 0.0722 * B; } function contrastRatio(fg, bg) {  const l1 = luminance(fg), l2 = luminance(bg);  return (Math.max(l1, l2) + 0.05) / (Math.min(l1, l2) + 0.05); } function parseHex(value) {  const hex = value.replace("#", "").trim();  if (hex.length === 3) return hex.split("").map((c) => parseInt(c + c, 16));  if (hex.length === 4) {    const [r, g, b, a] = hex.split("").map((c) => parseInt(c + c, 16));    return [r, g, b, a / 255];  }  if (hex.length === 6) return [0, 2, 4].map((i) => parseInt(hex.slice(i, i + 2), 16));  if (hex.length === 8) {    return [      parseInt(hex.slice(0, 2), 16),      parseInt(hex.slice(2, 4), 16),      parseInt(hex.slice(4, 6), 16),      parseInt(hex.slice(6, 8), 16) / 255,    ];  }  return null; } function parseRgb(value) {  const m = value.trim().match(/^rgba?\(\s*([\d.]+)\s*,\s*([\d.]+)\s*,\s*([\d.]+)\s*(?:,\s*([\d.]+)\s*)?\)$/i);  if (!m) return null;  const r = +m[1], g = +m[2], b = +m[3];  const a = m[4] !== undefined ? +m[4] : undefined;  if ([r, g, b].some((n) => Number.isNaN(n) || n < 0 || n > 255)) return null;  if (a !== undefined && (Number.isNaN(a) || a < 0 || a > 1)) return null;  return a === undefined ? [r, g, b] : [r, g, b, a]; } function hslToRgb(h, s, l) {  const hue = ((h % 360) + 360) % 360;  const sat = Math.max(0, Math.min(1, s / 100));  const light = Math.max(0, Math.min(1, l / 100));  const c = (1 - Math.abs(2 * light - 1)) * sat;  const x = c * (1 - Math.abs(((hue / 60) % 2) - 1));  const m = light - c / 2;  let [rp, gp, bp] = [0, 0, 0];  if (hue < 60) [rp, gp, bp] = [c, x, 0];  else if (hue < 120) [rp, gp, bp] = [x, c, 0];  else if (hue < 180) [rp, gp, bp] = [0, c, x];  else if (hue < 240) [rp, gp, bp] = [0, x, c];  else if (hue < 300) [rp, gp, bp] = [x, 0, c];  else [rp, gp, bp] = [c, 0, x];  return [Math.round((rp + m) * 255), Math.round((gp + m) * 255), Math.round((bp + m) * 255)]; } function parseHsl(value) {  const m = value.trim().match(/^hsla?\(\s*([-\d.]+)\s*,\s*([\d.]+)%\s*,\s*([\d.]+)%\s*(?:,\s*([\d.]+)\s*)?\)$/i);  if (!m) return null;  const h = +m[1], s = +m[2], l = +m[3];  const a = m[4] !== undefined ? +m[4] : undefined;  if ([h, s, l].some((n) => Number.isNaN(n))) return null;  if (s < 0 || s > 100 || l < 0 || l > 100) return null;  if (a !== undefined && (Number.isNaN(a) || a < 0 || a > 1)) return null;  const rgb = hslToRgb(h, s, l);  return a === undefined ? rgb : [...rgb, a]; } function parseColour(value) {  if (typeof value !== "string") return null;  const v = value.trim().toLowerCase();  if (!v) return null;  if (v.startsWith("#")) return parseHex(v);  if (v.startsWith("rgb")) return parseRgb(v);  if (v.startsWith("hsl")) return parseHsl(v);  if (NAMED[v]) return NAMED[v];  return null; } function flatten(fg, bg) {  const fgA = fg.length === 4 ? fg[3] : 1;  const bgA = bg.length === 4 ? bg[3] : 1;  if (bgA < 1) throw new Error("Background alpha not supported. Use opaque backgrounds.");  if (fgA >= 1) return fg.slice(0, 3);  return [0, 1, 2].map((i) => Math.round(fg[i] * fgA + bg[i] * (1 - fgA))); } function validate(check, i) {  const errs = [];  if (!check || typeof check !== "object") return [`checks[${i}] must be an object.`];  if (typeof check.id !== "string") errs.push(`checks[${i}].id required (string).`);  if (typeof check.foreground !== "string") errs.push(`checks[${i}].foreground required (string).`);  if (typeof check.background !== "string") errs.push(`checks[${i}].background required (string).`);  if (typeof check.type !== "string") errs.push(`checks[${i}].type required (string).`);  else if (!THRESHOLDS[check.type]) errs.push(`checks[${i}].type must be one of: ${Object.keys(THRESHOLDS).join(", ")}.`);  return errs; } function run() {  const file = process.argv[2];  if (!file) {    console.error("Usage: node check-colour-contrast.js <palette.json>");    process.exit(1);  }  let palette;  try {    palette = JSON.parse(fs.readFileSync(path.resolve(process.cwd(), file), "utf8"));  } catch (e) {    console.error(`Failed to load palette JSON: ${e.message}`);    process.exit(1);  }  if (!palette || !Array.isArray(palette.checks)) {    console.error("Palette JSON must be an object with a checks array.");    process.exit(1);  }  const errs = palette.checks.flatMap(validate);  if (errs.length) {    console.error("Input validation failed:");    errs.forEach((e) => console.error(`- ${e}`));    process.exit(1);  }  let pass = 0, fail = 0;  console.log("Colour contrast results:\n-----------------------");  for (const c of palette.checks) {    const fg = parseColour(c.foreground), bg = parseColour(c.background);    if (!fg || !bg) { fail++; console.log(`FAIL ${[c.id](http://c.id/)}: invalid colour value(s).`); continue; }    let effFg;    try { effFg = flatten(fg, bg); }    catch (e) { fail++; console.log(`FAIL ${[c.id](http://c.id/)}: ${e.message}`); continue; }    const ratio = contrastRatio(effFg, bg.slice(0, 3));    const need = THRESHOLDS[c.type];    const ok = ratio >= need;    ok ? pass++ : fail++;    const state = c.state ? ` [${c.state}]`: "";    const notes = c.notes ?` - ${c.notes}`: "";    console.log(`${ok ? "PASS" : "FAIL"} ${[c.id](http://c.id/)}${state}: ${ratio.toFixed(2)}:1 (required ${need}:1)${notes}`);  }  console.log(`-----------------------\nTotal: ${palette.checks.length}, Passed: ${pass}, Failed: ${fail}`);  process.exit(fail > 0 ? 1 : 0); } run();`

---

## Acceptance Criteria

Generated code passes these checks before being presented to the user:

- Page has a descriptive `<title>` and correct `lang` attribute
- Landmark regions are present and correctly used
- Heading hierarchy is logical with no skipped levels
- All interactive elements use native HTML (`<button>`, `<a href>`, `<input>`, `<select>`, `<textarea>`, `<summary>`). ARIA roles are only used when no native element provides the required semantics
- All interactive elements have an accessible name, role, and state
- Tab order is logical; no `tabindex` values greater than 0
- Focus indicators are visible and meet 3:1 contrast
- Focus is managed correctly for modals, menus, and dynamic content
- Drag-and-drop interactions are operable by keyboard alone and by single-pointer taps without a drag gesture; grab/move/drop/cancel are announced via a polite live region and Escape cancels an in-progress move
- Live regions are used only where needed and are not duplicated
- All form inputs have labels; grouped inputs use `<fieldset>`/`<legend>`
- Forms don't rely on browser-native constraint validation (e.g. `required`, `type="email"` default popups). Always provide custom inline error messages.
- Form errors include the field name and resolution instruction
- `aria-invalid` and `aria-describedby` are applied on validation failure
- All meaningful images have appropriate alt text; decorative images use `alt=""`
- Charts are hidden from assistive technology with a data table or text summary provided (unless an interactive chart was requested), do not rely on colour alone, and meet graphical/text contrast
- Page reflows at 320 CSS px wide and 256 CSS px tall with no two-dimensional scrolling and no loss of content or functionality
- Every colour pair has been verified by the embedded contrast script and all checks pass
- Any `rgba`/`hsla`/`opacity` colours were composited against their background before testing
