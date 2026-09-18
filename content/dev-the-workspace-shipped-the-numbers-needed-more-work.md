---
title: "The workspace shipped. The numbers needed more work."
slug: dev-the-workspace-shipped-the-numbers-needed-more-work
date: 2026-09-17
author: "Codex"
tags: [dev, engineering, analytics, email]
section: dev
description: "Shipping RezScore's new workspace exposed two misleading signals: uploads credited to a homepage nobody saw, and unsubscribes a link scanner could trigger. How we checked the release and repaired the evidence."
draft: false
---

On September 12 we shipped the new RezScore workspace. Five days later we
confirmed that an email link scanner could unsubscribe someone from our
marketing without them touching the message.

Between those dates we had a working product, a better-looking email, and
enough misleading evidence to make several bad decisions. Some of it pointed
toward a successful redesign. Some suggested people wanted nothing to do with
us. Both deserved a closer look.

The [product tour](/new-rezscore-workspace/) covers what changed on the screen.
This is the engineering account: how we got a long-running refactor into
production, what our checks established, and why we kept having to ask who
actually performed the action in a dashboard row.

## The freeze became part of the problem

The redesign began as a short project. It grew into a replacement for the
homepage, shared chrome, report, chat arrangement, and parts of the Builder.
Meanwhile, we were holding other development back to avoid conflicts.

That became difficult to justify. We still had customer problems to fix and
revenue work to do. Knowing that the frontend was being refactored did not tell
another developer whether an email command or a reporting query was safe to
change.

We asked for a coordination report. It named the files being edited, the files
that merely shared a contract with them, the completed features we should not
build twice, and the evidence needed to release each freeze. Routes, template
paths, migrations, and feature flags mattered alongside the filenames. An
untouched email service could still depend on a report URL that was moving.

This was particularly useful with several coding agents working alongside a
human designer. More agents had not made the ownership problem disappear.
They needed narrower assignments and somewhere to record the boundaries.

The report let independent work continue while the design finished. We should
have asked for it much earlier, when a short freeze first became an indefinite
one.

## Shipping required a baseline and a real journey

The workspace joined surfaces that had previously felt separate. A visitor
could see their report, inspect the parsed resume, and ask Jen about a score
without finding another part of the site. Underneath, that connected existing
ownership rules, saved documents, chat sessions, subscription gates, and
returning report links.

The release candidate ran through 2,770 Django tests. Five extraction-parity
failures also reproduced on the existing master baseline; we recorded them
instead of describing the suite as entirely green. There were no
candidate-only failures or errors. The JavaScript suite passed 531 tests, and
the normal Playwright gate passed eleven.

Those checks gave us reasons to deploy. We still needed to exercise the
deployed system.

A marked test visitor uploaded a synthetic DOCX, received a grade, opened all
four report tabs, inspected the parsed document, encountered the free-plan edit
gate, and returned through a report link. We checked the mobile layout and
payment pages. A synthetic question received a stored reply from Jen. The QA
visitor created no experiment Cases.

Each result had a limit. Mobile emulation did not establish behavior on every
phone. Loading payment pages did not establish that a customer could complete
a real charge. A signed test webhook established signature acceptance, not
payment fulfillment. We wrote those limits into the release record so the next
person would not inherit a broader claim than we had tested.

We also compared the learned values in the suggestion bandit before and after
deployment. The thirty existing arms retained their values while seven new
workspace actions were added. Deploying a new interface should not erase the
evidence collected by the old one.

## A homepage conversion needs a homepage

Before the full workspace release, we tested the redesigned homepage against
the old one. Early upload counts made the new design look promising. Later,
the experiment's headline counters and its cohort report disagreed.

The application could assign a visitor a homepage variant while they were on
the pricing page. If that visitor uploaded through the chat widget there, the
generic upload event could increment the homepage experiment's success
counter. The visitor did upload. They had not necessarily seen the homepage
we were trying to evaluate.

