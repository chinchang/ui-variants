# ui-variants

A skill that takes **any section of a website** and, in one shot, generates **3 alternate UI designs** for it, injects a **dev-only floating toggle**, and wires it up so you can cycle `Original → Variant A → Variant B → Variant C` **live in the browser** — no rebuild, no manual swap.

Built to help you A/B/C/D-compare real designs side-by-side before committing to one.

![chip](./hero-b.png)

---

## Install

Using the [`skills` CLI](https://www.npmjs.com/package/skills):

```bash
# Global (available in every project)
npx skills add chinchang/ui-master -g

# Or just this project
npx skills add chinchang/ui-master
```

> Replace `chinchang/ui-master` with the actual `<owner>/<repo>` path once pushed to GitHub.

To install from a local clone:

```bash
npx skills add ./path/to/ui-master -g
```

The `skills` CLI installs into the agent-specific skills directory (for example `~/.claude/skills/ui-variants/`, `~/.cursor/skills/ui-variants/`, etc., or the project-scoped equivalent). Use `-a <agent>` to target a specific agent and `-g` for global scope.

---

## Usage

Once installed, invoke the skill by name or via slash command (syntax varies by agent):

```
/ui-variants try 3 versions of the hero section
```

Or just describe what you want in plain language:

> give me 3 UI variants of the testimonial
> A/B the pricing block
> try alternate designs for the features grid

The skill will:

1. **Resolve the target** — accepts a file path + selector, a natural-language description, or a screenshot + rough pointer.
2. **Ask about scope** — visual-only (safer) or allow-restructure (richer exploration).
3. **Pick 3 distinct aesthetic directions** using a built-in design-principles reference (see `skills/ui-variants/references/design-principles.md`), varying on both aesthetic axes (editorial / depth-forward / unexpected) AND polish axes (typographic restraint / layered depth / color commitment).
4. **Generate 3 variant implementations** as sibling files alongside the original — preserving content, links, handlers, and interactive behavior by default.
5. **Inject a dev-only `VariantSwitcher`** around the section's render site. Gated on `NODE_ENV !== 'production'` or `?variants=1`. Production builds short-circuit to the original with zero runtime cost.
6. **Render a floating toggle chip** (bottom-center by default) showing `O · A · B · C` with tooltips. Clicking swaps the section in realtime; selection persists via `localStorage`.
7. **Verify live** — runs an anti-AI-slop pre-ship checklist against every variant, then browser-tests swapping, reload persistence, and focus-ring visibility.

---

## What makes the variants non-generic

The skill is opinionated about producing **award-site-level work** — the kind of output you'd see on design-awards sites, not yet-another-SaaS-landing. Two layers of guardrails keep variants out of the "polished but safe" zone:

### Layer 1 — Boldness Quota (the "crazy" requirement)

Every variant MUST include **≥3 signature moves** drawn from different categories (composition, typography, motion, texture, interaction). See `skills/ui-variants/references/signature-moves.md` for the full catalog. Examples:

- Extreme scale contrast (240px headline next to 11px tabular labels)
- Breaking the grid — diagonal composition, radical asymmetry, content rotated
- Oversized ornament — giant numerals, single punctuation mark at 40vw
- Novelty display fonts — Honk, Fraunces, Unbounded, Bungee, Climate Crisis
- Text scramble / magnetic cursor / scroll-pinned reveal
- Acid accent color — saturated lime, electric blue, hot magenta
- Ticker marquees, coordinate displays, duotone imagery, custom cursors

**And across the three variants**, the set MUST collectively include: one dark variant, one typography-led variant, one motion-heavy variant, at least one saturated accent, and three different font pairings.

### Layer 2 — Anti-AI-slop checklist (the "don't be generic" floor)

Every variant must also clear this polish + accessibility checklist (from `references/design-principles.md`):

- ❌ No pure `#000` on pure `#fff`
- ❌ No untinted `#888` / `#ccc` grays — colored grays only
- ❌ No symmetric `0 0 Xpx` glow shadows — offset + multi-layer only
- ❌ No purple-to-pink gradient as a primary accent
- ❌ No centered paragraphs longer than 3 lines
- ❌ No identical drop-shadow across every card
- ❌ No linearly-spaced 8/16/24/32 — non-linear scale required
- ❌ No hierarchy by size alone — weight + color carry the load
- ❌ No Inter / Roboto / system-ui as the sole font

10 iconic rules are baked into the workflow:

1. Hierarchy by weight + color, not size
2. Amplify by de-emphasizing
3. Design in grayscale first
4. Colored grays, not pure grays
5. Dark-gray on off-white, not black on white
6. Offset shadows (two-layer: ambient + direct)
7. Fewer borders
8. Not every button needs a fill
9. Too much whitespace by default, pull back
10. Hue-rotate to brighten, don't just lighten

Accessibility is non-negotiable: WCAG 4.5:1 body / 3:1 large text, visible focus rings, semantic HTML, `prefers-reduced-motion` respect.

---

## Supported frameworks

| Framework | Variant files | Switcher | State |
|---|---|---|---|
| React / Next.js | Sibling `.tsx` | `<VariantSwitcher>` + `useState`/`useEffect` | `localStorage` |
| Vue 3 | Sibling `.vue` | `<VariantSwitcher>` + `ref`/`watchEffect` | `localStorage` |
| Svelte / SvelteKit | Sibling `.svelte` | `{#if}` wrapper + `$state` rune | `localStorage` |
| Vanilla HTML/CSS/JS | `<template>` tags | `<script>` swapping `innerHTML` | `localStorage` |
| Astro | Client island | React/Vue/Svelte island | `localStorage` |

Drop-in templates live in `skills/ui-variants/references/toggle-ui.md`.

---

## Repo layout

```
.
├── README.md
├── LICENSE
├── skills/
│   └── ui-variants/
│       ├── SKILL.md                      # the skill entrypoint
│       └── references/
│           ├── design-principles.md      # polish + accessibility baseline
│           ├── signature-moves.md        # Boldness Quota + curated font library
│           ├── toggle-ui.md              # per-framework switcher templates
│           └── variant-playbook.md       # aesthetic archetypes + role-specific starts
├── index.html                            # demo site (vanilla HTML) you can try the skill on
├── styles.css
├── ui-variants.css                       # the switcher's CSS + generated variant styles
├── ui-variants.js                        # the vanilla-HTML switcher script
└── *.png                                 # reference screenshots of generated variants
```

---

## Try it on the included demo

The root contains a tiny landing page (`index.html`) for a fictional focus-timer app called **Drift**, already pre-seeded with variants for the hero and testimonial sections so you can see what the skill produces without running it.

Open it:

```bash
open index.html
```

A floating chip appears bottom-center with `O · A · B · C` for each variant-enabled section. Click through — the section swaps live.

Reference screenshots of the generated variants:

- Hero: `hero-original.png`, `hero-a.png` (Swiss Broadsheet), `hero-b.png` (Ambient Depth), `hero-c.png` (Midnight Terminal)
- Testimonial: `variant-original.png`, `variant-a.png` (Pull-Quote Brutalist), `variant-b.png` (Editorial Portrait), `variant-c.png` (Dev Log Terminal)

---

## Credits

Skill distribution format compatible with the [`skills` CLI by vercel-labs](https://github.com/vercel-labs/skills).

## License

MIT — see [LICENSE](./LICENSE).
