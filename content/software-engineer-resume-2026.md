---
title: "Software engineer resume tips for 2026: what 500 real resumes get wrong"
slug: software-engineer-resume-2026
date: 2026-10-02
author: "RezScore"
tags: [resume, software-engineering, bullets, careers, data]
description: "We pulled 500 software engineer resumes. Strong verbs everywhere, results almost nowhere: about 1 in 11 bullets says what changed. Here is how to fix yours."
image: /static/blog/images/software-engineer-resume-2026/social-card-v1.png
image_alt: "Eleven resume bullet marks in a row, one highlighted in orange, under the headline 1 in 11 bullets says what changed"
image_width: 1200
image_height: 630
draft: false
---

*The numbers below come from a sample of real resumes, measured in aggregate. The before and after examples are composites we wrote for this post, not anyone's resume, and the facts inside them are invented so they can be shown.*

Software engineer resumes describe the work well and rarely say what it changed.

We pulled 500 software engineer and developer resumes uploaded to RezScore since January 2025 and measured their bullets against the two questions a hiring manager asks: what did this person build, and what was different afterward? The first half of that question is answered on almost every line. The second half is answered on about one line in eleven.

That gap matters more in 2026 than it used to. The market is still large, but it is more selective, and the entry level is harder than it has been in years. We covered the posting data and the unemployment numbers in [the software engineering job market in 2026](/software-engineering-job-market-2026/). When a hiring manager has more qualified applicants than interviews, "Built X with Y" describes what everyone in the pile did. Saying what X changed is how one resume stands apart.

## Short answer

In our sample:

- **About 1 in 11 bullets** carries a number that measures a result: a percentage, a dollar amount, a time saved, a multiplier.
- **42 percent of resumes** had no result numbers at all. The typical resume had one, out of sixteen bullets.
- **Weak openers are rare.** Fewer than 2 bullets in 100 start with "Responsible for," "Worked on" or "Helped." The usual advice to "start with an action verb" is already followed.
- **Testing and production work almost never make it into bullets.** Testing shows up in about 2 bullets in 100, production and operations work in about 5, even though four in ten resumes mention a testing tool somewhere.
- **AI tools are listed, not explained.** Three in ten resumes mention AI tools. Fewer than one in ten of the bullets that mention them says what the tool changed.

The fix is one fact per bullet, and most of those facts already sit in a dashboard, a CI history or a ticket tracker.

## 1. Strong verbs, missing results

About a quarter of all the bullets in our sample start with one of four words: **Built, Developed, Designed, Implemented.** Those are accurate verbs for what engineers do. The problem is where the bullet stops.

**Before.** Built a REST API in Node.js for the order service.

**After.** Built the order-status API (Node.js) that replaced a nightly batch export, so support saw order changes within a minute instead of the next morning.

*What changed.* The technology stayed. What it replaced and what that meant for the people using it got added. No percentage needed: "within a minute instead of the next morning" is a before and after anyone can verify.

**Before.** Implemented caching with Redis to improve performance.

**After.** Added Redis caching to the product-search endpoint and cut p95 latency from 900 ms to 220 ms.

*What changed.* "Improve performance" became the endpoint, the metric and the two values. The numbers came from the team's latency dashboard, two weeks before and two weeks after the change.

"Improved," "optimized" and "enhanced" are the tell. Each of those words promises a number and then does not deliver it. If you wrote one, the measurement probably exists somewhere.

## 2. Size is not the same as change

About a quarter of the bullets in our sample contain some number other than a date. Two thirds of those numbers are not results. Many describe size: team members, services, terabytes, users. Size is useful context. It is not a result.

**Before.** Worked in a 6-person team maintaining 14 microservices.

**After.** Owned deploys for 14 services on a 6-person team and moved them from Jenkins to GitHub Actions, cutting median build time from 18 to 7 minutes.

*What changed.* The size stayed, because it gives the reader scale. A change got attached to it. The build times came straight from the CI run history, which keeps every run.

A good bullet often has both: the size tells the reader how big the problem was, and the result tells them you solved it.

## 3. Testing and production work are invisible

Four in ten resumes in our sample named a testing tool or practice: pytest, Jest, JUnit, Cypress, test-driven development. More than a third of those resumes name it only in a skills list and never in a bullet. Across all the bullets in the sample, testing came up in about 2 in 100. Production work (on-call, incidents, monitoring, latency) came up in about 5 in 100.

That gap costs people. As our [job market analysis](/software-engineering-job-market-2026/) found, employers increasingly expect engineers to use AI tools while still owning correctness. Tests and production ownership are how a resume shows the second half.

**Before.** Wrote unit tests for backend services.

**After.** Raised test coverage on the billing module from about 40 to 85 percent; the next two quarters shipped without a billing hotfix.

*What changed.* Coverage numbers live in the coverage report. "Without a billing hotfix" came from the incident tracker. Both are easy to defend in an interview.

**Before.** Participated in the on-call rotation.

**After.** Covered one week in five of on-call for the payments service and wrote runbooks for the three alerts that paged most, which ended the repeat pages for all three.

