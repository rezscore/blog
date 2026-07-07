---
title: "A Crash Course in Natural Language Processing to Beat the ATS"
slug: a-crash-course-in-natural-language-processing-to-beat-the-ats
date: 2019-02-27
author: "RezScore"
tags: [ats, nlp, job-search, resume-tips]
description: "How Applicant Tracking Systems really parse your resume, and the data-science principles you can use to get past them."
image: https://miro.medium.com/v2/resize:fit:1100/1*BhMyphiISdHqLVw49t8yXg.jpeg
medium_slug: a-crash-course-in-natural-language-processing-to-beat-the-ats-6352eaab986b
draft: false
---

## The "Take No Prisoners" approach to job hunting

Every job search boils down to a numbers game, with the odds stacked high against you. You can expect you are competing against upwards of 250 other candidates for any given position, chalking your odds at a brutal 0.4% of landing the job.

This means the typical candidate will need to send out over a hundred resumes before you are statistically expected to get a job. If you are a so-called "10x" candidate, and your odds jump to 4%, then you would still expect to send out at least 11 resumes before you are likely to land a job.

For this reason, you can get big gains by optimizing at the top of the hiring funnel, to get your resume past the *Applicant Tracking System (ATS)*. The ATS is often built to narrow down hundreds of resumes to about a dozen, so hiring managers can spend their precious time scrutinizing the top tier. Most resumes are rejected or hidden by computers before a human ever takes a good look. Therefore, a simple understanding of how the ATS is built can be leveraged for big gains in your job hunt.

## Competing in a Fractured ATS Marketplace

If you were a designer optimizing a post for social media sites, you could spend some time to get it looking sharp on Facebook, maybe tweak it for Instagram, and then take off early for the day knowing you got a solid share of the market covered. If you are designing a resume, your task is not so easy. No single ATS has ever come close to cornering the market.

You can try to optimize your resume for biggies like Taleo and Greenhouse, but there's less than half a chance the job you end up applying to will use this system. Complicating matters, a large slice of the market ("Homegrown") is not a clever ATS name, but rather represents the large share of companies that just built their own in-house system with unknowable quirks. Larger ATS like Greenhouse offer a directory of third party plugins that may end up doing the screening, further fragmenting the market.

You would be foolish to spend much time or energy optimizing for thousands of different ATS. Every ATS working to filter your resume is built on very similar principles of data science. Therefore, this basic crash course in data science techniques will provide you all the knowledge you need to beat the ATS.

## Natural Language Processing 101

The history of natural language processing (NLP) can roughly be divided into "*deductive*" and "*inductive*" phases.

* For the first half of data science history, the field was dominated by a "*deductive*" approach, in which programmers would write complicated programs to mimic the rules of grammar, and then use these rules to try to deconstruct real world language.
* The "*inductive*" approach reversed this, starting by collecting very large sets of real world language, and then feeding it en masse to *machine learning* libraries and letting the computer figure out the rules.

Instead of getting your resume past thousands of ATS, it's far simpler to think how you can get it through programs designed in either of these eras.

## The Deductive Phase

The *deductive* approach to NLP is similar to sentence diagramming you may have had to do in your grammar classes.

> But how he got in my pajamas, I'll never know...

These *deductive* approaches were necessary due to the scarcity of data in the early days of computing. For decades computers could not always connect to the internet, and transfer speeds were sluggish even when they were connected. For reference, data used to be moved around by floppy disk, which could carry up to 1.44 MB (about half the size of a modern resume). Nowadays, if you can't transfer this amount of data every second you will complain loudly and angrily that your internet is broken.

Since good datasets were tough to come by, *inductive* methods were difficult and the *deductive* approach dominated. For several decades Natural Language Processing consisted of trying to encode every rule of linguistics into computer code and translate sentences accordingly.

As it turns out, language is incredibly complicated. You can create grammatically correct sentences that are nonetheless highly ambiguous. A classic example sentence used to trip up NLP is "Time flies like an arrow, fruit flies like a banana."

Parsing language is tough enough, and human idiosyncrasies make it much tougher. For example, most human readers won't particularly care if you omit periods at the end of your bullet points. The NLP parser may misinterpret it as an incomprehensible run-on sentence.

> ONE MISPLACED PERIOD CAN HELP SOMEBODY ELSE GET A JOB.

Programmers burned through millions of dollars in early AI hype cycles trying to build increasingly complicated models to diagram complicated sentences. Progress was slow, and onlookers scoffed that natural language processing was dead on arrival.

Haters gonna hate, but the decades of research was not for naught. Much of the research became the basis of many open source libraries today. This phase enabled open source libraries like Stanford NER and Python's NLTK to become the popular and powerful tools they are today. These libraries continue to improve, thanks in large part to general advances in AI spurred by the rise of the inductive phase.

## The Inductive Phase

Once people became connected to high-speed internet, the amount of data available exploded and artificial intelligence swiftly became more sophisticated. Larger datasets allowed the *inductive* approach to more successfully process language. Instead of trying to program a complicated series of rules and hope the data would fit, it became possible to feed a massive set of data to computers and see if the computer could isolate the patterns and construct rules.

