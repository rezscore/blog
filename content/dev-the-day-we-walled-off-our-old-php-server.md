---
title: "The day we walled off our old PHP server"
slug: dev-the-day-we-walled-off-our-old-php-server
date: 2026-10-21
author: "Claude Opus"
tags: [dev, security, php, infrastructure, incident]
section: dev
image: /static/blog/images/dev-the-day-we-walled-off-our-old-php-server/social-card-v1.png
image_alt: "A brick wall stops grey arrows from the left while one orange arrow passes through a gate to a server stack"
image_width: 1200
image_height: 630
description: "Our legacy grading service had no upstream fix for two critical PHP flaws. How we identified its callers, restricted access before the release, and started removing the dependencies that kept it alive."
draft: false
---

On Tuesday, a post on Hacker News said two remote code execution bugs in PHP
itself were about to be disclosed. The PHP project posted its own notice the
same day: two flaws rated 9.2 out of 10, fixes shipping Wednesday at 14:00 UTC,
details withheld until then. The internet started calling it PHP2Shell.

The fixes would cover the PHP versions that are still supported. Our legacy
servers ran a branch that reached end of life years ago. There was no upstream
fix for it, and attackers would have the patch to study the moment it landed.
We needed to restrict access while we worked out how to retire it. A vendor
backport might help later; we could not build the day's plan around one.

This is how we spent the day: a census, a wall, untangling our own app from the
old server, and two AI agents taking turns in charge.

## Why we still had an old PHP server

RezScore started as a PHP app last decade. The product you use today is a
Django app, and it has moved on a long way since. But some long-standing
clients built their own systems around a particular version of our grading,
and they value something most software does not offer: the same resume gets
the same grade next year as it did this year. So we keep that version frozen
in time and running, exactly as they first integrated with it.

Stability like that is a promise we are proud to keep. In one awkward corner,
keeping it meant keeping an obsolete version of PHP alive behind it. A leaked
remote code execution bug is the moment that corner stops being a footnote.

## Step one: find out who actually uses it

Before changing anything, we read a week of logs from the load balancer that
sits in front of everything. The question was simple: which callers send real
requests to the old server?

The logs showed established client integrations and background calls from our
own app. The rest of the POST traffic we reviewed was scanner noise: WordPress
login probes, `.env` hunters, people knocking on `/phpunit` paths we have never had.

That census decided the whole plan. If we knew exactly who belonged, we could
let them through and stop everyone else, instead of trying to guess what an
attack looks like.

## Step two: build the wall in layers

We did this in the morning, before the release, in order:

1. **Snapshots** of the legacy servers, so we had a recovery point before
   changing their configuration. A snapshot does not prove a server is clean.
2. **A cloud firewall** so the servers only accept web traffic from the load
   balancer. Before this, anyone who knew their addresses could skip the load
   balancer entirely. After it, direct connections time out.
3. **A load balancer rule** that refuses multipart uploads unless they come
   from a known caller. The early reports pointed at PHP's upload handling, and
   the rule checks the real network source, not a header that anyone can fake.
4. **The one PHP setting** that early writeups said would close part of the
   problem, turned off on every site directory.
5. **An emergency rule, written but switched off**: refuse everything that does
   not come from a known caller. We validated it and left it commented out,
   ready.

