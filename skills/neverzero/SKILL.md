---
name: neverzero
description: Apply proven psychological UX principles when building or improving UI. Use when designing onboarding flows, pricing pages, forms, signup gates, dashboards, or any user-facing interface where motivation, trust, or conversion matters.
license: MIT
metadata:
  author: semeikhan-t
  version: "1.0.0"
---

# UX Principles: Psychology-Driven Design

Before writing any UI code, identify which principle(s) below apply to the current screen or flow. You must apply **at least one** principle explicitly — state in a comment or planning note which principle you chose and why.

---

## The Six Principles

### 1. Smart Defaults
**Never make users think more than necessary.** Pre-fill forms with the most probable values. Select the most common option. Infer from context (timezone from browser, country from IP, role from referral source). The cognitive cost of every blank field is real — eliminate it where possible.

**When to apply:** Any form, settings screen, onboarding step, or configuration wizard.

**Implementation:**
- Pre-select the most common radio/checkbox
- Pre-fill inputs with inferred or example values
- Set sliders/steppers to the statistically likely value
- Auto-detect locale, currency, timezone

```jsx
// ✅ Smart default: pre-select most common plan
const [plan, setPlan] = useState("pro"); // not "" or null

// ✅ Smart default: infer country from browser
const [country, setCountry] = useState(
  Intl.DateTimeFormat().resolvedOptions().timeZone.split("/")[0] ?? "US"
);
```

---

### 2. Goal Gradient Effect
**Never start a user at 0%.** People work harder as they get closer to a goal. Show progress before they've done anything — start at 20–30% to create momentum, not emptiness.

**When to apply:** Onboarding flows, profile completion, multi-step forms, level/streak systems.

**Implementation:**
- Progress bars begin at 20–25%, not 0%
- Label completed steps as done even for trivial ones ("Account created ✓")
- Show "3 of 5 steps complete" framing after step 1
- Celebrate micro-wins with subtle animations

```jsx
// ✅ Goal gradient: start at 25%, not 0
const INITIAL_PROGRESS = 25;
const [progress, setProgress] = useState(INITIAL_PROGRESS);

// ✅ Pre-mark the first step as already done
const steps = [
  { label: "Create account", done: true },   // auto-complete
  { label: "Set your goal", done: false },
  { label: "Add first item", done: false },
];
```

---

### 3. Reciprocity
**Give value before asking for anything.** Let users experience the product's benefit — see a result, use a tool, get a report — before you ask them to register or pay. The free value creates a felt obligation and trust.

**When to apply:** Signup gates, paywalls, free trial flows, lead generation, any "register to see results" moment.

**Implementation:**
- Show real output/results first, then gate with signup
- Let users complete one full action before the wall appears
- Frame the gate as "save your results" not "register to continue"
- Never gate the value proposition itself — gate the persistence/export

```jsx
// ✅ Reciprocity: show result first, then ask to save
function ResultPage({ result }) {
  const [showGate, setShowGate] = useState(false);

  return (
    <>
      <ResultCard data={result} />  {/* value first */}
      {!user && (
        <SavePrompt
          headline="Save your report for free"
          subtext="Your analysis is ready — create a free account to keep it."
          onSignup={() => setShowGate(true)}
        />
      )}
    </>
  );
}
```

---

### 4. IKEA Effect
**Let users build something before they register.** People place higher value on things they've partially created. The more a user configures, customizes, or creates — even trivially — the harder it is to abandon.

**When to apply:** Any tool where configuration precedes output: dashboards, generators, planners, editors, quizzes.

**Implementation:**
- Allow meaningful customization without login
- Persist state in localStorage so work survives page reload
- Name the created artifact after the user ("Your report", "Your plan")
- At the gate, frame it as "finishing" what they started, not "starting"

