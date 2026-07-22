---
title: "Your funnel is lying"
slug: dev-your-funnel-is-lying
date: 2026-07-21
author: "RezScore"
tags: [dev, analytics, observability, product]
section: dev
description: "A user followed a chat handoff into our resume Builder. Our funnel said they never arrived. The pageview was correct, and the funnel was still wrong."
draft: false
---

We had a user who looked like a familiar product failure.

They opened an email, clicked its call to action, started a chat, and asked a
question that made the next step obvious: compare the resume to a job and make it
better. Our advisor invited them into the resume Builder. Then the trail stopped.

The dashboard showed no Builder landing. No Builder pageview. No edit. No save.
It was tempting to tell a clean story: they lost interest, the invitation was too
soft, or the product did not deliver what the email promised.

Then we traced the request path instead of the dashboard. The user had reached the
Builder. Our dashboard just had no way to say so.

That distinction matters. A funnel is not the product. It is a model of the
product, assembled from events. If the model leaves out a step, the missing step
does not become a failure. It becomes a blind spot that looks exactly like one.

## The pageview that was true and misleading

The old funnel was built around a standalone Builder page. If someone navigated
there, the server recorded a landing event. That was a sensible event when the
Builder was a destination with its own URL.

The new handoff did not work that way. A chat response returned a small instruction
to the browser: replace the existing panel with the Builder, then load the resume
data into that panel. No navigation happened. There was no new page to view.

The server did have evidence that the handoff was offered. The browser made the
Builder data request a moment later. The Builder data was valid and complete. But
the events we normally use to diagnose activation began only at the standalone
page, which this user never visited because they did not need to.

The pageview was accurate. Nobody visited that page. It was also useless as a
measure of whether the embedded Builder was reached.

That is a common trap in product analytics. Teams often name an event after the
screen they expect a person to see, then quietly change the architecture. A modal
replaces a route. A side panel replaces a modal. A client-side component replaces a
server render. The old event keeps counting faithfully, and the new path becomes
invisible.

## One missing event hides several different failures

At the time, the only evidence after the handoff was a successful data request and
an abrupt end to activity. That is enough to prove the server did not reject the
request. It is not enough to answer the product question.

Several very different things can produce the same old funnel:

- The chat invitation may not have been noticed.
- The panel may have opened below the fold or behind another element.
- The Builder may have loaded slowly or failed to render in that browser.
- The resume may have appeared correctly, but the user may have found no obvious
  first action.
- The user may simply have decided not to edit right then.

Those causes call for different changes. A hidden panel is a layout problem. A load
failure is an engineering problem. A complete Builder with no first edit is a
product and copy problem. Treating all three as "no landing" turns a useful
investigation into guesswork.

The same is true in reverse. A successful API response is not activation. It only
proves that a request completed. The user experience begins after that response is
turned into something a person can see and use.

## The smallest useful event sequence

We added five events around the embedded handoff:

| Event | What it tells us |
| --- | --- |
| `chat_builder_handoff_received` | The browser received the chat instruction to open the Builder. |
| `chat_builder_panel_visible` | The embedded Builder container was shown. |
| `chat_builder_data_loaded` | Resume data was fetched and rendered into the Builder. |
| `chat_builder_load_failed` | The client failed while restoring or loading the Builder. |
| `chat_builder_first_edit_saved` | The person made and saved their first edit. |

The order is the important part:

```text
chat handoff
    -> panel visible
        -> data rendered
            -> first edit saved
```

Every arrow is a question we can now answer. Did the chat send the instruction?
Did the browser display the panel? Did the data become a visible resume? Did the
person take the first meaningful action?

The events are deliberately narrow. We do not send resume text, chat text, names,
email addresses, or a record of every keystroke. The event includes only the
minimum stable identifiers needed to connect the handoff to the already-authorized
session, an elapsed time, and a small failure category when a load fails.

The collection endpoint also verifies that the current visitor owns the matching
chat and Builder records. Analytics endpoints are often treated as harmless, but
an endpoint that accepts arbitrary identifiers can become an accidental way to
write misleading activity into someone else's funnel. "It is only telemetry" is
not a reason to skip authorization.

## The boundary between code and experience

We placed the events at the boundaries that correspond to user-visible reality.

The first two fire in the page code that receives the chat response and reveals the
embedded panel. The data-loaded event fires only after the Builder has fetched and
rendered the resume, not when the fetch starts. The first-edit event fires only
after a successful save, rather than when a user focuses a field or types one
character.

That last choice is intentional. Focus and keystrokes are useful for interface
research, but they are noisy as a definition of activation. A saved first change is
a modest, durable claim: the person used the Builder to alter their resume.

We send the telemetry on a best-effort path and deduplicate each handoff event in
the browser. If analytics is unavailable, the Builder still opens and saves. The
product should not wait for its own measurement system before helping a person.

## What this changes in the next investigation

The next embedded Builder handoff will not magically tell us why someone left. It
will give us a much narrower and more honest starting point.

If we see handoff received but no panel visible, we inspect the client transition.
If we see panel visible but no data rendered, we inspect the loader and its failure
classes. If we see a rendered Builder with no first edit across many sessions, we
look at the first action, the invitation, and the value we promised. If people edit
but do not finish, that is a later step with its own question.

This is less glamorous than declaring a conversion funnel complete. It is also how
the funnel becomes a tool instead of a story generator.

The broader rule is simple: whenever a user journey changes shape, redraw the
instrumentation with it. Routes, pageviews, and server responses are implementation
details. The funnel should describe what a person actually experienced.

Otherwise it will keep reporting a clean answer to a question your product no
longer asks.
