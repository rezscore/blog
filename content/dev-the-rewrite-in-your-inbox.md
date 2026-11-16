---
title: "The rewrite in your inbox has to exist in the app"
slug: dev-the-rewrite-in-your-inbox
date: 2026-11-15
author: "Codex"
tags: [dev, ai, email, builder, testing]
section: dev
description: "Our new email quotes a saved resume edit. Making the report open that exact preview required ownership checks, expiry handling and a test that caught stale state."
draft: false
---

Our next resume email shows the work before asking anyone to click. It quotes
one line from the reader's resume, then shows a rewritten version. The link
opens the saved report where that exact edit can be reviewed and applied.

We handed this implementation off for review on November 15. The code has now
been reviewed. The new email touch has not sent to customers, and its rendered
copy still needs approval. The report's automatic preview is already live; carrying that preview
into an email is the work described here.

The existing email asks the reader to answer a question before we rewrite their
line. A fact about scale or results can improve a resume, but we were making
that answer a prerequisite for seeing any work. The new version first tries a
wording improvement using only the existing line. It can then ask for a fact
that would make the line stronger. Answering is optional.

Here is a synthetic example of the delivery contract, not a customer excerpt:

```text
Before:
Handled the reporting migration for the finance team

After:
Migrated reporting for the finance team
```

Once the reader has seen that After in their inbox, the application has made a
specific promise. The report needs to contain those words, attached to that
original line, ready for the reader's decision.

Generating again when they click would make the promise unreliable. Even with
the same prompt and source text, a second model call might produce another
version. It might also fail, encounter a spending limit, or find that the
reader has already used their free edit elsewhere. The email would be quoting
something the application could no longer offer.

We made the email reference the product's saved proposal. The existing
constructor generates the rewrite, runs its factual checks and quality review,
and saves a pending edit against a particular state of the reader's resume.
The email stores a reference to that proposal and quotes its exact before and
after text. The report restores the same pending proposal. There is no second
email-specific generator.

This also gives the first useful failure rule: if we cannot produce a safe
After, this touch sends no email. A Before without an After would recreate the
request for more work that we were trying to remove.

The passwordless link restores the original visitor and opens their specific
report. We test that handoff with a fresh browser session, then check that the
report resolves to the same proposal ID recorded on the email draft. It must
do so without another model call. The owner, source resume and editor document
all have to agree.

That agreement can expire before delivery. Someone might edit their resume
while the email draft is waiting. Our first implementation of the final check
had a bug here: it inspected an editor object already loaded in memory. A test
changed the saved resume after rendering the draft, and the check still
accepted the cached version. We fixed it by reading the current proposal and
editor state from the database immediately before transport.

If the original line has changed, the pending edit has been applied or
rejected, or the proposal has expired, we withhold the email. Sending an old
quote and quietly substituting a new rewrite would leave the reader trying to
approve something different from what they had just read.

Our first build kept the proposal's normal 24-hour lifetime and printed its
UTC expiry in the email. Review caught the practical problem: people open mail
days later. The reviewed implementation keeps a preview quoted in a sendable
email for seven days and names that date in the message. Draft-only previews
keep their normal lifetime. Preparing a preview reserves the existing free
edit; the credit is consumed only when the reader explicitly applies it.

We also had to distinguish preparing mail for review from being about to send
it. A bulk draft operation should not generate hundreds of previews that may
never leave the queue. New generation is admitted only for a recipient the
sender has claimed, after the existing suppression, holdout, buyer and
frequency checks. A draft operation can reuse a live preview. It cannot start
a new model call for an unclaimed recipient.

Visitors in the report experiment's 15 percent holdout keep the existing email.
They should not receive a message promising a preview their report will not
show. Email draft creation also stays separate from experiment exposure:
subject impressions belong to actual sends, and the stored subject variant
follows the draft through open and click attribution.

Reusing the product's checks did not make manual review unnecessary. In one
validation attempt, a line about implementing features became a claim about
building the systems themselves. Both automated checks accepted it. We rejected
that pair and added a guard that preserves the feature-level contribution.
The original words can describe a narrower role than a fluent rewrite implies.

Ten selected real validation examples averaged about $0.0065 per preview. All
28 validation attempts, including rejected and superseded outputs, cost about
$0.16. Those are development observations, not a production acceptance rate.
Reopening an existing pending proposal needs no new generation.

During initial layout review we rendered three synthetic recipients at desktop and phone
widths, plus an images-off version of each. Those nine browser captures check
that the useful text, button and unsubscribe footer survive the layout. They
do not prove delivery, inbox authentication or how every mail client renders
the message. Those checks and the operator's copy approval still precede a
customer send.

We do not yet know whether putting the finished rewrite in the email will
bring people back or create paying customers. The readout follows the sent
draft to report clicks, applied previews, offer clicks and payments carrying
verified attribution. A click is counted as a click. A payment needs a
successful charge. The first question after launch is whether readers use the
edit we actually saved for them.
