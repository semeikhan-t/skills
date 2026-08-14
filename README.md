# skills

Public collection of agent skills by [semeikhan-t](https://github.com/semeikhan-t).

Installable with [`npx skills`](https://skills.sh) / `gh skill`. Indexed on [skills.sh](https://skills.sh/semeikhan-t/skills/neverzero).

---

## Skills

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

## Install

All skills in this repo:

```bash
npx skills add semeikhan-t/skills
```

Only `neverzero`:

```bash
npx skills add semeikhan-t/skills --skill neverzero
```

Via GitHub CLI:

```bash
gh skill install semeikhan-t/skills
```

Pin a release:

```bash
gh skill install semeikhan-t/skills neverzero --pin v1.0.1
```

---

## How the agent should use neverzero

1. Identify which principle(s) apply to the current screen (see the table above).
2. State the choice briefly before coding, e.g. `// Applying: Goal Gradient + Smart Defaults`.
3. Implement at least one principle in behavior and component structure.
4. Do not stack all six — overloading triggers feels manipulative.

---

## Repo layout

```text
skills/
└── neverzero/
    └── SKILL.md
LICENSE
README.md
```

---

## Links

| | |
|--|--|
| Repository | https://github.com/semeikhan-t/skills |
| Catalog | https://skills.sh/semeikhan-t/skills/neverzero |
| Latest release | https://github.com/semeikhan-t/skills/releases |
| License | [MIT](LICENSE) |
