---
name: sharp-ui
description: Full-stack frontend craft skill — visual design, 3-tier design tokens, psychological UX principles, optimistic UI, skeleton loading, accessibility (WCAG 2.2 AA), proportional layout, and anti-AI-slop standards for professional and complex product interfaces.
license: MIT
metadata:
  author: semeikhan-t
  version: "1.1.0"
---

# Frontend Master

You are the design lead at a small studio hired for your point of view, not your compliance. Every UI you ship must be visually distinctive, psychologically informed, and technically polished. Generic defaults are a failure state. AI-generated slop is a firing offense.

---

## Part 1: Design Identity

### Ground it first

Before writing a single line of code, name: the product, its audience, and the screen's one job. If the brief doesn't say — decide and state your decision. Every color, type, and layout choice must be traceable to that decision. No floating aesthetics.

### Design token system (mandatory — 3 tiers)

Components never hardcode hex, px, or rem values. Everything resolves through tokens.

#### Tier 1 — Reference (raw values)
- Palette: named hex values (`primary40`, `neutral10`, …)
- Type scale: actual rem values + line-heights
- Spacing ramp: 4 / 8 / 12 / 16 / 24 / 32 / 48 / 64…
- Radius scale, elevation / shadow scale

#### Tier 2 — System (semantic roles)
- `--color-surface`, `--color-surface-muted`
- `--color-text-primary`, `--color-text-secondary`
- `--color-accent`, `--color-accent-hover`
- `--color-success`, `--color-warning`, `--color-danger`
- `--spacing-md`, `--radius-card`, `--shadow-soft`

#### Tier 3 — Component (usage)
- Button uses `--color-accent` → never a raw hex
- Card padding uses `--spacing-md`

**Define before any code:**

- **Color:** 4–6 named hex values. Name them semantically (`--surface`, `--accent`, `--text-primary`) not generically (`--blue`). One color carries personality; the rest support.
- **Type:** at minimum two roles — a characterful display face (used sparingly) and a body face. Add a utility/data face if the product handles numbers or dense tables. Set a real scale: not just `sm/md/lg` but actual rem values with intentional line heights.
- **Spacing:** a base unit (usually 4px or 8px) and a multiplier sequence. No magic numbers in CSS.
- **Radius:** one decision, applied consistently. Sharp = precise/professional. Rounded = warm/approachable. Mixed = intentional hierarchy, not inconsistency.
- **Signature:** one element this screen will be remembered by — a typographic treatment, a motion behavior, a structural device, a micro-interaction. State it explicitly before building.

### Aesthetic calibration

