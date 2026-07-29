---
categories: quality economics process testing risk
date: 2026-07-29 00:00:00
layout: post
title: "How Much Is a Bug Worth?"
---

A few evenings ago I was out for dinner with a friend who works as a developer. At some point the conversation drifted to work: how do you actually bring some quality into a company that doesn't seem to want any? Which arguments land, and which ones fall flat.

<!--more-->

And almost by reflex, one of us pulled out the classic argument - the one we all reach for sooner or later: *look how much a bug costs you if you find it late.*

That's where it got interesting. Because the more we talked about it, the more I realized that argument isn't about the cost of a bug at all. It's about something else entirely.

---

### The Number We All Cite and No One Can Source

Every talk about quality eventually shows the same chart. A bug caught in requirements costs 1x. In design, 10x. In production, 100x. Clean, exponential, convincing. I've used it myself.

There's just one problem: that chart has no solid source behind it.

That finding a problem late costs more - the *direction* - we all know from experience. But the precise multiplier, that neat and reassuring 100x, has no real research anyone can point to. It's a number that propagated from slide to slide until it became common sense.

So the interesting question isn't "what's the right figure?", it's a different one:

> Why do we keep clinging to a number nobody knows the origin of?

Because we need it. That 100x isn't an intellectual mistake - it's a symptom. It's the weapon we pull out when we have to justify an investment whose return we can't prove. The very fact that we need an inflated number says everything about how quality actually gets funded.

## Quality Only Gets Funded When It Hurts

![Hurry](/assets/2026-07-29-how-much-is-a-bug-worth/fretta.jpg)

> Nobody decides to invest in quality because quality is good. They decide when they can see what *not* having it is costing them.

And that's the whole problem: that cost only becomes visible after the fact.

## The Prevention Paradox

The classic Cost of Quality model splits spending into three buckets: prevention, appraisal and failure. Prevention is largely the developer's work - tests, reviews, refactoring, type systems. Appraisal is the QA/SDET's work - verification, exploration, testing. Failure is the bug that bites, before release or, worse, in front of the user.

Of these three, only one is visible: failure.

Nobody gets credit for the outage that didn't happen. You can't put "disasters averted" on a balance sheet, because you can't prove a counterfactual. So prevention and appraisal look like pure overhead - until a failure comes along that you can attribute to their absence.

And look at who's caught inside this paradox: the dev and the QA. Not rivals - "why didn't QA catch it?" - but two roles whose value is invisible for the exact same accounting reason. Failure is the only thing that leaves a trace in a spreadsheet someone actually looks at. All the work that *prevents* failure leaves none.

## The Sawtooth

![The Risk-Reward Cycle](/assets/2026-07-29-how-much-is-a-bug-worth/Gemini_Generated_Image_h3fkcph3fkcph3fk.png)

Watch a company for a few years and you'll always see the same curve: incident → panic → budget → the memory fades → underinvestment → next incident. The half-life of a postmortem. The quality budget spikes right after the pain and decays as you move away from it.

Teams stay chronically underinvested in prevention not because anyone is stupid, but because prevention's return can't be shown in advance - and whoever holds the budget, rationally, optimizes the number they can actually see.

If you're a dev or a QA and you feel like you're fighting the same battle at every planning session, now you know why: you're not losing because you're incompetent. You're losing against a structural distortion.

## So Is Reactive Always Wrong? No.

Here I'll play devil's advocate against myself. Investing *only* reactively isn't entirely irrational. You don't know which failures will materialize, and over-investing in prevention against disasters that never arrive is a cost too. "Wait until it hurts" is a crude risk-pricing strategy - but sometimes it works.

What matters, far more than the lifecycle phase, are two axes:

- **Blast radius** - how many users, which flow.
- **Reversibility** - a feature flag you kill in ten seconds, or corrupted data you can't come back from.

|  | Reversible | Irreversible |
|---|---|---|
| **Low blast radius** | Fix it in prod, move on | A cheap guardrail is enough |
| **High blast radius** | Flag it, roll out gradually | Prevent at almost any cost |

A quick example. A CSV export with the wrong separator: you spot it, fix it, release, nobody gets hurt. A migration that mis-rounds amounts already written to the database: there's no rollback that gives you back the lost cents. The same "bug" on paper, two different worlds.

In the first case, waiting until it hurts is legitimate - that's exactly where "not testing" is a rational choice, not laziness. Testing is never free; sometimes the premium is higher than the claim. In the second case the opposite holds, and it's the only real exception to the reactive model:

> You can only afford to learn from failure where failure is cheap and recoverable.

Where the first failure is catastrophic or irreversible, reactive is already too late. You're not pricing the risk - you're betting the company on a coin flip you never even calculated.

## Budget Helps, But It's Not Enough

To be clear, so this isn't misread: funding quality is right, and translating it into money - a cost avoided, a risk priced - is how you get that funding. I'm not arguing the opposite.

I'm arguing that budget alone doesn't change the *way* quality is seen.

If the only moment quality gets attention is when it hurts and someone attaches a price tag to it, then it stays something you repair after the incident. Reactive by definition. The funding arrives, but always in response to pain - and that leaves the sawtooth we started from completely intact.

Quality becomes solid when it stops being a line item to approve and becomes part of *how* software gets built every day: in the reviews, in the tests, in how a team decides what deserves attention before anything breaks. Budget can enable that, but it doesn't create it on its own.

That's why the economic argument is necessary but not sufficient. It gets you the resources - and the resources are the start of the work, not the end.

So the honest answer to "how much is a bug worth?" isn't a number. The number was always folklore.

The real question is: how much are you willing to pay, and in which currency, to not have it? And above all - are you paying it *before*, while you can still choose, or *after*, when the disaster decides for you?
