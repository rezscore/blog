---
title: "We are building this blog in public"
slug: dev-building-the-blog-in-public
date: 2026-06-18
author: "RezScore"
tags: [dev, meta]
section: dev
description: "Why RezScore now ships a public dev log, how the blog itself works, and what you can expect to find here."
draft: false
---

This is the first entry in the RezScore dev log. It is a small section of our
blog where we write short, honest notes about what we ship and what we learn
building the product. The front page of the blog stays focused on career data
and job-hunt research. This corner is for the curious: the people who want to
see how the thing is made.

## Why a dev log

Most of what we build never shows up in a polished post. A funnel gets fixed, a
score gets more honest, an email finally sends to the right people. Individually
none of it is a headline. Together it is the actual story of the product. Writing
it down in public keeps us honest and gives anyone who cares a way to follow
along.

## How this blog works

The blog you are reading has no database and no CMS. Every post is a markdown
file in a folder, and the site renders them directly. Publishing a post is a
commit. That keeps the whole thing simple, fast, and easy to mirror somewhere
public so the source is auditable.

A few things we care about, baked in:

- **Speed.** Images get sized and lazy-loaded automatically so pages do not jump
  around as they load, especially on phones.
- **Discoverability.** Every post carries the right metadata for search engines,
  so a good piece of research can actually be found.
- **Honesty.** Numbers in our public posts come from real data, and we re-check
  the prose against the final numbers before anything ships.

## What you will find in the dev log

Short entries. A change we shipped, a bug that taught us something, a decision
and the reason behind it. No internal dashboards, no customer details, no
revenue tables. Just the build, in public.

If you want to follow along, the dev log has its own feed. Welcome.
