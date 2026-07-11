# Toss Design System (TDS)

A code-first recreation of **Toss** (토스) — the design language of Korea's
fintech super-app, operated by **Viva Republica** (비바리퍼블리카). This system
packages Toss's tokens, typography, color, motion, reusable React primitives,
and a mobile UI kit so design agents can produce on-brand Toss interfaces.

> **Sources.** Built from the supplied `uploads/DESIGN.md` — an OmD v0.1 brand
> dossier reconciled against the public **TDS Mobile** docs
> (`tossmini-docs.toss.im/tds-mobile/`) and the live marketing site
> (`toss.im`). No codebase or Figma file was provided; everything here is
> derived from that spec. The TDS Mobile docs are the authority for component
> geometry; `toss.im` uses a parallel `.p-button` web system noted where relevant.

---

## Brand context

Toss began in 2014 as a money-transfer app rejecting the institutional-indigo,
Active-X aesthetic of Korea's legacy banks (KB, Shinhan, Woori, Hana). The
signature cerulean **`#3182f6`** was chosen precisely because it was *not* the
indigo of any incumbent — optimism was the thesis: money could feel light. Toss
is now a super-app spanning transfers, payments, brokerage, insurance, lending,
and credit scoring, planning a US IPO (~$15B) in 2026.

The design's whole job is to flatten that complexity into **one gesture per
screen**. That demands extreme restraint: one type family in three weights, a
blue-and-neutral palette, single-layer black shadows. Every ornament costs
clarity, and clarity is the entire brand promise.

---

## CONTENT FUNDAMENTALS

How Toss writes copy:

- **Voice**: a friend who happens to be a fiduciary — calm, unhurried, zero
  jargon, positive statements without hedging. Korean is the *primary* voice;
  English strings are secondary translations, not parity.
- **Person & tone**: second person, conversational `~해요` register (not the
  stiff `~합니다`, except in legal/disclosure copy). One idea per screen.
- **Casing & punctuation**: sentences end in periods; **buttons do not**. CTAs
  are short imperative Korean verbs — `송금하기`, `확인`, `가입하기`.
- **Money**: bare numerals with comma separators, then the unit — `1,240,000원`,
  never `₩1.24M` and never approximations (`약 120만원` is forbidden on primary
  surfaces). Exact numerals only.
- **Success**: past-tense single sentence — `송금이 완료되었어요`. Money-moved
  confirmation is a *dedicated screen*, never a toast.
- **Errors**: specific + blameless + actionable. Never the generic
  `문제가 발생했습니다` or `데이터가 없습니다`.
- **Emoji**: none in financial contexts. (Toss ships *Tossface*, a custom emoji
  font, for brand decoration only — disallowed on money-handling screens.)
- **Forbidden**: `불편을 드려 죄송합니다`, `Oops`, `죄송하지만`, rounded currency.

Vibe in one line: **trustworthy, light, and quietly confident — never playful,
never institutional.**

---

## VISUAL FOUNDATIONS

- **Color.** White canvas (`#ffffff`), charcoal text (`#191f28`), and a single
  interactive blue (`#3182f6`). Blue means *interaction, never decoration* — it
  appears only where the user can tap. Positive money is green (`#03b26c`),
  negative is red (`#f04452`). The grey scale (grey50→grey900) carries a warm
  blue undertone. Brand blue `#0064ff` is logo/marketing only.
- **Type.** One family — Toss Product Sans, here substituted by **Pretendard**
  (see Caveats). Eight weights ship; the UI uses **400 / 600 / 700** only.
  Financial amounts are typography: 700 weight + **tabular numerals**,
  right-aligned in lists.
- **Spacing.** 8px base unit; **20px** standard horizontal page padding (wider
  than the usual 16 — "breathing room for money"). Summary screens are spacious;
  detail screens progressively denser.
- **Backgrounds.** Flat fills only — white surfaces on a `#f2f4f6` section grey.
  No gradients, no photographic hero washes, no repeating textures, no
  hand-drawn illustration. Negative space is the brand asset.
- **Corner radii.** 8px inputs · 12px cards · 16px featured cards & sheets · pill
  (9999px) for toggles/chips. Never exceed 16px except pills.
