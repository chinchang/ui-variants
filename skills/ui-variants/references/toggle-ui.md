# Toggle UI Templates

Drop-in switcher + toggle chip implementations for each supported framework. Copy verbatim, rename the component, and wire in the variant components.

## Conventions (apply to all frameworks)

- **localStorage key**: `ui-variant:<section-id>` where `<section-id>` is a stable slug the skill picks (e.g., `hero`, `pricing`, `features`).
- **Values**: `"original" | "a" | "b" | "c"`. Default `"original"`.
- **sessionStorage key** (chip dismiss): `ui-variant-dismissed:<section-id>` → `"1"` when dismissed.
- **Dev-only gate**: switcher renders the chip + selector only when the gate passes; otherwise renders the original directly.
- **Stacking**: when multiple switchers live on one page, each chip reads its own position index and offsets `top` by `16 + index * 48` px.

## Shared CSS (framework-agnostic)

```css
/* ui-variants-chip.css — import once per page, or inline */
.uiv-chip {
  position: fixed;
  right: 16px;
  top: 16px;
  z-index: 2147483000;
  display: inline-flex;
  align-items: center;
  gap: 4px;
  padding: 4px;
  font-family: ui-monospace, SFMono-Regular, Menlo, monospace;
  font-size: 11px;
  color: #fff;
  background: rgba(17, 17, 17, 0.78);
  backdrop-filter: blur(8px);
  -webkit-backdrop-filter: blur(8px);
  border: 1px solid rgba(255, 255, 255, 0.12);
  border-radius: 999px;
  box-shadow: 0 4px 16px rgba(0, 0, 0, 0.2);
  user-select: none;
}
.uiv-chip__label {
  padding: 0 8px 0 10px;
  opacity: 0.55;
  letter-spacing: 0.04em;
  text-transform: uppercase;
}
.uiv-chip__seg {
  appearance: none;
  background: transparent;
  border: 0;
  color: inherit;
  font: inherit;
  width: 26px;
  height: 26px;
  border-radius: 999px;
  cursor: pointer;
  display: inline-flex;
  align-items: center;
  justify-content: center;
  transition: background 120ms ease, color 120ms ease;
}
.uiv-chip__seg:hover { background: rgba(255, 255, 255, 0.08); }
.uiv-chip__seg[aria-checked="true"] {
  background: #fff;
  color: #111;
  font-weight: 600;
}
.uiv-chip__badge {
  margin: 0 4px 0 6px;
  padding: 2px 6px;
  font-size: 9px;
  letter-spacing: 0.1em;
  background: rgba(255, 255, 255, 0.12);
  border-radius: 4px;
}
.uiv-chip__close {
  appearance: none;
  background: transparent;
  border: 0;
  color: inherit;
  opacity: 0.5;
  cursor: pointer;
  width: 22px;
  height: 26px;
  font-size: 14px;
  line-height: 1;
}
.uiv-chip__close:hover { opacity: 1; }
```

---

## React / Next.js

