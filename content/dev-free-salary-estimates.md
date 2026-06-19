---
title: "Free salary estimates, and a loop that makes them smarter"
slug: dev-free-salary-estimates
date: 2026-06-16
author: "RezScore"
tags: [dev, salary]
section: dev
description: "We added a free salary range to the report card, and built a correction loop so it gets more accurate as people use it."
draft: false
---

Your resume already carries a lot of signal about what you are worth: your
title, how senior you are, how many years you have, your industry, where you
work. We added a small feature to the report card that reads those signals and
gives you a likely salary range. It is free, and it shows up right next to your
grade.

## Estimating from resume signal

The estimate does not read your whole resume as a wall of text. It works from a
short, structured summary of the things that actually move pay: seniority, years
of experience, industry, and location. From those, a simple and transparent
engine produces a range. We made the range deliberately wide. A salary estimate
that pretends to know your exact number to the dollar is lying to you. A
believable range is more useful, and more honest.

This is a rebuilt version of an idea we ran years ago. The old one was a small
classifier that got somewhere around eighty percent of the way there. The bar to
beat is not high, and a free engine clears it comfortably.

## A teacher that nudges it

On top of the free engine, we run a second pass that reviews each estimate and
suggests a correction when the first number looks off. Think of it as a teacher
checking the work. When the two agree, we are confident. When they disagree, that
disagreement is information we keep. The free engine stays the product, the
review just sharpens it.

## The part that makes it smarter: your corrections

Here is the loop. Under the estimate is a slider. If our range is wrong, you can
drag it to where you think it should be and tell us. We store every one of those
corrections.

One thing we were careful about: when you move the slider, you are telling us
your target, the number you are aiming for, not necessarily what you make today.
We record it as exactly that. Mixing up "what I want" with "what I have" would
quietly poison the data, so we keep those two ideas separate from the start.

Every honest correction is a real-world label. Over time, a pile of those labels
lets the estimate calibrate against what people actually report, instead of
guessing in a vacuum. That is the flywheel: the more people use it, the better
it gets, and it improves without us having to hand-tune anything.

## A small confession

Building the thing was the easy part. Making it actually appear on the page took
some debugging. The widget was quietly tied to an unrelated piece of the report
card, so it loaded only some of the time and looked broken the rest. We gave it
its own trigger, and now it shows up every time. A good reminder that a new
feature should stand on its own and not borrow another feature's plumbing.

The estimate is live on the report card now. Try it, and if we get your number
wrong, tell us. That is the whole point.
