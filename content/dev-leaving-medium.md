---
title: "Leaving Medium: git is our CMS now"
slug: dev-leaving-medium
date: 2026-06-16
author: "RezScore"
tags: [dev, meta]
section: dev
description: "We moved the RezScore blog off Medium onto a file-backed system we own, where every post is a markdown file in a git repo."
draft: false
---

The blog you are reading used to live on Medium. It does not anymore. We moved
it onto a system we own, where every post is a markdown file in a git repository
and the site renders those files directly. This is the story of why, and how the
new setup works.

## Why we left

Renting your publishing platform is fine until the day it stops being fine.
Medium changed how it handled our custom domain, and our blog address simply
stopped resolving. One platform decision, made by someone else, and our writing
was suddenly harder to reach. That was the push, but the reasons had been
stacking up for a while.

We wanted to own our content. The posts are ours, the research in them is ours,
and they should not depend on a third party deciding to keep the lights on.

We wanted to own the SEO. Years of writing build up search equity, and that
equity should point at a domain we control, not one we borrow.

We wanted to own the design. Every page should look and behave the way we
choose, with no platform chrome we did not ask for.

## How the new setup works

The new blog has no database and no admin panel. Every post is a markdown file
in a folder. The site reads those files, turns them into clean HTML, and serves
them. Publishing a post is just adding a file and committing it. Git is the CMS.

That choice buys a few things for free:

- **Fast pages.** There is no database query behind a post. The content is a
  file, sized and cached, so pages load quickly and do not jump around as they
  render.
- **Simple to reason about.** Want to know exactly what is published and when it
  changed? Read the commit history. The full state of the blog is right there in
  version control.
- **Auditable.** Because the whole thing is just files in a repo, the source can
  be public. It is, at github.com/rezscore/blog. If you are curious how a post is
  structured or how the site is put together, you can read it yourself.

## Not breaking old links

A migration is only as good as the links it does not break. Every old address
from the previous home still works. Requests for the old URLs are redirected to
the right place, so anyone arriving from an old bookmark or an inbound link lands
exactly where they expect. Nothing that used to point at our writing now points
at a dead page.

## The takeaway

Owning the stack is more work up front and far less worry afterward. The blog is
now a folder of text files we fully control, fast to serve and easy to move if we
ever need to move it again. That is the whole point: never again should one
platform decision be able to quietly unplug our writing.
