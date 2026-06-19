---
title: "Salary Forth"
slug: salary-forth
date: 2016-08-09
author: "RezScore"
tags: [salary, data-science, resume-tips, careers]
description: "How RezScore built the first crowdsourced salary estimator, and what 25,000 user salary reports revealed about pay, geography, grades, and education."
image: https://miro.medium.com/v2/resize:fit:1100/1*BhMyphiISdHqLVw49t8yXg.jpeg
medium_slug: salary-forth-de8206b8ebfd
draft: false
---

Recently a friend graduating from b-school asked us for a reliable measure of their earning potential. We were surprised to find no simple resources existed online. So we built the first crowdsourced salary estimator. Here's how we did it.

## Data: When everybody is above average

We initially thought to scrape data from other career sites as a jumping off point. Unfortunately, these resources largely suck. Some exaggerate salaries for publicity, some understate salaries to aid HR managers in their negotiations. Rather than try to untangle these biases, we chose to play to our strengths: we crowdsourced data from our awesome users with this cyclical method:

1. Give users our salary estimate when you upload your resume
2. Ask users to confirm salary estimates or submit corrections
3. Retrain the model

As of publication of this article we've received over 25,000 salary reports. Thank you! We're currently on the sixth draft of our model, and we continue to iterate quickly.

## Results: Mo Money Math Problems

We have tested many factors, and we currently regress three which prove strongly predictive. Working from least to most predictive:

**1. Geography:** To little surprise, proximity to wealthy zip codes is a good predictor of salary. The p-value is currently at a respectable .056

**2. RezScore Grade:** As a nice validation of our hard work, a good RezScore grade is a slightly better predictor of salary than geography, with a p-value of .045

**3. Bayesian Filter:** We trained a Bayesian filter (the technique commonly used to sort out spam email) to assess the probability of a resume's salary being "above average" or "below average". (Technically, below average was reserved for the 5th-33rd percentile, and above average for the 67th-95th percentile.) This method proved most effective.

A quick note to `R` junkies: it's too difficult to search resumes and job boards for `R`.

## Discussion: We Don't Need No Education

Keen readers will notice that we have tuned our model to set our median income about 10K underneath the reported income. Just as salary data reported by corporate sources are prone to bias, so too are data as reported by users. We found users tend to overreport their salary estimate between $5K and $20K. At first we assumed this was because of the tendency of job seekers to stretch the truth. However, there's a more benign explanation, which is that people in job hunt mode are optimistic that their next career move will carry a pay increase.

Some of the rejected data sources surprised us. Education achievement was a shockingly poor predictor of salary. We only observed 20% correlation between a user's education score and their reported salary. (On the education score scale, 2 is roughly a dropout, 10 an Ivy League Ph.D.)

Additionally, we've trained basic deep learning models and tested the prediction as an operand in our regression. Results are currently not predictive, but we expect better results will emerge as we accumulate more data.

## Conclusion

As of publication, 80% of our users receive salary estimates accurate within 10% of their actual salary. This accuracy is only possible because you helped us build RezScore as a community-powered resource. We again call for your help: if you're interested in data science, please contact us with your thoughts for how to improve our estimation.

Together we will overcome the frustrations of the job hunt. Please help us by confirming your salary estimate at RezScore.
