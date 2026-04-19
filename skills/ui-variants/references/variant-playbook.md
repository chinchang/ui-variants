# Variant Playbook

How to pick three aesthetic directions that are genuinely different from each other **and** from the original — not three flavors of the same idea.

## Core rule: differentiation across three axes

Every variant should differ from the other two (and from the original) on at least two of these axes:

| Axis | Poles |
|---|---|
| **Typography** | display serif ↔ geometric sans ↔ mono ↔ humanist ↔ novelty/display |
| **Color mood** | bright/saturated ↔ muted/dusty ↔ monochrome ↔ dark/neon ↔ earth/natural |
| **Density** | generous negative space ↔ balanced ↔ packed/maximalist |
| **Layout logic** | centered/symmetrical ↔ asymmetric/off-grid ↔ grid-broken/overlapping |
| **Motion** | static ↔ subtle micro-interactions ↔ ambient loops ↔ scroll-driven ↔ interactive/physics |
| **Surface** | flat ↔ layered/glass ↔ textured/grainy ↔ dimensional/3D |

If two variants share four or more poles, they are too similar. Change one before implementing.

## Anti-patterns (reject on sight)

- Three dark-mode variants. Pick at most one.
- Three serif-body variants. Vary the type pairing.
- "Rounded corners" vs "sharper rounded corners" vs "slightly rounded corners" — these are not distinct directions.
- Three variants that all use gradient purple-to-pink. That is the generic AI aesthetic, not a direction.
- Swapping the accent color as the only change. Color alone isn't a direction.

## Aesthetic archetypes (starter library)

Use these as launch pads — **do not** name the variants after the archetype verbatim. Derive a project-specific name.

### Editorial / Magazine
Large serif display type, generous margins, restrained palette (often cream/black/one accent), rule lines as dividers. References: *The New Yorker*, *Monocle*, *Kinfolk*.

### Brutalist / Raw
Unstyled-looking, system-font-adjacent (but deliberate), harsh grid, high-contrast, visible structure (borders, grids, debug aesthetic), monospaced accents. References: early web, gallery sites, *Bloomberg*.

### Neo-Retro Terminal
Monospace throughout, phosphor green or amber accents, scanline or CRT texture, block cursors, ASCII-art embellishments. Works for technical products.

### Maximalist / Y2K
Bold typography stacks, chromatic gradients, grain, sticker-like elements, rotated components, marquee scrolls, sound effects on hover.

### Soft / Organic
Rounded geometry, warm neutrals with one muted accent, blob shapes, hand-drawn arrows or annotations, gentle eased motion. Works for wellness, lifestyle, DTC.

### Industrial / Utilitarian
Tight letter-spacing on condensed sans, coordinate-style annotations, numeric grids, engineering-blue or safety-orange accents, blueprint feel.

### Luxury / Refined
Minimal type, wide letter-spacing on small caps, plenty of negative space, single hero image, tasteful parallax, elegant easing. No more than two colors.

### Playful / Toy
Saturated primaries, thick borders, drop shadows as offsets, oversized clickable elements, springy animations, illustrative icons.

### Art Deco / Geometric
Symmetric layouts, metallic accents, stepped type, ornamental rules, tall thin proportions.

### Editorial-Tech Hybrid
Mono for metadata and serif for body — looks like *Stripe Press* or technical long-reads. High trust for SaaS and docs.

### Glassmorphic / Frosted
Layered translucent panels, blurred backdrops, subtle gradient globes behind the glass. Use sparingly — already trending toward generic.

### Swiss / International
Helvetica-adjacent sans, rigid grids, red accent on white, numbered sections. Restrained, functional, dateless.

## Role-specific starting points

### Hero
- One **typographic** variant — the headline IS the art.
- One **media-forward** variant — full-bleed image/video with text overlay.
- One **interactive/animated** variant — spline scene, WebGL gradient, cursor-reactive element, or live data.

### Features grid
- One **bento grid** with varied tile sizes and one hero tile.
- One **sequential storytelling** variant (numbered, scroll-snap, side-by-side).
- One **dense compact table** with icons — for users who want to scan.

### Pricing
- One **comparison table** with a highlighted recommended column.
- One **single-focus** variant (one plan front and center, others collapsed).
- One **calculator/slider** variant — user picks usage, price updates live.

### Testimonial / social proof
- One **quote-forward** with giant pull quote and minimal author.
- One **logo wall + stat band** (quantitative proof).
- One **carousel of video thumbnails** with play-on-hover.

### Navigation
- One **classic horizontal nav** with a bold type redesign.
- One **vertical sidebar** (magazine-style).
- One **mega-menu / command-menu** hybrid with search.

### Footer
- One **sitemap-style** with dense columns and decorative ornament.
- One **oversized wordmark** variant with minimal links.
- One **newsletter-first** variant with the signup as the hero element.

### FAQ
- One **accordion** (standard, typographically elevated).
- One **two-column with live search**.
- One **conversational/chat-style** (Q and A as messages).

### CTA band
- One **quiet, typographic** variant (no box, just a headline and one link).
- One **loud, bordered banner** variant.
- One **interactive** variant (form inline, or progress bar hinting at value).

## Naming the variants

The toggle shows one-letter segments (`O · A · B · C`) but **tooltips show the full direction name** and the user sees the names in handoff notes. Good names:

- Describe the direction, not the change: ✅ "Editorial Serif" ❌ "Variant A"
- Are 2–3 words: ✅ "Neo-Retro Terminal" ❌ "Terminal with green text and mono font"
- Are project-specific when brand has personality: ✅ "Runway Hi-Viz" for a logistics brand ❌ "Safety Orange"

## Sanity check before implementing

Before writing variant code, answer these out loud:

1. If I described the three variants to a designer over the phone, would they draw three clearly different things?
2. Does the spread feel like three real options a product team would argue about — or three minor permutations?
3. Does each variant have a reason to exist beyond "it's different"?

If any answer is no, re-pick.
