---
name: ui-variants
description: When the user wants to explore alternate UI designs for a specific section of a website by generating multiple variants and toggling between them live in the browser. Use when the user says "give me variants of this section," "show alternate designs for the hero," "try 3 versions of this pricing block," "redesign this with options," "A/B the header," "variant toggle," "compare UI options," "cycle through designs," or points to a section and asks for alternatives. Generates 3 distinct redesigns plus keeps the original, injects a dev-only floating toggle chip, and makes switching realtime with no rebuild. For designing a single UI, use frontend-design. For marketing copy variations, use copywriting.
---

# ui-variants

This skill takes any section of a website and delivers a live A/B/C/D comparison tool in the running page. It (1) generates **3 alternate UI designs** for the section with distinct aesthetic directions, (2) injects a dev-only floating toggle, and (3) wires the toggle so clicking cycles `Original → Variant A → Variant B → Variant C` **in realtime in the browser** — no rebuild, no manual swap.

The toggle never reaches production: it is gated by `NODE_ENV` or a `?variants=1` query param, and production builds short-circuit to render only the original.

## Inputs to gather

If any are missing, ask the user:

1. **Target section.** Accept any of:
   - Explicit file path + CSS selector or component name (e.g., `src/components/Hero.tsx`, or `index.html` with selector `.hero`)
   - Natural-language description (e.g., "the hero on the homepage") — resolve by searching the codebase with Glob/Grep
   - Screenshot + rough pointer — resolve by reading likely files

   If you resolved the target (not given explicitly), **confirm it with the user** before generating variants.

2. **Tech stack.** Infer from `package.json` and file extensions; confirm if ambiguous. Supported: plain HTML/CSS/JS, React, Next.js, Vue, Svelte/SvelteKit, Astro.

3. **Scope — ask every invocation:**
   - *Visual-only* (recommended): same DOM structure, same handlers, only typography/layout/color/motion/spacing change. Produces a fair realistic comparison.
   - *Allowed to restructure*: variants may reorganize CTAs, change form flow, reorder content. Richer exploration but variants no longer behave identically.

4. **Aesthetic guardrails.** Any brand constraints (required fonts, colors, tone) or total freedom?

Use `AskUserQuestion` to collect these concisely when multiple are missing.

## Workflow

### Step 1 — Analyze the target

- Read the file containing the section.
- Identify semantic content: headings, body copy, CTAs, media, interactive elements, data bindings.
- Identify the section's **role** (hero, features grid, pricing, testimonial, nav, footer, CTA band, logo wall, FAQ, etc.) — this informs variant direction.
- Detect framework and styling approach: Tailwind, CSS Modules, styled-components, plain CSS, CSS-in-JS, etc.
- List every prop, state value, handler, and external data dependency the section touches. Variants must preserve all of these unless the user chose "restructure."

### Step 2 — Pick 3 distinct aesthetic directions

**Load all three references before choosing:**
- `references/variant-playbook.md` — aesthetic archetypes and role-specific starting points
- `references/design-principles.md` — baseline polish + accessibility rules; the **anti-AI-slop checklist**
- `references/signature-moves.md` — **the Boldness Quota.** Concrete award-site-level signature moves, required per variant, plus a curated font library and set-level diversity requirements

The goal is output that looks like a professional, daring designer made it — the kind of work that wins at design awards shows. Polished-but-safe is the failure mode. Commit to unusual moves.

Core rules:

