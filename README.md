# Arabic-First RTL Platform — Case Study

> Architecture and decisions behind a production Arabic-first corporate platform,
> built and shipped solo end to end. Client code is private (NDA); this repo is
> the engineering record: decisions, trade-offs, and verified state.

**Live product:** https://trioksa-website.vercel.app
**Method record:** [AI-Augmented Delivery](https://github.com/bipolar703/case-study-ai-augmented-delivery)

## What it is

An Arabic-first corporate platform for an energy systems and generator supply
company — Arabic as the primary language, English secondary, RTL as the default
layout direction rather than an afterthought.

Built solo, design through deploy: brand-aligned visual system → architecture →
implementation → production deployment on Vercel.

## Stack (honest version)

- **Next.js + React + TypeScript**, Tailwind CSS for the utility layer
- Deployed on **Vercel**
- Delivered through an **AI-augmented workflow**: tuned harness + prompt-driven
  iteration, validated before every deploy by Lighthouse and test suites

That last line is deliberate. I don't claim to hand-tune framework internals;
I claim to ship validated production output through a workflow I control. The
verification section below is how you check that claim yourself in about ten
seconds.

## The RTL decisions that mattered

1. **Logical properties, not left/right.** `margin-inline-start`, `padding-inline`,
   `text-align: start`. The site is direction-agnostic at the CSS layer; Arabic
   renders RTL and English LTR from the same rules.
2. **Arabic typography is not a font drop-in.** Line-height and letter-spacing
   tuned for Arabic script readability; mixed Arabic/Latin content (product
   codes, model numbers) set without direction flips mid-string.
3. **Numerals decided per context.** Arabic-Indic vs Western digits chosen by
   content type (contact numbers Western for legibility, quantities Arabic-Indic
   where the audience expects them) — not globally.
4. **`dir` per document, not per component.** One document-level direction with
   scoped bidirectional islands for embedded Latin content.

## Verification — check this yourself

Everything below was measured on **2026-10-11** with plain HTTP requests — no
dashboards, no screenshots you have to trust:

```bash
# Live, and Arabic is the *document* default, not a component patch
curl -sL https://trioksa-website.vercel.app | grep -o 'lang="[a-z]*" dir="[a-z]*"'
# → lang="ar" dir="rtl"

# Same probe on any surface I claim is live
curl -sS -o /dev/null -w '%{http_code}\n' https://trioksa-website.vercel.app
# → 200
```

Run Lighthouse against the live URL for Performance / Accessibility /
Best-Practices / SEO. I don't publish scores I haven't run in the same
environment you'd run them — measured receipts are the point of this repo, and
your run is the receipt. What I do stand behind: every deploy in this workflow
passed those checks before it shipped.

## Why no source code here

Client work under commercial terms is private. What transfers between projects —
the decisions, the failure modes, the measurements — is published instead. Ask me
about any decision in a technical interview and I can go a level deeper than this
README.

## What I'd do differently

- Start the visual system as tokens from day one instead of retrofitting.
- Add a WCAG 2.2 AA audit pass as a release gate, not a post-launch item.

---

*Maintained by Rayyan Ghassan — Amman, Jordan.
Product engineer, 8 years: five client platforms shipped end to end, and 19 months
building Arabic NLU training data at Amazon Alexa.*
*Portfolio: trilogytradingllc.com · optimal-website-beryl.vercel.app · royjes-hair.vercel.app*
