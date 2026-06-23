---
title: "The landing page that could not fail"
slug: dev-the-landing-page-that-could-not-fail
date: 2026-06-22
author: "RezScore"
tags: [dev, infrastructure]
section: dev
description: "Right before a big email send, our blog started returning a 500. The cause was not a bug in our code, and the real lesson was about what we let take the page down."
draft: false
---

We were a day away from sending a large email to people who had used RezScore,
pointing them at a new post on this blog. Then a screenshot landed: the blog home
was showing a bare "Server Error (500)." Not a great look for the page an entire
campaign was about to drive traffic to.

## It was not our code

The first instinct on a 500 is "what did we just break." So that is where we
looked. The blog code had not changed in two days. Loading the page in a test
harness against the live database returned a clean 200 every time. Whatever was
wrong, it was not in the part of the system we could reproduce on demand.

The answer was in the server's own logs. For a brief window, the server could not
look up the address of its own database. A momentary network hiccup, the kind that
resolves itself in a minute and never explains why. While it lasted, every page
that needed the database failed, and the blog home was one of them.

So the proximate cause was not something we wrote. It was a blip in the plumbing.
Which is the least satisfying kind of answer, because there is nothing to fix in
the thing that "broke."

## The real problem was what we let break

Here is the part worth writing down. The blog home does not strictly need the
database to show you a post. The posts are files. The only reason it touches the
database at all is to show a small view-count next to each headline. A nice
detail. Completely optional.

And that optional detail was load-bearing. When the database blinked, the
view-count lookup threw an error, and because nothing caught it, the error took
down the whole page. A feature nobody would miss for thirty seconds had been
quietly holding the front door hostage.

That is the bug. Not the DNS blip, which we cannot prevent. The bug is that we let
a thing that does not matter break a thing that does.

## Two fixes, one principle

The principle is simple: the page should survive losing anything that is not
essential to it.

So first, the view-count lookup now degrades. If the database is unreachable, the
page shows the posts with no view counts and moves on, instead of throwing its
hands up. You would never notice the difference.

Second, we built a proper error page for the rare case when something truly does
go wrong. The old one was the framework's default: a black-on-white "Server Error
(500)" with nothing else. The new one is a calm, branded "We'll be right back"
with a link back home.

That second fix had a trap worth flagging, because it is the same shape as the
first. An error page that runs during a database outage must not itself touch the
database. The obvious version of a branded page pulls in the site's normal layout,
and that layout quietly asks the database for things. So during the exact outage
the page exists to handle, it would try to load, hit the dead database again, and
fall back to the ugly default it was meant to replace. We had to build the error
page to stand completely on its own, needing nothing, so it can render when
nothing else can.

## Why this one is worth a post

Because the failure was invisible until it was not, and the cause was not where
the symptom pointed. The symptom said "the blog is down." The first look said
"our code is fine." Both were true, and neither was the lesson. The lesson was a
quiet assumption, made months earlier, that a small convenience could lean on a
heavy dependency with nothing to catch it if the dependency wobbled.

Find one of those in your own work and you have probably found your next outage.
We found ours the day before it would have greeted a few thousand visitors.