- **Cards.** White surface, 12px radius, 20px padding, **single** shadow
  `0 2px 8px rgba(0,0,0,0.08)`. Compact variant swaps shadow for a 1px
  `#e5e8eb` border. No colored or layered shadows — ever.
- **Shadows.** Five-step single-layer pure-black scale (subtle→modal). Restraint
  is the statement; in finance, visual noise is a credibility tax.
- **Borders.** 1px `#e5e8eb` default, `#d1d6db` emphasized. Dividers are
  `#f2f4f6`.
- **Transparency / blur.** Sticky nav blurs subtly on scroll; floating menus use
  backdrop blur. Overlays are a blue-tinted dark scrim `rgba(2,9,19,0.5)`.
- **Animation.** Named durations (fast 150 / standard 250 / slow 400) and
  easings. `ease-enter` for things appearing, `ease-exit` (lighter) for leaving.
  **Spring easing is licensed for exactly one place** — the money-moved success
  checkmark. Money numbers never cross-fade: old slides up & out, new slides up
  & in. `prefers-reduced-motion` collapses everything to instant.
- **Hover / press.** Buttons dim via brightness on press (no shrink); disabled
  drops opacity to ~0.4. Inputs keep a stable border geometry; focus ring is
  always blue500.
- **Imagery vibe.** Minimal. Bank/service logos at 24–40px. No decorative
  photography in core flows; clarity over imagery.

---

## ICONOGRAPHY

The spec mandates **one icon library, used everywhere — no mixing, no emoji.**
TDS-Mobile ships its own proprietary icon set; it is not publicly distributable,
so this system standardizes on **Lucide** as the closest open substitute
(consistent 24×24 grid, 1.5–2px outline stroke, geometric, `currentColor`).

- **Library**: [Lucide](https://lucide.dev) — linked from CDN in the UI kit
  (`unpkg.com/lucide`). Swap for the licensed Toss icon set if it becomes
  available. **(Substitution — flagged.)**
- **Rules**: inline SVG only (never `<img>`); color via `currentColor`; stroke
  1.5–2px kept consistent. Sizes follow the type scale — 16px inline, 18–20px in
  buttons, 24px standalone, 32–48px feature/empty-state.
- **Emoji**: forbidden in UI. Status uses colored dots or icon components.
- **Assets**: the Toss app logo is referenced from Toss's static CDN
  (`static.toss.im`) in the Brand card; see `assets/` for local fonts.

---

## Index / manifest

**Root**
- `styles.css` — global entry point (consumers link this). `@import`s only.
- `tokens/` — `fonts.css`, `colors.css`, `typography.css`, `spacing.css`,
  `motion.css`, `base.css`.
- `assets/fonts/` — Pretendard webfonts (ttf).
- `readme.md` — this file. · `SKILL.md` — Agent-Skills entry point.

**Components** (`window.TossDesignSystem_247c4a.<Name>`)
- `components/core/` — **Button**
- `components/forms/` — **Input** (TextField), **Toggle**
- `components/data/` — **Card**, **Badge**, **Table**
- `components/navigation/` — **Tabs** (segmented / underline)
- `components/feedback/` — **Dialog** (center modal / bottom sheet)

Each directory has `<Name>.jsx`, `<Name>.d.ts`, `<Name>.prompt.md`, and a
`*.card.html` specimen.

**Foundations** (`foundations/`) — specimen cards for the Design System tab:
colors (primary / grey / semantic), type (display / body / numbers), spacing,
radius, elevation.

**UI kit** (`ui_kits/toss-app/`) — interactive mobile app recreation: home
(자산), transfer flow, and money-moved success screen.

---

## Caveats

- **Font substitution.** Toss Product Sans is proprietary and unavailable.
  **Pretendard** (bundled) is the standard open substitute — same Korean+Latin
  metrics family. Replace `tokens/fonts.css` + `assets/fonts/` if licensed files
  arrive.
- **Icon substitution.** Lucide stands in for Toss's proprietary icon set.
- **No source code / Figma.** Geometry comes from `DESIGN.md` + public TDS docs,
  not a live codebase. *Tossface* emoji font is referenced but not bundled.
