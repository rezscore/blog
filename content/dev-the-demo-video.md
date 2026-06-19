---
title: "How we made our demo video (and let a script remake it)"
slug: dev-the-demo-video
date: 2026-06-19
author: "RezScore"
tags: [dev, product]
section: dev
description: "We built a product walkthrough video the unusual way: a script drives the real app, records the genuine flow, and rebuilds the cuts. So when the product changes, the video changes with it."
video_url: /static/video/grader-walkthrough-tiktok.mp4
video_poster: /static/video/grader-walkthrough-poster.jpg
faqs:
  - q: "Is this a real recording of the product?"
    a: "Yes. It is not a mockup or an after-effects render. A script drives the actual RezScore app in a real browser, uploads a resume, waits for the real grade, and records what happens. What you see is the product."
  - q: "Why build the video with a script instead of just recording it once?"
    a: "Because the product keeps changing. A hand-recorded video is stale the moment you ship the next improvement. Ours regenerates from one command, so the video can stay current as the product gets better."
  - q: "Who is Jen Doe?"
    a: "A fictional demo candidate, a senior product manager. We use a synthetic resume so no real person's data ever appears in the video. Jen, the advisor in the chat, is our AI career advisor."
draft: false
---

Most product demo videos are made once, by hand, and then slowly go out of date. The product ships a new feature, the video still shows the old one, and nobody wants to re-record the whole thing. So it rots.

We did not want a video that rots. So we built ours a slightly unusual way.

## The video is generated, not filmed

There is one command. It starts the app, opens a real browser, and walks through the genuine free-grader flow start to finish: upload a resume, get an instant A to F grade with the category breakdown, ask Jen a real question and get a real answer, see a target-salary estimate, paste in a job and watch the resume rebuild itself to fit it, then land on the Skills Explorer.

Nothing in that is staged. It is the actual product responding in real time, including the real AI. When the run finishes, the same pipeline trims the dead spots and produces two cuts: a short one for social, and a longer walkthrough for anyone who wants the full tour. The short one is above.

## The part we cared about most

We wanted the video to always match the live product. The trick is that the recording script and the app are the same thing. The script does not approximate the flow, it *is* a user going through the flow. So when we improve the grader, or change how Jen opens, or speed up the rebuild, we run the command again and the new video shows the new product. No re-shoot, no editor, no drift.

It even tunes its own edits. The recording drops markers as it passes each step, and the cutting stage reads those markers to decide where to trim. If a step gets faster or slower, the cuts re-aim themselves. The video keeps its shape even as the product underneath it moves.

## What recording it taught us

Watching the real product play back, uninterrupted, is a brutal and useful design review. Things you click past every day become obvious on screen. In one pass we caught a headline rendering white on a white background, an upload box that collapsed into a thin line mid-upload, body text too small to read, and the advisor introducing herself by the wrong name. None of those survive a 60-second video. All of them were quietly live before we filmed.

So the video did double duty: it shows the product, and making it made the product better.

## One honest note

This is an early cut. It is a clean screen recording with deliberate pacing, not a polished, narrated, professionally edited piece. We shipped it because showing the real thing, today, beats waiting for perfect. If it earns its keep, a real editor gets a turn. Until then, the script keeps it honest and current.

Want to see your own grade instead of ours?

[Grade my resume - free](https://ai.rezscore.com)
