---
title: "We ran six homepage A/B tests. Almost nothing moved."
slug: dev-six-homepage-ab-tests-almost-nothing-moved
date: 2026-08-29
author: "RezScore"
tags: [dev, ab-testing, product, analytics]
section: dev
description: "We tested the upload button, drop zone, social proof, value copy, video, and music on the RezScore homepage. None produced a reliable lift. Here is what the null results changed."
draft: false
---

For five months, we changed nearly everything around the upload button on the
RezScore homepage.

We changed the words on the button. We changed the shape of the drop zone. We
added social proof, benefit copy, a speed claim, two cuts of a product video,
and three classical soundtracks.

Every experiment asked the same question: did the visitor upload a resume?

The answer, across all six tests, was the same. None produced a reliable lift.

## The results we are closing

These are the lifetime upload rates in each arm. The smallest video arms saw
roughly 27,000 recorded impressions each. The largest homepage arms saw roughly
65,000.

| Test | Upload rate by arm | Overall result |
| --- | --- | --- |
| Button copy | 2.68%, 2.60%, 2.73% | p = 0.47 |
| Circular or rectangular drop zone | 2.74%, 2.60% | p = 0.13 |
| No social proof or animated resume count | 2.65%, 2.69% | p = 0.69 |
| No supporting copy, benefit bullets, or speed claim | 2.56%, 2.68%, 2.78% | p = 0.12 |
| No video, short video, or long video | 1.76%, 1.69%, 1.75% | p = 0.78 |
| Three soundtrack options | 1.75%, 1.74%, 1.69% | p = 0.84, but not interpretable |

The table does not prove that every option is identical. It says that the
differences we observed are consistent with noise at the sample sizes we have.
That is enough to reject the large wins we hoped to find. It is not proof that
a tiny effect does not exist.

At a 2.7% upload rate, detecting a 5% relative lift with 80% power and a
two-sided 5% significance threshold would require roughly 232,000 visitors per
arm. Even an 8% relative lift needs roughly 92,000 per arm. We could keep all
six tests running for months to chase smaller differences. That would be a poor
use of the homepage and an even worse use of our attention.

## The one result that almost tempted us

The speed-focused value copy finished at 2.78%, compared with 2.56% for no
supporting copy. Looking only at that pair gives an unadjusted p-value just
under 0.05.

It would make a satisfying winner. It would also be a post-hoc choice from a
three-arm test whose overall p-value was 0.12, selected after we had been
watching several homepage experiments for months. The more comparisons you
inspect, the more likely one of them is to cross 0.05 by chance.

We may still keep the speed copy. It fits the product and performed best. That
would be a design decision informed by the data, not a statistically proven
win. Labeling it a winner would give the next person reading the experiment
history more certainty than we earned.

## One of the tests did not really test its premise

The music result deserves a larger warning than the table can hold.

The homepage video autoplays muted. Visitors have to scroll to it and then
choose to turn on sound. Some visitors in the music experiment were also in the
no-video arm of the separate video experiment, which means there was no music
for them to hear at all.

The dashboard can compare upload rates among the three assigned soundtracks,
but most assigned visitors never experienced the thing being compared. A flat
result here is not evidence that people have no musical preference. It is
evidence that the homepage was the wrong surface for answering that question.

We are closing it instead of asking more traffic to repair a test whose exposure
was weak by design.

## Six tests at one gate was too many

The larger mistake was the shape of the batch.

All six experiments began at the homepage and converted on the same upload
event. Because their assignments were independent, one visitor could receive a
button variant, a drop-zone variant, a social-proof variant, a value-copy
variant, a video variant, and a music variant at once. Across the six tests,
that produced 324 possible homepage combinations.

Random assignment still lets us estimate the average effect of each change.
But it makes interactions difficult to read and turns one important moment into
a crowded laboratory. A better button may be paired with weaker copy. A useful
video may appear below a layout that already persuaded someone to upload. Most
combinations will never receive enough traffic to inspect on their own.

It also creates an organizational trap. Six experiments look like six active
questions, so none feels disposable. They keep collecting traffic while the
team waits for a clean winner that the effect size and sample size may never
produce.

## What the null result changed

We are not taking the result to mean that homepage design is irrelevant. We are
taking it to mean that none of these local changes fixed the main constraint.

The upload control was already understandable. People who arrived ready to
grade a resume could find it. Changing "Choose file" to "Browse & Grade" did
not create more intent. Making the target rectangular did not create more
intent. An animated count did not create more intent. Video did not create more
intent.

That changes where we spend the next batch of experiments.

The relaunch will measure the moments after the doorway: whether someone sees
their grade, receives a useful first recommendation, applies it, makes an edit,
and eventually starts checkout. Each gate gets one primary question and an
event that describes something the person actually experienced.

These six results cleared the homepage queue. The next batch can put one test at
each meaningful gate instead of asking the upload button to answer six questions
at once. The next hard question begins after the upload.
