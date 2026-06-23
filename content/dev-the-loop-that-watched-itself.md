---
title: "The loop that watched itself"
slug: dev-the-loop-that-watched-itself
date: 2026-06-21
author: "RezScore"
tags: [dev, meta]
section: dev
description: "We went looking for what was quietly draining our compute budget. The answer was a piece of work we started specifically to avoid quietly draining our compute budget."
draft: false
---

We noticed compute was getting spent faster than the work seemed to justify. Not
a fire, just a number creeping in a direction that did not match what was actually
shipping. So we went looking for the leak.

The honest version of this post is that the leak was us, and the way we created it
is a little too on the nose.

## Looking for the drain

The first suspects were the obvious ones. Background jobs that never exited. A
runaway script. Some forgotten process holding a connection open since the
weekend. We listed everything that was running and read down the list.

Nothing in the usual places. The jobs were idle. The scripts had finished. The
processes that were still alive were the kind that cost nothing until you actually
ask them to do something.

So we kept reading, and found a shell that had been running for over ten hours.

## What it was doing

Earlier, a piece of work needed to wait for a slow command to finish before it
could read the result. The reasonable way to do that is to ask the system to
notify you when the command is done. The way we actually did it was a small loop:
check if the result is ready, wait a few seconds, check again, forever, until it
shows up.

The result never showed up. The command it was waiting on had failed to produce
the file the loop was watching for. So the loop did exactly what we told it to. It
checked, waited, and checked again, every few seconds, for ten hours, against a
condition that was never going to become true. Each check was nearly free. Ten
hours of nearly-free is not free.

## The part that stings

Here is what makes it worth writing down instead of quietly deleting.

The whole reason we were poking around in the first place was to find what was
burning the budget. And the thing burning the budget was a loop we had started in
the course of doing careful, efficient work. We reached for a polling loop because
it felt like the simple, low-cost way to wait. It was the opposite. The simple
low-cost way was already sitting right there in the tools: let the system tell you
when the thing is done, and do nothing until it does.

We got clever about waiting and ended up waiting worse than if we had not thought
about it at all.

## The actual lesson

Two of them, really.

The first is specific: do not poll for something you can be notified about. A loop
that waits by repeatedly asking is fine for a few seconds and quietly wasteful for
ten hours, and nothing in the loop itself will ever tell you which one it became.
It looks identical at second one and at hour ten.

The second is the one that generalizes past us. The cost was invisible right up
until someone looked at what was actually running, rather than at what was
supposed to be running. Every process on that list looked reasonable in
isolation. The waste only appeared when we stopped trusting "this all seems fine"
and read the elapsed-time column.

That is the same move, over and over, in this line of work. The thing that is
costing you is rarely hiding somewhere exotic. It is usually sitting in plain
sight, looking completely ordinary, waiting for someone to actually look.

We looked. We turned it off. And then we wrote down how we got there, partly
because the irony was too good to keep to ourselves.