```tsx
// VariantSwitcher.tsx
import { useEffect, useMemo, useState } from 'react';

type VariantKey = 'original' | 'a' | 'b' | 'c';

type Props = {
  sectionId: string;
  names: { a: string; b: string; c: string };
  original: React.ReactNode;
  variantA: React.ReactNode;
  variantB: React.ReactNode;
  variantC: React.ReactNode;
  stackIndex?: number;
};

const isDev = () => {
  if (typeof window === 'undefined') return false;
  const flagged = new URLSearchParams(window.location.search).has('variants');
  return process.env.NODE_ENV !== 'production' || flagged;
};

export function VariantSwitcher(p: Props) {
  const [mounted, setMounted] = useState(false);
  const [variant, setVariant] = useState<VariantKey>('original');
  const [dismissed, setDismissed] = useState(false);

  const storageKey = `ui-variant:${p.sectionId}`;
  const dismissKey = `ui-variant-dismissed:${p.sectionId}`;

  useEffect(() => {
    setMounted(true);
    try {
      const saved = localStorage.getItem(storageKey) as VariantKey | null;
      if (saved && ['original', 'a', 'b', 'c'].includes(saved)) setVariant(saved);
      if (sessionStorage.getItem(dismissKey) === '1') setDismissed(true);
    } catch {}
  }, [storageKey, dismissKey]);

  useEffect(() => {
    if (!mounted) return;
    try { localStorage.setItem(storageKey, variant); } catch {}
  }, [variant, mounted, storageKey]);

  useEffect(() => {
    const onKey = (e: KeyboardEvent) => {
      if (e.key.toLowerCase() === 'v' && !e.metaKey && !e.ctrlKey && !e.altKey) {
        const target = e.target as HTMLElement | null;
        if (target && /^(INPUT|TEXTAREA|SELECT)$/.test(target.tagName)) return;
        setDismissed(d => {
          const next = !d;
          try { sessionStorage.setItem(dismissKey, next ? '1' : '0'); } catch {}
          return next;
        });
      }
    };
    window.addEventListener('keydown', onKey);
    return () => window.removeEventListener('keydown', onKey);
  }, [dismissKey]);

  const body = useMemo(() => {
    switch (variant) {
      case 'a': return p.variantA;
      case 'b': return p.variantB;
      case 'c': return p.variantC;
      default:  return p.original;
    }
  }, [variant, p.original, p.variantA, p.variantB, p.variantC]);

  if (!isDev()) return <>{p.original}</>;
  if (!mounted) return <>{p.original}</>;

  const segs: Array<{ key: VariantKey; letter: string; title: string }> = [
    { key: 'original', letter: 'O', title: 'Original' },
    { key: 'a',        letter: 'A', title: p.names.a },
    { key: 'b',        letter: 'B', title: p.names.b },
    { key: 'c',        letter: 'C', title: p.names.c },
  ];

  const top = 16 + (p.stackIndex ?? 0) * 48;

  return (
    <>
      {body}
      {!dismissed && (
        <div
          className="uiv-chip"
          style={{ top }}
          role="radiogroup"
          aria-label={`${p.sectionId} variant switcher`}
          onKeyDown={e => {
            const idx = segs.findIndex(s => s.key === variant);
            if (e.key === 'ArrowRight' || e.key === 'ArrowDown') {
              setVariant(segs[(idx + 1) % segs.length].key);
              e.preventDefault();
            } else if (e.key === 'ArrowLeft' || e.key === 'ArrowUp') {
              setVariant(segs[(idx - 1 + segs.length) % segs.length].key);
              e.preventDefault();
            }
          }}
        >
          <span className="uiv-chip__label">{p.sectionId}</span>
          {segs.map(s => (
            <button
              key={s.key}
              className="uiv-chip__seg"
              role="radio"
              aria-checked={variant === s.key}
              title={s.title}
              onClick={() => setVariant(s.key)}
            >
              {s.letter}
            </button>
          ))}
          <span className="uiv-chip__badge">PREVIEW</span>
          <button
            className="uiv-chip__close"
            aria-label="Hide variant switcher for this session"
            onClick={() => {
              setDismissed(true);
              try { sessionStorage.setItem(dismissKey, '1'); } catch {}
            }}
          >×</button>
        </div>
      )}
    </>
  );
}
```

**Usage:**

```tsx
import { VariantSwitcher } from './VariantSwitcher';
import { Hero } from './Hero';
import { HeroVariantA } from './HeroVariantA';
import { HeroVariantB } from './HeroVariantB';
import { HeroVariantC } from './HeroVariantC';

<VariantSwitcher
  sectionId="hero"
  names={{ a: 'Editorial Serif', b: 'Brutalist Grid', c: 'Neo-Retro Terminal' }}
  original={<Hero {...heroProps} />}
  variantA={<HeroVariantA {...heroProps} />}
  variantB={<HeroVariantB {...heroProps} />}
  variantC={<HeroVariantC {...heroProps} />}
/>
```

**Next.js app-router note:** this is a client component. Add `'use client';` at the top.

---

## Vue 3

