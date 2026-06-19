---
title: "Meet Jen: building an AI career advisor with a real voice"
slug: dev-meet-jen
date: 2026-05-12
author: "RezScore"
tags: [dev, ai]
section: dev
description: "How our chat advisor went from a generic bot to a resume-aware peer who gives specific advice and helps you act on it."
draft: false
---

Jen is the advisor who lives in the chat widget on RezScore. You upload a resume,
you get a grade, and Jen talks you through what to do next. That part has not
changed. What has changed, a lot, is how she talks.

The first version of Jen was a generic chatbot. It gave the kind of advice you
could get from any career blog. "Use strong action verbs." "Quantify your
achievements." All true, all useless, because none of it was about your resume.
This post is about how we rebuilt her voice on purpose.

## Peer, not tutor

The single biggest decision was to stop letting Jen sound like a tutor grading
your homework. A tutor hands you a checklist and a gold star. That register quietly
tells you the resume is a test you are failing, and it makes people defensive
instead of curious.

So we rewrote Jen as a peer. She talks like a knowledgeable colleague who has read
a lot of resumes and is happy to look at yours. She reacts, she has opinions, and
she is comfortable saying "I would not lead with that" the way a friend in your
field would. The goal is sitting next to someone who knows the terrain, not being
lectured by someone who holds the answer key.

One rule we locked in: if you push back, that ends the line of advice. If Jen
suggests something and you say it does not fit, she does not keep selling it. She
moves on. A peer respects that you know things she does not.

## Specific, not generic

Generic advice is the easiest thing for an AI to produce, and it is worthless.
"Add metrics" means nothing if you do not have a number to add. We pushed Jen hard
the other way: toward the specific line on your resume, the specific phrase, the
specific thing you could say better.

We even pulled back on the reflex to demand numbers for everything. Not every
strong resume is a wall of percentages. For a lot of careers, the fix is sharper
language and clearer scope, not a metric that does not exist. Jen notices what a
line is actually trying to say and helps you say it well, instead of chanting
"quantify" at every bullet.

## Resume-aware

None of this works unless Jen has actually read your resume. So she has. Your
parsed resume, your grade, and the specific strengths and gaps we found all go
into the conversation, so when Jen talks about your summary or your most recent
role, she means yours. We had to fix real bugs here, including cases where a long
resume got cut off before Jen saw the bottom of it. An advisor who has not read
the whole document is just guessing in a friendly voice.

## Helping you act

Advice you cannot act on is just noise. So Jen does not stop at "you should rewrite
this headline." She can open the builder and help you do it, turning a suggestion
into an actual edit. The aim is a colleague who reads your resume, tells you the
true thing, and then helps you fix it, all in one conversation.

She is still evolving. But the voice is the part we care most about, because it is
the difference between advice you ignore and advice you use.