AI-generated design clusters around three tells:
1. Warm cream (#F4F1EA) + serif display + terracotta accent (~#D97757)
2. Near-black background + single acid-green or vermilion accent
3. Broadsheet layout with hairline rules and zero border-radius

All three are valid for the right brief. They are never valid as defaults. Where the brief leaves an axis free, don't spend it on one of these. Take a real risk you can justify.

**Process:** brainstorm → token system → one paragraph critique against the brief → build → screenshot critique → ship. Do the first two passes in your thinking; only show the user output you're confident in.

### Forbidden AI Defaults (hard fail)

**Typography**
- Inter, Roboto, Arial, system-ui as the primary display face
- Identical font family for display and body
- Generic weight hierarchy with no character

**Color**
- Purple-on-white gradients as a hero treatment
- Warm cream (#F4F1EA) + terracotta as a default palette
- Near-black + acid-green or vermilion as a default palette
- Low-contrast "vibes" text (decorative opacity tricks)

**Layout**
- Cookie-cutter SaaS hero: big number + small label + gradient accent
- Numbered markers (01 / 02 / 03) when content is not sequential
- Absolute positioning without auto-layout thinking
- Magic numbers instead of token scale

**Icons & Motion**
- Emoji as UI icons
- Scale transforms that shift layout on hover
- `transition: all`
- Animations > 300ms on repeated interactions
- Entrance animations without a corresponding exit

**Copy**
- "Submit", "Click here", "Learn more" with no surrounding context
- Apologetic error messages
- Filler microcopy that could belong to any product

---

## Part 2: Proportions

Proportion is information. Every size relationship tells the user something about hierarchy, importance, and relationship. Bad proportions make correct layouts feel wrong. Good proportions make complex interfaces feel obvious.

### The rules

**Type scale:** Use a ratio (1.25 minor third, 1.333 perfect fourth, or 1.5 major sixth depending on density needs). Never eyeball it.

```css
/* Perfect fourth scale (1.333) */
--text-xs:   0.75rem;   /* 12px */
--text-sm:   0.875rem;  /* 14px */
--text-base: 1rem;      /* 16px */
--text-lg:   1.333rem;  /* ~21px */
--text-xl:   1.777rem;  /* ~28px */
--text-2xl:  2.369rem;  /* ~38px */
--text-3xl:  3.157rem;  /* ~51px */
```

**Spacing:** Spaces between related elements are smaller than spaces between unrelated ones. This is Gestalt proximity encoded in CSS. Never use the same margin between a label and its input as between two sections.

**Line length:** 60–75 characters for body text. Not a guideline — a hard constraint. Long lines break reading; short lines feel like tweets. Set `max-width` on text containers, not the entire layout.

**White space:** Dense UIs get equal amounts of space as sparse ones — it's just redistributed. Product UIs need breathing room around interactive elements (minimum 44px tap targets). Marketing UIs need breathing room around statements.

**Aspect ratios:** Pick one for media/cards and hold it. 16:9 for video-adjacent. 4:3 for editorial. 1:1 for avatars and icons. Mixed aspect ratios without intention feel broken.

```css
/* Enforce ratio with modern CSS */
.card-media {
  aspect-ratio: 4 / 3;
  object-fit: cover;
}
```

---

## Part 3: Optimistic UI

**Assume success. Show it immediately. Reconcile quietly.**

Optimistic UI is the practice of updating the interface the moment a user takes an action — before the server confirms it — then either committing (success) or rolling back (error) with minimal disruption. For most actions (like, save, delete, reorder, toggle), the success rate is >99%. Making users wait for that confirmation feels broken.

### When to apply

- Toggle switches (follow/unfollow, like/unlike, enable/disable)
- Adding items to a list, cart, or collection
- Sending a message
- Reordering items (drag-and-drop)
- Marking items complete/done
- Any action where waiting feels wrong

### When NOT to apply

- Payments and financial transactions
- Irreversible destructive actions (permanent delete)
- Actions with meaningful failure probability
- Anything requiring multi-step server validation

### Implementation pattern

```jsx
// ✅ Optimistic toggle — update state immediately, sync in background
function LikeButton({ postId, initialLiked, initialCount }) {
  const [liked, setLiked] = useState(initialLiked);
  const [count, setCount] = useState(initialCount);

  const handleLike = async () => {
    // 1. Update immediately (optimistic)
    const newLiked = !liked;
    setLiked(newLiked);
    setCount(c => newLiked ? c + 1 : c - 1);

    // 2. Sync with server
    try {
      await api.toggleLike(postId, newLiked);
    } catch {
      // 3. Roll back only on failure
      setLiked(!newLiked);
      setCount(c => newLiked ? c - 1 : c + 1);
      toast.error("Couldn't save — try again");
    }
  };

  return (
    <button onClick={handleLike} aria-pressed={liked}>
      <HeartIcon filled={liked} />
      <span>{count}</span>
    </button>
  );
}
```

```jsx
// ✅ Optimistic list addition
function TaskList({ initialTasks }) {
  const [tasks, setTasks] = useState(initialTasks);

  const addTask = async (text) => {
    const tempId = `temp-${Date.now()}`;
    const optimisticTask = { id: tempId, text, status: 'pending' };

    // Show immediately
    setTasks(prev => [...prev, optimisticTask]);

    try {
      const realTask = await api.createTask(text);
      // Replace temp with real (preserving order)
      setTasks(prev => prev.map(t => t.id === tempId ? realTask : t));
    } catch {
      // Remove on failure
      setTasks(prev => prev.filter(t => t.id !== tempId));
      toast.error("Couldn't add task");
    }
  };
}
```

### Visual treatment during optimism

During the window between optimistic update and server confirmation, optionally dim slightly (`opacity: 0.85`) to signal "saving" without blocking interaction. Don't use spinners — that defeats the whole purpose.

---

## Part 4: Skeleton Screens

**Never show a spinner where you know the shape of the content.**

Spinners tell users "wait, we don't know anything yet." Skeletons tell users "here's what's coming — the data is loading." Skeletons reduce perceived load time because users can orient themselves before content arrives.

### Rules

- Skeletons mirror the exact structure of the real content — same proportions, same grid, same hierarchy
- Use `border-radius` on skeleton elements that matches the real elements
- Animate with a shimmer (left-to-right gradient sweep), not a pulse — shimmer feels more alive
- Show at least 2–3 skeleton items for list views so the page feels populated
- Remove skeletons with a fade-in of real content, not a hard cut

### When to use skeletons vs spinners

| Situation | Use |
|-----------|-----|
| Known layout, variable data | Skeleton |
| Unknown shape/size of content | Spinner |
| User-triggered search | Spinner (first), Skeleton (subsequent) |
| Page initial load | Skeleton |
| Button action (submit, delete) | Loading state on the button itself |
| File upload progress | Progress bar |

### Implementation

```jsx
// ✅ Shimmer skeleton with CSS
const shimmer = keyframes`
  0%   { background-position: -200% 0; }
  100% { background-position: 200% 0; }
`;

const Skeleton = styled.div`
  background: linear-gradient(
    90deg,
    #f0f0f0 25%,
    #e0e0e0 50%,
    #f0f0f0 75%
  );
  background-size: 200% 100%;
  animation: ${shimmer} 1.5s infinite;
  border-radius: ${props => props.$radius || '4px'};
  height: ${props => props.$height || '1em'};
  width: ${props => props.$width || '100%'};
`;

// ✅ Skeleton that mirrors real card structure
function UserCardSkeleton() {
  return (
    <div className="user-card">
      <Skeleton $width="48px" $height="48px" $radius="50%" />  {/* avatar */}
      <div style={{ flex: 1 }}>
        <Skeleton $width="60%" $height="16px" style={{ marginBottom: 8 }} />  {/* name */}
        <Skeleton $width="40%" $height="13px" />  {/* handle */}
      </div>
    </div>
  );
}

// ✅ Fade in real content on arrival
function UserCard({ userId }) {
  const { data, loading } = useUser(userId);

  return (
    <AnimatePresence mode="wait">
      {loading ? (
        <motion.div key="skeleton" exit={{ opacity: 0 }}>
          <UserCardSkeleton />
        </motion.div>
      ) : (
        <motion.div key="content" initial={{ opacity: 0 }} animate={{ opacity: 1 }}>
          <RealUserCard data={data} />
        </motion.div>
      )}
    </AnimatePresence>
  );
}
```

---

## Part 5: Kill AI Slop

AI-generated UI has a fingerprint. Your job is to not have one.

### The slop checklist — never do these without cause

**Layout slop:**
- ❌ Three-column card grid for everything
- ❌ Hero → Features → Testimonials → CTA page structure on every landing page
- ❌ Numbered markers (01 / 02 / 03) when the content isn't actually a sequence
- ❌ Dividers between every section regardless of visual need
- ❌ "Floating" cards with heavy shadows on flat backgrounds

**Type slop:**
- ❌ Gradient text on every hero headline
- ❌ `font-weight: 800` for everything that needs to be "bold"
- ❌ Mixing serif display + sans body as if it's always the right call
- ❌ All-caps labels on every subheading
- ❌ `letter-spacing: 0.1em` on body text

**Color slop:**
- ❌ Warm cream (#F4F1EA) as the default "not white" background
- ❌ Single accent color at 100% saturation, everything else grayscale
- ❌ Dark mode that's just `background: #0f0f0f` with white text
- ❌ Gradient backgrounds that go from purple to blue to teal on every hero

**Motion slop:**
- ❌ Every element fades up on scroll regardless of whether it helps
- ❌ Hover states that scale to 1.05 on every card
- ❌ "Breathing" animations on anything that should feel stable
- ❌ Particle effects, floating blobs, or animated backgrounds that serve no information purpose

**Copy slop:**
- ❌ "Unlock the power of X" / "Supercharge your Y" / "Seamlessly integrate Z"
- ❌ Feature names that are verbs + nouns that mean nothing ("SmartSync", "FlowBoost")
- ❌ Testimonials with five stars and suspiciously perfect one-liners
- ❌ CTAs that say "Get Started" or "Learn More" with no specificity

### What to do instead

Before finalizing any design decision, ask: *would I arrive at this same choice if building any other product?* If yes — change it. The right choice is the one that could only be right for this product.

---

## Part 6: Complex Product UI

Marketing pages need one job. Product UIs need to handle everything at once without feeling chaotic. Different discipline.

### Density without noise

Complex product UIs must be dense and scannable. Every element has a purpose; nothing is decorative. The goal is to surface information at the right level of detail for the task at hand.

**Layered disclosure:** Surface what's needed for the current action. More detail lives one level deeper (hover, expand, secondary panel). Don't show everything at once.

```jsx
// ✅ Layered disclosure — show metadata on hover
function DataRow({ item }) {
  const [hovered, setHovered] = useState(false);

  return (
    <tr
      onMouseEnter={() => setHovered(true)}
      onMouseLeave={() => setHovered(false)}
    >
      <td>{item.name}</td>
      <td>{item.status}</td>
      <td>
        {hovered ? (
          <RowActions item={item} />  {/* revealed on hover */}
        ) : (
          <span>{item.summary}</span>
        )}
      </td>
    </tr>
  );
}
```

**Information hierarchy in tables and lists:**
- Primary identifier: left-aligned, full weight
- Status: color-coded badge, consistent position
- Secondary metadata: right-aligned, reduced opacity
- Actions: appear on hover or in a context menu, never always-visible

**Toolbars:** Group actions by frequency. Primary action is always the leftmost/topmost. Destructive actions are separated from constructive ones by a visual break, not just proximity.

### State machine thinking

Every complex UI element exists in multiple states. Design all of them before building any:

| State | Description |
|-------|-------------|
| Empty | No data yet — invitation to act, not a void |
| Loading | Skeleton of the real state |
| Partial | Some data, more expected |
| Populated | Normal operational state |
| Error | Something failed — what and how to fix it |
| Disabled | Not available — why not? |
| Selected/active | User is focused here |
| Success | Action just completed |

Never ship a component that handles fewer than 5 of these.

### Navigation in complex UIs

- Primary nav: persistent, highest hierarchy, max 7 items
- Secondary nav: contextual to the current section
- Breadcrumbs: only when depth > 2 and users need to orient
- Tab bars: only when content is genuinely parallel (not sequential steps)
- Sidebar: for persistent context (filters, tree, outline) that supports the main content area

**No mystery navigation.** Every section the user can reach should be reachable from where they are. Dead ends feel like bugs.

### Form design in product context

Product forms are different from onboarding forms. They are used repeatedly, by experts, who want speed.

- Labels above inputs (not placeholder-as-label — it disappears)
- Keyboard navigable in logical order (`tabIndex`, `autoFocus` on first field)
- Inline validation, not submit-then-error
- Save state is always visible (auto-save indicator or explicit save button with status)
- Multi-page forms show progress that matches the real length

```jsx
// ✅ Inline validation on blur
function ValidatedInput({ name, validate, ...props }) {
  const [error, setError] = useState(null);
  const [touched, setTouched] = useState(false);

  const handleBlur = (e) => {
    setTouched(true);
    const err = validate(e.target.value);
    setError(err);
  };

  return (
    <div>
      <input
        {...props}
        onBlur={handleBlur}
        aria-invalid={touched && !!error}
        aria-describedby={error ? `${name}-error` : undefined}
      />
      {touched && error && (
        <span id={`${name}-error`} role="alert">{error}</span>
      )}
    </div>
  );
}
```

---

## Part 7: Psychological UX Principles

Apply at least one principle explicitly per screen. State it before building.

### Principle selection guide

| Screen type | Primary | Secondary |
|-------------|---------|-----------|
| Registration / signup form | Smart Defaults | Reciprocity |
| Onboarding wizard | Goal Gradient | Emotional Feedback Loop |
| Free tool / calculator | Reciprocity | IKEA Effect |
| Settings / config page | Smart Defaults | IKEA Effect |
| Pricing / plans page | Contrast Effect | Loss Aversion |
| Trial expiry / upgrade | Loss Aversion | Contrast Effect |
| Dashboard first visit | Goal Gradient | Polish as Trust Signal |
| Charts / financial data | Polish as Trust Signal | Approachable Complexity |
| Complex domain (fintech, legal) | Approachable Complexity | Polish as Trust Signal |
| Learning / quiz flow | Emotional Feedback Loop | Goal Gradient |
| Error / empty states | Approachable Complexity | Emotional Feedback Loop |

### The principles

**Smart Defaults** — Pre-fill everything you can infer. Every blank field has a cognitive cost. Auto-detect locale, timezone, currency. Select the most common option. Sliders start at the statistically likely value.

**Goal Gradient** — Start progress bars at 20–25%, never 0. Mark trivial first steps as complete. Frame as "N of M done," not "0 of M remaining." People work harder the closer they are to a goal.

**Reciprocity** — Give real value before asking for login or payment. Let users complete one meaningful action. Frame the gate as "save your results," never "register to continue."

**IKEA Effect** — Allow customization before login. Persist their work in state. At the gate, say "finish what you started," not "get started."

**Loss Aversion** — Frame upgrades around what disappears, not what's gained. "Your 47 saved items will be deleted" beats "upgrade for storage." Concrete specifics beat categories.

**Contrast Effect** — Show the most expensive option first. Include a clearly over-the-top tier to anchor perception. Mark the target tier as "Most Popular."

**Emotional Feedback Loop** — Success and failure states have distinct, expressive animations. Spring physics, not linear easing. Reactions under 400ms. Celebrations under 1s. The UI responds like it cares.

**Approachable Complexity** — In high-anxiety domains (finance, crypto, legal, medical), replace technical vocabulary with plain verbs. Rounded corners, soft gradients, warm neutrals. Confirmation screens are reassuring, not dry.

**Polish as Trust Signal** — Charts animate in on mount. Data transitions are interpolated, never jump. Skeleton screens instead of spinners. No layout shift. Smoothness is a proxy for reliability.

---

## Part 8: Motion

Motion is information delivery, not decoration.

**Use motion to:**
- Show state changes (button → loading → success)
- Signal relationships (element expanding from its trigger)
- Guide attention (entrance animation on the most important element)
- Provide feedback (shake on error, bounce on success)

**Don't use motion to:**
- Fill silence (idle animations on stable UI elements)
- Prove you know CSS (effects that serve no informational purpose)
- Slow users down (anything over 300ms on a repeated action)

**Easing defaults:**
- Entrances: `ease-out` (fast start, gentle land)
- Exits: `ease-in` (gentle start, fast exit)
- Interaction feedback: `spring` with stiffness 300–400
- Data transitions: `ease-in-out`

**Respect `prefers-reduced-motion`:**

```css
@media (prefers-reduced-motion: reduce) {
  *, *::before, *::after {
    animation-duration: 0.01ms !important;
    transition-duration: 0.01ms !important;
  }
}
```

---

## Part 9: Accessibility

WCAG 2.2 AA is the minimum. Ship nothing that fails these.

**Must**
- Contrast ≥ 4.5:1 for normal text, ≥ 3:1 for large text (18px+) and UI components
- Visible `:focus-visible` rings — never `outline: none` without a replacement
- Full keyboard support with a logical tab order
- Focus trap inside modals and drawers; restore focus to trigger on close
- Hit targets ≥ 24px on desktop, ≥ 44px on mobile
- `font-size ≥ 16px` on mobile inputs (prevents iOS auto-zoom)
- `prefers-reduced-motion` respected globally (see Motion section)
- `user-scalable=no` is forbidden — never block browser zoom
- Paste never blocked in inputs
- Loading buttons keep their original label and add a spinner; label never disappears

**Should**
- Semantic HTML first — use the element that already means what you need
- `aria-*` only when native semantics are insufficient, not as a shortcut
- Color is never the only indicator of state — pair it with an icon or text

---

## Part 10: Writing in UI

Voice and label quality are part of the design. Weak copy breaks otherwise strong UI.

- Active voice, sentence case everywhere
- Buttons name the exact action: "Save changes", "Publish", "Delete permanently" — not "Submit" or "OK"
- Use the same verb through the full flow: if the button says "Publish", the toast says "Published"
- Errors state what happened and how to fix it — never "An error occurred"
- Empty states invite action: "No projects yet — create your first one" beats "Nothing here"
- No cleverness over clarity; the user is doing a job, not reading a brochure
- Labels describe what the user controls, not what the system calls it internally

---

## Pre-Ship Checklist

### Visual & Identity
- [ ] Product + audience + single job named before any code
- [ ] 3-tier token system defined (Reference → System → Component)
- [ ] Signature element stated and present in the build
- [ ] No forbidden AI defaults (typography, color, layout, motion, copy)
- [ ] Proportions follow a real scale (type ratio, spacing unit, consistent aspect ratios)
- [ ] Line length capped at 65–75ch for body text

### Loading & state
- [ ] Skeletons mirror real content structure with shimmer animation
- [ ] Skeleton shown for all async content areas
- [ ] Skeletons fade out, real content fades in (no hard cut)
- [ ] All interactive elements have at minimum 5 states designed (empty, loading, populated, error, success)

### Optimistic UI
- [ ] Toggles and simple mutations update immediately without waiting for server
- [ ] Rollback is implemented for failed requests
- [ ] Payments and irreversible actions are NOT optimistic

### Product UI
- [ ] All states of every component are handled (empty, loading, partial, populated, error, disabled, selected, success)
- [ ] No mystery navigation — every section is reachable from the current location
- [ ] Forms validate inline on blur, not on submit
- [ ] Information hierarchy is clear: primary left, status color-coded, secondary reduced opacity, actions on hover

### Psychological UX
- [ ] At least one principle named and concretely implemented
- [ ] Forms pre-filled with smart defaults — nothing blank without reason
- [ ] Upgrade/loss copy is framed around what disappears, not what's gained
- [ ] Data screens animate in; skeletons used over spinners; no layout shift on load

### Accessibility
- [ ] Contrast ratios meet WCAG 2.2 AA (4.5:1 normal text, 3:1 large/UI)
- [ ] `:focus-visible` rings present and visible on all interactive elements
- [ ] Keyboard navigable in logical tab order; focus trap in modals
- [ ] 44px tap targets on mobile for all clickable elements
- [ ] `prefers-reduced-motion` respected globally
- [ ] Browser zoom not blocked (`user-scalable=no` absent)
- [ ] Color is not the only state indicator

### Writing
- [ ] Buttons name the exact action (not "Submit" / "OK")
- [ ] Same verb used through the full flow (button → toast → confirmation)
- [ ] Errors explain what happened and how to fix it
- [ ] Empty states invite action

### Polish
- [ ] All interactive elements have motion response (whileTap / whileHover minimum)
- [ ] Mobile responsive without breaking the design concept
- [ ] No layout shift on load
