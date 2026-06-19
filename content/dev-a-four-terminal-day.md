---
title: "A four-terminal day"
slug: dev-a-four-terminal-day
date: 2026-06-19
author: "RezScore"
tags: [dev, meta]
section: dev
description: "What shipped on a day we had four work sessions running at once, and the small bugs that taught us something."
draft: false
---

Today we had four work sessions running in parallel. Different corners of the
product, different problems, all moving at the same time. By the end of it we had
pushed a couple dozen changes. It felt worth writing one of them down, especially
since one of the four sessions was the one that built this very dev log, and none
of us thought to actually post to it. So here we are.

## What shipped

A rough tour, because the variety is the point:

- **This blog got a redesign.** A featured post on top, the rest in a clean
  chronological list, tags moved off the cards and into a footer, and a hidden
  dev section (the one you are reading) for building in public. The whole blog is
  now mirrored to a public repo, so the source of every post is open.
- **The email side learned to be more careful.** We stopped greeting people by
  names guessed from their email handle, tightened up our suppression hygiene so
  we do not re-mail addresses that have bounced, and added a fresh subject-line
  test to a campaign going out this week.
- **The product got faster.** We added latency tracking so we can actually see
  where time goes on each request, concluded an experiment about which model
  powers a small feature, and started two more tests aimed squarely at speed.
- **The report card got a steadier salary widget.** It now loads cleanly instead
  of jumping around mid-estimate, and it gives the number room to signal when a
  resume points well above the usual range.

No single one of these is a headline. Together they are a normal day.

## The small bugs that taught us something

The interesting part of a day like this is rarely the features. It is the bugs
that hide in plain sight.

**A comment that would not stay a comment.** One template had a few multi-line
comments in it. They looked fine. They were not fine. The templating language we
use only treats a comment as a comment if it stays on one line. Span two lines
and the whole thing renders as visible text on the page. So a note meant for the
next developer was showing up in the middle of a published post. The fix was
trivial once we knew. Finding it took a screenshot from someone going "uh, what
is this."

**A button with invisible text.** We restyled a call-to-action button and it
came out as a solid shape with no words on it, just an arrow floating in the
middle. Nobody had broken the text. The cause was a styling rule elsewhere that
colored all links the same shade as the new button background. The text was
there the whole time, the same color as what was behind it. A good reminder that
"it disappeared" and "it is the wrong color" can look identical.

## Why write this down

Because the small stuff is the real story of building something. The features
get announced. The two-line fix that took an hour to find does not, unless you
make a place for it. We made a place. This is the first post in it about an
ordinary day, and there will be more.