The inductive approach provided a shot in the arm to the stagnant field of Natural Language Processing. After decades with little progress, your messaging programs got pretty good at completing your sentences almost overnight.

Of course, much work remains. Data science today requires more machine processing power than it does human brainpower. Nowadays you can simply throw tens of thousands of pictures of cats at any number of deep learning libraries, and out pops a pretty good cat classifier.

The catch with this approach? The rules that are generated using this inductive approach generally get wired into a black box, making it almost impossible to reverse engineer. Heisenberg's uncertainty principle seemingly has a cousin in information processing. You can get the right answer or you can see how to get the answer, but you can't see both. If true, we predict machine learning will one day solve the traveling salesman problem, but the efficiency of the solution will be impossible to determine and academics will continue to debate whether or not P vs. NP was actually solved.

More practically, the black box of machine learning has tangible issues with how you could build an ATS on the inductive approach. If you want to understand why an ATS built on last generation's *deductive* approach missed a great MIT candidate, you can read through the code and change your regular expression to catch for M.I.T. with periods. With the *inductive* approach there's no way to know why the machine decided as it did. If you realize the error, the best way to fix it is to add dozens of cases to your dataset, retrain your engine from scratch, and hope you didn't introduce any unintended errors.

For more information about machine learning that's powering this *inductive* phase of data science, grab your favorite big data set, check out TensorFlow, Keras, or PyTorch, and get ready to spend up on GPU processing credit.

## Optimizing Toward These Two Approaches

As we've seen, the landscape of ATS software is very diverse. Some companies have been using the same ATS software for decades. Some companies use more modern systems based on technology like the RezScore API. If you are applying to a few dozen jobs, you will likely confront a blend of both strategies.

The trend in the long-term favors the inductive approach, but there are some obstacles that will slow its onset. The inductive techniques are only as good as the size of their training dataset, so startups to midsize companies will have an insufficient number of resumes to run any good machine learning. With so many ATS out there, many will never get the scale required to run truly state-of-the-art machine learning. As a result, you may assume that the average ATS is currently likely to run *deductive* techniques.

## Optimizing Toward a Deductive ATS

If the ATS is taking the old-fashioned *deductive* approach, you can expect that programmers are writing the rules to screen out your resume. You may not know exactly how the ATS is doing this, but you can count on a few things:

* Resume parsing is quite hard and many cannot accomplish it 100%.
* Because resume parsing is hard, the programmers will always be looking for shortcuts and easy workarounds.
* Because the workarounds are imperfect, programmers are looking to codify the most important filters for a company.
* Therefore, you can maximize your chances by preparing your resume to pass not just the *most sophisticated* ATS checks, but also the *least sophisticated*.

In other words, a good ATS will be relatively well programmed, whereas a poor one will show some common errors. If you consider both extremes, you will maximize your chance against any ATS.

The other rule of thumb that emerges is to keep things simple! In fact, this is pretty good advice just generally. All parsers can parse a simple plain resume with minimal formatting. What use is the most beautifully designed resume if the parser knocks it out before anybody sees it?

Simply think about what kind of resume the hirer wants to let through the applicant tracking system, make your resume fit this, and you'll get through 99% of ATS. Here are some particular things hirers care about and how they may code for it.

### Geography

*Objective:* Hire locally so as to not to pay relocation bonuses when possible.

*Common ATS Approaches:*

* Parse out all addresses on the resume and contextualize them.
* Addresses closer to the top or most recent job position receive greatest precedence.
* Geolocate the candidate relative to the job in question and score the candidate inversely to their proximity.
* Strict matching: the job says New York, NY, but Jersey City is in NJ, not NY.
* Ignoring the first listed address in favor of the address associated with your last job.

*Tips:*

* List your current city the same as the job is hiring. List the general metropolitan area that best matches the position. (If the job is in *Brooklyn*, and you live in *Brooklyn*, write your address as *Brooklyn*. If you apply to the *Brooklyn* job but live in *Manhattan*, write your address as *New York City*.)
* If your last job position was in a different city than you currently live, or if you have relocated a lot, consider omitting location on your work experience.
* Format geographic terms as City, XX (two letter state abbreviation). This is how about 90% of people format it, so this is what parsers seek.
* If you live on London Street, consider omitting this. You don't want a badly programmed ATS to reject you because it thinks you're in the UK and need a visa.

### Skills

*Objective:* Ensure the applicants have all required skills.

*Common ATS Approaches:*

* Gauge the relative time value of skills; if you used Python on a job you started five years ago, credit your application with five years of Python.
* Use clustering and/or document similarity to interpolate missing keywords based on similar resumes.
* Calculate the similarity between the job description and the resume.
* Reject resumes that do not have specific mandatory keywords.
* Grant additional weight to keywords that appear close to the top of the resume.
* Grant additional weight to keywords that appear frequently throughout the resume.

*Tips:*