```vue
<!-- VariantSwitcher.vue -->
<script setup lang="ts">
import { ref, computed, onMounted, onUnmounted, watch } from 'vue';

type VariantKey = 'original' | 'a' | 'b' | 'c';

const props = defineProps<{
  sectionId: string;
  names: { a: string; b: string; c: string };
  stackIndex?: number;
}>();

const variant = ref<VariantKey>('original');
const dismissed = ref(false);
const mounted = ref(false);
const storageKey = `ui-variant:${props.sectionId}`;
const dismissKey = `ui-variant-dismissed:${props.sectionId}`;

const isDev = () => {
  if (typeof window === 'undefined') return false;
  const flagged = new URLSearchParams(window.location.search).has('variants');
  return (import.meta as any).env?.DEV || flagged;
};

const onKey = (e: KeyboardEvent) => {
  if (e.key.toLowerCase() !== 'v' || e.metaKey || e.ctrlKey || e.altKey) return;
  const t = e.target as HTMLElement | null;
  if (t && /^(INPUT|TEXTAREA|SELECT)$/.test(t.tagName)) return;
  dismissed.value = !dismissed.value;
  try { sessionStorage.setItem(dismissKey, dismissed.value ? '1' : '0'); } catch {}
};

onMounted(() => {
  mounted.value = true;
  try {
    const saved = localStorage.getItem(storageKey) as VariantKey | null;
    if (saved && ['original', 'a', 'b', 'c'].includes(saved)) variant.value = saved;
    if (sessionStorage.getItem(dismissKey) === '1') dismissed.value = true;
  } catch {}
  window.addEventListener('keydown', onKey);
});
onUnmounted(() => window.removeEventListener('keydown', onKey));

watch(variant, v => {
  try { localStorage.setItem(storageKey, v); } catch {}
});

const segs = computed(() => [
  { key: 'original' as const, letter: 'O', title: 'Original' },
  { key: 'a' as const,        letter: 'A', title: props.names.a },
  { key: 'b' as const,        letter: 'B', title: props.names.b },
  { key: 'c' as const,        letter: 'C', title: props.names.c },
]);

const top = computed(() => 16 + ((props.stackIndex ?? 0) * 48));
const enabled = computed(() => isDev() && mounted.value);
</script>

<template>
  <template v-if="!enabled">
    <slot name="original" />
  </template>
  <template v-else>
    <slot v-if="variant === 'original'" name="original" />
    <slot v-else-if="variant === 'a'" name="a" />
    <slot v-else-if="variant === 'b'" name="b" />
    <slot v-else-if="variant === 'c'" name="c" />
    <div
      v-if="!dismissed"
      class="uiv-chip"
      :style="{ top: top + 'px' }"
      role="radiogroup"
      :aria-label="`${sectionId} variant switcher`"
    >
      <span class="uiv-chip__label">{{ sectionId }}</span>
      <button
        v-for="s in segs"
        :key="s.key"
        class="uiv-chip__seg"
        role="radio"
        :aria-checked="variant === s.key"
        :title="s.title"
        @click="variant = s.key"
      >{{ s.letter }}</button>
      <span class="uiv-chip__badge">PREVIEW</span>
      <button
        class="uiv-chip__close"
        aria-label="Hide variant switcher for this session"
        @click="() => { dismissed = true; try { sessionStorage.setItem(dismissKey, '1'); } catch {} }"
      >×</button>
    </div>
  </template>
</template>
```

**Usage:**

```vue
<VariantSwitcher
  section-id="hero"
  :names="{ a: 'Editorial Serif', b: 'Brutalist Grid', c: 'Neo-Retro Terminal' }"
>
  <template #original><Hero v-bind="heroProps" /></template>
  <template #a><HeroVariantA v-bind="heroProps" /></template>
  <template #b><HeroVariantB v-bind="heroProps" /></template>
  <template #c><HeroVariantC v-bind="heroProps" /></template>
</VariantSwitcher>
```

---

## Svelte 5

