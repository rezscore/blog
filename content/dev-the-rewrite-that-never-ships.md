---
title: "The rewrite that never ships"
slug: dev-the-rewrite-that-never-ships
date: 2026-08-12
author: "Claude Fable"
tags: [dev, ai, builder, trust, product]
section: dev
description: "The hardest Builder feature we shipped this summer is machinery for refusing to show you AI output. A dev log on grounding checks, withheld rewrites, and why an AI resume tool needs a working no."
draft: false
---

During a staff smoke test this summer, we asked Jen, our chat assistant, for a rewrite of a resume line. Jen produced one. Nobody ever saw it. Before the proposal could render, a grounding check asked one question: does the original text this rewrite claims to improve exactly match something in this person's Builder right now? It did not. So the rewrite was withheld, a content-free telemetry event fired (`chat_bridge_rewrite_not_in_builder`, we count refusals without storing anyone's text), and Jen went on with the conversation.

That refusal took most of the summer to build, and it is the most important thing the Builder does right now.

## Why build a machine that says no

In July we wrote about [why AI resume builders make things up](/why-ai-resume-builders-make-things-up/). The short version: a language model asked to improve a resume is really being asked to make it more impressive, and the cheapest route to impressive is invention. Nothing since has made that less true, and 2026 keeps raising the stakes. Greenhouse's CEO [told Fortune in July](https://fortune.com/2026/07/27/greenhouse-ceo-daniel-chait-ai-doom-loop-job-seekers-spam-interview-applications-unemployment/) that postings on their platform now average roughly 250 applicants, with applications per recruiter up more than 400 percent since 2022. Employers have started [bringing interviews back in person](https://www.computerworld.com/article/4044734/to-counter-ai-cheating-companies-bring-back-in-person-job-interviews.html) specifically because they can no longer trust what arrives in writing. The market is drowning in confident documents, and confidence is exactly what a fabricated bullet has most of.

An invented credential does not fail at the applicant tracking system. It fails in the interview, in the reference call, in the first month on the job. And it fails hardest for the person with the least margin: someone rebuilding after a layoff, or finishing a training program, who finally got the one interview and now has to defend a bullet point they did not write and cannot back up.

## Honesty is a pipeline property

Last month we wrote that [a preview is a database constraint, not a modal](/dev/dev-preview-is-a-database-constraint-not-a-modal/). This is the same lesson one layer up. A prompt instruction like "never invent facts" is a suggestion the model usually follows, and "usually" is a terrible word to build a trust product on. So in the evidence-first Builder work now deployed behind test flags, the honesty lives in the request pipeline, where the model cannot talk its way past it:

- Every proposed rewrite must quote the original text it claims to improve.
- That quote must exactly identify one current value in the user's Builder. Close does not count. Paraphrase does not count. No match, no proposal.
- A generated summary, profile, or objective that asserts anything beyond the fields the user actually filled in is suppressed before a preview renders.
- Every applied change goes through preview, explicit acceptance, and exact undo. The user stays the author.
- Every refusal emits a content-free event, so we can measure how often the model tries to overreach without logging a word of anyone's resume.

The model can be as creative as it likes inside those walls. Outside them, its output simply never reaches the screen.

## What it costs, honestly

None of this is public yet. The rollout flag sits at zero percent, organic visitors cannot enroll, and only marked staff accounts can touch it. There are two unglamorous reasons.

First, the system currently has two sources of truth about your resume: the raw document you discussed in chat and the independently parsed structure in the Builder. They can disagree. An exact-match grounding rule pointed at the wrong source of truth would misfire, rejecting honest rewrites and passing stale ones, and a withholding mechanism that is itself wrong is worse than none. Aligning those sources comes before any wider release.

Second, Jen still drifts toward summary prose we have not approved, the kind that opens with polished generalities instead of scanning as evidence. Her prompt only changes through measured experiments here, so that fix waits its turn in the queue rather than getting hot-patched on a hunch.

We have accepted an uncomfortable ordering: the "no" path ships before the "yes" path. Refusal machinery first, features second. It makes for slow-looking release notes and a much better product.

## Who this is actually for

About 72,000 resumes have been graded on the modern RezScore stack, and the people behind them are mostly not confident optimizers gaming a system. Many are people in transition: laid off after years of stability, retraining into a new field, re-entering after a gap. The organizations that help them, career centers, workforce programs, training providers, outplacement counselors, stake their credibility on one thing being true: that the documents leaving their offices describe real people accurately. An AI assistant that helps someone state their actual experience more clearly, and is built so that it cannot quietly help them invent, is the only kind that population can afford to use.

We should be precise about the claim we are making, because this industry usually is not. A well-grounded resume does not get anyone hired, and we do not say it does. Hiring depends on the market, the opening, the interview, and a dozen things no document controls. What a grounded resume does is survive the conversation it starts.

The next milestones are as unglamorous as the last ones: align the two sources of truth so the grounding check cannot misfire, then run the measured prompt experiment on summary drift. When those land, the rollout number moves off zero. Until then, the most valuable thing our Builder produces is the rewrite you never see.
