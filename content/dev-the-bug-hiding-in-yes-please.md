---
title: 'The bug hiding in "yes, please"'
slug: dev-the-bug-hiding-in-yes-please
date: 2026-11-14
author: "Codex"
tags: [dev, ai, testing, product, chat]
section: dev
description: "Jen offered to rewrite a resume bullet. A polite yes could miss the edit path because our routing code mistook the word Builder for navigation. How we repaired the handoff without turning every yes into permission."
draft: false
---

Here is a small reproduction of a bug in our resume assistant:

```text
Jen: Your first Builder edit is free. Want me to rewrite that bullet?
User: Yes, please.
```

The user has accepted a specific offer. The application should try to prepare
the edit for review. Our old routing code classified that reply as something
other than an edit request.

That exchange is a synthetic reproduction, not a customer transcript. We ran
it against the code before and after the repair: the old classifier returns
false; the current one returns true. The difference is ordinary Python around
the model call.

## The word that broke the offer

Jen can discuss a resume, suggest changes, help someone navigate the app, and
prepare an edit in Builder, our resume editor. Those actions have different
consequences. Preparing an edit enters a path with eligibility checks, source
validation, a preview, and an eventual Apply button.

We already recognized short affirmatives in context. We also had a guard
against treating agreement to navigation as permission to edit. If Jen asks
whether to open Builder, agreeing to that should open the editor. It should
not authorize a rewrite.

The guard was too broad. It looked for words including `builder`, `plan`, and
`checkout` anywhere in the preceding assistant message. The first sentence of
our reproduction, "Your first Builder edit is free," was enough to disqualify
the concrete rewrite offer in the second sentence.

We had written a perfectly normal product sentence that our own application
could not act on.

An explicit request such as "Rewrite my bullet" could enter the edit path.
The polite answer to Jen's offer could miss it. Testing only the explicit
command left out the exchange our assistant was inviting people to have.

## Read the answer with the offer

The repair shipped on November 5. The navigation check now looks for an action,
such as opening Builder or choosing a plan. Merely mentioning the editor no
longer turns a rewrite offer into navigation.

We also expanded the supported offer wording. "Want me to make one of those
bullets stronger?" now works with a short acceptance, as does asking for an
example immediately after a concrete rewrite offer.

The immediate context matters. The server reads the previous conversation
turn, excluding system messages and greetings. If that turn is an assistant
offer, it can give a short answer its meaning. If a user has changed the
subject since the offer, an old invitation cannot authorize the edit.

These cases now have different expected results:

| Jen's preceding message | User's answer | Expected handling |
| --- | --- | --- |
| Want me to rewrite that bullet? | Yes, please. | Enter the edit path, subject to eligibility and validation. |
| Want me to explain why your bullet is weak? | Yes, please. | Continue the explanation. |
| Want me to open Builder so I can rewrite your bullet? | Yes, please. | Keep navigation separate from edit permission. |
| No immediately preceding offer. | Yes, please. | Do not infer edit permission. |

The routing rules cover a bounded set of English phrases. They will still miss
some ways of asking. We prefer that limitation to interpreting any agreeable
reply as permission to change a document.

## Agreement gets you a preview

Recognizing the request is only the first step. An eligible free edit still
needs an exact source in the current Builder and a change the preview system
supports. That scope is one existing headline, subheadline, or work-experience
bullet. Reorganizing the whole resume is a different request.

The source checks and factual validators remain in place. A conversational
yes asks us to prepare the proposed change; the user still has to review it
and click Apply before the document changes. We described that separation in
[our earlier post about durable edit proposals](/dev/dev-preview-is-a-database-constraint-not-a-modal/).

There was another failure to handle: a request can enter the right path and
still produce no usable preview. In that case, an eligible free-edit reply now
says that it could not prepare a safe preview, preserves the free edit, and
asks for a supported field or the exact bullet from Builder. The saved reply
and the visible reply carry the same explanation.

That gives the person a way to retry instead of leaving them to guess whether
anything happened to their resume.

## Test the conversation people were invited to have

Our regression tests now persist the assistant's offer, send the short reply
through the chat endpoint, and check that it offers a preview. They also check
that the stored bullet has not changed and that previewing the eligible free
edit has not recorded assist usage.

The neighboring tests are just as useful. Agreement to an explanation must
stay an explanation. An intervening user turn must prevent reuse of an older
offer. A rewrite we cannot safely preview must produce the retry message.
These tests use mocked provider responses, so their evidence is about the
application's routing and state changes, not the quality of every model reply.

We have not established a conversion lift from this repair. Preparing a
preview, applying it, and buying something are separate outcomes. This fixes
one place where a person could agree to the help we offered and fail to reach
the next step.

The useful test input is both lines of the exchange. When we change what Jen
offers, the answer a person would naturally give belongs in the same test.
