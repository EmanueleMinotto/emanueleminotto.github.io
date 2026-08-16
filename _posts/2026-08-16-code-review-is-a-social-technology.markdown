---
categories: code-review process culture teams collaboration ai
date: 2026-08-16 00:00:00
layout: post
title: "Code Review Is a Social Technology"
---

Every team I know does code review. Very few have ever asked themselves *what they are actually doing* when they do it.

<!--more-->

On paper it's a simple practice: before a change lands on the main branch, somebody who didn't write it reads it. It started as a quality control mechanism - [Fagan's formal inspections](https://dl.acm.org/doi/10.1147/sj.153.0182) at IBM in the seventies, with assigned roles, scheduled meetings and defect logs - and over time it got lighter until it became what we now call [modern code review](https://arxiv.org/pdf/2103.08777): informal, tool-based, continuous, one comment at a time inside a pull request.

So much for the textbook definition. The problem is that this definition describes the mechanism and describes nothing at all about the experience. Because opening a PR isn't "submitting an artifact for inspection". It's putting your work in front of your colleagues and waiting for them to say something. It's a technical act wrapped in a social one, and the social part is what decides whether the practice works or wrecks the team.

My argument is this: **review doesn't produce quality, it amplifies whatever signal is already circulating in the team.** If what's circulating is cooperation, review multiplies it. If it's fear, it multiplies that. Same tool, opposite outcomes.

Let me show it through two environments I've seen, or lived in, and one situation that shows up in both.

---

## First environment: uncertainty, stress, constant judgement

![A man buried under sticky notes covering his face and desk](/assets/2026-08-16-code-review-is-a-social-technology/uncertainty.jpg)

There's a kind of company where it's never quite clear what's expected of you. Priorities rotate every two weeks, performance reviews are opaque, and feedback almost always arrives in the shape of a correction. In a place like that, review isn't a reading of the code. It's **a documented, public, permanent evaluation of your work**.

Notice the three words. *Documented*: it stays written. *Public*: everyone with repo access can see it. *Permanent*: two years from now it's still there. No other feedback channel in the company has all three properties at once. Not even the performance review, which at least happens behind a closed door.

The result is predictable. PRs become defensive: they get opened late, when the work is finished and changing anything is expensive, so objections arrive when it's "too late to redo it". Comments become deliberately vague, because writing "this doesn't work" to a colleague who might take it badly is a risk. Courtesy approvals appear - the thirty-second *LGTM*, which is the polite way of saying "I don't want to get into this". And so do their opposite, the nitpicks about naming and formatting, which are the safe way to prove you reviewed something without exposing yourself on anything that matters.

The paradox is that all of this looks a lot like a healthy process. PRs get opened, comments exist, approvals arrive. The process metrics are green. It's the content that has been hollowed out.

Here's the uncomfortable part, and it's the objection I'd raise against myself: **the answer is not to remove review**. In an environment like this, removing it doesn't reduce judgement, it makes it invisible. The feedback keeps existing, it just stops being written down and becomes something people say about you, out loud, elsewhere. A harsh comment under a diff beats a reputation assembled in hallways.

What you can do - and it doesn't require "changing the culture", a phrase that has never changed anything - is strip review of the three properties that make it a weapon:

1. Make sure comments never feed into individual evaluations in any form, and say so explicitly rather than leaving it implied.
2. Make the distinction between blocking and optional comments visible: a plain `nit:` in front of what is a personal preference changes the tone of an entire conversation.
3. Give the author the final word on matters of taste.

These are small, almost bureaucratic things. They work precisely because they're small.

---

## Second environment: cooperation

![A group of developers gathered around a table, working together on their laptops](/assets/2026-08-16-code-review-is-a-social-technology/cooperation.jpg)

Then there's the other extreme, and anyone who's worked inside it knows exactly what I mean. Teams where the code belongs to everybody, where asking for help costs nothing, where a mistake is a technical event and not a moral one.

There, review changes nature. It stops being a checkpoint and becomes **a second pair of eyes you actually want**. You open a PR halfway through the work, in draft, not because the process demands it but because you have a doubt and you know somebody will resolve it. Comments turn into real questions. "Why did you go this way here?" read in a hostile environment means *you screwed up*; read in a cooperative one it means exactly what it says.

And something happens that the numbers never show: review becomes the place where people learn. That's where context travels - the story of why that module looks like that, the knowledge that isn't written in any document.

This isn't just a personal impression. [Bacchelli and Bird's study at Microsoft](https://www.microsoft.com/en-us/research/wp-content/uploads/2016/02/ICSE202013-codereview.pdf) - they observed, interviewed and manually classified hundreds of review comments across different teams - found that while *finding defects* remains the number one stated motivation, the actual outcomes are something else: reviews mostly produce code improvements, knowledge transfer, team awareness and alternative solutions to problems. The single most frequent category of comments in their sample was "code improvements", not defects.

It's worth pausing on that, because it inverts the common sense. **If the primary value of review isn't finding bugs, then the argument "let's automate it, a tool finds bugs better anyway" loses most of its force.** I'll come back to this at the end.

---

## Rules worth writing down

The two sections above describe climates, and a climate isn't something you get to choose on a Monday morning. What you can choose is how a disagreement is allowed to unfold once it's open - and that's a much smaller problem, with much better rules.

Here are the six I'd write down. They're about **how a discussion converges**, which is the part of review nobody designs and everybody improvises.

1. **An objection comes with an alternative.** If you don't propose one, the comment is a note, not a blocker. This gives every objection something to aim at, and it puts the same effort on the reviewer that's being asked of the author.
2. **A blocking comment states its cost.** Say what happens if nothing changes: a bug, a hole, a maintenance burden with a name. It's the fastest way to sort real objections from preferences, and the cost is usually the most useful thing in the whole thread - it's the part the author will remember next time.
3. **Two rounds, then talk.** After two exchanges on the same thread, move to a ten-minute call and write the outcome back under the diff. Written threads are excellent at recording a decision and poor at reaching one: each round adds latency and strips tone. Changing channel isn't an escalation, it's the cheaper tool for that specific job.
4. **Name the tie-breaker in advance.** Decide who calls it - a person, a role, a rotation - before you need it. A team that knows its tie-breaker can afford to disagree openly, because everyone knows the disagreement has an exit.
5. **"Not in this PR" is a valid answer,** with a ticket attached. It keeps the branch focused on the thing it was opened for, and it lets a good idea survive without being paid for by whoever happened to touch that file today.
6. **Let a machine assign reviewers.** Round robin, load balancing, whatever fits - as long as it's automatic. It widens the pool, spreads context around the team, and takes the request out of the "who do I bother about this" category, which is where PRs go to wait.

You'll notice these have the same shape as the three from earlier: procedural, checkable from the outside, indifferent to anyone's intentions. That's deliberate, and it's the whole reason they're worth writing down. A team already doing all of this by instinct loses nothing by making it explicit; a team that isn't gets the behaviour without having to first become the kind of team that produces it spontaneously.

---

## A note on enormous PRs

There's a second way to ruin reviews, and it has nothing to do with culture: running them on enormous diffs.

A big, complex PR doesn't just slow review down, it degrades it. The reviewer loses attention, comments cluster around the first few screens, substantive objections arrive when redoing the work is too expensive, and meanwhile everything depending on that branch sits still. In a cooperative team that's a lost day. In a tense one it's perfect ground for every dynamic described above, because an unreadable diff justifies both the courtesy approval and the nitpick.

The right measure, though, isn't the line count. An automated rename touching two thousand lines takes two minutes to review; eighty lines of pricing logic doesn't. What matters is **the number of independent decisions you're asking the reviewer to make**. "Small PR" is a crude proxy for "reviewable PR", which is why enforcing it as a numeric threshold produces theatre: branches split to satisfy the rule rather than to make the work legible.

---

## What I do about it: moving feedback left

Out of all this comes a conviction I arrived at slowly. **Anything a machine can decide should never become an opinion between two people.**

The comment about the stray comma, the unused import, the method ordering, the name that doesn't match the convention: all of these cost real social friction and produce value close to zero. They're also, conveniently, the easiest comments to write when you don't have the time or the appetite to engage with the substance. Taking them off the human table isn't laziness: it's removing the surface where the stupid conflicts get generated.

Hence shift-left: formatter, linter, type checker, tests, static security analysis, as much as possible *before* the PR is opened. The developer gets feedback in seconds, alone, without anyone having to tell them.

With one warning, because I've watched this go wrong too. Shift-left slides easily into shift-the-burden: fifteen pre-commit hooks and a four-minute local pipeline produce mass `--no-verify` within a month. The real constraint isn't how many checks you add, it's **how much time passes between saving the file and getting the feedback**. Past ten seconds, people route around it. And one distinction I keep rigid: deterministic automation - the kind where a wrong answer is a bug in the tool - is allowed to block. Probabilistic automation isn't. Ever. On why, let's get to the last part.

---

## What about AI?

![An arcade claw machine, its empty claw hanging above a pile of plush toys](/assets/2026-08-16-code-review-is-a-social-technology/ai.jpg)

This is the point where most articles on the subject stop reasoning and start selling, so let's begin with the numbers.

AI reviewers have got good. In [Martian's independent 2026 benchmark](https://www.coderabbit.ai/blog/coderabbit-tops-martian-code-review-benchmark), CodeRabbit came first out of ten tools evaluated, with [an F1 around 51% and precision around 49%](https://particula.tech/blog/greptile-vs-coderabbit-vs-qodo-ai-code-review-2026). In [a separate independent test on 118 runtime bugs](https://levelop.dev/blog/best-ai-code-review-tools-2026-coderabbit-greptile-qodo-compared), it caught roughly 46% of them while producing very few false positives.

Look at those numbers and translate them into plain language. **The best tool on the market misses about half the bugs, and about one of its comments in two leads to no real change.** I'll add that nearly every published comparison happens to be won by the vendor who published it: selection bias, not coincidence. So take these numbers with a pinch of salt as well.

It's an excellent first pass. It isn't a gate. And that's why I said probabilistic automation must never block a merge: turning 49% precision into a mandatory gate means handing a machine the power to stop work on the basis of a slightly loaded coin flip.

There's also an architectural limit no model improvement is going to resolve soon: **these tools review the diff, not the intent.** They see what changed. They don't know what was supposed to change, they haven't read the conversation where you settled on that approach three months ago, they don't know whether you're solving the right problem. Excellent on the "how", blind on the "why".

And two risks that worry me more than precision.

The first is **diffusion of responsibility**. The human reviewer opens the PR, sees twenty bot comments all resolved, and approves. The review formally exists. Substantially it has evaporated. The bot hasn't replaced human judgement: it has provided credible cover for its absence. In an environment of the first kind - where review is already a defensive formality - this accelerates the decay instead of slowing it.

The second is the **volume paradox**. AI writes more code, so more PRs get opened, so the human review queue gets worse. A non-trivial share of the value of AI reviewers consists of cleaning up a jam that AI itself created. Presenting it as a net gain without saying so is dishonest.

---

## The relief this can give, and the relief it can't

Let me go back to the first environment, the bad one, because that's where all of this matters most - and it's usually stated backwards.

The line you hear is: in a toxic environment automation doesn't help, fix the culture first. I've almost always heard it from people who weren't working in a toxic environment at the time. Whoever is inside one doesn't have the lever to change it, or doesn't have it on the timescale of the next PR they have to ship. And in the meantime, shift-left and AI review do three very concrete things.

**They shrink the surface exposed to judgement.** Every problem you find on your own, before opening the PR, is a problem nobody gets to write anything about. That's not cowardice: it's reducing the number of handholds in a place where handholds get used.

**They depersonalise the feedback.** "The formatter reorders imports" isn't something a person says to you, it's a rule. Opinions about style and naming are the preferred ammunition of low-grade hostility, because they're infinite, hard to argue with, and dressed up as technical correctness. Taking them out of people's hands and giving them to a tool disarms without anyone having to be accused.

**They give you a zero-cost interlocutor.** In a team where asking for help costs reputation, an AI reviewer is someone you can ask the stupid question at eleven at night without it ending up in anybody's memory. It's the most underrated of the three, and possibly the most valuable: it gives you back the right not to know.

These are real benefits, and anyone working in a place like that feels them on day one. But they should be called by their name: this is an analgesic, not a treatment. And analgesics have a well-known side effect, which is that they let you walk on a broken leg. An environment where reviews hurt enough to force somebody to intervene, made tolerable by automation, stops generating the pressure that was going to change it. The noise goes away, the problem stays - and it stays quieter than before.

---

## What's left for humans

The comfortable answer would be that what's left for humans is writing the rules to feed the AI. It doesn't hold up: writing rules is a tiny fraction of the value, and if the main outcome of review is transferring knowledge and building shared awareness, reducing people to maintainers of the bot's config file throws away exactly the part that counts.

But the opposite answer doesn't hold up either - the one where context, mentoring and alignment are inherently un-automatable. A good deal of that work is already delegable, and will be more so over time.

**Documentable context** - why that module looks like that, which decision established it, which incident produced it, who touched it last - is something a model with access to the repository, the tickets and the docs reconstructs better than a colleague, because it doesn't forget and it wasn't on holiday that week. What stays out of reach is tacit context: that workaround exists because a big customer threatened to leave, that library is untouchable because the team maintaining it is about to be dissolved. It's written nowhere, and often it isn't writable.

**Mentoring** isn't information transfer - if it were, books would have replaced it long ago. A junior corrected by a bot learns the rule; corrected by a senior, they learn the rule plus the judgement about when to break it, and on top of that they acquire a person with a stake in their growth. Nobody wants to look good in front of a tool. The push to improve, in a human review, comes from the fact that on the other side there's someone whose judgement you care about. And it runs both ways: explaining something changes the person explaining it too.

**Design alignment** isn't an analysis problem, it's a decision problem. Enumerating the trade-offs between two architectures is exactly the kind of work a model is good at, often better than the average person in a PR. But alignment doesn't mean the analysis exists: it means a group has committed to a direction and will carry the consequences. A bot can produce the perfect ADR; it can't produce the buy-in for that ADR. And whoever wasn't in the conversation isn't aligned, document or no document.

So the dividing line doesn't run between what AI can do and what it can't: that line moves every six months, and anyone who builds an argument on it watches it age fast. **It runs between what somebody is answerable for and what nobody is answerable for.** Human review doesn't survive because the machine is incapable. It survives because at three in the morning there's a person on call, and that person needs to have understood the system *beforehand*.

Which brings us back to the start. **Automation doesn't solve a cultural problem, it reduces its surface area.** It takes everything decidable off the table; what's left is legitimate conflict, about design and about the domain, and that has to be discussed, not optimised away. A team that uses review as an instrument of power will use the bot as an instrument of power too: "the AI found twelve problems in your PR" is a sentence that in certain rooms hurts more than any human comment, and it comes with the false authority of mechanical objectivity.

Automation buys time, removes noise and - as I said - provides real relief even where the environment is bad. Those are measurable gains and I'll defend them. But relief isn't recovery: it buys neither psychological safety nor accountability, and anyone selling it to you as either is selling you an excellent first pass at the price of a senior colleague.
