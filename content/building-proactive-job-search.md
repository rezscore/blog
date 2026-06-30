---
title: "Building Proactive Job Search: From 43 Chat Messages to Real Matched Roles"
slug: building-proactive-job-search
date: 2026-06-30
author: "RezScore"
tags: [engineering, job-search, ai, product, behind-the-scenes]
section: dev
draft: false
description: "How we shipped Proactive Job Search at RezScore: the demand signal that justified it, the architecture that reused our resume-tailoring engine, and the three real-path bugs that only showed up when a human actually clicked the button."
---

Most features start with a hunch. This one started with a number.

For months, people kept typing the same thing into Jen, our AI career advisor: "find me jobs." Not "tailor my resume to this posting I found," which we already do for free, but "go find the openings for me." We counted: 43 messages with that intent, 81 percent of them from the US. Five out of five of our discovery sessions for job matching showed real buy-signal language. That was the strongest pull of any product idea we had on the board.

So we built it. Here is how, and more honestly, here is everything that broke when a real person finally used it.

## The two asks hiding under "job matching"

The first design decision was realizing there are two completely different requests wearing the same words.

One is "tailor my resume to THIS job." The user already has a posting. They paste it, and we rebuild their resume to fit. That has shipped for a while, and it stays free.

The other is "FIND me jobs." The user has no posting. They want us to surface real openings that fit their background. We had no job inventory at all, so this was the net-new thing to build, and it became the paid feature.

Splitting the request this way mapped cleanly onto tiers, which mattered for the next decision.

## Reusing the engine instead of rebuilding it

The expensive, hard part of job matching is not finding jobs. It is rewriting a resume to fit each one without lying. We already had that: a service that takes a structured resume and a job description and re-angles the real experience toward the role, with hard guardrails against inventing skills the candidate never listed.

So the new feature is mostly plumbing around an engine that already works. Surface real openings, then for any one the user likes, feed that posting into the same tailoring engine we trust. The anti-fabrication discipline carries over for free, because the only thing that changed is where the job description comes from: a real listing instead of a paste.

## Real roles only, from a real source

The single rule we will not break is that every job we show is real. We pull from a live jobs API, and every listing carries a working link to the actual posting. If the search comes back empty, we say so. We never invent a company or a role to fill space.

We enforced that in code, not just in good intentions. Any result that comes back without a real link gets dropped before it can ever reach a user. An empty result is a first-class, honest state, not something to paper over.

## Pricing by cost shape

Job search has a recurring cost. Every search is an API call, and every tailored resume is an AI call. That shaped the pricing: it is a subscription, not a one-time purchase, because a one-time buyer who searches every day would drain shared credits forever. The per-tier daily limits we already had became the natural throttle.

We also resisted the urge to invent new pricing tiers. A subscription already maps one person to one tier, so we added the feature as flags on the tiers we already had rather than minting a parallel ladder. New product, same spine.

## And then a human clicked the button

Everything above passed its tests. The engine returned real jobs in our checks. We were confident.

Then we ran a real resume through the real flow, and it returned nothing.

The resume belonged to an "Economics Data Analyst." Our code took that title and searched the jobs API for the exact phrase "Economics Data Analyst." The API treats a multi-word query as a strict phrase, and no posting on earth has that precise title, so it matched zero jobs. The user saw "I did not find a strong match," which was technically honest and completely useless, because the same person searching "data analyst" would have seen plenty.

That is the difference between a green test suite and a working feature. Our test used a clean two-word query. The real resume did not.

The fix was to stop treating a job title like a search phrase and start treating it like keywords. We loosen the query: drop qualifier words like "Senior" and "Economics," keep the core role, and if the first attempt comes back empty, automatically retry with progressively broader terms before giving up. "Data Analyst" returns five real jobs where "Economics Data Analyst" returned none.

There was a second, quieter version of the same bug. The skill keywords we extract from some resumes came back as blank whitespace that still passed a naive "is this empty" check, producing a garbage query. We now clean those out too.

## The bugs you only see in the real chair

While we were in there, the smoke test surfaced two more issues that had nothing to do with job search and everything to do with shipping software that real people touch.

Jen's avatar flickered. The first greeting showed one face, the next two showed a different one. The cause was two pieces of code resolving the same setting independently, in slightly different ways, so they occasionally disagreed inside a single session. We made them agree, and we locked the avatar to whatever the first message showed so it can never change mid-conversation.

And the resume builder sometimes got stuck saying "Sit tight, we're drafting killer bullets" with the bullets never arriving. The work that fills in those bullets runs in the background, and if the server process recycled at the wrong moment, that work could die silently and leave the page waiting forever. We added a self-heal: if the page asks for a builder that is still "drafting" but has no content and we have the source resume, we finish the job right then instead of waiting on a thread that may already be gone. We also fixed a polling link that pointed at a URL that did not exist, which had been quietly failing the whole time.

## What this taught us, again

None of these bugs showed up in unit tests. All of them showed up the instant a human ran the actual path with a real resume. The lesson is not "write more tests," though we did add regression tests for every one of these. The lesson is that "the tests pass" and "the feature works" are different claims, and only the second one matters to the person clicking the button.

Proactive Job Search is live in beta. If you try it and it does something strange, that is genuinely useful to us. The last few bugs were found exactly that way.
