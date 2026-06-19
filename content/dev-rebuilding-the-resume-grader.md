---
title: "Rebuilding the resume grader to be transparent"
slug: dev-rebuilding-the-resume-grader
date: 2026-03-25
author: "RezScore"
tags: [dev, scoring]
section: dev
description: "How we replaced an opaque grading call with a deterministic, rubric-based scorer that gives stable, explainable grades."
draft: false
---

The grade is the heart of RezScore, so it has to be trustworthy. For a long time
it was not as trustworthy as we wanted. Under the hood, the old grader was a black
box. We sent it a resume, it sent back a letter, and we mostly took that letter on
faith. That worked until people started noticing the same resume could come back
with a different grade on a second upload, and we had no good way to explain why.

This post is about tearing that out and rebuilding the grader as something we can
actually reason about.

## The problem with a black box

A grade is only useful if it is stable and if you can explain it. The old system
struggled on both counts. The same text could score differently across uploads.
A failing grade could mean "this resume is weak" or it could mean "we could not
read the file," and the two looked identical from the outside. And because all we
got back was a letter, anything that explained the "why" had to be improvised after
the fact.

You cannot give honest career advice on top of a score you do not understand.

## A deterministic rubric

So we rebuilt the scorer from scratch as an explicit rubric. The grade now comes
from a fixed set of categories with defined weights: how cleanly the resume parses,
how clear its structure is, how much real evidence and measurable impact it shows,
how well it is positioned for a target, and how credible it reads. Each category
breaks down into smaller checks, and each check has a weight, so the math matches
the rubric instead of drifting away from it.

The biggest decision was to make scoring fully deterministic. No model sits in the
scoring loop. The same resume text produces the same score, every time, in every
environment. That sounds obvious, but it is the whole point: a grade you can trust
to repeat is a grade you can build on.

## Guardrails against wiggle

Determinism alone is not enough. A grade should not lurch because someone added a
space or swapped a bullet character. So the scorer normalizes the text before it
scores, smooths the boundaries between letter grades so a one-point change near a
threshold does not flip your grade, and compares each new result against your last
one to tell real change apart from noise. When the input is hard to read, it says
so with a confidence flag instead of pretending to be sure.

One lesson worth sharing: we deleted a rule that rewarded "approved" action words.
Scoring "spearheaded" higher than "led" or "ran" was rewarding resume dialect, not
information. The rubric now scores what a line actually communicates, the scope, the
result, the specifics, and ignores the costume the words are wearing.

## Shadow, calibrate, then cut over

We did not flip a switch. The new scorer ran silently alongside the old one first,
grading real uploads in the background so we could compare the two without changing
anything anyone saw. That shadow data showed us where the rubric was too generous or
too harsh, and we tuned the weights until strong resumes clearly separated from weak
ones and the ordering made sense. Only then did we move people onto it.

The old grader answered one question: what letter did the service return. The new one
answers a better set: what did we actually see in this resume, how confident are we,
and what changed since last time. That is a foundation we can keep building on.
