---
categories: qa testing ai claude skill developer-experience
date: 2026-05-01 00:00:00
layout: post
title: "minottobot: encoding a decade of QA experience into a Claude skill"
---

Back in 2016, my colleagues called me "minottobot" - out of both affection and frustration. Every pull request came back with meticulous comments: syntax, style, naming, indentation. I was a human linter. Relentless. Insufferable.

Years later, I moved deeper into quality engineering. The line-by-line work became irrelevant - it was treating symptoms, not disease. The real questions were: *Why are tests flaky? How often can we actually deploy? Do developers understand what they're maintaining?*

But then AI got good.

I found myself asking: what if I could automate that old version of me? Not just syntax checking - anyone's linter does that. But the *reasoning*. The strategy. The coaching. What if I could take a decade of QA knowledge, convert it into a decision-making process, and hand it off to Claude?

That's where [minottobot](https://github.com/EmanueleMinotto/minottobot) came from. It's a Claude skill that audits an engineering team's quality practices and proposes concrete, prioritized improvements.

<!--more-->

---

## The philosophy

minottobot is a *QA consultant* that operates on two core beliefs:

**Quality is built into how teams collaborate, not assigned to a department.** It's not a phase or a person - it's a daily practice. When quality is diffused across the team, it survives personnel changes.

**Developer Experience is the mechanism.** When developers have fast feedback loops, understand the codebase, and face low friction, quality improves naturally. Conversely, slow CI, unclear systems, and fear of change guarantee poor quality regardless of test coverage.

---

## How it works

minottobot runs an audit in three steps:

**Code Reconnaissance** - before asking anything, minottobot reads your codebase. It scans CI configs, tests, build scripts, monitoring, and git history to build an evidence map of what actually exists.

**Quantitative Baseline** - 13 questions: team size, test count, CI time, deployment frequency, lead time, failure rate, coverage, MTTR, etc. Any metric you can't answer is immediately a finding.

**Scoring & Strategy** - your team is evaluated across six areas (CI/CD, Testing, Code review, Monitoring, Developer Experience, Ownership). Each gets a 1-5 score with evidence. Then minottobot builds a three-horizon improvement plan: this sprint, this quarter, this half.

<div style="float:right">
    <img src="/assets/2026-05-01-minottobot-encoding-qa-experience-into-a-claude-skill/output-format.png" alt="minottobot audit report format">
</div>

The output always looks like this:
- Executive summary (3 bullets max)
- Area scores table with one-line findings
- Top 3 blockers
- Short/medium/long-term improvement plan
- Action items with stable IDs (`A1`, `A2`, ...) so you can track them across sessions

No surprises, no freeform alternatives. Consistency makes it possible to measure progress.

---

## When and how to use it

You can load minottobot into Claude Code with a single command:

```sh
/skills add minottobot/
```

Then describe your team. You can trigger it with prompts like:

- "Audit our QA practices"
- "Our CI is broken and tests are flaky - help us fix it"
- "How do we improve our testing strategy?"
- "Our team is 12 engineers with 5,000 tests and a 30-minute build - what's wrong?"

It won't trigger on:

- "Help me implement this feature" (that's product work, not QA)
- "How do I configure Kubernetes?" (that's infrastructure)
- "Explain how React hooks work" (that's education)

---

## Example: a startup with zero tests

Swiftly Inc: 4 engineers, Node.js backend + React frontend, shipping multiple times daily with no tests, no staging, no monitoring.

**minottobot's audit:**

| Area | Score | Finding |
|------|-------|---------|
| Testing | 1/5 | Zero tests; no safety layer |
| Monitoring | 1/5 | Users detect incidents, not the team |
| CI/CD | 2/5 | Manual deploys; no pipeline |

**Top blocker:** Zero tests + direct production pushes = every keystroke risks a production outage.

**Recommended plan:**
- *This sprint:* Wire up Sentry, add Slack alerts, write 5-10 critical unit tests, require 1 code review
- *This quarter:* Staging environment, GitHub Actions pipeline, integration tests
- *This half:* Test pyramid, technical standards, post-mortems

Concrete. Prioritized. Actionable.

---

## Why this matters

The real question: can a decade of QA knowledge - pattern recognition, priority judgment, knowing which questions to ask - be transferred to an AI agent?

The answer is yes. minottobot encodes a specific philosophy: quality is a team responsibility, not a department. It pushes for transparency, measurement, and developer experience over perfection. Those aren't bugs - they're features.

The 2016 minottobot was annoying. But the questions were good. Now we have a way to ask them at scale, automatically, for any team that needs them.