*What changed.* The rotation became a responsibility with a result. The pager history shows which alerts fired and when they stopped.

## 4. AI tools without a verification story

Three in ten resumes mention AI tools: Copilot, ChatGPT, LLMs, LangChain, prompt engineering. Of the bullets that mention them, fewer than one in ten carries a result number. Here is the usual shape:

**Before.** Leveraged GitHub Copilot and ChatGPT to accelerate development.

**After.** Used an LLM to draft migration scripts for 60 legacy SQL reports, checked each one against the old report's output, and finished the migration in three weeks instead of the planned eight.

*What changed.* The tool became a workflow, the workflow got a check, and the check got a result. "Checked each one against the old output" is the part a hiring manager is looking for, because it shows you can trust what the tool gives you.

## 5. Smaller things worth fixing

- **A GitHub or portfolio link.** About a quarter of resumes in our sample had one. If your best work is public, link to it. If it is not, a link to an empty profile does more harm than none.
- **Filler adjectives.** About one resume in five used "passionate," "team player," "detail-oriented," "results-driven" or similar. These take space that a result could use. Cut them.
- **Length.** The typical resume in our sample ran about 575 words, and the typical bullet about 13. Almost no bullets ran past 35 words, so cutting words will not fix most resumes.

## Where engineers find their numbers

Much of an engineer's work is already measured and recorded somewhere. The table below lists where to look.

| What you did | Where the number lives |
| --- | --- |
| Made something faster | Latency dashboards (p50, p95), load test results, page speed reports |
| Made the build or deploy faster | CI run history, deploy logs, release calendar |
| Made something cheaper | Cloud billing console, cost explorer, the invoice before and after |
| Made something more reliable | Incident tracker, pager history, error tracker, uptime reports |
| Shipped a feature people used | Product analytics, feature flag reports, support ticket volume |
| Made the team faster | Pull request history, ticket cycle time, onboarding time for new hires |
| Built a school or side project | Sign-ups, GitHub stars, downloads, the number of people who used it |

Do not have access anymore? Use the number you remember and round it down, or describe the before and after state in words. "Cut a nightly job that took most of the night to under an hour" is honest and specific without a percentage. More on that in [how to quantify your resume without inventing numbers](/how-to-quantify-your-resume-without-numbers/).

## How to fix your resume in an hour

1. **Pick your five most recent bullets.** They sit at the top of the page.
2. **For each, ask what was different afterward.** Faster, cheaper, more reliable, used by more people, less work for someone else.
3. **Find one fact for that change.** Use the table above. One number or one before and after is enough.
4. **Rewrite the line around the change.** Keep what you built and the technology, and add what was different afterward.
5. **Check every number.** If you could not explain where it came from in an interview, replace it with something you can.

## Let RezScore do the first one with you

Upload your resume to the [free resume grader](https://ai.rezscore.com/). The report picks one line from your resume and asks you one question about it, with an example of the kind of answer that helps. Give it a fact you know is true, like a number from a dashboard or what changed after you shipped, and it rewrites the line using only your resume and your answer. You see the before and after side by side and decide whether to keep it. Your first edit is free.

## Questions people ask

**Should a software engineer resume be one page?** One page if you have under about ten years of experience, two if you have more and every line earns its place. A second page of unproven bullets hurts more than it helps.

**Should I list every language and framework I know?** List what you would be comfortable being interviewed on. A long skills list with no bullets behind it reads as keywords. Put your strongest tools in the bullets where you used them.

**Do I need a number on every bullet?** No. You need a specific on every bullet. A number is the easiest specific, but "replaced a nightly batch export" is specific too.

**Should I mention AI tools?** Yes, if you use them, and say how you checked the output. That sentence is what separates a skill from a buzzword.

**I am a new grad. Where do my results come from?** Projects, internships and coursework. How many people used your app, what your internship project replaced, how fast your solution ran compared to the baseline. A real project with one real user number beats three tutorials.

For more examples outside engineering, see [weak resume bullets, rewritten](/resume-bullet-points-before-and-after/).

## Methodology

We pulled 500 resumes uploaded to RezScore between January 2025 and September 2026 that name a software engineer or software developer role, keeping one resume per person and excluding test accounts. All figures were computed in aggregate by automated text analysis. We read a small random set of bullets only to check the matching rules, and no resume text appears here.

Resume-level figures (AI tools, testing tools, GitHub links, filler adjectives, word count) use all 500 resumes. Bullet-level figures use the roughly half of the sample, 232 resumes, whose bullets could be reliably separated by their bullet characters; that subset contained more than 4,000 bullets of five words or more.

A "result number" is a percentage, a dollar amount, a multiplier (such as 3x), a duration (such as 200 ms or two days), or a quantity with a thousand or million suffix. Years and common version numbers were excluded when counting "some number other than a date." Weak openers were matched at the start of a bullet against phrases such as "Responsible for," "Worked on," "Involved in," "Helped" and "Assisted." Testing, production and AI-tool mentions were matched against fixed lists of terms. Keyword matching undercounts results written in words and can misread some lines, so treat every figure as an estimate of the pattern rather than a precise rate.
