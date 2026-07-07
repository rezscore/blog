---
title: "Your resume gets two grades now"
slug: dev-your-resume-gets-two-grades-now
date: 2026-07-06
author: "RezScore"
tags: [dev, product, grading]
section: dev
description: "For twelve years we graded the document. This week we started grading the candidate too. Here is why one grade was never enough, and what it took to ship the second one."
draft: false
---

For twelve years, RezScore has answered one question: how good is this resume?
Upload a document, get a letter grade in about a second. That grade measures
presentation - structure, evidence, phrasing, whether the page sells what is
on it.

But that was never the question people were actually asking. Watch real
sessions and the question underneath is always: **am I going to get the job?**
And a presentation grade alone cannot answer it, because two very different
people can hold the same B-. One is a strong candidate whose document
undersells them. The other has a beautifully polished page with not much
behind it. Same grade, opposite problems, opposite advice.

So as of this week, uploads get two grades.

**Presentation** is the grade we have always given: how persuasively the
document communicates what you bring. **Qualifications** is new: how strong
you are as a candidate for the role you are targeting, judged from the
substance on the page - the skills, the experience, the trajectory -
independent of how well the document dresses it up.

The two grades come with a map. Your resume lands as a dot on a
two-axis chart: Qualifications across, Presentation up. The four quadrants
are four different strategies, and they name the honest next move:

- **Apply to jobs.** Both hold up. Stop editing, start applying.
- **Polish your resume.** The person is ready; the paper is not. This is the
  most fixable position on the map, and more people sit here than you would
  guess.
- **Build your skills.** The paper reads well, but the substance behind the
  target is thin. No amount of polish closes that gap, and telling you
  otherwise would be malpractice.
- **Get started.** Both need work. The plan is sequenced: apply where you
  qualify today, close the biggest gap in parallel.

That last sentence took us several drafts. An early version said "skills
first, then the resume," and a reviewer pointed out that not everyone has the
luxury of pausing a job search to study. Nobody gets told to stop job-hunting.

A few things we held ourselves to while building this:

**The verdict is honest, even when it stings.** If the document is not
competitive for the target, the card says so in the first sentence. A grader
that flatters you is a friend; a grader that levels with you is a tool.

**Nothing is invented.** The qualifications read judges only what is actually
on the page. No imagined credentials, no fabricated statistics, and no
"chance of getting hired" percentage - anyone who sells you one of those is
guessing.

**The target is yours to correct.** We infer the role you are aiming at from
the resume itself, and we show our inference with an edit button next to it.
If we guessed wrong - you are a marketer going after engineering roles, say -
one tap fixes the whole read. The mistake we refused to repeat was guessing
silently.

**Speed is a feature.** The presentation grade still lands in about a second,
and the full two-axis read follows a few seconds behind it, filling in while
you read. Getting that fast required splitting our analysis into a quick
first pass and a deeper background pass, because our first version made
people wait twenty seconds staring at a spinner, and twenty seconds is an
eternity. We wrote up the gory engineering details in a companion post for
the dev-curious.

The two-grade card is rolling out as an experiment, which is how everything
ships here: half of uploads see it, half see the classic report, and the data
decides. If you want to see where your dot lands,
[the grader is free, same as always](https://ai.rezscore.com).