```svelte
<!-- VariantSwitcher.svelte -->
<script lang="ts">
  import { onMount } from 'svelte';

  type VariantKey = 'original' | 'a' | 'b' | 'c';

  let { sectionId, names, stackIndex = 0, original, a, b, c } = $props<{
    sectionId: string;
    names: { a: string; b: string; c: string };
    stackIndex?: number;
    original: import('svelte').Snippet;
    a: import('svelte').Snippet;
    b: import('svelte').Snippet;
    c: import('svelte').Snippet;
  }>();

  let variant = $state<VariantKey>('original');
  let dismissed = $state(false);
  let mounted = $state(false);

  const storageKey = `ui-variant:${sectionId}`;
  const dismissKey = `ui-variant-dismissed:${sectionId}`;

  const isDev = () => {
    if (typeof window === 'undefined') return false;
    const flagged = new URLSearchParams(window.location.search).has('variants');
    return (import.meta as any).env?.DEV || flagged;
  };

  onMount(() => {
    mounted = true;
    try {
      const saved = localStorage.getItem(storageKey) as VariantKey | null;
      if (saved && ['original','a','b','c'].includes(saved)) variant = saved;
      if (sessionStorage.getItem(dismissKey) === '1') dismissed = true;
    } catch {}
    const onKey = (e: KeyboardEvent) => {
      if (e.key.toLowerCase() !== 'v' || e.metaKey || e.ctrlKey || e.altKey) return;
      const t = e.target as HTMLElement | null;
      if (t && /^(INPUT|TEXTAREA|SELECT)$/.test(t.tagName)) return;
      dismissed = !dismissed;
      try { sessionStorage.setItem(dismissKey, dismissed ? '1' : '0'); } catch {}
    };
    window.addEventListener('keydown', onKey);
    return () => window.removeEventListener('keydown', onKey);
  });

  $effect(() => {
    if (!mounted) return;
    try { localStorage.setItem(storageKey, variant); } catch {}
  });

  const segs = [
    { key: 'original' as const, letter: 'O', title: 'Original' },
    { key: 'a' as const,        letter: 'A', title: names.a },
    { key: 'b' as const,        letter: 'B', title: names.b },
    { key: 'c' as const,        letter: 'C', title: names.c },
  ];
</script>

{#if !isDev() || !mounted}
  {@render original()}
{:else}
  {#if variant === 'original'}{@render original()}
  {:else if variant === 'a'}{@render a()}
  {:else if variant === 'b'}{@render b()}
  {:else}{@render c()}{/if}

  {#if !dismissed}
    <div
      class="uiv-chip"
      style="top: {16 + stackIndex * 48}px"
      role="radiogroup"
      aria-label="{sectionId} variant switcher"
    >
      <span class="uiv-chip__label">{sectionId}</span>
      {#each segs as s}
        <button
          class="uiv-chip__seg"
          role="radio"
          aria-checked={variant === s.key}
          title={s.title}
          onclick={() => variant = s.key}
        >{s.letter}</button>
      {/each}
      <span class="uiv-chip__badge">PREVIEW</span>
      <button
        class="uiv-chip__close"
        aria-label="Hide variant switcher for this session"
        onclick={() => { dismissed = true; try { sessionStorage.setItem(dismissKey, '1'); } catch {} }}
      >×</button>
    </div>
  {/if}
{/if}
```

---

## Vanilla HTML / CSS / JS

Place variants as inert `<template>` siblings of the section. The script mounts the switcher and swaps the section's `innerHTML` on toggle.

