# skills

Public collection of agent skills by [semeikhan-t](https://github.com/semeikhan-t).

Installable with [`npx skills`](https://skills.sh) / `gh skill`. Indexed on [skills.sh](https://skills.sh/semeikhan-t/skills).

---

## Skills

### hackathon-tz

Generates a full hackathon technical specification (ТЗ) as a Markdown file the whole team — and AI agents — can follow.

**Interview first, then ship the spec.** The agent collects the brief, team roster, and stack preferences before writing anything, proposes a default stack when needed, and produces a structured ТЗ with MVP scope, API sketch, per-developer task boards, roadmap, and verification checklists.

| Section | What you get |
|---------|--------------|
| Project brief | Problem, solution, audience, value proposition |
| Team & stack | Roles, tech choices, suggested repo layout |
| Functional requirements | Must / Should / Won't Have for a realistic MVP |
| API endpoints | Domain-grouped request/response sketches |
| Task breakdown | Per-developer boards with priorities and DoD |
| Roadmap | Individual timelines plus dependency graph |
| Verification | Block-level checklists before demo |
| Risks | Constraints, bottlenecks, mitigation notes |

**Use when:** preparing for a hackathon, writing a team ТЗ, splitting work across members, building a 24–48h roadmap, or when the user invokes `/hackathon-tz`.

**Output:** `ТЗ_[hackathon_name].md`

**Source:** [`skills/hackathon-tz/SKILL.md`](skills/hackathon-tz/SKILL.md)

---

### neverzero

Psychology-driven UX for product UI. Before writing interface code, the agent picks **one or two** principles that fit the screen, states the choice, and applies them in logic and structure — not only in copy.

**Never start at zero.** Progress, defaults, and value should be in place before the user has to work or pay.

| Principle | One-liner | Typical screens |
|-----------|-----------|-----------------|
| Smart Defaults | Pre-fill the most probable values; blank fields cost cognition | Forms, settings, onboarding |
| Goal Gradient | Start progress above 0% so momentum exists early | Wizards, profile completion |
| Reciprocity | Deliver real value before signup or paywall | Gates, trials, lead tools |
| IKEA Effect | Let users build or configure before they register | Editors, generators, dashboards |
| Loss Aversion | Frame upgrades around what they lose, with concrete stakes | Trials, upgrades, cancel flows |
| Contrast Effect | Anchor with a high option so the target plan feels reasonable | Pricing, plan pickers |

**Use when:** onboarding, pricing, forms, signup gates, dashboards, or any flow where motivation, trust, or conversion matters.

**Source:** [`skills/neverzero/SKILL.md`](skills/neverzero/SKILL.md)

---

### sharp-ui

Full-stack frontend craft skill. Before writing any UI code, the agent grounds the design in a named token system, picks a signature element, and checks every decision against the brief — then applies optimistic UI, skeleton loading, and psychological UX principles to make the result feel alive and trustworthy.

**Design by brief, not by default.** Every aesthetic axis is a decision traceable to the product and its audience.

| Area | Coverage |
|------|----------|
| Design identity | Token system, aesthetic calibration, anti-slop audit |
| Proportions | Type scale ratios, spacing units, line length, aspect ratios |
| Optimistic UI | Immediate state updates with rollback on failure |
| Skeleton screens | Shimmer-animated skeletons that mirror real content structure |
| AI slop checklist | Layout, type, color, motion, and copy anti-patterns to avoid |
| Complex product UI | Layered disclosure, state machine thinking, dense navigation, form design |
| Psychological UX | Smart Defaults, Goal Gradient, Reciprocity, Loss Aversion, Contrast Effect, Emotional Feedback Loop, Approachable Complexity, Polish as Trust Signal |
| Motion | Information-driven easing, spring physics, `prefers-reduced-motion` |

**Use when:** building any product UI, marketing page, dashboard, onboarding flow, or component library where visual quality, loading performance, and psychological effectiveness matter.

**Source:** [`skills/sharp-ui/SKILL.md`](skills/sharp-ui/SKILL.md)

---

## Install

All skills in this repo:

```bash
npx skills add semeikhan-t/skills
```

A single skill:

```bash
npx skills add semeikhan-t/skills --skill hackathon-tz
npx skills add semeikhan-t/skills --skill neverzero
npx skills add semeikhan-t/skills --skill sharp-ui
```

Via GitHub CLI:

```bash
gh skill install semeikhan-t/skills
```

Pin a release:

```bash
gh skill install semeikhan-t/skills hackathon-tz --pin v1.0.0
gh skill install semeikhan-t/skills neverzero --pin v1.0.1
gh skill install semeikhan-t/skills sharp-ui --pin v1.1.0
```

---

## How the agent should use these skills

### hackathon-tz

1. Ask the discovery questions in one message (brief, team, stack) before writing the ТЗ.
2. If the stack is unset, propose the default table and wait for confirmation.
3. Generate `ТЗ_[hackathon_name].md` with the full structure from the skill.
4. Keep scope honest: Must Have for demo, Should Have if time remains, Won't Have explicit.

### neverzero

1. Identify which principle(s) apply to the current screen (see the table above).
2. State the choice briefly before coding, e.g. `// Applying: Goal Gradient + Smart Defaults`.
3. Implement at least one principle in behavior and component structure.
4. Do not stack all six — overloading triggers feels manipulative.

### sharp-ui

1. Before writing any code: name the product, audience, and the screen's single job.
2. Define the token system (colors, type scale, spacing unit, radius, signature element).
3. Identify which psychological UX principle(s) apply (see the principle selection table in the skill).
4. Build, then run through the Pre-Ship Checklist before marking the task done.

---

## Repo layout

```text
skills/
├── hackathon-tz/
│   └── SKILL.md
├── neverzero/
│   └── SKILL.md
└── sharp-ui/
    └── SKILL.md
LICENSE
README.md
```

---

## Links

| | |
|--|--|
| Repository | https://github.com/semeikhan-t/skills |
| Catalog | https://skills.sh/semeikhan-t/skills |
| hackathon-tz on skills.sh | https://skills.sh/semeikhan-t/skills/hackathon-tz |
| neverzero on skills.sh | https://skills.sh/semeikhan-t/skills/neverzero |
| sharp-ui on skills.sh | https://skills.sh/semeikhan-t/skills/sharp-ui |
| Latest release | https://github.com/semeikhan-t/skills/releases |
| License | [MIT](LICENSE) |
