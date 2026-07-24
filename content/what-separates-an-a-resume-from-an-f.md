---
title: "We Have Graded 13 Million Resumes. Here Is What Separates an A From an F."
slug: what-separates-an-a-resume-from-an-f
date: 2026-07-06
updated: 2026-07-23
author: "RezScore"
tags: [data, resumes, grading]
description: "RezScore has graded more than 13 million resumes. We analyzed the newest 70,000+ of them: the distribution, the quantification gradient, and a counterintuitive finding about length."
image: /static/blog/images/what-separates-an-a-resume-from-an-f/social-card-v2.png
image_alt: "Resume quality analysis illustrated by a progression from rough to evidence-forward resumes"
image_width: 1200
image_height: 630
medium_slug: resume-grades-what-72791-resumes-reveal
draft: false
---

*Most people assume their resume is above average. The measured distribution of more than 70,000 recently graded resumes says otherwise: the median resume is a C, and only 17 percent earn an A.*

RezScore has graded more than 13 million resumes since it launched. This analysis covers the newest slice of them: over 70,000 resumes uploaded from November 2020 through July 2026, the modern corpus, where we hold full extracted text and letter grades in the current grading system and can measure everything below directly. A handful came back with an unparseable grade and are excluded; everything below is measured against the rest, aggregated and anonymized. Exact counts are in the methodology note at the end. Two thirds of the uploads are PDFs, about one in five is a Word document, and the rest is the usual scatter of formats people still keep resumes in.

Here is the full distribution.

| Grade | Resumes | Share |
|---|---|---|
| A | 12,407 | 17.1% |
| B | 21,481 | 29.6% |
| C | 22,276 | 30.7% |
| D | 10,741 | 14.8% |
| F | 5,661 | 7.8% |

The median grade is a C. More than half of all resumes land at C or below. If you assume your own resume is "probably fine," the base rate says you are more likely sitting at the median than you think.

## The quantification gradient

The single cleanest signal in the data is whether a resume contains a measurable result: a percent figure or a dollar figure, anywhere in the text. We checked, grade by grade, among resumes with extractable text.

| Grade | Has a % or $ figure |
|---|---|
| A | 62.7% |
| B | 46.2% |
| C | 32.3% |
| D | 22.2% |
| F | 13.5% |

That is not noise. It is a monotonic staircase, every single step down in grade matched by a step down in quantification, and the ends of the range are stark: A resumes are 4.6 times more likely than F resumes to contain a number that describes an outcome.

The reason isn't mysterious once you sit with it. A number on a resume is a claim someone can check. "Reduced onboarding time by 30 percent" is falsifiable in a way that "responsible for onboarding" is not, and a reader treats it accordingly. Task-only bullets describe throughput: things anyone in the role, increasingly including an AI, could plausibly have done. A measured outcome is evidence of judgment: you had to decide what to measure, track it, and report it honestly. That is a harder thing to fake and a more useful thing to know about a candidate, which is exactly why graders and hiring managers weight it so heavily.

There is also a simple attention argument underneath the accountability one. A reader skimming a resume for thirty seconds is not parsing full sentences; they are pattern-matching for anything that looks like proof. A number breaks the visual monotony of a bullet list and gives the eye somewhere to land. That is a mechanical, almost boring explanation, but it compounds with the accountability effect rather than competing with it: the number gets read first because it stands out, and it gets believed because it can be checked. Neither effect alone would produce a staircase this clean across five full letter grades. Together they do.

To be concrete about the form, not the content, here is the shape of the rewrite, invented as an illustration rather than pulled from any resume in the corpus:

- Before: "Responsible for managing the customer support queue and responding to tickets."
- After: "Cut average ticket response time from 14 hours to 3 hours across a queue of 200+ tickets per week."

Same job. One version is a duty. The other is a result someone can hold you to. That is the entire gradient in miniature.

If you want the fuller breakdown of what a top-scoring resume looks like structurally, we covered that in [Anatomy of an A+ Resume](/anatomy-of-an-a-resume/), and the responsibilities-versus-results distinction gets a full treatment in [Responsibilities vs. Achievements](/responsibilities-vs-achievements/).

For a practical look at which tools reward that evidence, see [our comparison of six free resume checkers and graders](/free-resume-graders-compared-2026/).

## The length surprise

Here is the finding that did not match our prior going in. We measured average word count by grade, again restricted to resumes with extractable text.

| Grade | Average word count |
|---|---|
| A | 586 |
| B | 583 |
| C | 609 |
| D | 617 |
| F | 459 |

F resumes are short, at 459 words, and that makes sense: there usually isn't enough on the page to judge, so there isn't enough to reward. But the surprise is what happens above F. Length does not keep climbing with grade. It goes the other way. A resumes, at 586 words, are shorter than both C resumes (609 words) and D resumes (617 words).

The failure mode above F is not brevity, it is bloat. A resume that has climbed out of F-territory but still stalls at a C or D is usually one that added words without adding evidence: more bullets, more adjectives, more restated duties, without a proportional increase in things a reader can verify. Every extra line without a number in it dilutes the two or three bullets that actually carry the resume's case. The reader's attention is finite, and padding spends it on nothing.

Practically, this means the fix for a resume stuck in the middle of the distribution is usually deletion, not addition. Cut the bullets that only restate the job title in different words, and let the bullets with real numbers stand out instead of competing for space with filler.

## What a grade can and cannot tell you

A grade measures how well a document sells what is on it: whether the claims are quantified, whether the structure gets out of the way, whether a reader can extract the case for you in the time they're actually willing to spend. It cannot measure whether what is on it is enough for the job you're chasing. Presentation and substance are different questions, and it is easy to conflate them because they are graded, in the end, by the same reader in the same thirty seconds. A perfectly written resume for a role you are underqualified for will still get an A on presentation and lose on substance, and no amount of rewriting closes that gap. Conversely, someone with genuinely strong substance can still land in the C or D band because the evidence is buried in prose that never quantifies it, formatted in a way that makes a reader work to find the point.

Knowing which problem you actually have, thin content or weak presentation of good content, is the first honest step, and the two require entirely different fixes. Thin content needs more experience, more scope, more time in the role before the resume can honestly claim more. Weak presentation needs exactly the kind of editing this data argues for: fewer words, more numbers, less padding. Confusing the two wastes an editing pass on a resume that needed a career move instead, or a career move on a resume that just needed to be cut in half. It's worth reading [what counts as a good resume score](/what-is-a-good-resume-score/) if you're not sure which one applies to you.

## Methodology

RezScore has graded more than 13 million resumes lifetime. This study analyzes the newest 72,791, uploaded from November 2020 through July 2026, of which 72,566 carry a valid letter grade. The quantification metric is a simple proxy: presence of at least one percent or dollar figure in the extracted text, checked only against resumes where text extraction succeeded. All figures are aggregate counts and percentages; no individual resume content is shown or attributable.

If you want to see where your own resume lands on this distribution, run it through the [free resume grader](https://ai.rezscore.com/).