```jsx
// ✅ IKEA effect: persist user's work before signup
useEffect(() => {
  localStorage.setItem("draft_project", JSON.stringify(projectState));
}, [projectState]);

// ✅ Gate copy references their investment
<SignupModal
  headline={`Your ${projectState.name} is almost ready`}
  subtext="Create a free account to save and share your project."
/>
```

---

### 5. Loss Aversion
**Show what they'll lose, not what they'll gain.** Losing something feels roughly twice as painful as gaining the equivalent feels good. Frame upgrades and CTAs around what disappears if they don't act, not what they'll receive.

**When to apply:** Upgrade prompts, trial expiry warnings, plan comparison pages, cancellation flows.

**Implementation:**
- "You'll lose access to X" > "Upgrade to get X"
- Show the features being taken away, not just the premium list
- Use concrete specifics: "Your 47 saved items will be deleted" > "Upgrade for storage"
- Trial countdowns emphasize disappearance, not a deadline

```jsx
// ✅ Loss framing on trial expiry banner
<Banner variant="warning">
  Your trial ends in 3 days — your 12 saved reports and custom dashboard
  will be deleted. <a href="/upgrade">Keep everything →</a>
</Banner>

// ❌ Gain framing (weaker)
// "Upgrade to Pro and get unlimited reports and custom dashboards"
```

---

### 6. Contrast Effect
**Value is relative, not absolute.** What a user sees first shapes how they judge everything after. Place an expensive or extreme option before your target option to make it feel reasonable and obvious.

**When to apply:** Pricing pages, plan selectors, package comparisons, upsell screens.

**Implementation:**
- Show the highest-priced plan first (left-to-right or top-to-bottom)
- Include a clearly "over the top" enterprise tier to anchor perception
- Mark the middle tier as "Most Popular" to signal the social norm
- Use price-per-day framing after showing the monthly anchor ("less than a coffee")

```jsx
// ✅ Contrast effect: order plans highest → lowest, highlight middle
const plans = [
  { name: "Enterprise", price: 299, highlight: false },  // anchor
  { name: "Pro",        price: 49,  highlight: true  },  // target (feels cheap now)
  { name: "Starter",   price: 9,   highlight: false },  // exists for reference
];
```

---

## Checklist Before You Ship a UI

- [ ] **Forms:** Are all inputs pre-filled with smart defaults? Does nothing start blank without reason?
- [ ] **Onboarding:** Does the progress bar start above 0%? Is step 1 pre-marked complete?
- [ ] **Gates:** Does the user receive real value before being asked to sign up?
- [ ] **Tools/editors:** Can the user create or configure something before they log in? Is it persisted?
- [ ] **Upgrades/paywalls:** Is the copy framed around loss, not gain? Are the specifics concrete?
- [ ] **Pricing:** Is the most expensive option shown first? Is there an anchor that makes the target feel reasonable?

---

## Principle Selection Guide

| Screen type              | Primary principle       | Secondary           |
|--------------------------|-------------------------|---------------------|
| Registration/signup form | Smart Defaults          | Reciprocity         |
| Onboarding wizard        | Goal Gradient           | IKEA Effect         |
| Free tool / calculator   | Reciprocity             | IKEA Effect         |
| Settings / config page   | Smart Defaults          | IKEA Effect         |
| Pricing / plans page     | Contrast Effect         | Loss Aversion       |
| Trial expiry / upgrade   | Loss Aversion           | Contrast Effect     |
| Profile / completion     | Goal Gradient           | Loss Aversion       |
| Dashboard first visit    | Goal Gradient           | Smart Defaults      |

---

## Usage Note for the Agent

When you start working on any UI screen:

1. **Identify** which principle(s) from the table above apply.
2. **State your choice** in a brief comment before writing code: `// Applying: Goal Gradient + Smart Defaults`
3. **Implement at least one principle concretely** — not just in copy, but in logic and component structure.
4. **Do not apply all six at once** — pick the one or two most relevant to this specific screen. Over-engineering psychological triggers makes them feel manipulative rather than helpful.