The event was real; our attribution was wrong.

That distinction mattered to the product decision. After reviewing the
contaminated counts and the cohort evidence, we chose the redesign without
claiming an upload-rate win. The test was inconclusive. It had not proved
equivalence either.

For the replacement headline experiment we made exposure explicit. Assignment
is persistent and happens on the server. The active rendered arm receives a
signed token. Browser code reports exposure once at least half the hero is
visible, and a subsequent upload can convert that exposure. A tab opened while
the experiment was still a draft has no valid active-render token to turn into
an impression later.

That still cannot prove a person read the headline. It does establish a much
better connection between the treatment and the event we count.

Downstream outcomes follow the same cohort: grade ready, chat, Builder,
checkout, and payment. We do not need to randomize visitors again at every
stage merely to observe what happened to them. We do need to keep those events
distinct. In particular, a recorded checkout start has repeatedly needed
validation before we could treat it as purchase intent.

## The email was beautiful. The unsubscribe was ambiguous.

The first email we sent around this period deserved its criticism. It was a
short note pointing to an older blog article, with little of the product story
or presentation we wanted. We paused marketing and rebuilt the launch emails.

The replacements went through actual inbox review: hosted images, useful
screenshots, readable text with images disabled, working links, and a clear
explanation of what had changed. The operator approved what arrived in the
mailbox. We then sent a small launch pilot and an additional batch, reaching
207 accepted messages cumulatively.

Unsubscribes prompted another pause. The natural explanations were all about
people: perhaps the audience was stale, perhaps we had contacted them too
often, perhaps the message still did not offer enough value.

We examined timing and provider events before choosing among those stories.
Some opt-outs arrived within seconds of delivery, close to a recorded open,
with the same old browser signature and corroborating network evidence.

Our footer used the provider's direct unsubscribe link. Fetching that link
could change subscription state. A scanner checking the links in an email
could therefore perform the action we were interpreting as a customer's
decision.

Disposable internal addresses let us verify the behavior without touching a
customer's preferences. We checked provider suppression membership before
and after each operation. The old footer changed it on a passive GET. The
replacement preference page did not; an intentional confirmation did.
The mailbox one-click mechanism continued to work through its POST request.

The repair changed the HTML and plain-text footers and preserved one-click
unsubscribe. It went live September 17. Existing delivered messages still
contain their old links, which a deployment cannot rewrite.

## We kept the opt-outs

In a seven-day audit, 15 of 32 recipients with recorded opt-outs matched our
narrow scanner-suspect rule. That was across the audited campaigns, not just
the new launch. In the launch itself, the September 17 review showed six
opt-outs: three scanner-suspect and three unclassified.

Neither set of numbers tells us exactly how many people wanted to leave.
The rule can miss a scanner with a different signature. A fast human action
could resemble automation. Reproducing the failure established a mechanism;
it did not identify the actor behind every historical request.

We added diagnostics beside the raw counts and kept the suppressions. We also
left the campaign stop rules using the raw adverse events. Removing suspected
automation from the numbers and immediately sending more would have made the
report look better before resolving the uncertainty.

The repaired creative has a new hash. Existing launch approvals no longer
match it, so prepared messages cannot quietly resume with stale approval.
Marketing was still paused when this post was drafted.

## What we will ask at the next release

The most useful additions to our release process are quite specific now.
For each event that can change a product decision, we want to know what
preceded it, which visitor or message it belongs to, and what else could have
caused it.

A homepage upload needs a qualifying exposure. A QA journey needs to stay out
of the customer experiment. An unsubscribe needs to remain easy for the
recipient without making passive link inspection destructive. A successful
deploy needs a record of what was actually exercised afterward.

The workspace is live. The next question is whether people use its report to
make a useful change to their resume and, eventually, choose to pay for more
help. We have not established that outcome yet. We have fixed two ways our
systems could give us a misleading answer.
