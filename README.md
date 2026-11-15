# How to Build Your Own CLI

A reference collection of terminal UI components, animations, and patterns
for building command-line interfaces. Extracted from the Elitza AI assistant
(an opencode fork). MIT licensed.

This repo exists so AI agents and developers can learn from real, working
implementations of CLI animations and TUI patterns. Each file is a
self-contained reference -- not a runnable project, but a blueprint you can
adapt to your own CLI.

## What's inside

### Animations

| Component | File | What it does |
|---|---|---|
| Starfield | `src/cli/home.tsx` | Deterministic hash-based star field for terminal home screens. 44 stars, stable across renders. |
| Wordmark shimmer | `src/web/wordmark.css` + `wordmark.tsx` | CSS mask sweep animation over a logo. Screen blend mode, reduced-motion aware. |
| Letter scramble | `src/web/animated-wordmark.tsx` | Animated wordmark that scrambles letters then settles into the target word. |
| Fade-in text | `src/animations/fade-in-text.tsx` | Sweep-masked text reveal with configurable duration and feather. |
| Shimmer text | `src/animations/shimmer-text.tsx` | Sweeping highlight over text, looping. |
| Title shimmer | `src/animations/title-shimmer.tsx` | Title rename with wipe transition and blend. |
| Tab pulse | `src/animations/tab-pulse.tsx` | Tab indicator pulse with glow, flash, and completion states. |
| Animated number | `src/web/animated-number.tsx` | Counting number with easing. |
| Animated presence | `src/web/animated-presence.ts` | Enter/exit transitions (SolidJS). |
| One-cell spinner | `src/cli/one-cell-spinner.tsx` | Minimal single-cell spinner with sequenced frames. |
| Startup loading | `src/cli/startup-loading.tsx` | Loading overlay with timing logic (500ms delay, 3s hold). |

### Core systems

| Component | File | What it does |
|---|---|---|
| Spring/tween engine | `src/animations/spring-tween-engine.ts` | `createAnimatable` with spring and tween transitions. Task scheduling at 16ms. |
| Masked text renderable | `src/animations/masked-text.ts` | Base class for all mask-based effects. Extends TextRenderable. |
| Logo | `src/cli/logo.ts` | Block-glyph wordmark (ELITZA) for terminals. |
| Color utilities | `src/utils/color.ts` | ANSI code to RGBA conversion. |

### Global styles

| File | What it does |
|---|---|
| `src/web/animations.css` | Global animation keyframes. |
| `src/web/animated-number.css` | Animated number styles. |
| `src/web/wordmark.css` | Wordmark shimmer CSS. |

## Dependencies

These components depend on the following packages:

- **@opentui/core** — Terminal rendering engine (OptimizedBuffer, RGBA, TextRenderable, etc.)
- **@opentui/solid** — SolidJS integration for OpenTUI (extend, JSX components)
- **solid-js** — Reactive UI framework (signals, effects, stores)

Install them with:
```bash
npm install @opentui/core @opentui/solid solid-js
```

Note: These files are reference material. They are not a runnable project on their own.
Copy the patterns you need into your own codebase and adapt them.

## How each animation works

### Starfield (home.tsx)

A deterministic star field using a hash function. 44 stars are placed using
a seeded pseudo-random number generator (mulberry32-style hash), so every
terminal renders the same sky. This makes frame tests stable.

```typescript
const hash = (seed: number) => {
  const a = Math.imul(seed ^ (seed >>> 16), 0x45d9f3b)
  const b = Math.imul(a ^ (a >>> 16), 0x45d9f3b)
  return ((b ^ (b >>> 16)) >>> 0) / 4294967296
}

const starField = Array.from({ length: 44 }, (_, index) => ({
  x: hash(index + 1),
  y: hash(index + 101),
  char: (["·", "✦", "°", "."] as const)[index % 4],
  accent: index % 5 === 0,
}))
```

### Spring/tween engine (spring-tween-engine.ts)

A frame-based animation system that supports spring and tween transitions.
Uses a 16ms interval timer with a task set. Springs use exponential decay
with configurable visual duration, rest delta, and rest speed. Tweens use
easing functions with configurable duration.

Key functions:
- `spring({ visualDuration, restDelta?, restSpeed? })` — create a spring transition
- `tween({ duration, ease? })` — create a tween transition
- `createAnimatable(initial, { transition, enabled? })` — create an animatable value

### Wordmark shimmer (wordmark.css + wordmark.tsx)

A CSS animation that sweeps a highlight across a logo using a mask-image
gradient. The shimmer uses `mix-blend-mode: screen` so it only adds light.
Respects `prefers-reduced-motion`.

### Fade-in text (fade-in-text.tsx)

Extends `MaskedTextRenderable` to create a text reveal effect. Uses a
sweep mask that moves across the text over 200ms with a coast easing.

### Letter scramble (animated-wordmark.tsx)

Scrambles letters randomly, then settles them into the target word over
~15 ticks (75ms each). Respects `prefers-reduced-motion`.

## Customizing the home screen tips

The `home.tsx` file contains Elitza-specific tips:

```typescript
const tips = [
  "Grab a free key at openrouter.ai/keys, then run /connect",
  "Elitza learns as you work — memories build up in ~/.elitza",
  // ...
]
```

To sanitize for your own CLI, replace these with your own tips or remove them.
The tips rotate on the home screen and are purely cosmetic.

## Getting started

1. Install dependencies: `npm install @opentui/core @opentui/solid solid-js`
2. Copy the files you need into your project
3. Adapt imports and component names to your codebase
4. Read the source — each file is self-contained with inline comments

## License

MIT — use freely, modify freely, ship freely.

## Origin

Extracted from [Elitza](https://github.com/dario-cositore/ai-enablement-agent),
an AI Enablement agent built on the opencode fork. The original code is
licensed under MIT.

## Contributing

Found a bug or want to add a pattern? Open an issue or PR on this repo.