- The three directions must be **meaningfully different from each other AND from the original**, not three flavors of the same idea.
- **Every variant must clear the Boldness Quota from `signature-moves.md`** (≥3 signature moves per variant, drawn from different categories, with at least one composition move and one typography move).
- **The set of three collectively must include**: one dark variant, one typography-led variant, one motion-heavy variant, at least one saturated/acid accent, and three different font pairings. See the set-level checklist in `signature-moves.md`.
- Vary on **polish axes** too: one variant leads with depth (layered shadows, overlap), one with typographic restraint, one with color commitment.
- Pick **characterful fonts** from the curated list in `signature-moves.md`. Inter / Roboto / system-ui as the only font is forbidden — it's the #1 AI tell.
- Apply the core design rules: colored grays (never pure `#888`), dark-gray on off-white (never `#000` on `#fff`), offset multi-layer shadows (never symmetric glows), hierarchy through weight + color (not just size), hue-rotate-to-brighten, non-linear spacing scales.
- **Name each direction explicitly** with words that convey the boldness (e.g., "Acid Editorial", "Kinetic Marquee", "Midnight Lookbook"). Avoid generic names like "Modern Clean" or "Variant A".

### Step 3 — Generate the 3 variant implementations

Create each variant as a sibling file next to the original. Naming convention:

- React/Next: `HeroVariantA.tsx`, `HeroVariantB.tsx`, `HeroVariantC.tsx` (original stays `Hero.tsx`)
- Vue: `HeroVariantA.vue`, etc.
- Svelte: `HeroVariantA.svelte`, etc.
- Vanilla HTML: `hero-variant-a.html` partials OR `<template id="hero-variant-a">` tags next to the original markup

Each variant must:

- Preserve all content, links, form `action`s, button handlers, and interactive behavior of the original (unless restructure was opted into)
- Match the project's framework and styling conventions
- Be self-contained: styles scoped, no global side effects, safe to mount/unmount repeatedly
- **Hit the Boldness Quota** from `references/signature-moves.md` — ≥3 signature moves per variant, including ≥1 composition move and ≥1 typography move. Use a characterful font (not Inter/Roboto/system-ui alone). Commit to the unusual moves; don't dilute them.
- **Obey `references/design-principles.md`.** As you write the CSS, run the anti-AI-slop checklist: tint grays toward brand temperature, offset multi-layer shadows (no symmetric glows), no `#000`-on-`#fff`, left-align paragraphs, non-linear spacing scale, hierarchy by weight + color rather than just size.
- Meet **WCAG contrast** (4.5:1 body, 3:1 large text), preserve visible focus rings, use semantic HTML, and respect `prefers-reduced-motion` for any added animation. Boldness does not override accessibility — motion-heavy variants MUST have a reduced-motion fallback that still looks intentional.
- Load any external fonts via `@import` or `<link>` at the top of the variant's CSS/JSX. Prefer Google Fonts for commercial-safe drop-ins; note the license if using a premium foundry.

**Do not modify the original** — it must remain byte-identical so "Original" in the toggle really is the original.

### Step 4 — Inject the variant switcher (dev-only)

Wrap the section's render site with a `VariantSwitcher` that:

- **Gates rendering on dev-only flags:**
  - Frameworks with bundlers: `process.env.NODE_ENV !== 'production' || new URLSearchParams(location.search).has('variants')`
  - Vanilla HTML: `location.hostname === 'localhost' || location.search.includes('variants=1')`
  - When the gate is false, render the original directly with zero runtime cost.
- Reads current variant from `localStorage` (key: `ui-variant:<section-id>`), defaults to `original`.
- Renders the floating toggle chip (Step 5).
- Renders the selected variant in place.
- On click: updates state and `localStorage` → realtime re-render.

Drop-in implementations per framework live in `references/toggle-ui.md`. Load it when you start writing the switcher.

### Step 5 — The toggle UI

Floating corner chip, `position: fixed`, top-right of viewport, 16px offset. Key behaviors:

- Four compact segments: `O · A · B · C` (single-letter) with the direction name as tooltip on hover.
- Active segment visually distinct (filled background, inverted text).
- Neutral monochrome styling with subtle `backdrop-filter: blur(8px)` — must not compete with any variant's aesthetic.
- Small **PREVIEW** badge on the chip so it can't be mistaken for production UI.
- Keyboard: arrow keys cycle segments, Enter selects, `V` key toggles chip visibility.
- Small `×` dismisses the chip for the session (`sessionStorage`) — useful for screenshotting.
- Multiple sections on the same page: chips stack vertically with a label prefix (e.g., "Hero", "Pricing"), each scoped by section id.
- ARIA: `role="radiogroup"`, each segment `role="radio"` with `aria-checked`.

