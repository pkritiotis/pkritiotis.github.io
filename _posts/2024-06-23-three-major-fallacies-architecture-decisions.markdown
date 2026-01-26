---
layout: single
title:  "3 Major Fallacies Engineers Make in Architecture Decisions"
date:   2024-06-23 23:00:00 +0300
toc: true
tags: software-architecture
# classes: wide
header:
    og_image: /assets/images/3-major-fallacies-OG.webp
---
Over the past 10 years, I've led and participated in numerous architecture designs, learning a lot from my mistakes and observations.

Architecture design is the foundation of successful software. It enables business strategy and vision, and it’s crucial for evolving a system without incurring huge costs as requirements change.

In this post, I’m sharing the top 3 fallacies I’ve seen in architecture decisions and how to avoid them. I like to call these the *Terrible Trio of Architecture Design*.

# 1. The Cool Factor, Shiny Objects Fallacy

When we see a new concept, tool, or paradigm, we’re instantly attracted to it. It's the well known shiny new toy tech pandemic.

I’ve fallen for this many times, and honestly, who hasn’t?

But a shiny new thing isn’t always the best solution.

Too many projects waste money, time, and resources on new technologies just because they seem cool and modern.

<p align=center>
<img alt="Shiny object" src="/assets/images/shiny-object.webp" width="400">
</p>


**How to Avoid the Coolness Fallacy**

Here are common pitfalls when choosing a new technology and how to avoid them:

1. **Adapting Your Problem to Fit a Modern Solution instead of the other way around**
  - Simple problems can become bigger if you force a specific technology. I admit to suggesting a big-bang monolith rewrite to microservices in the past. Shame!
  - **How to Avoid:** Ensure the technology solves your specific problem rather than fitting your problem to the technology.
2. **Overlooking the Complexity That New Tech Introduces**
  - New tech often means fewer docs, less community support, and a steeper learning curve. Consider the maintenance costs involved.
  - **How to Avoid:** Evaluate the learning curve, documentation, community support, and maintenance costs.
3. **Ignoring the Unknown Factors**
  - Many problems already have proven solutions. Introducing a novel solution can bring unexpected challenges.
  - **How to Avoid:** Research existing solutions and case studies. Be wary of unknown factors with novel solutions. If you’re not ready to accept the risk, reconsider the adoption.

Be aware of these pitfalls and be extra careful when transitioning to new technologies.

**The coolness bias is real**. The more aware you are of it, the better you can control it.

# 2. The Wishful Thinking Fallacy

*"But, we’ll eventually need it."*

No. You Aren't Gonna Need It (YAGNI). There’s a fine line between under-engineering and over-engineering.


<p align=center>
<img alt="Shiny object" src="/assets/images/wishful-thinking.webp" width="400">
</p>

As engineers, we tend to lean towards over-engineering, adding unnecessary complexity. And complexity can kill your project.

## The Play It Safe Fallacy

*“Let’s stay on the safe side even if it's significantly more complex because we don’t know how much traffic we’ll have.”*

I’ve heard this many times, and I’ve used it myself. Shame on me! **This is wishful thinking disguised as caution.** Keep it simple and less complex until you absolutely need it.

Extreme proactiveness is not a game you want to play in architecture design.

**How to Avoid Wishful Thinking**

Here are a few rules to have in mind when making architecture decisions:

1. If you are not 100% sure you’ll need it in the next couple of months, don’t even think about it.
2. Aim for extensible and scalable code, but if it dramatically increases complexity without needing it immediately don’t invest in it.

# 3. The False Equivalence Fallacy

*“We miss module ownership. Microservices are well known for solid ownership boundaries. Look at Amazon’s ways of working. That’s what we need.”*

Meanwhile, the total engineering team at your company consists of just 10 engineers across two teams.

<p align=center>
<img alt="Shiny object" src="/assets/images/false-equivalence.webp" width="300">
</p>

This might be an extreme and obvious example, but false equivalence fallacy can be tricky to recognize. It happens when you make incorrect comparisons by focusing on a few selected characteristics of a case study or success story to justify a solution.

The key here is context. You need to put things into perspective and understand the full context, including the implications of such options. Just because a solution works for Amazon doesn’t mean it will work for your smaller team.

**How to Avoid False Equivalence**

1. **Understand the Full Context**: Look beyond the surface and analyze the entire situation.
2. **Consider Scale and Resources**: Ensure that your comparisons account for differences in team size, budget, and resources.
3. **Evaluate Appropriateness**: Determine whether the solution fits your specific needs and constraints.


# Conclusion

That’s the trinity of architecture design fallacies: Cool shiny objects, wishful thinking, and false equivalence.

Avoid these pitfalls at all costs. Here are a few guidelines to help you:

1. **Awareness**: Be aware of these biases. Recognize them and work against them.
2. **Simplicity over Complexity**: Choose simplicity if it solves the problem, even if it’s not future-proof.
3. **Evaluate Cons**: Document the downsides of your proposal.
   - If you can’t find any, dig deeper. There's always a tradeoff.
   - When you do find them, re-evaluate your approach.
   - Remember, architecture decisions are all about tradeoffs. There’s no perfect solution.

Stay alert, and may your architecture be strong and resilient!