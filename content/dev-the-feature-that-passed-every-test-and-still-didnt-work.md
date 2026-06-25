---
title: "The feature that passed every test and still didn't work"
slug: dev-the-feature-that-passed-every-test-and-still-didnt-work
date: 2026-06-24
author: "RezScore"
tags: [dev, testing, product]
section: dev
description: "We shipped a small feature with hundreds of green tests behind it. It was broken four different ways. Every bug lived in a place no test was looking, and the only thing that found them was a person clicking the button."
draft: false
---

A user did something we had never built a way to do. Deep into a chat with our
resume advisor, after editing his resume on his own, he asked: "I have a new
version, can I upload it?"

There was no answer to that question in the product. He had used his one upload
to get graded, then talked his way to a much better resume, and at the moment he
wanted to check his work, the door was a wall. So we built the door: a way to
re-upload an edited resume right inside the conversation and get a fresh grade
without starting over.

We wrote the feature. We wrote the tests. Hundreds of them passed. We shipped it.

It was broken four different ways, and not one of the tests noticed.

## Why the tests were so confident and so wrong

The way you test a feature like this is to break it into pieces and check each
piece. Does the part that decides which version of the button to show pick the
right one? Does the part that records a grade record it? Does the part that tells
the helpful chat to react actually react? Each of those is a small, honest
question with a clean yes-or-no answer, and each of our answers was yes.

The bugs were not in any of those pieces. They were in the seams between them, and
in the older machinery the new feature leaned on. A test that checks one piece in
isolation cannot see a seam, because a seam only exists when two pieces are
connected to the rest of the running system. Our tests were like checking that a
key turns and a lock turns, separately, and concluding the door opens.

It did not open. Here is what we found, in order, each one only after the last was
fixed.

## Bug one: the button reused machinery that switches itself off

The clean way to re-upload is to reuse the upload mechanism the page already has,
rather than build a second one. So that is what we did. The new button handed the
file to the existing uploader.

The existing uploader turns itself off after the first upload. It is supposed to:
on the normal path you upload once and move on, so it locks the door behind you to
avoid surprises. Hand it a second file and nothing happens. Silently nothing. No
error, no grade, no sign anything was even attempted.

Reusing existing code is usually the right instinct. But you inherit its whole
life story, including the parts that assumed it would only ever be used once. We
stopped reusing the old uploader and sent the file straight to the grader instead.

## Bug two: the feature was free, and the free tier is one resume

A free account grades one resume. That is the whole free tier. A person's edited
re-upload is, by the counter, their second resume, so the cap blocked it every
time. The feature was dead on arrival for exactly the people it was built for:
the engaged free users who did the work and wanted to see it pay off.

This one is more interesting than a bug, because the fix is a product decision. We
decided a re-grade of your own edited resume is a revision, not a new resume, and
gave every free user exactly one of them. You get to watch the re-grade work once,
see your letter move, and feel the thing be useful. The second re-grade is where
the upgrade conversation naturally starts. Generosity and a paywall, placed where
each does the most good.

## Bug three: the button was inside a box that hides itself

With the mechanics working, the button still was not there. For one whole class of
users, it rendered into a container that the page hides under certain conditions,
and those users always met the condition. The button existed, was styled
correctly, did the right thing when clicked, and was invisible, because its parent
was set to display nothing.

This is the seam problem in its purest form. Nothing about the button was wrong.
The bug was a fact about the box we put it in, a fact that lived in a different
file written long before, and the only way to know it was to read that file or to
look at the actual page and notice the button was missing.

We moved the button somewhere that is always shown.

## Bug four: the rest of the report did not get the memo

Now the re-grade worked, the grade updated, and the user pointed out that the
detailed breakdown below the grade had not changed at all. We had updated the one
number we were thinking about, the letter, and left the five charts beneath it
frozen on the old resume. A grade that says B+ over a breakdown that still
describes the C+ version is worse than no update, because it looks like the
product is confused about its own answer.

We made the re-grade refresh the whole report, not the headline of it.

## The pattern, and what we are doing about it

Four bugs, four different hiding places: a reused component's lifecycle, a pricing
gate, a conditionally hidden container, a stale view. They share one trait. Every
one of them was a fact about how the new code touched code that already existed,
and every one was invisible to a test that looked at the new code alone.

A green test suite measures the thing you thought to ask. It is silent about the
things you did not know to ask, and the things you do not know are, by definition,
where the bugs are. This is not an argument against tests. We kept all of them and
added more. It is an argument that "the tests pass" and "the feature works" are
two different claims, and the second one has to be earned separately.

So we earned it the only way that actually works: someone used the feature, as a
real person, on the real running site, and clicked the button to see what
happened. Four times, that surfaced something no amount of green checkmarks had.
Now, before we call any user-facing feature done, we trace the whole path a person
takes through it and watch it happen with our own eyes. Not the pieces. The path.

The user got his re-grade. And we got a sharper version of a rule we keep
relearning: the test of whether a thing works is whether it works when someone
uses it.
