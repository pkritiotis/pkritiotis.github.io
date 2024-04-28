---
layout: single
title:  "Engineering Execution - My Aha Moments as an Engineering Manager"
date:   2024-04-27 16:36:00 +0300
tags: software-processes software-engineering-philosophy
toc: true
header:
    og_image: /assets/images/Sustainable-Technical-Decision-Making-OG.png
# classes: wide
---
Arguably, the most important engineering management trait is the ability to execute; delivering the results that achieve your company's goals. Throughout my career as an engineering manager, I came upon some execution 'Aha Moments' that have been incredibly revelationary for me.

Some of them, are obvious in hindsight, but can be easy to miss. 
These principles transformed the way I approach my work in terms of execution and allowed me to have a more systematic and straightforward way on managing my actions on delivering results.

In this article, I write about some of the 'aha moments' I've come across as an engineering manager on the less obvious - at least to me at the time - aspects of execution. I hope some of them can be useful to you as well.

# 1. The execution focus is not static. Define it, align, revisit
*What* we build in software is all about prioritization. Usually we have an infinite list of ideas and feature requests that we want to build but we only need to pick some of them. How do we prioritize? Based on the business direction; the product focus.


What we often miss, is that the *How* part, how we want to execute and deliver, is not only tactical, it also includes a strategic part - the engineering execution focus. 


By engineering execution, I'm referring to the set of processes, and engineering methodologies and practices that enable a team to deliver the the product requirements.

And here comes the first aha moment:

✨**The execution focus, what you optimize for, is dynamic.**  **It's what your company needs from you at that specific time.**


This is true for individual contributors, but much more important for engineering managers when you shift the focus of the team.


So what do we optimize for when it comes to execution?

**What we focus on changes from time to time depending on the targets**. It could be knowledge sharing when we're dealing with a bus factor situation at the expense of throughput. It can be throughput in other cases at the expense of knowledge transfer and stability if we absolutely need something out otherwise the business will collapse. It can be stability at the expense of throughput because the technical debt is killing the ability to move fast in future development.


The most important takeaway from this first engineering execution nugget is that you always need to understand, define and align on what's your current focus - you should ask for it and ensure you are aligned with your manager. And as a manager, you need to communicate this to your team and apply it through your execution process.

## 2. Don't obsess about the perfect team utilization

One of the most stressful thoughts you have as a manager is optimal team utilization in building product features. And when things go wrong (and they will), sometimes you don't have a product backlog ready for development for your team. Not because there is no work, but because you are temporarily blocked. This can happen for many reasons including dependencies, sudden change of focus etc.

Here's the third aha moment:

✨**Optimizing or striving for team utilization is not your goal; delivering results is.**


If your engineers are unutilized for some period, embrace it - invest time for ther personal growth or other items that contribute to the company's success even if they are not immediately needed. 
As one of my managers says, inevitably, at some point the work will come and it'll be heavy. Let the team charge, they'll need the energy later.
This of course doesn't include permanent under utilization - something's wrong there

+queue theory
+production flow offsets

# 3. Fix your execution process, optimize everything else later

As a manager, the most important resource you manage is time. There is an infinite number of tasks waiting to be completed, and you need to choose wisely how much time you spend on which.

When joining a team/company that is in a very bad shape, many things can be wrong: there is no documentation, the knowledge is consolidated to specific people, there is a slow release process and the team has no goal-setting framework. How do you start?

✨**Fix your execution process first, then optimize anything else.**

If you don't have a streamlined process for execution don't work on goal setting, performance management and career ladder. Execution is what keeps your company alive, and it's what you're hired for. As long as you deliver you can survive. You can always optimize other areas later.

# 4. Ensure Stability first, then optimize for throughput

When we think of execution most people think about throughput. How quickly and how frequently we can deliver features to the end user.

We often forget about a crucial dimension that enables throughput: stability.  If you have a constant stream of critical incoming bugs interrupting your normal workflow all the time, and if it takes you ages to troubleshoot and resolve an error, your execution flow has a problem.

✨ **Define the processes and ensure stability first, then focus on optimizing the throughput using other technical and workflow techniques.**

Stability enables throughput, and throughput helps stability. The two main metrics that you can optimize for is time to recovery, and failure rate per release. The less critical issues you face and the quicker you resolve them if they occur, the more features you can work on without interruption .

# 5. Don't get distracted by output, focus on outcomes

That's one of the most common advise you see in all kinds of software management books with regards to execution. 

And it comes in multiple flavours. It's one of the fundamental Lean principles - focusing on global outcomes instead of local ones. Andy Grove mentions output over activities. Accelerate mentions outcomes vs output. 

No matter what you want to choose this is absolutely important and one of the most common mistakes. Obsess over a KPI, instead of the global objective. Optimize the availability of your service at the expense of a new competitive advantage.


✨ Continuously align on global goals vs local goals and output.

# 6. Execution is not just about process

Usually when we think about execution we are thinking about processes like workflow management, planning, prioritization and other areas like org structure, goal frameworks etc.

What is not obvious is that execution can be optimized on the technical level and also at the people level.

Technical:
- Architecture matters
- Continuous delivery principles
- Tooling
People management
- Feedback
- Meetings and Decision making
- Training

You can also enable execution through technical methodologies and you can promote execution through people management.

Technical methodologies are continuous delivery, lightweight 

# References

[^amazon-two-way-doors]: [Two-way Doors - Daniel Slater - AWS](https://aws.amazon.com/executive-insights/content/how-amazon-defines-and-operationalizes-a-day-1-culture/)

[^complexity-bias]: [Complexity Bias - Farnam Street](https://fs.blog/complexity-bias/)

[^neglect-of-probability]: [Neglect of Probability - Wikipedia](https://en.wikipedia.org/wiki/Neglect_of_probability)

[^product-driven-development]: [Product Driven Development - UsabilityHub](https://usabilityhub.com/blog/product-driven-development)

[^definition-of-done]: [Definition of Done - Leading Agile](https://www.leadingagile.com/2017/02/definition-of-done/)

[^taylor-town]: [11 Ways to Shave a Yak - Taylor Troesh](https://taylor.town/shave-a-yak#4)