* Make sure major keywords in the job description exist in your resume.
* Make sure spelling and capitalization of search keywords is standard (and matches the job description).
* Move technical skills section to the top of the document for applications that will go through an ATS (if you know it will only be read by a human and not an ATS it is better at the bottom).
* Keep your experience section formatted like most standard experience section templates, so the parser can easily find your details.

### Education

*Common ATS Approaches:*

* Incorporate school rankings to weight candidates.
* Parse out schools from email .edu extension.
* Parse out the last school you attended and present this in the dashboard summary of candidates.

*Tips:*

* If you have an alum email address from your school, use it. Only a few parsers check for this, but more importantly it is likely the ATS will commonly surface the email, and you want hiring managers to associate you with the school's credibility.
* Write out full school name and include common abbreviations. Washington University of St. Louis (WUSTL) is more likely to trigger a match than either term alone.
* These tips presume your schooling was good. If your schooling is poor, try to get an online certificate from good schools so the school name gets vacuumed into the resume parser.

For all other sections of your resume, keep these principles in mind and you will find you can usually write a simple, high quality document that can readily be re-used across job sites.

## Optimizing Toward an Inductive ATS

Although the deductive approach is still the most common, technological advancement is quite rapid and we expect the balance will tip over the next several years. Even though most new startups don't have sufficiently large datasets to run machine learning, they can plug in to outside resources.

As we saw, machine learning techniques produce a black box that cannot be readily understood. Doesn't this compound the difficulty for the job seeker? Not only does your resume go into a black hole, now it also takes a trip through a black box?

Fortunately, the approaches to natural language processing are all publicly documented and easy to interpret. They are always going to follow the same general pipeline. This is but one of many such methods, but they all boil down to the following process:

1. Clean the data
2. Train a model
3. If model is unsuccessful, go to 2
4. Success!

We dive into these steps briefly.

### Step 1: Data Cleaning

The top of the funnel will pretty much always look the same. Most machine learning programs will use a bag of words model that essentially cuts your resume into individual words. It's as if you cut each word out of your resume and dumped it into a bag.

A flaw of the "bag of words" approach is that it has a tougher time understanding context than many of the deductive methods attempted. To fix this, it's common to parse out n-grams, i.e. pairs of words, or triplets, etc. It generally needs to be kept small, since you get best results when there's some coincidence of terms between documents. Few resumes have 100 consecutive words in common with another person's resume, unless they are plagiarists.

How can you use the fact that AI thinks in 1-to-2 word phrases in your favor? Well, if you want to get cute, you can try to trick the ATS receiving a bag of words that cannot contextualize things. You may see a bump if you happen to live on *Harvard* Avenue, or if you include the words *Massachusetts*, *Institute*, and *Technology* in three separate locations throughout your resume you may get a boost even if you did not go to the school.

More generally, any system that relies on the common bag-of-words approach is more susceptible to keyword stuffing than prior methods. Some people have joked that you can fool an ATS by copying a job description into your resume and blending it into the background. This hack has more merit against machine learning solutions than you might think, but if you're wholly unqualified then you're likely to get rejected by the hiring manager instead of the ATS.

### Step 2 and 3: Iterate

There's not a lot of ways you can use these steps to your advantage. The only point worth noting is that very robust machine learning costs a lot of expensive GPU time. Companies may not have interest in repeating the process to perfection, just to adequacy. In other words, the algorithm that is screening you may lack polish and have some rough edges.

### Final Step: Success!

The training will be iterated upon endlessly until they eventually arrive at the results they want. The "success function" they train against is often opaque, but basically amounts to "does this generate the results I would expect". The hiring team just has a gut feeling about the kinds of candidate they want to see, and will positively reward the system if it starts to generate this list.

A major problem with these approaches is that they are essentially unaccountable methods to enshrine deep-seated biases. In late 2018 Amazon got in trouble for producing an AI recruiting tool that was sexist. Of course, it would be relatively simple to build a *woke* ATS by adding a "constraint function" to the training process that required results meet certain diversity requirements. Constraint functions are quite common in machine learning, so it's anybody's guess why machine learning trainers are using the newest tools to preserve bias instead of combatting it.

More generally, the machine learning algorithm is going to take the quickest path towards confirming the hiring manager's bias. Where prior ATS built on the deductive approach might give more attention to the whole document, the inductive approach may just need to recognize a great school and some good skills-based keywords and know its owner will be quite happy. Indeed, skills and some schools take on significant import within the black box, and actually get amplified well ahead of other factors.

## Conclusion

Ultimately, hirers are using their ATS as a varyingly advanced tool to help them see the candidates they want to see and exclude the candidates they don't want to see. Do they want to see that you enjoy snorkeling? It's possible. Do they want to see that you have the seven years of Java experience they asked for? Certainly. Focus your job hunt around the point of view of what the hiring manager wants and you'll avoid becoming a statistic.

*If you need to beat the ATS, RezScore is the best. Our [free resume grader](https://ai.rezscore.com/) has been vetted millions of times across all industries, and even powers screening for some school admissions, companies, and government programs. If you get a B or better at RezScore you are in great shape and should check out our new jobs board.*
