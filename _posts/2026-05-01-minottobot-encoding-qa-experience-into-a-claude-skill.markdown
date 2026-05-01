---
categories: qa testing ai claude skill developer-experience
date: 2026-05-01 00:00:00
layout: post
title: "minottobot: encoding a decade of QA experience into a Claude skill"
---

Back in 2016, my colleagues started calling me "minottobot" — not out of affection, but out of frustration. Every pull request I reviewed came back with meticulous comments about syntax issues, stylistic inconsistencies, naming conventions, indentation. I was a human linter, really. Relentless. In retrospect, I was probably insufferable.

The thing was, I wasn't wrong about the issues. But I was doing this manually, one PR at a time, leaving comments, waiting for fixes, reviewing again. It was tedious work, but it mattered. Code quality isn't just about passing tests — it's about the small accumulation of intent and care that keeps a codebase habitable.

<!--more-->

Years passed, and my role evolved. I moved deeper into quality engineering. The syntax comments faded into the background, replaced by bigger questions: *Why are 40% of your tests flaky? How often can you actually deploy? Do your developers understand the system they're maintaining?* The old minottobot work — the line-by-line review work — became less relevant because it was treating the symptom, not the disease.

But then AI got good. Really good.

And I found myself asking: *what if we could automate that old version of me?* Not just the syntax checking — anyone's linter can do that now. But the *reasoning*. The questions. The strategy. The coaching. What if I could take a decade of QA knowledge, convert it into a decision-making process, and hand it off to Claude?

That's where [minottobot](https://github.com/EmanueleMinotto/minottobot) came from. It's a Claude skill — a bundle of knowledge and workflow that teaches an AI how to audit an engineering team's quality practices and propose concrete, prioritized improvements.

---

## The philosophy underneath

minottobot isn't a test-writing robot. It doesn't suggest architecture patterns or name variables. It's a *QA consultant* — someone who has shipped code, lived with legacy systems, watched teams make and recover from mistakes.

It believes a few specific things:

**Quality is a team lifestyle, not a department or a phase.** When "quality" lives in a single person or a specialized team, it becomes invisible to everyone else. The moment that person leaves or gets busy, quality deteriorates. Real quality is built into how the team collaborates every day.

**Developer Experience is the mechanism of quality.** This is the hill minottobot will die on. Every recommendation ultimately serves the customer first, and the developer second — because when developers have the right tools, fast feedback, a codebase they understand, and low friction in their day, quality improves as a natural consequence. When they're fighting the CI system, waiting 40 minutes for feedback, or afraid to touch the code — quality suffers no matter how many test cases exist.

**Prevention and observation are parallel, not sequential.** Shift Left without Shift Right is theoretical confidence. Shift Right without Shift Left is constant firefighting. You need unit tests *and* monitoring. You need code reviews *and* feature flags. Both, always.

**Ownership is everything.** The most common pattern minottobot encounters: "that's not my problem." When things break and the team's first question is "who did this?" instead of "what do we learn?", quality becomes tied to fear. Transparency and shared ownership are how teams build sustainable quality.

---

## How it works

minottobot runs an audit in four phases.

**Session init** — it first checks whether you've run an audit before (by looking for a `.minottobot/audit-*.md` file in your repository). If you have, it loads the previous results and opens with a comparison view. "What's changed since then?"

**Code Reconnaissance** — before asking you a single question, minottobot reads your codebase. It scans your CI configs, test files, build scripts, linting setup, monitoring integrations, and recent git history. It builds an evidence map of what actually exists, not what you *think* exists. Teams often describe a better reality than the code shows — not from dishonesty, but because they don't know what they don't know.

**Phase 0 — Quantitative baseline** — thirteen questions. Team size. Total test count. Average CI run time in minutes. Deployment frequency per day/week/month. Lead time for changes. Change failure rate. CI success rate. MTTR from incidents. Test coverage %. Skipped tests. Feature flags. Days since last production incident. Open bugs older than 30 days.

Any number you can't answer is immediately a finding. This grounds the conversation in data, not vibes.

**Phase 1 + Phase 2 — Audit and Strategy** — minottobot evaluates your team across six areas: CI/CD, Testing, Code review, Monitoring, Developer Experience, Ownership & culture. Each area gets a score from 1 (critical) to 5 (excellent), backed by evidence from code and your answers.

Then it builds a three-horizon improvement plan: short term (this sprint), medium term (this quarter), long term (this half). The output follows a fixed format — the same structure every time, so you can compare audits over time and feed the results into your ticket tracker.

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
- "Our CI is broken and tests are flaky — help us fix it"
- "How do we improve our testing strategy?"
- "Our team is 12 engineers with 5,000 tests and a 30-minute build — what's wrong?"

It won't trigger on:

