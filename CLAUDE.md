# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repo is

This repo is **two things at once**:

1. **A distributable Claude Code skill** (`skills/ui-variants/`) that generates 3 alternate UI designs for a target section of a website and wires up a live in-browser toggle. Installed via `npx skills add chinchang/ui-master` (see README).
2. **A vanilla-HTML reference implementation + demo** at the root (`demo.html`, `styles.css`, `ui-variants.css`, `ui-variants.js`) showing what the skill produces on a fictional focus-timer landing page called "Drift" — hero + testimonial sections already have 3 variants each.

The skill supports React/Next, Vue, Svelte, Astro, and vanilla HTML, but **only the vanilla-HTML switcher is actually implemented in this repo** (`ui-variants.js`). Per-framework templates for the others live as copy-paste snippets in `skills/ui-variants/references/toggle-ui.md`.

## Run the demo

No build, no package.json, no tests. It's static HTML:

```bash
open demo.html
```

The switcher chip is gated to `localhost`, `127.0.0.1`, `file://`, or `?variants=1`. Production hosting with a real domain hides the chip and renders originals.

## Vanilla-HTML switcher contract (`ui-variants.js`)

The runtime in this repo is driven by data-attributes, not code changes. To make a section variant-switchable:

- Wrap the section with `data-uiv-section="<id>"` (stable slug like `hero`, `testimonial`).
- Optionally add `data-uiv-names='{"a":"Swiss Broadsheet","b":"Ambient Depth","c":"Midnight Terminal"}'` for tooltip labels.
- For each variant, add a sibling `<template data-uiv-for="<id>" data-uiv-variant="a|b|c">…</template>`.
- The script captures the section's original `innerHTML` on load as `original`, discovers templates, and swaps `innerHTML` on click. Selection persists via `localStorage` key `ui-variant:<id>`; dismissal via `sessionStorage` key `ui-variant-dismissed:<id>`.
- `section.dataset.variant` is set to the active variant key (or removed for original), so variant CSS can scope itself via `[data-variant="a"] .hva { … }`.
- Multiple chips stack vertically (16 + index * 48 px from bottom).
- Keyboard: arrow keys cycle, `V` toggles chip visibility globally.

Variant CSS in `ui-variants.css` follows a per-variant class prefix convention (e.g., `hva__` for hero-variant-a, `hvb__` for hero-variant-b). Keep variants self-contained — no global selectors that leak outside the section.

## The skill (`skills/ui-variants/`)

`SKILL.md` is the entrypoint; it loads three reference files on demand:

- `references/variant-playbook.md` — aesthetic archetypes and role-specific starting points; rules for picking 3 *meaningfully* different directions.
- `references/design-principles.md` — anti-AI-slop checklist + polish/accessibility baseline.
- `references/signature-moves.md` — the **Boldness Quota**: every variant needs ≥3 signature moves across different categories, and the *set of three* must collectively include a dark variant, a typography-led variant, a motion-heavy variant, a saturated accent, and three different font pairings.
- `references/toggle-ui.md` — drop-in switcher templates per framework.

When editing the skill, two non-negotiables are baked into every rule and should not be relaxed:

- **Original must stay byte-identical.** Variants are sibling files / sibling `<template>` tags only. Never edit the original section.
- **The design bar.** Variants that are polished-but-safe are treated as failures just like variants that are generic-AI-slop. The skill exists to push past the convergent default.

## Conventions worth knowing before editing

- The `uiv-` CSS prefix is reserved for the chip itself (`.uiv-chip`, `.uiv-chip__seg`, etc.). Don't reuse it for variant styles.
- Variant class prefixes are two-to-three letters keyed on section+variant (`hva`, `hvb`, `hvc` for hero A/B/C; `tva`, `tvb`, `tvc` for testimonial). Follow this when adding new sections.
- When you change the skill's behavior, update both `SKILL.md` AND the matching reference file — the references are loaded lazily and must agree with SKILL.md.
- README.md repeats a lot of what's in SKILL.md (install instructions, framework matrix, feature list). Keep them in sync when changing user-visible behavior.
