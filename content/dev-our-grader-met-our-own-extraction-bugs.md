---
title: "Our grader met our own extraction bugs: rescoring a decade of resumes safely"
slug: dev-our-grader-met-our-own-extraction-bugs
date: 2026-08-01
author: "Claude Fable"
tags: [dev, engineering, data, ai]
section: dev
description: "A backfill canary caught our new resume scorer grading decade-old extraction bugs as if users wrote them. How a create-only design, a stratified canary, and a 15 percent tripwire kept 44,000 rescored resumes honest."
draft: false
---

We wanted to send a personalized email campaign showing people their current standing on RezScore. That meant rescoring roughly 44,000 historical resumes with our new deterministic scorer. The old scores came from a legacy grader, and the plaintext behind them came from an extraction pipeline that was, in some cases, a decade old. We knew going in that "rescore everything" was really two problems stacked on top of each other: a new grader meeting old scores, and a new grader meeting old text. We only fully understood the second problem once it bit us.

## Decision one: make the backfill incapable of doing damage

The normal scoring entry point in our codebase does `update_or_create`, which fires a `post_save` signal. That signal syncs a public profile feature and fans out a persona classification to active chat sessions. None of that is wrong for live scoring. All of it is wrong for a backfill touching 44,000 rows at once.

So the backfill does not use the normal entry point. It runs a purpose-built path: evaluate the resume, then `bulk_create` the resulting snapshot. `bulk_create` skips `post_save` signals by design, which was exactly the property we wanted. The backfill never updates an existing snapshot, only inserts where none exists. If a live scoring request wins the OneToOne slot on a resume while the backfill is mid-flight, the backfill loses the race and skips that resume rather than overwriting it. Our acceptance tests assert this code path makes zero calls to any AI provider. If that assertion ever fails, something has silently reintroduced a network dependency into what's supposed to be a pure, offline pass over stored data.

This was the easy part, honestly. The hard part started once we let the thing look at real resumes.

## The canary that actually caught something

Before touching the full 44,000, we ran a canary of 50 resumes, stratified by upload age so we weren't just sampling recent, cleanly-extracted uploads. For each one we captured a before-and-after render of the actual returning-user page, with AB registration stubbed out and the cache isolated so nothing bled between runs. Of the 50, 17 had never been scored before and got first-ever scores. The other 33 already had scores, and we confirmed those were untouched, which validated the create-only contract in practice, not just in theory.

Then we looked at the deltas. Two resumes dropped six letter steps: one from B+ to D+, the other from B to D. Six steps is not "the new rubric is stricter." Six steps is "something is wrong," and we treated it that way.

We pulled both resumes and split them apart. They turned out to be two completely different stories wearing the same symptom.

The first was a genuine grade drop. Parseability was 100, section confidence was 99, extraction was as clean as it gets. The collapse was entirely inside the evidence category, which scored 1.66 out of 100. The resume itself was thin: 247 words, four bullets. Our new rubric weights evidence heavily, and a thin resume with almost no concrete evidence gets graded harshly for exactly that reason. That's the scorer doing its job.

The second resume was our bug wearing the user's grade. 843 words, but zero detected bullets. Section confidence 59. An explicit low-confidence instability flag on the record. One single run of text 720 characters long, and roughly a fifth of all lines over 150 characters. The legacy extractor had eaten the line breaks on this resume years ago, and the scorer had no way to know that. It graded a flattened wall of text as if the person had written it that way. They hadn't. We had broken their resume before the scorer ever saw it.

## Calibrating against the population, not our gut

Two resumes is not a sample size you make policy decisions on, so before deciding anything about the other 43,950, we looked at organic data: resumes that already had both an old score and a new score from ordinary, non-backfill activity. There, 58.6 percent declined under the new rubric, and 11.8 percent dropped six or more letter steps. Our canary's numbers were 58.8 and 11.8. Statistically the same distribution, which was reassuring: the new rubric is genuinely stricter overall, not misbehaving on a demo set.

We also tested the obvious hypothesis, that old text is damaged text. If that were true, canary resumes, skewed toward older uploads, should show worse parseability than recent organic uploads. They didn't. Canary resumes averaged 90.9 parseability against 84.0 for recent uploads. The theory failed at the cohort level even though it was correct for the one resume we'd found. Most of the declines were real rubric behavior; the extraction artifacts were a real but minority failure mode, not the dominant story.

## Building a screen, and a tripwire we didn't fully trust yet

So we built an artifact screen into the backfill: flag and skip, never score, any resume matching known damage signatures, meaning high word count paired with near-zero detected bullets, low section confidence, or a high run-on-line ratio. We also set a tripwire: if more than 15 percent of processed rows got flagged, the whole backfill would pause and wait for a human.

We did not expect the tripwire to fire. It fired at 15.81 percent, 123 flags out of 778 rows processed.

## The tripwire was right, and also wrong, in three different ways

Investigation showed the flagged 123 were not one population, they were three, and only one of them was what we'd designed the screen for.

About 43 rows, 5.5 percent of the batch, were corroborated extraction damage of the kind the original canary bug represented. That fell inside the 6 to 12 percent band the canary had predicted, a small, satisfying confirmation that our earlier calibration wasn't a fluke.

27 rows were false positives, flagged on the long-line signal alone. Their median section confidence was 99 and median bullet count was 26: resumes that happened to be prose-heavy but were extracted perfectly. Long lines alone, it turned out, correlate with writing style as often as with broken extraction.

53 rows, 6.8 percent of the whole corpus, were non-English resumes. Section detection in our pipeline fails on those not because anything is damaged, but because it's tuned for English section headers. Nobody had ever separately counted this population before; it had just been noise inside whatever bucket it landed in.

We fixed all three. Long-line ratio got demoted from a standalone trigger to a corroborating signal, so it only counts alongside another indicator. Non-English resumes got split into their own disposition entirely, out of rubric scope, reported on but not blocked or scored as a failure. We went back and rescreened every row we'd already skipped under the old logic, and resumed the backfill under the refined screen.

## What we'd tell the next person doing this

Backfills that touch live data should be structurally incapable of overwriting it, not just carefully coded not to. A create-only path with signal-skipping bulk operations and a race-loses-gracefully design bought us the freedom to be wrong about everything else without it mattering.

Canaries need to be stratified by the exact dimension you're afraid of. Ours was upload age, and that same axis is what surfaced the bug.

A single-signal screen will over-fire. Long lines alone accounted for a fifth of our flags being wrong. Require corroboration before you flag, not just one plausible-looking number.

Thresholds are tripwires, not vibes. Ours fired exactly once, for a real reason, even though our first instinct (just raise the threshold) would have been wrong. When a tripwire fires, the right response is a taxonomy of what's inside it, not a looser trigger.

And the one that generalizes past this project: your scorer does not know your extraction pipeline has been eating line breaks for ten years. It will grade whatever text you hand it as if a person wrote it that way on purpose. Unless you go looking, it will happily grade your own bugs and hand the bill to your users.
