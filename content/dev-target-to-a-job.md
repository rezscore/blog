---
title: "Target to a Job: watch your resume rebuild itself"
slug: dev-target-to-a-job
date: 2026-06-16
author: "RezScore"
tags: [dev, product]
section: dev
description: "We shipped Target to a Job, where you name a role and watch the builder rebuild your resume to fit it."
draft: false
---

We just shipped a feature we are genuinely excited about: **Target to a Job**.

The idea is simple. You have a resume. You have a job you actually want. Those two things should not live in separate worlds, and tailoring should not mean an hour of copy, paste, and second-guessing. So now you point the builder at a target, and it does the re-angling for you while you watch.

## What it does

You give it a target two ways:

1. **Name a role.** Something like "Senior PM at a fintech" or "County Auditor" is enough.
2. **Paste a job description.** If you have the full posting, drop it in.

From there, the builder rebuilds your resume aimed at that role and saves it as its own **named variant**. Your original stays untouched. You end up with a clean, labeled version targeted at the job, sitting right next to everything else you have built.

## The part we cared about most

We could have shown you a spinner and then a finished document. We did not want that. The good part is not the result alone, it is seeing your resume reshape itself toward the job in front of you.

So when targeting kicks off, the builder starts to **build itself**. The resume morphs into its new shape while short notes narrate what is happening as the work runs. It reads less like a loading screen and more like watching an editor reorder your experience to make the case for one specific role. By the time the motion settles, the targeted version is ready.

Getting that to feel instant took some work. We wanted the page to open right away and start the animation immediately, then do the heavier rewriting in the background and slide you into the finished resume when it lands. The reward should be the rebuild itself, not a wait.

## One honest rule

The resume re-angles toward the job. It does not invent things about you. Target to a Job reorders, reframes, and emphasizes what is already true in your experience so it lines up with what the role asks for. It will not hand you skills you never claimed or numbers you never earned. Tailoring is about pointing your real story at the right target, not making up a new one.

## Where to find it

You can launch it from your grade page and from your dashboard, where every resume card now has a target button. Name a role or paste a posting, and watch it build.

We are shipping in public and learning as we go. Try it, point it at a job you actually want, and tell us where it helps and where it falls short.
