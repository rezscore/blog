---
title: "The 20-second page our tools said was fast"
slug: dev-the-20-second-page-our-tools-said-was-fast
date: 2026-06-23
author: "RezScore"
tags: [dev, performance, infrastructure]
section: dev
description: "Our homepage took up to 20 seconds to load for real visitors, but every tool we checked it with said it was instant. The gap between those two facts was the whole bug."
draft: false
---

A user mentioned the homepage felt slow. Twenty seconds slow, on a normal laptop
on a normal connection. That is the kind of number that quietly kills a site:
slow enough that people leave, fast enough that it never throws an error you would
notice.

So we measured it. And every tool we reached for said the page was fine.

## The page was fast and slow at the same time

The first move on a slow page is to time it. A command-line request to the
homepage came back in about two tenths of a second. We ran it again. Still fast.
We added a cache-busting parameter to force a fresh render. Fast. We timed a
couple of cheap endpoints on the same server. Fast.

Meanwhile, loading the actual page in a browser took anywhere from two to thirteen
to twenty seconds, and the number was different every time. Fast tools, slow
browser, no error anywhere. That contradiction was not noise to push past. It was
the bug pointing at itself.

The thing the fast requests had in common turned out to be the answer. Our site
tells bots and crawlers apart from real people, and it does less work for bots on
purpose. A command-line request, with no cookies and no browser fingerprint,
looks exactly like a bot. So it skipped the very work that was slow. The tools
were not lying. They were measuring a different page than the one humans get.

The lesson generalizes well past our setup: if any part of your stack branches on
"is this a real user," then the quick checks you trust most may be exercising the
branch your users never touch. You have to test as the user, cookies and all, or
you are measuring a page nobody visits.

## What the real page was actually doing

Once we sent a request that looked like a person, the slow path showed itself. The
homepage was doing a burst of bookkeeping on every genuine visit: recording which
version of several ongoing experiments that visitor was seeing. Useful work, the
kind that lets us learn what actually helps people. But it was happening in the
worst possible way.

Two problems stacked on top of each other.

First, the table that stored all of this had grown enormous, and most of its size
was indexes. An index makes reads faster and writes slower, because every write
has to update the index too. This table had more index than data, and we were
writing several rows to it on every single visit. Each write was paying a tax to
keep indexes nobody was reading.

The instinct in a moment like this is "the queries are slow, add an index." It
was exactly backwards. The fix was to remove indexes. A table that is written
constantly and read rarely should be kept deliberately thin: pay for the few
lookups you truly make, and not a byte more.

Second, the running tally of each experiment was being updated by reading the
current count into memory, adding one, and writing it back. Do that from many
visitors at once and they line up single file, each waiting for the one before it
to finish. We replaced it with an update that does the addition inside the
database in a single step, so visitors stop queueing behind each other.

Thin out the indexes, stop the counters from queueing, and the page that took up
to twenty seconds settled to under half a second, fresh visitors and returning
visitors alike. The wild variation, the tell that something was contending under
load, was gone.

## Two smaller lessons we paid for

While fixing the slow path, we also shipped the slow path's quiet sibling: a
feature flag had us serving the slow version to half of users on purpose, as an
experiment to decide whether the fast version was worth keeping. At some point you
have to admit that A/B testing the fix for a problem you have already diagnosed is
not rigor. It is just a slower way to help half your users. We turned it on for
everyone and retired the test.

And a humbler one. The first version of a layout change we made looked perfect in
the isolated preview we screenshotted, then shipped a real regression: on a wide
screen, one whole column dropped below the fold. The preview could not show it
because the bug only existed in how two columns shared a row, and we had only
looked at one of them. Now we render layout changes in a real browser at the width
a real laptop uses, and check the two columns actually sit side by side, before it
goes out. A preview of a part is not a preview of the page.

## Doing it carefully

The bloated table was being written to constantly by a live email campaign while
we worked on it. Cleaning out the dead data, rows from experiments that ended
months ago, meant deleting hundreds of thousands of rows from under live traffic.

The rule we held to: archive before you delete, and make the tool refuse to delete
at all unless it has somewhere to put the archive first. Deletes ran in small
batches with a pause between them, so live visits were never starved waiting for a
lock. An earlier, hastier pass had deleted some rows without saving a copy first,
and the only good thing about that was that it was partial, so most of the data
was still there to do properly. We turned that near miss into the rule, then put
the whole cleanup on a schedule so the table never silently bloats again.

None of this is glamorous. It is the unglamorous part that was costing real people
twenty seconds, and real people leaving is the only metric that ever mattered.
The most useful thing we did was the least technical: we stopped trusting a green
checkmark from a tool that was quietly testing the wrong thing.
