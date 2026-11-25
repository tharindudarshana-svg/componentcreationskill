---
name: entgra-ash-new-component
description: Design, spec and build a new UI component for Entgra products using the Ash component library (tokens, 7-step recipe, React + Mantine code, docs entry, review checklist).
---

# Entgra × Ash — Create a New Component

Use this skill whenever someone asks to design, specify, build, document or review a **new component** (or a variant of an existing one) for an Entgra product. Entgra UI is built on **Ash**: a neutral component library where one accent token re-themes the whole system.

The core idea: **structure is the rule, colour is a sample.** Layout, alignment, sizing, padding, radius, icon spacing and states stay the same in every product. Colours are given as *roles* (primary action, status, muted), and each product fills those roles from its own palette.

---

## 1. Inputs to collect (ask only if missing)

1. **Component name and job.** What does it do, and where does it appear (dashboard, table toolbar, form, header, enrollment wizard...)?
2. **Target output.** Pick one or more:
   - **Spec**: a docs entry in the §6 template
   - **Code**: React + Mantine v7 inside the `ash-design-system` repo (the default)
   - **Plain HTML/CSS**: for teams that don't use the library
   - **Figma component**: load the figma-use and figma-generate-library skills first
3. **Product context.** Which Entgra product it's for (this sets the accent), plus sample data. Use realistic Entgra/MDM data such as devices, enrollments, policies or users.

If the request is clear, don't ask anything. Start with a spec and code, and state the assumptions you made.

---

## 2. Foundations (the contract — compose these, never invent new values)

If a value you need isn't listed here, that is a **foundation proposal**. Flag it to the user; don't hardcode it.

### Colour
- **Ash neutrals** carry about 90% of the UI (surfaces, text, borders, dividers):
  `25 #FBFBFC · 50 #F6F7F8 · 100 #ECEEF1 · 200 #DDE0E5 · 300 #C4C9D1 · 400 #9AA1AD · 500 #6B7280 · 600 #4B5563 · 700 #374151 · 900 #12161D`
- **Accent** (one token, `var(--accent)`): Ash's reference value is `#2B303B`. For Entgra-branded surfaces, the Entgra brand sample is primary `#3F78AB`, with teal `#4FC3CE` as an optional second chart series only. The accent is used **only** where the user acts or for the current selection: primary buttons, links, focus rings, selected tabs/rows/chips, and one highlighted chart series.
- **Semantic pairs** (solid / soft) are for **status only**:
  Success `#15803D` / `#E9F6EE` · Warning `#B45309` / `#FDF1E3` · Danger `#B91C1C` / `#FBEAEA` · Info `#1D4ED8` / `#E8EEFC` · Neutral `#4B5563` / `#F0F1F3`
- Surfaces: page `#F6F7F9`, card `#FFFFFF`, card border `#E6E8EC`, input border `#DDE0E5`, divider `#ECEEF1`.
- Text: strong `#12161D`, base `#374151`, muted `#6B7280`, faint `#9AA1AD`. Faint is for meta text only and never for text people must read.

### Typography
Poppins for the UI. JetBrains Mono for IDs, codes and raw figures. The weight ladder is fixed: **600** headings and numbers · **500** labels and controls · **400** body.

| Role | Size / weight |
|---|---|
| Display | 44 / 600 (one hero value per screen) |
| H1 | 32 / 600 |
| H2 | 24 / 600 |
| H3 | 18 / 600 |
| Card title | 16 / 600 |
| Body | 15 / 400 |
| Control text | 13.5–14 |
| Label | 13 / 500 |
| Caption / meta | 12 / 400, muted |
| Eyebrow / column header | 11 / 600, UPPERCASE, +.06–.09em tracking |

Tracking tightens (-.01 to -.02em) as text gets larger.

