---
title: "Going global: localizing for an international audience"
slug: dev-going-global
date: 2026-05-29
author: "RezScore"
tags: [dev, infrastructure]
section: dev
description: "How we detect where a user is from and route AI work to fit cost and latency so a free global product stays sustainable."
draft: false
---

RezScore is free to use, and people use it from everywhere. Once you look at the traffic that way, two engineering questions show up fast. How do you tailor the experience for someone who is not in your home market? And how do you keep an AI-heavy product affordable enough to serve the whole world without putting it behind a paywall?

Here is how we approached both.

## Knowing where someone is, locally

The first instinct for geolocation is to call a third-party API on every request. We did not want that. It adds latency to the hot path, it adds a per-lookup cost that scales with traffic, and it puts a network dependency between a user and their first page view.

Instead we resolve country from the visitor's IP address against a local, offline database file loaded once when the process starts. Lookups are sub-millisecond and never leave the box. We store a two-character country code on the visitor record so any later logic can read it without a repeat lookup.

A few design choices made this safe to ship:

- **Fail open.** If the database file is missing or the IP does not resolve, we simply record nothing and move on. No request ever errors because geolocation was unavailable.
- **Indexed and cheap.** The country code is indexed, and downstream code reads it with a single narrow query rather than loading the full visitor object.
- **Honest about trust.** IP-derived location is a hint, not proof. We use it only for things that are fully recoverable if wrong, never for access control.

To fill in history, we wrote a backfill job that streams existing rows in chunks straight from the database rather than loading the whole table into memory. Our production box is small, so anything that materializes a large table in Python is a non-starter. The job is idempotent and refuses to run if the lookup database is unavailable, so it can never stamp every row with a wrong default.

## Routing AI work to fit the job

The second half is cost. Every chat conversation runs through a language model, and not all models cost the same. Some are far cheaper and plenty good for routine help, while others are worth their price for harder work.

So we route. Geography is one input that can bias a session toward a more cost-appropriate model when nothing more specific applies. The key word is bias: it is only a fallback. If a higher-priority signal already picked a model for that session, that choice always wins. The goal is to spend where it counts and economize where it does not, so we can keep serving everyone well rather than turning anyone away.

We wired this in at session creation so a conversation lands on the right model from its very first message, with no follow-up reconciliation step.

## The point

None of this should make any user feel like a second-class visitor. Localization is about meeting people where they are, and smart model routing is what lets a small team keep an AI product free and fast for a genuinely global audience. Serve everyone, and make the economics work so you can keep doing it.