Pull the exact CSS/JS from `references/toggle-ui.md`.

### Step 6 — Verify live

Before opening the browser, run **both** pre-flight checklists against every variant and iterate on anything that fails:

- **Boldness checklist** from `references/signature-moves.md` — count signature moves per variant, verify set-level diversity (dark / typography-led / motion-heavy / acid accent / three font pairings).
- **Pre-ship checklist** from `references/design-principles.md` — polish + accessibility.

If a variant passes polish but fails boldness, it's a "safe" variant — rework it with a stronger signature move from category 1, 2, or 3.

Then verify live:

1. Start the dev server (or open the static file in a browser).
2. Navigate to the page containing the section.
3. Confirm the toggle chip appears.
4. Click each of the 4 options — each must swap the section **instantly** with no page reload.
5. Reload the page — the last selected variant must persist.
6. Confirm the rest of the page is unaffected.
7. Confirm interactive behavior (forms submit, links navigate, buttons fire their handlers) inside every variant.
8. Tab through each variant — focus rings must be visible on every interactive element.
9. Build for production (or visit without `?variants=1`) and confirm the toggle does **not** appear and the original renders.

If you have access to Chrome DevTools MCP tools, use them to take screenshots of all 4 variants for the user.

### Step 7 — Handoff notes

Tell the user:

- **To promote a variant**: replace the original's contents with the chosen variant's contents, then delete the switcher wrapper and the unused variant files.
- **To remove entirely**: revert the switcher wrapper and delete `*Variant*` files. The original is untouched, so the page returns to its exact prior state.
- **To add more variants later**: re-invoke this skill on the same section; it will add `VariantD` (and so on) alongside the existing ones.

## Framework matrix

| Framework | Variant files | Switcher | State |
|---|---|---|---|
| React / Next.js | Sibling `.tsx` files | `<VariantSwitcher>` with `useState` + `useEffect` | `localStorage` |
| Vue 3 | Sibling `.vue` files | `<VariantSwitcher>` using `ref` + `watchEffect` | `localStorage` |
| Svelte / SvelteKit | Sibling `.svelte` files | `{#if}` block in wrapper | `$state` rune + `localStorage` |
| Vanilla HTML/CSS/JS | `<template>` tags or HTML partials | `<script>` swapping `innerHTML` | `localStorage` |
| Astro | Client island (`client:load`) around section | React/Vue/Svelte island | `localStorage` |

## Rules

- **Never modify the original section's file.** Original must stay byte-identical.
- **Never ship the switcher to production users.** The dev gate is mandatory, not optional.
- **Variants must not break page layout around them.** Test scroll, sticky siblings, and responsive breakpoints after injection.
- **Do not invent new content.** Keep headings, copy, and CTAs intact unless the user opted into restructure.
- **Design bar is non-negotiable.** Every variant must clear (a) the Boldness Quota in `references/signature-moves.md` AND (b) the anti-AI-slop + pre-ship checklists in `references/design-principles.md`. Variants that look like generic AI output (pure black/white, untinted grays, symmetric glow shadows, purple-pink gradients, centered paragraphs) are failures. Variants that are polished-but-safe (technically correct, zero signature moves, convergent defaults) are ALSO failures. The skill exists to produce award-quality design exploration, not more safe variants.
- **Accessibility is non-negotiable.** WCAG contrast, visible focus rings, semantic HTML, `prefers-reduced-motion` — apply on every variant.
- **Prefer the references.** Don't reinvent the toggle — pull from `references/toggle-ui.md` verbatim and adjust only the integration glue.