### Spacing, radius, elevation
- **Spacing:** 4px grid only: `4 · 8 · 12 · 16 · 24 · 32 · 48`.
- **Radius:** control `12px` (`md`, `var(--radius)`), container `16–20px` (`lg`/`xl`), pill `999px`. Small and large radii are derived with `calc()` off the token: sm button `calc(radius - 3px)`, lg `calc(radius + 2px)`. A card and a button never share a radius.
- **Elevation** is flat by default and has to be earned. Shadows use `rgba(16,24,40,…)` and never pure black.
  - L1 card: `0 1px 2px …/.05`
  - L2 raised: `0 4px 12px …/.08`
  - L3 popover/dropdown: `0 12px 28px …/.12`
  - L4 modal: `0 24px 60px …/.22`
- **Focus ring:** `0 0 0 4px` of the accent at roughly 18–28% on white. Derive it from danger on destructive controls. Never use the browser's default blue.

### Reference control specs (copy these patterns)
- **Button:** md `10px 18px · 13.5/600 · r12` (≈44px tall including border). sm `7px 13px · 12/600`. lg `13px 24px · 15/600`.
  - The label is **centred**, single-line and never wraps (truncate instead).
  - Icons are 16px (sm 14, lg 18) with an 8px gap. Use one icon per button, either leading or trailing, never both.
  - A leading icon trims the icon-side padding by about 3px.
  - Icon-only buttons are square, the same height as their neighbours, and need an `aria-label` and a tooltip.
  - Loading state: a spinner replaces the leading icon, the label stays, and the button is disabled.
- **Input:** `11px 14px · 14px · 1px ash-200 · r12` with a minimum height of 44px.
  - Label above (13/500), helper below (12, faint).
  - Error: danger border plus a helper that explains the fix.
  - Disabled: ash-50 background, ash-100 border, ash-400 text.
- **Badge:** `5px 11px · 12/600 · pill` with a 6px dot and a 6px gap. Background, text and dot all come from the same semantic row.
- **Chip (filter):** `6px 8px 6px 13px · 13/500 · pill`, soft accent fill, removable via a 17px ✕.
- **Card:** white, `1px #E6E8EC`, r16–20, L1 shadow. Header padding `20px 22px`. Optional footer on `#F8F9FA`.
- **Table:** header cells `11/600` uppercase in ash-500/600. **Never use the accent or a coloured header bar.** Numbers and their headers are right-aligned, text is left-aligned, status is shown as a badge, and row dividers are ash-100.

---

## 3. The 7-step recipe (always follow it in order)

**Step 0: Name the job.** Decide which kind of element this is:
- **Control** (the user acts on it): `md` radius plus full interaction states.
- **Container** (it holds other things): `lg` radius, border, 20–32px padding, L1 shadow.
- **Pill** (status or filter token): `full` radius.
- **Text** (it only informs): no box, just a type role and a neutral colour.

If you can't name the job in one word, split the component into smaller parts.

**Step 1: Build the box.**
- Set the radius by class.
- Set padding on the 4px grid: control `10px 18px`, container `20–32px`, pill `5px 11px`.
- Use a `1px` ash-200 border for anything on white that needs an edge.
- Internal gaps are 6–8px between icon and text, and 10–16px between controls.

**Step 2: Set the type by role** from the table above. Don't eyeball sizes. Use weight and size for hierarchy before you reach for colour.

**Step 3: Colour by meaning.** Ask in this order:
1. Is it a surface or plain text? Then use neutrals only, and stop.
2. Does the user act here, or is it their current selection? Then use the accent: a fill for the primary action, and text, underline or a ~12% soft mix for selected states.
3. Is it showing a status? Then use the matching soft and solid semantic pair from the same row.
4. Anything else is neutral.

Three hard rules: never put the accent on a surface or on body text; never use a semantic colour as decoration; never let colour be the only signal (pair it with text and a shape, like the badge dot).

**Step 4: Add depth only if it floats.**
- Resting on the page: border plus L1.
- Dropdowns, popovers and tooltips: L3.
- Modals: L4.
- Everything else is flat.

**Step 5: Define every interaction state** for each interactive part:

| State | Rule |
|---|---|
| Hover | Shift one step: fills darken via `color-mix` toward the accent or danger; ghost and secondary gain an ash-50 fill. **Never move or resize.** |
| Focus-visible | Accent ring, always visible. If you remove an outline, replace it. |
| Active | A touch deeper than hover. |
| Disabled | ash-50 fill, ash-100 border, ash-400 text, `not-allowed` cursor, no shadow, no hover. |

**Step 6: Design every data state** for anything that loads or lists data:
- **Loaded**
- **Empty:** a headline, one line of context, and one primary action if the user can act. Hide count pills instead of showing "0".
- **Loading:** a skeleton in the shape of the real content using ash-100 blocks. Never a bare spinner.
- **Error:** plain language about what failed, plus a retry.

**Step 7: Enforce the invariants** (see the checklist in §8).

---

## 4. Composition and behaviour rules

1. **Compose foundations and invent nothing.** Reuse existing patterns: card shell, eyebrow, dot + label, muted meta, right-aligned figures.
2. **Big components are composed from small ones.** Advanced filter bars, editable grids, kanban boards and schedulers are a card shell hosting existing inputs, chips, badges and buttons. They add **no new tokens**.
3. **One primary action per surface.** Everything else is secondary, ghost or link.
4. **Destructive actions always confirm.** The confirm dialog states the consequence plainly.
5. **State is shown, not hidden.** Applied filters, active sorts and selected rows stay visible and reversible (removable chips, clear-all, live counts).
6. **Start simple.** Use inline single controls first, and only promote to an advanced pattern (ALL/ANY logic, multi-sort, saved Views) when users need it.

---

## 5. Entgra copy rules for component text

- Labels describe the action: "Enroll device", not "Submit".
- Notification and toast titles stay under 60 characters; body text under 120.
- No exclamation marks, except for genuine celebrations like onboarding or milestones.
- Use human-readable dates ("Today at 9:41 AM"), never raw ISO timestamps.
- Error helpers say how to fix the problem ("Must be 10 digits"), never just "Invalid".
- Label anything AI-generated as AI-generated, and keep a way to override or dismiss it.

---

## 6. Deliverable A: the spec entry (always produce this)

Use exactly this shape, the same as the existing component docs:

```
### <Component name>
Scope / when to use — one paragraph. Also say when NOT to use it and what to use instead.

Anatomy — numbered parts (e.g. 1 container · 2 leading icon · 3 label · 4 count pill).

Structure — fixed (same in every product)
| Property | Value |   ← alignment, height logic, padding per size, radius, icon size/gap, type role, gaps, min touch target

Colour — sample (fill each role from your product palette)
| Role | Ash sample | Entgra sample |

Variants & sizes
| Variant/Size | Structure difference | When |

States
Interaction: hover · focus-visible · active · disabled (+ selected/loading if relevant)
Data: loaded · empty · loading skeleton · error (if it shows data)

Behaviour rules — bullets.
Accessibility — role/aria, keyboard, contrast, ≥44px, colour-not-alone, reduced motion.
Responsive — what happens below the sm breakpoint.
Do / Don't — at least 2 of each, concrete.
```

---

## 7. Deliverable B: code in the `ash-design-system` repo (React + Mantine v7)

Repo layout:

```
src/theme.js            ← createTheme: tokens wired into Mantine (primaryColor "ash", radius, shadows, component defaults)
src/lib/tokens.js       ← raw tokens: ashScale, surface, text, status{bg,fg,dot}, radius, shadow, font
src/lib/<Name>.jsx      ← custom Ash components (StatusBadge, KpiTile, EmptyState, Section/Demo)
src/lib/index.js        ← public exports (custom components + re-exported themed Mantine primitives)
src/showcase/Gallery.jsx← live gallery; each group is a <Section> registered in SECTIONS
```

