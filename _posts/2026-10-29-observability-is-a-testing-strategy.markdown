---
categories: quality testing observability monitoring sre shift-right
date: 2026-10-29 00:00:00
layout: post
title: "Observability Is a Testing Strategy"
---

A test is an assertion about how a system should behave. An alert is exactly the same thing - it just runs in production, against real traffic, forever.

<!--more-->

Most teams don't see it that way. Tests belong to developers, live in the repository and get reviewed. Alerts belong to ops or SRE, live in a dashboard and get added after an incident, without anyone reviewing them. Two worlds, two owners.

In [Shift Left vs Shift Right](/blog/shift-left-vs-shift-right) I described monitoring as the medical check-up that complements a healthy diet. This post is about the mechanism: **if alerts are assertions, they deserve the same discipline we give to tests - and a testing strategy that ignores them has a hole exactly where users live.**

---

## A green suite and three days of silence

The suite is green, the deploy goes out on Tuesday. On Friday a support ticket arrives: payments in one specific currency have been failing since Tuesday, only through one provider, only above an amount nobody ever used in staging.

Nobody wrote a wrong test. Tests answer "does it work in the cases I imagined?". Production answers "does it work in the cases that actually exist?". For three days the system knew the answer - the errors were in the logs - but nothing was listening.

---

## The same thing, with a different name

| Testing | Observability |
|---|---|
| Assertion | SLO / alert condition |
| Flaky test | Noisy alert |
| Test that can never fail | Alert that never fires |
| Testing implementation details | Alerting on CPU instead of user impact |
| Smoke test after deploy | Synthetic monitoring |
| Coverage | Which user journeys have a signal at all? |

We've learned to worry about everything on the left. Rarely about the right, even though it's the same problem.

And alerts get the same diseases. **Noisy alerts destroy trust like flaky tests**: as I argued in [Trust Is a System Property](/blog/trust-is-system-property), a signal everyone ignores trains people to ignore signals. **Dead alerts** - on a metric that stopped being emitted two refactorings ago - are green forever, and green forever looks just like "everything is fine". **Alerting on causes** like CPU or queue size is testing the implementation; the [Google SRE book](https://sre.google/sre-book/monitoring-distributed-systems/) says to page on symptoms, for the same reason we test behaviour: internals change, the promise to the user doesn't.

---

## Your E2E suite, on a schedule

The most literal version of the idea is synthetic monitoring: take a user journey and run it against production every few minutes. If you have an end-to-end suite - like the [Playwright](/blog/playwright-in-the-real-world) one I wrote about - most of the work is done.

It isn't free: you need dedicated accounts, a way to recognise synthetic traffic, and journeys that stop before anything irreversible. Five journeys that matter, not five hundred. In exchange you get something CI can't give you: proof that it works *right now*, with real DNS, real certificates, real third parties. Half the incidents I've seen weren't code at all.

---

## SLOs: acceptance criteria that never expire

![A monitoring dashboard on a screen, with metric tiles and trend lines](/assets/2026-10-29-observability-is-a-testing-strategy/dashboard.jpg)

An acceptance criterion is checked once, before the release. An SLO - *99.5% of checkouts succeed within two seconds over 28 days* - is the same sentence, checked for as long as the feature exists. And the error budget is the most honest answer I know to [How Much Is a Bug Worth?](/blog/how-much-is-a-bug-worth): it turns "how much quality is enough" from opinion into a number agreed in advance.

---

## Closing the loop

The two halves should feed each other:

- **Every incident found in production should produce a test.** Production had to tell you, so the suite was missing a question.
- **Every test you can't write should produce a signal.** Scale, timing, third parties: decide deliberately - "we won't test this, so we'll watch it" - not by omission.

The obvious objection: so just watch production? No. Observability finds problems **after** a user has had them. It can shrink the time between breaking and knowing from three days to three minutes, never to zero. Fine for a typo on a landing page, not for a payment or a deleted file. Charity Majors made the case for [testing in production](https://increment.com/testing/i-test-in-production/) better than anyone, and she never argued for dropping pre-production tests.

Observability is a testing strategy for what is too expensive to test before, not a replacement for what is cheap to test before.

---

## Three questions for the next design review

1. **How will we know this works in production?** Which signal, with which threshold.
2. **Who gets woken up if it breaks?** "Whoever is on call, I guess" means nobody owns it.
3. **Which test will the first incident produce?** If you can imagine the incident, you can probably write the test now.

None of these needs a new tool. Just tests and alerts discussed in the same room - which, if they're the same thing, is where they belonged all along.