- "Help me implement this feature" (that's product work, not QA)
- "How do I configure Kubernetes?" (that's infrastructure)
- "Explain how React hooks work" (that's education)

---

## Example: a startup with zero tests

Imagine a small team at Swiftly Inc — four engineers, Node.js backend + React frontend, shipping to production multiple times a day with no testing infrastructure and no staging environment. Everything is manual.

minottobot runs the audit and produces:

| Area | Score | Finding |
|------|-------|---------|
| CI/CD | 2/5 | Linting only; deploys are fully manual with no pipeline gate |
| Testing | 1/5 | Zero tests across the entire stack |
| Code review | 2/5 | Informal and inconsistent; no policy |
| Monitoring | 1/5 | No alerting; users are the de facto monitoring system |
| Developer Experience | 2/5 | No staging means devs can only validate in production |
| Ownership & culture | 3/5 | Small team, likely high trust — but no structures backing it up |

**Top 3 blockers:**
1. No monitoring — incidents are discovered by users, not the team
2. Zero tests + direct pushes to production — no safety layer between a keystroke and a live outage
3. No staging environment — every untested change lands directly on paying customers

**Short term (this sprint):**
- Wire up error tracking (Sentry, free tier) and Slack alerts
- Add basic uptime monitoring
- Write 5–10 unit tests on the highest-risk business logic (auth, billing)
- Require 1 code review approval before merge on main

**Medium term (this quarter):**
- Provision a staging environment
- Replace `git push heroku main` with a GitHub Actions pipeline
- Add integration tests on core API endpoints

**Long term (this half):**
- Build a test pyramid: units, integrations, 2–3 E2E tests on critical paths
- Define a technical standards document
- Introduce incident post-mortems as a learning practice

This is concrete. Prioritized. Actionable. And it came from understanding the team's constraints, not from a generic checklist.

---

## Example: returning engagement and the delta view

Six weeks later, the team comes back. They've made progress — Sentry is in place, they've written some tests, they've slowed down slightly to do code review. But new problems have emerged.

minottobot loads the previous audit snapshot, runs the full process again, and then shows you what changed:

<div style="float:right">
    <img src="/assets/2026-05-01-minottobot-encoding-qa-experience-into-a-claude-skill/delta-view.png" alt="minottobot delta view between two audit sessions">
</div>

| Area | Previous | Current | Change |
|------|----------|---------|--------|
| CI/CD | 2/5 | 2/5 | — |
| Testing | 1/5 | 2/5 | ↑ +1 |
| Code review | 2/5 | 3/5 | ↑ +1 |
| Monitoring | 1/5 | 3/5 | ↑ +2 |
| Developer Experience | 2/5 | 2/5 | — |
| Ownership & culture | 3/5 | 3/5 | — |

**Resolved blockers:**
- No monitoring — *Sentry and uptime monitoring are now in place*

**Still open:**
- No staging environment — *Still pushing directly to production; deploy times have slowed slightly but the risk remains*
- Zero tests — *5–10 tests now exist on critical paths, but most of the codebase is still untested*

**New blockers:**
- Deployment friction — *Code review is working but it's slowing down deployment; developers are frustrated with the additional gate; need to streamline the process*

**New action items:**
- `A4`: Profile deployment frequency impact; consider fast-track review for low-risk changes
- `A5`: Implement automated checks to reduce manual review time
- `A6`: Schedule next audit in 6 weeks to measure progress on staging environment

This is the power of the delta view: not just "here's your score," but *"here's what moved, here's what stuck, here's what surprised us."* It turns audits into a feedback loop.

---

## The validation system

minottobot is tested against five realistic scenarios:

- **startup-chaos:** 4 engineers, zero tests, Heroku pushes
- **enterprise-legacy:** 85 engineers, 30% flaky tests, 47-minute Jenkins builds
- **post-incident:** A production incident that affected thousands of users; how does minottobot respond?
- **high-functioning:** A team doing 4+ deploys per day, 0.1 P1s per month, 12-minute MTTR; is minottobot smart enough not to over-prescribe?
- **org-chaos:** Two competing CI systems, no product ownership, competing priorities

In iteration 5 (April 2026), minottobot scored a 96% pass rate across all five scenarios (24 out of 25 assertions). The single failure is interesting: on the high-functioning team, minottobot recommended tools that the team already owned. That's not a bug — it's a validation that the skill is giving pragmatic advice based on actual context, not boilerplate recommendations.

<div style="float:right">
    <img src="/assets/2026-05-01-minottobot-encoding-qa-experience-into-a-claude-skill/phase-0.png" alt="minottobot Phase 0 quantitative baseline questions">
</div>

---

## Why this matters

The goal wasn't to automate the 2016 version of me — the one leaving syntax comments on every PR. That version is less relevant now. Linters have gotten better. Culture has shifted.

The real question was: *can a decade of accumulated QA knowledge — pattern recognition, priority judgment, the ability to ask the right question at the right time — be transferred to an AI agent?*

The answer, it turns out, is yes. With caveats. minottobot is opinionated. It assumes quality is a team responsibility, not a department. It pushes for transparency and measurement. It values developer experience and pragmatism over perfection.

But those aren't bugs. Those are features. They're a particular philosophy about how teams build software. And if they align with how you think about quality, minottobot can accelerate your progress.

The skill is open source on GitHub. It's being used and evaluated in the real world. And I'm curious to see where it goes — not just as a tool, but as an example of how domain knowledge can be encoded into AI systems in ways that are reproducible, measurable, and actually useful.

The 2016 minottobot was annoying. But the questions were good. And now we have a way to ask them at scale.