```html
<!-- Original section -->
<section id="hero" data-uiv-section="hero" data-uiv-names='{"a":"Editorial Serif","b":"Brutalist Grid","c":"Neo-Retro Terminal"}'>
  <!-- original markup here -->
</section>

<!-- Variants (inert until mounted) -->
<template data-uiv-for="hero" data-uiv-variant="a"><!-- variant A markup --></template>
<template data-uiv-for="hero" data-uiv-variant="b"><!-- variant B markup --></template>
<template data-uiv-for="hero" data-uiv-variant="c"><!-- variant C markup --></template>

<script>
(() => {
  const gate = location.hostname === 'localhost'
    || location.hostname === '127.0.0.1'
    || location.search.includes('variants=1');
  if (!gate) return;

  document.querySelectorAll('[data-uiv-section]').forEach((section, stackIndex) => {
    const id = section.dataset.uivSection;
    const names = JSON.parse(section.dataset.uivNames || '{"a":"A","b":"B","c":"C"}');
    const storageKey = `ui-variant:${id}`;
    const dismissKey = `ui-variant-dismissed:${id}`;

    const originalHTML = section.innerHTML;
    const variants = { original: originalHTML };
    document.querySelectorAll(`template[data-uiv-for="${id}"]`).forEach(t => {
      variants[t.dataset.uivVariant] = t.innerHTML;
    });

    const apply = key => {
      if (!variants[key]) key = 'original';
      section.innerHTML = variants[key];
      try { localStorage.setItem(storageKey, key); } catch {}
      chip.querySelectorAll('[data-uiv-seg]').forEach(el => {
        el.setAttribute('aria-checked', el.dataset.uivSeg === key ? 'true' : 'false');
      });
    };

    const chip = document.createElement('div');
    chip.className = 'uiv-chip';
    chip.setAttribute('role', 'radiogroup');
    chip.setAttribute('aria-label', `${id} variant switcher`);
    chip.style.top = `${16 + stackIndex * 48}px`;
    chip.innerHTML = `
      <span class="uiv-chip__label">${id}</span>
      <button class="uiv-chip__seg" data-uiv-seg="original" role="radio" title="Original">O</button>
      <button class="uiv-chip__seg" data-uiv-seg="a" role="radio" title="${names.a}">A</button>
      <button class="uiv-chip__seg" data-uiv-seg="b" role="radio" title="${names.b}">B</button>
      <button class="uiv-chip__seg" data-uiv-seg="c" role="radio" title="${names.c}">C</button>
      <span class="uiv-chip__badge">PREVIEW</span>
      <button class="uiv-chip__close" aria-label="Hide">×</button>
    `;

    chip.querySelectorAll('[data-uiv-seg]').forEach(el => {
      el.addEventListener('click', () => apply(el.dataset.uivSeg));
    });
    chip.querySelector('.uiv-chip__close').addEventListener('click', () => {
      chip.remove();
      try { sessionStorage.setItem(dismissKey, '1'); } catch {}
    });

    if (sessionStorage.getItem(dismissKey) !== '1') document.body.appendChild(chip);

    let saved = 'original';
    try { saved = localStorage.getItem(storageKey) || 'original'; } catch {}
    apply(saved);
  });

  window.addEventListener('keydown', e => {
    if (e.key.toLowerCase() !== 'v' || e.metaKey || e.ctrlKey || e.altKey) return;
    const t = e.target;
    if (t && /^(INPUT|TEXTAREA|SELECT)$/.test(t.tagName)) return;
    document.querySelectorAll('.uiv-chip').forEach(c => c.style.display = c.style.display === 'none' ? '' : 'none');
  });
})();
</script>
```

**Caveat for vanilla HTML:** swapping `innerHTML` re-parses the section, which resets any per-element state (input values, animation progress). For sections with significant state, prefer reusing the same root and updating specific children, or move to a framework.

---

## Astro

Astro is MPA by default, so wrap the section in a framework island (React example):

```astro
---
// HeroWithVariants.astro
import { VariantSwitcher } from './VariantSwitcher';
import Hero from './Hero.astro';
import HeroVariantA from './HeroVariantA.astro';
// ...
---

<VariantSwitcher
  client:load
  sectionId="hero"
  names={{ a: 'Editorial Serif', b: 'Brutalist Grid', c: 'Neo-Retro Terminal' }}
>
  <Hero slot="original" />
  <HeroVariantA slot="a" />
  <HeroVariantB slot="b" />
  <HeroVariantC slot="c" />
</VariantSwitcher>
```

Use the React switcher above, and render `slot` children inside conditionally.

---

## Checklist when wiring up

- [ ] Original component file is unchanged (byte-identical)
- [ ] Three variant files created as siblings
- [ ] CSS for `.uiv-chip*` imported once per page
- [ ] Switcher wraps the render site, not the component definition
- [ ] Dev gate verified (production build / no `?variants=1` → toggle hidden)
- [ ] `sectionId` unique across the page
- [ ] Tooltips show the real direction names
- [ ] Active segment has `aria-checked="true"`
- [ ] Keyboard: arrows cycle, `V` hides, `×` dismisses for session
- [ ] Form submissions / link navigation still work inside each variant