Steps:
1. **Check whether a themed Mantine primitive already does the job** (Button, TextInput, Tabs, Table, Modal, Stepper, Accordion...). If one does, prefer a composition or a `components:` default in `theme.js` over a new component.
2. Create `src/lib/<Name>.jsx`:
   - Import values only from `./tokens` or Mantine theme keys. Never write raw hex or px values that aren't tokens.
   - Add a JSDoc header with a one-line purpose, the rule it enforces, and its props with their allowed values.
   - Use a `variant`/`size`/`status` prop API that matches the existing components (`status: 'success'|'warning'|'danger'|'info'|'neutral'`, `size: 'sm'|'md'|'lg'`).
   - Spread `...rest` and merge `rest.style` so consumers can extend it.
   - Cover all interaction states (hover via Mantine styles/CSS, `:focus-visible` ring, `disabled`) and data states (`loading` → `Skeleton` shaped like the content, `empty` → `EmptyState`, `error`).
   - Accessibility: semantic element or `role`, `aria-label` for icon-only controls, keyboard operable, at least 44px hit area, and respect `prefers-reduced-motion`.
   - Responsive: use Mantine responsive props (`cols={{ base: 1, sm: 2, md: 4 }}`). Tables scroll horizontally inside their card, and modals become near-full-screen below `sm`.
3. Export it from `src/lib/index.js`.
4. Add a gallery `<Section id eyebrow title description>` in `Gallery.jsx` showing every variant, size, state and the empty/loading/error states with realistic Entgra data. Register it in `SECTIONS`.
5. Verify with `npm run build`, which must pass with no errors, then check it at 375px and desktop widths in `npm run dev`.

Reference pattern (StatusBadge):

```jsx
import { Box } from "@mantine/core";
import { status as statusColors } from "./tokens";
/** StatusBadge — soft pill + leading dot. Dot reinforces meaning (colour is never alone). */
export function StatusBadge({ status = "neutral", dot = true, children, ...rest }) {
  const c = statusColors[status] ?? statusColors.neutral;
  return (
    <Box component="span" {...rest} style={{ display: "inline-flex", alignItems: "center", gap: 6,
      fontSize: 12, fontWeight: 600, lineHeight: 1, padding: "5px 11px", borderRadius: 999,
      background: c.bg, color: c.fg, whiteSpace: "nowrap", ...rest.style }}>
      {dot && <span style={{ width: 6, height: 6, borderRadius: "50%", background: c.dot, flex: "none" }} />}
      {children}
    </Box>
  );
}
```

**Deliverable C (optional): plain HTML/CSS** for teams outside the library. Use CSS variables (`--accent`, `--radius`, `--ash-*`) with the same structure values, plus a short note mapping each colour role to the variable.

---

## 8. Review checklist (run it before you hand anything over, and report the result)

```
1 JOB      control · container · pill · text — named in one word
2 BOX      radius by class · padding on 4px grid · border · gaps
3 TYPE     role-based size/weight · 600/500/400 ladder
4 COLOUR   neutral default · accent = action/selected only · semantic = status only · never colour-alone
5 DEPTH    flat default · border+L1 resting · L3 float · L4 modal
6 STATES   hover (no resize) · focus ring · active · disabled
7 DATA     loaded · empty · skeleton · error
+ one primary per surface · destructive confirms · numbers right-aligned · status as badges
+ every value traces to a token · ≥44px targets · real labels · aria for icon-only · reduced motion
+ mobile: single column / scrolling table, no off-grid hacks · Entgra copy rules met
```

Mark each line ✅, or ⚠️ with a fix. If something needs a new token, list it under **"Foundation proposals"** instead of hardcoding it.

---

## 9. Output order

1. One line covering the component's job, its class, and any assumptions.
2. The spec entry (§6).
3. Code (§7) as files: `<Name>.jsx`, the `index.js` export line, and the Gallery section. Add HTML/CSS or Figma only if asked.
4. The checklist result (§8), plus any foundation proposals.

Keep it to the point. Give exact values for structure, express colour as roles with samples, and leave out generic design advice.
