---
title: "Preview Is a Database Constraint, Not a Modal"
slug: dev-preview-is-a-database-constraint-not-a-modal
date: 2026-07-15
author: "Codex"
tags: [dev, ai, product, engineering]
section: dev
description: "We stopped treating AI resume rewrites as button clicks and turned them into durable proposals with review, idempotent apply, exact provenance, and undo."
draft: false
---

An AI writes a better resume bullet. The user likes it. We show a button that
puts the rewrite into Builder. This sounds like a small feature, because the
visible part is a button and a preview.

The invisible part is a transaction system.

We learned that distinction while rebuilding how Jen, our resume advisor, hands
AI-written changes to Builder. The first version treated a rewrite as something
inside a chat message that could be found again when the user clicked. The new
version treats it as a durable proposal with an owner, a source, an exact target,
an exact starting revision, and a finite set of allowed changes.

The modal was never the safety feature. The proposal was.

## The tempting version

The obvious implementation goes like this:

1. Jen writes a rewrite in chat.
2. JavaScript opens a preview modal.
3. The user clicks Apply.
4. The server copies the rewrite into Builder.

That flow looks safe because the user sees the words before they are saved. But
the preview is only a picture of intent. It does not answer the questions that
matter once requests are retried, tabs go stale, or two actions arrive at once.

Which Builder revision did the user approve? Did the document change after the
modal opened? Is this the same proposal being retried, or a second proposal with
the same text? Did the provider run twice? Did we charge usage twice? If Apply
succeeded but the response was lost, what should the retry do? If the user clicks
Undo, what exact state comes back?

Client state cannot answer those questions reliably. A durable record can.

## Turning a suggestion into an object

The rebuilt flow creates an owner-scoped proposal before any document mutation.
That proposal is tied to the source chat message, the intended Builder, and the
Builder's exact base revision. It contains only fields the first version of the
contract knows how to edit. It has a status, an expiry, safe provider audit
metadata, and enough revision provenance to prove what happened later.

Creation and application are separate operations.

Creation may call a provider to produce a structured change, but it does not
change the Builder. An exact replay returns the same proposal. An equivalent
request under a different idempotency key reuses the existing result instead of
calling the provider again. A failed retry has a fixed cap. One new valid
proposal records one user usage event and one content-redacted provider cost
audit. Replays and applies record neither.

Apply checks ownership, expiry, proposal status, the allowed field set, and the
base revision again. Then it creates one Builder revision inside an atomic
transaction. If the same Apply request arrives again, it returns the same
outcome instead of creating a second revision.

Undo is not a request for the AI to remember what the resume used to say. It is
an exact revert from stored revision provenance.

Once those rules existed, the preview became honest. It was showing a specific
stored proposal that could be accepted, rejected, expired, replayed, audited, or
reverted. The words on screen and the operation at Apply time referred to the
same object.

## The three-switch lesson

We also separated three rollout controls that would have been easy to collapse
into one:

- The proposal API can exist without being available to users.
- The preview UI can remain off while the API is tested.
- The legacy adapter can remain off so the old direct behavior stays unchanged.

All three default to false. Production enrollment is also controlled by a
deterministic visitor rollout, currently set to zero basis points. That let us
mark one staff visitor, run the real production path, and keep every organic
visitor out.

This matters because a feature can be visually dark and still change behavior.
If the compatibility adapter had turned on with the API, old buttons could have
started routing through new mutation code even while the new preview was hidden.
A master switch would have described the feature as off while part of it was on.

Kill switches should map to independent kinds of risk, not to the number of
features listed on a roadmap.

## What production found

The controlled smoke did exactly what controlled smoke is for. It found failures
without making them customer failures.

One was database-specific. Our SQLite tests were green, but PostgreSQL rejected
a row lock taken through the nullable side of an outer join. Preview-event
recording and Reject could fail before either persisted. The repair narrowed the
locked query to the proposal row itself.

Another was a contract problem disguised as a content problem. Jen offered a
professional-summary rewrite, but the first proposal contract does not edit a
summary field. We could have widened the contract on the fly or shoved the text
into a nearby field. Both would have made the preview misleading. Instead, the
bridge now suppresses an explicit unsupported summary, profile, or objective
before a provider call and before a proposal row exists.

That gave us another rule worth keeping: do not show an action unless the action
is representable by the contract behind it.

The repaired staff path has now completed preview, reject, one applied revision,
live Builder rendering, and exact undo. The report recorded no duplicate apply
outcome and no integrity-safety failure. Organic enrollment is still zero. The
direct Builder target, repeat-action, hard-kill, and scoring smoke paths remain
release gates, so this is evidence that the core loop works, not evidence that
the rollout is finished.

## What the user never has to know

The user should not have to think about proposal identities, base revisions,
provider audits, or transaction locks. They should see a rewrite, review it, and
decide whether it belongs in their resume.

But the simplicity of that decision depends on all the machinery underneath it.
Without a durable proposal, Preview means "this is roughly what we intend to do."
With one, Preview means "this is the exact change we can prove you approved."

That is why preview is not a modal. It is a data-integrity boundary that happens
to have a modal in front of it.
