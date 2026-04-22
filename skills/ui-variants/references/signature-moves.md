# Signature Moves — Award-Site Level Boldness

This reference exists because `design-principles.md` covers *polish* and *accessibility* but not *daring*. Without explicit daring, LLM-generated variants converge on polished-but-safe — technically correct, aesthetically boring, and still recognizably AI.

Award-winning design sites do something different: they **commit to unusual moves**. Extreme scale contrast. Acid color. Broken grids. Kinetic type. Custom cursors. Oversized ornament. Novel fonts. Variants that win have at least a few of these; variants that lose have none.

**Load this file at Step 2 alongside `variant-playbook.md` and `design-principles.md`.** It defines hard requirements (the Boldness Quota) that every variant MUST clear before Step 6 verification.

---

## The Boldness Quota (hard requirement)

Every single variant (A, B, C) MUST include **at least 3 signature moves** drawn from different categories below. Counting:

- 1 move from **Composition** is required (minimum)
- 1 move from **Typography** is required (minimum)
- 1 additional move from Composition / Motion / Texture / Interaction

**Across the three variants, you must cover:**

- [ ] At least **one dark-mode** variant (dark/saturated background, not just a dim light theme)
- [ ] At least **one typography-led** variant (no imagery or media, the type IS the art)
- [ ] At least **one motion-heavy** variant (≥2 motion signature moves that aren't just a spin or fade)
- [ ] At least **one saturated/acid** accent (pure lime, electric blue, hot magenta, safety orange — not muted brand orange)
- [ ] **Three different font pairings** (no two variants use the same fonts)

If the three variants collectively fail any of these boxes, rework before moving on. "Safe but polished" is the failure mode this file exists to prevent.

---

## The 5 categories of signature moves

### 1 — Composition (pick ≥1 per variant)

- **Extreme scale contrast** — 180–280px display headline paired with 10–12px tabular labels in the same viewport. Nothing in between.
- **Break the grid** — diagonal composition, elements radically off-center, a single column pushed to 1/8 width, content rotated 6–15° off horizontal.
- **Bleed off edges** — headline or image intentionally cropped at the viewport edge. The word ends mid-letter at the right margin.
- **Oversized ornament** — giant `01`, `02`, `03` numerals as decoration; or a single punctuation mark (a giant `"`, `.`, `&`) at 40vw size.
- **Vertical / rotated text** — side labels running vertically along the viewport edge; rotated CTAs; upright vertical columns of metadata.
- **Radical asymmetry** — not just "off-center" but 20/80 or 15/85 splits with aggressive rhythm.
- **Oversized wordmark** — a logo or brand name at 25–35vw width, treated as a compositional anchor.
- **Content-as-background** — huge repeating wordmark, manifesto paragraph, or ticker as the background layer.
- **Coordinate / telemetry display** — `40.7°N · 74.0°W · 03:14 UTC · session 014` as decorative micro-data.
- **Number stack** — rank numerals (`№ 01 / 03`) as vertical sidebar composition.

### 2 — Typography (pick ≥1 per variant)

- **Novelty display face** — use something with real character: Honk, Bungee, Rubik Mono One, Climate Crisis, Rubik Wet Paint, Monument Extended, Anton, Unbounded, Archivo Black, Fraunces (at heaviest weight), Editorial New.
- **Mixed case as composition** — "Deep WORK, quietly." or "deep Work Quietly" within the same phrase. Case changes become a design decision, not a typo.
- **Ligature / stylistic alternates** — turn on `font-variant-ligatures` and `font-feature-settings` for swashes, discretionary ligatures, old-style figures.
- **Inline type treatments** — strikethrough that corrects ("~~safe~~ *dangerous*"), highlighter marker on a phrase, bracketed `[asides]`, editor's marks.
- **Drop cap at sentence length** — first word scaled 4–6×, wrapping around it, Dominican Missal–style.
- **Type-as-texture** — a word or phrase repeated as a pattern filling a shape or column.
- **Variable-font animation** — weight or width axis animates on hover or scroll. `font-variation-settings` morphs live.
- **Stacked-weight hierarchy** — same word appears 3× at 3 different weights stacked.
- **Vertical type column** — `writing-mode: vertical-rl` used for labels or entire sections.

### 3 — Motion (pick ≥1 for motion-heavy variant)

- **Text scramble / decrypt on enter** — characters shuffle through random glyphs before settling to final text.
- **Magnetic cursor** — CTAs subtly translate toward the cursor when it approaches (15–30px pull, springy return).
- **Scroll-pinned reveal** — headline pins while sub-content scrolls behind, then unpins.
- **Staggered letter reveal** — each letter of the headline animates in with 40–80ms stagger, with a slight overshoot easing.
- **Marquee / ticker band** — infinite horizontal scroll of repeated text or logos, often crossing the full viewport.
- **Morphing shapes** — SVG `path` with animated `d` attribute, or a blob with keyframed control points.
- **Cursor-reactive visual** — WebGL/canvas shader or SVG filter that distorts around the cursor position.
- **Hover morph (image/object)** — image duotone shifts, crop reveals secondary layer, or object inflates on hover.
- **Ink-bleed transition** — clip-path reveals content as if ink spreads into paper.
- **Parallax depth stack** — 3+ scroll-speed layers simulating z-axis (careful: accessibility).

All motion MUST be wrapped in `@media (prefers-reduced-motion: no-preference)` or have an explicit reduced-motion fallback.

### 4 — Texture / Material (pick ≥1 across the set)

- **Heavy grain overlay** — SVG feTurbulence noise at 30–50% opacity on every surface.
- **Duotone imagery** — CSS `filter: grayscale(1) brightness(0.9)` layered with `background-color` via `mix-blend-mode`.
- **Noise gradient mesh** — layered radial gradients (warm + cool) with grain overlay. See Variant B of the demo hero for a canonical example.
- **Paper / sticky-note aesthetic** — subtle paper texture, handwritten annotation, pinned-corner shadow simulating a clipped page.
- **Scanline / CRT** — 1px horizontal stripe overlay at 4–8% opacity with slight `hue-rotate`.
- **Watercolor blobs / ink drops** — soft irregular colored blobs as backdrops (SVG paths with heavy blur).
- **Wireframe grid overlay** — visible construction grid underneath content with coordinate labels.
- **Selective glow / bloom** — single element gets a large soft glow halo (only on saturated accents).

### 5 — Interaction (pick ≥1 across the set)

- **Custom cursor** — replace system cursor with a branded shape, a pill showing the current action ("Drag", "View"), or a liquid blob that trails with delay.
- **Hover-reveal secondary state** — image swaps to a detail shot, hidden copy unlocks, or object flips in 3D.
- **Draggable / physics** — physics-based movable elements (spring-bounded, throwable).
- **Live data decoration** — current time, weather, visitor count, or playlist track rendered as ornament.
- **Progress / scroll indicator** — oversized scroll progress bar or circular scroll indicator as a design element, not chrome.

---

## Curated font library

**Use something with character. The default "Inter / Roboto / system-ui" choice is the #1 AI tell.** Each variant should use a different pairing.

### Free / Google Fonts (commercial-safe, drop-in)

- **Display serifs**: Fraunces (variable, versatile), Young Serif, Instrument Serif, DM Serif Display, Cormorant Garamond, Playfair Display, Newsreader
- **Display sans**: Unbounded, Archivo Black, Anton, Bricolage Grotesque, Syne, Space Grotesk (only if paired unusually)
- **Novelty display**: Honk (playful), Bungee (bold), Climate Crisis, Rubik Mono One, Rubik Wet Paint, Rubik Doodle Shadow, Monoton, Major Mono Display
- **Mono**: JetBrains Mono, Space Mono, IBM Plex Mono, Fira Code, Red Hat Mono
- **Characterful body**: Lora, Spectral, Literata, Fraunces (at 400), Source Serif 4
- **Handwritten / sketch**: Caveat, Kalam, Special Elite (typewriter), Permanent Marker

### Premium (license required; substitute with closest Google Font if not licensed)

- Pangram Pangram: PP Neue Machina, PP Editorial New, PP Editorial Old, PP Right Grotesk, PP Object Sans
- Colophon: Monument Extended, Monument Grotesk
- Grilli Type: GT America, GT Sectra, GT Flexa
- Linotype: Neue Haas Grotesk, Neue Haas Unica
- Studio Feixen: Feixen Sans

### Pairing rules

- **Distinctive + neutral**: pair ONE characterful face (display) with a neutral workhorse (body). Never two characterful faces fighting.
- **Mono + serif**: a classic award-site combo. Try it for at least one variant.
- **Weird + weird allowed once**: one of your three variants may pair two characterful faces if executed intentionally (e.g., Honk + Space Mono).
- **Never use Inter as the only font** with no display pairing. That's the generic-AI baseline.

---

## Color — commit, don't accommodate

Award-site palettes are rarely "tasteful." They're **committed**.

- **Acid accent** — one variant MUST use a saturated accent color: lime (`#CCFF00`, `#C6FF00`), electric blue (`#0050FF`, `#3A00FF`), hot magenta (`#FF0080`), safety orange (`#FF5A1F`), volt yellow (`#DAFF00`).
- **Monochrome + one acid** — 95% a single hue (black/cream/burgundy/forest) + 5% acid accent. Highest commitment.
- **Clashing on purpose** — red + green, orange + pink, pink + burgundy. Confident, not accidental.
- **Duotone images** — two-color filter on all media.
- **Colored shadows** — not black shadows; use hue-shifted dark of the background color (warm-dark shadow on warm surfaces).

---

## Inspiration anchors (named patterns, not copied sites)

Study sites that win annual design awards. Without naming specific sites, the patterns that recur:

- **Editorial / Magazine Maximalist** — giant serif, rule lines, bylines, masthead, column rules, oversized numerals, drop caps. Like a printed broadsheet pushed to the extreme.
- **Studio Portfolio Brutalist** — raw, all-caps, monospace-heavy, high-contrast, intentional ugliness, black on off-white with a single acid accent.
- **Fashion Lookbook** — dense full-bleed imagery, tiny captions, vertical labels, duotone, scroll-pin storytelling.
- **Agency Kinetic** — motion-heavy, text scramble, cursor effects, horizontal scroll sections, custom transitions between sections.
- **Product Launch / Hero Reveal** — oversized wordmark, scroll-pinned headline, parallax 3D object, ambient gradient backdrop.
- **Festival / Event Poster** — dense layered type, rotated labels, date/location coordinates, loud saturated palette.
- **Manifesto / Essay Longform** — massive type, minimal chrome, scroll-driven pacing, drop caps, pull quotes at large scale.
- **Live Data Dashboard** — tabular numerics as decoration, ticker bands, coordinates, timestamps, clock.

---

## The "safe interpretation" trap — things NOT to do

When writing a variant, if you find yourself defaulting to any of these, STOP and pick something from above instead:

- ❌ Two-column hero (headline + lede on left, media on right) — too obvious.
- ❌ Centered everything — the safe fallback.
- ❌ Gradient background in "brand colors" — timid.
- ❌ Circle/pill buttons with soft shadow — generic SaaS.
- ❌ Stock-photo hero with overlay text — magazine-meets-AI.
- ❌ "Clean, minimal" as an aesthetic goal — say what you ARE, not what you're not.
- ❌ Light drop shadow under every card — elevation-by-default.
- ❌ Using only 1 font — always pair.
- ❌ Headline ≤ 64px when the section deserves more.
- ❌ Arrow icon inside every CTA — be specific with the ornament.

---

## Boldness checklist (run before Step 6 verification, per variant)

For each variant, count signature moves:

- [ ] ≥1 composition move from category 1
- [ ] ≥1 typography move from category 2 (and using a characterful font, not Inter/Roboto/system)
- [ ] ≥1 additional move from category 3 / 4 / 5
- [ ] Passes all `design-principles.md` polish rules
- [ ] Meets WCAG 4.5:1 contrast despite the boldness

And across the set:

- [ ] Dark variant present
- [ ] Typography-led variant present
- [ ] Motion-heavy variant present
- [ ] Saturated/acid accent appears at least once
- [ ] Three different font pairings

If either checklist fails, rework. Safe variants fail this skill's promise.