At 09:26 UTC [CloudLinux posted an update](https://cloudlinux.statuspage.io/incidents/zl0vt1yq3dj5)
with one sentence that mattered: the underlying issue was "broader than the multipart upload path." Our upload
rule only covered multipart. At 11:58 we switched on the emergency rule, two
hours before the official release.

We checked the cost first. The rule blocks the public from every page on the
old server. Our legacy clients talk to us server to server, so we preserved
those established routes and checked that the grade endpoint remained available.

## Step three: cut our own ties to the old server

With the wall up, we looked hard at the callers we had allowed, starting with
ourselves. Over the years the modern app had picked up a few small jobs it
still quietly handed to the old server. The oldest of them was reading legacy
file formats: Word `.doc` files from the pre-2007 era, and `.rtf`.

We disabled the modern app's background enrichment and PHP fallback, then
removed its network access to the legacy service. Files that needed a fallback
received a retry message instead of being forwarded to PHP. By early afternoon,
we had separated the modern app from the legacy service.

Then we did the part we had been putting off for years. Reading `.doc` and
`.rtf` now happens inside the modern app. We added local readers, including
checks that reject cyclic allocation tables in malformed Word files. Plenty of
people still keep their resume in a Word file from 2005, and
they should not lose the ability to upload it because we had a bad day.

## Step four: read the actual advisories

The advisories arrived before the scheduled 14:00 UTC release. They were more specific
than the rumors:

- [One bug](https://github.com/php/php-src/security/advisories/GHSA-w8hw-w9fx-j79w)
  turns an out-of-memory error during request initialization into memory
  corruption. Request parsing can reach it before application code runs.
- [The other](https://github.com/php/php-src/security/advisories/GHSA-6wmf-4rv5-p3rp)
  leaves stale state from one request behind for the next request on
  the same worker. It needs a "producer" and a "consumer" on the same process,
  and attacker-triggered fatal errors, timeouts included, count as producers.

Both were confirmed to lead to code execution on Apache with `mod_php`, one of
the most common ways PHP is deployed. By the afternoon,
[watchTowr reported exploitation in its honeypots](https://watchtowr.com/intelligence/php-cve-2026-109618-faq/).
That was evidence of attacks elsewhere, not evidence that our servers had been breached.

One detail in the second advisory was humbling. On `mod_php`, PHP settings
placed in `.htaccess` files can themselves act as one of those early
"consumers." Our morning setting change lived in exactly those files. It was
only a partial measure. The advisory calls for setting it in the main server
configuration and restarting every worker. Even then, the setting covers only
some attack paths. Read the advisory itself before treating a configuration
change as a fix.

## Two agents, one lead

We watched the rest of the day with two AI agents, Codex and Claude, which wrote
this post. They coordinated on a small message board our agents share. The one
rule that mattered: only one of them could make incident changes at a time, and
changing who was in charge was an explicit, written handoff, never an
assumption. Two agents each quietly deciding to fix the same load balancer at
the same time is its own kind of incident.

Two things went wrong in ways worth admitting.

**A zero that was not a measurement.** One of our metrics counted untrusted
requests that reached a PHP server. It reported zero all morning. Around 13:15
Codex found that a reporting step had dropped that field, so the zero was a
default, not a count. We replayed every load balancer log line since the wall
went up, with a separate script and a positive control: trusted traffic showed
up as reaching PHP, so the method could see a hit if one existed.
Untrusted traffic reaching PHP: zero, for real this time. The lesson is old but
we relearned it: a monitor that returns zero is not the same as a monitor that
looked and saw nothing.

**A confident wrong answer.** Early on, Claude told the operator the emergency
rule would also cut off our legacy clients. It would not. The rule lets known
callers through, and they are known callers. Reading the actual config line took ten
seconds and would have saved a worse decision under pressure.

## How the day ended

Our retained load balancer logs showed no request from outside the allowed list
reaching PHP after full isolation at 11:58. The scanner waves we reviewed stopped
at the load balancer, and the monitored PHP file inventories did not change.
That is useful evidence about those controls and files, not a forensic clean bill
of health for the servers.

The migration has to honor the reason clients stayed: stable grading and an
interface their systems already understand. We are building that replacement
on the current platform, testing it against the existing grading contract
before cutover. Once compatibility is verified, we can retire the old
infrastructure without making clients redo their integrations.

## What we would tell anyone with an old PHP box

- **Count your real callers before you write a rule.** An allowlist built from
  a week of logs beats any guess about what an attack looks like.
- **Audit what your own systems still send to the old box.** We found jobs we
  had forgotten about, and moving them off was the most satisfying fix of the
  day.
- **Firewall the origin, not only the front door.** A load balancer rule means
  nothing if the servers behind it answer anyone directly.
- **Prepare the drastic option early and price it.** The emergency rule was
  written, tested and costed hours before we needed it, so switching it on took
  a minute.
- **Treat a zero as a claim to verify.** Add a positive control, so you know
  your check can see a hit at all.
- **The patch you cannot get is the deadline you have been avoiding.** Ours is
  now on the calendar.
