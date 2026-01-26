---
layout: single
title:  "5 Essential Prioritization Frameworks for Engineering Managers"
date:   2024-06-14 12:30:00 +0300
toc: true
tags: software-processes engineering-management
# classes: wide
header:
    og_image: /assets/images/PrioritizationFrameworks--OG.webp
---
Your time is finite, and your team's backlog feels endless — welcome to life as an Engineering Manager.

As we highlighted in the previous post on the [Engineering Manager's ultimate goal](https://pkritiotis.io/engineering-manager-goal-3-principles/), the most important part of your role is execution — you need to deliver results. To do this as efficiently and effectively as possible, you need to identify the highest-leverage activities that will give you the best value at a specific time. This principle applies not only to your own task list but also to your team’s backlog.

With numerous tasks in line, prioritization becomes crucial - It's the first step to successful execution.

While many decisions on what to work on next may come from the Product team, as an EM, you can influence prioritization as the owner of the execution process and the technical expert. On top of that, you also have to make your own decisions when managing the team backlog, your tech-debt list, and your technical initiatives.

In this post, I’m sharing five prioritization frameworks that I’ve regularly used as an Engineering Manager to make the decision-making process more structured and systematic.

# 1. The **MoSCoW** Method

One of the most popular frameworks, the MoSCoW method, is particularly useful for quickly categorizing tasks into high-level priority buckets for fixed-scope projects.

The MoSCoW framework has three priority buckets, from highest to lowest:

1. Must-have
2. Should-have
3. Could-have

<p align=center>
<img alt="The Moscow Method" src="/assets/images/PrioritizationFrameworks-Moscow.webp" width="600">
</p>

## How to run it:

1. Map each task into one of the buckets.
2. The buckets are pretty self-explanatory:
    - **Must-have:** We don’t release the feature unless everything in this bucket is done. (e.g., essential login functionality)
    - **Should-have:** Not a release blocker, but if we have the time, we need to do it. If we don’t manage to push it in the release, prioritize it immediately afterward. (e.g., user profile customization options)
    - **Could-have:** Don’t think about it now. Re-evaluate later with other tasks. (e.g., additional theme options for user interface)

## Key Characteristics:

- You only start picking tasks from one bucket if the previous one is empty.

## Benefits:

- Forces pragmatic thinking and focus on essential features.
- Particularly useful for MVPs and fixed-scope projects.

## Common Pitfalls:

- Avoid overloading the Must-have category.
- Regularly revisit and adjust priorities as project conditions change.

While simplistic, this model forces the product team and me to think pragmatically and focus on the important buckets. It’s particularly useful for MVPs. *Plus, who doesn't like saying "MoSCoW"?*

# 2. **The Value-Effort Map**

The Value-Effort Map is a powerful tool that helps you make smart decisions based on the time and resources available.

The matrix has four buckets:

1. **High Effort, High Value**: Worth it, but brace yourself.
2. **High Effort, Low Value**: Avoid these like a production bug on a Friday evening.
3. **Low Effort, High Value**: Quick wins—your best friends, like fixing that one-line bug that’s been annoying everyone.
4. **Low Effort, Low Value**: Not worth your time. Go get a coffee instead.

<p align=center>
<img alt="The Value-Effort Map" src="/assets/images/PrioritizationFrameworks-Value Effort.webp" width="700">
</p>

## How to run it:

1. Go through each task one by one and place it on the map relative to other tasks.
2. As you add more items, tweak previous placements to keep the distribution balanced.

## Key Characteristics:

- Remember, effort and value are relative. Compare each task to others on the matrix rather than using fixed values.

I’ve had great success using this for tech-debt items. The collaborative nature of this framework is fantastic, and it really makes you compare items directly. *Plus, it’s a great way to sneak in some team bonding over which tasks are truly the worst!*

# 3. **The Severity-Frequency Matrix**

The Severity-Frequency framework is a go-to for incident management, helping you gauge the urgency of issues.

## How it works:

Each issue is associated with a predefined set of rules.
- **Severity Level**: Indicates the business impact of an issue.
  - What’s the business impact (cost, reputation, data loss, security, etc.)?
- **Frequency Level**: Indicates how often and how many people experience the issue.
- **Priority Level** is determined based on Severity and Frequency Levels and is associated with a predefined set of actions.

<p align=center>
<img alt="The Severity-Frequency Matrix" src="/assets/images/PrioritizationFrameworks-Severity Frequency.webp" width="500">
</p>

## How to run it:

1. Define the severity and frequency levels:
   - **Severity Level:** 
     - Define a Severity level based on the criticality of the issue.
     - Is it in the critical path of the system?
     - Examples:
       - **S0:** System not operational/Security attack/Extreme cost issue.
       - **S1:** A non-critical component does not work properly.
       - **S2+:** Contact form UI not responsive on small screens.
   - **Frequency Level/Number of Impacted Users:** How often does it occur?
     - This depends on the industry and domain, so numbers can vary.
     - Examples:
       - **F0:** Can reproduce 30-100% of cases and affects > 10% of users.
       - **F1:** Can reproduce > 10% and < 30% of cases and affects < 10% of users.
       - etc.
2. Map Severity and Frequency pairs to Priority levels, and then impose rules on how to handle each priority level:
   - Examples:
     - **P0:** S0/S1 with F0 - Fix it NOW.
     - **P1:** S1 with F1, or S2 with F0 - Top priority within working hours.
     - etc.

## Key Characteristics:

- This is a crucial prioritization framework, especially for incidents.
- Given its business criticality, it's essential to revisit the rules of Severity and Frequency regularly and ensure the whole team is aligned.

## Benefits:

- **Objective Decision-Making**: Provides a clear, data-driven approach to prioritize incidents based on their impact and frequency.
- **Consistent Prioritization**: Establishes a standardized method for assessing and responding to incidents, promoting consistency across the team.

## Common Pitfalls:

- **Communication Gaps**: If not regularly updated and communicated, the framework can become outdated, causing misalignment within the team.
- **Too open to interpretation**: Loose definitions of severity and frequency might lead to wrong prioritization.

This framework helps keep your incidents under control and your sanity intact—well, mostly. Plus, there's nothing more exciting than saying "P0 finally solved"!

# 4. **Stack Ranking**

The simplest, yet one of the most powerful prioritization frameworks when you really need to make hard choices.

## How it works:

- Each task is assigned a unique priority.
- It is either higher priority or lower priority than each other item being prioritized.

<p align=center>
<img alt="Stack Ranking" src="/assets/images/PrioritizationFrameworks-Stack Ranking.webp" width="600">
</p>

## How to run it:

1. Put all the tasks in a list.
2. Choose what’s most important with a top-down order.
3. There are no buckets. Each task is either more important than something or less important.

## Key Characteristics:

- Forces absolute prioritization of tasks.

## Benefits:

- **Clear Prioritization:** Provides a clear, unambiguous order of tasks, ensuring that the most important tasks are addressed first.
- **Simplicity:** Easy to implement without the need for complex frameworks or criteria.

I use this all the time in combination with the above frameworks, and it is particularly useful when you work with deadlines and time-limited execution processes. *Plus, it's a relief to finally put an end to the 'Which task should we work on next?' debates—just pick the top one!*

# 5. **The Eisenhower Matrix**

The Eisenhower Matrix is an insightful framework, particularly useful for managers deciding whether to delegate tasks and how to deal with tasks based on urgency and importance.

## How it works:

There are four buckets:

1. **Important and Urgent:** Do these tasks immediately. They are critical and need your attention right away.
2. **Not Important but Urgent:** Delegate these tasks. Someone else can handle them.
3. **Important but Non-urgent:** Plan to do these tasks later. Schedule them when you have more time.
4. **Not Important and Non-urgent:** Discard these tasks. They are not worth your time.

<p align=center>
<img alt="The Eisenhower Matrix" src="/assets/images/PrioritizationFrameworks-Eisenhower.webp" width="700">
</p>

## Key Characteristics:

- Helps in distinguishing between urgent and important tasks.
- Facilitates better time management and task delegation.

## Benefits:

- **Improved Focus:** Ensures that you spend your time on tasks that truly matter.
- **Better Delegation:** Helps you identify tasks that can be handed off to others.
- **Enhanced Productivity:** Reduces the clutter of unimportant tasks, allowing you to focus on high-impact activities.

## Common Pitfalls:

- **Misclassification:** Incorrectly categorizing tasks can lead to neglected important tasks or wasted effort on non-essential ones.
- **Over-reliance on Delegation:** Delegating too many tasks might overwhelm your team.
- **Ignoring Non-urgent Tasks:** Important but non-urgent tasks might be infinitely postponed if not carefully scheduled.

This framework is a game-changer for managing your workload effectively. Plus, it’s fun to finally have a legitimate reason to discard those pointless tasks that have been rotting on your to-do list!

# TL;DR; Summary

| Framework             | How                                                                                         | Use Cases                     | When                                                                  |
|-----------------------|---------------------------------------------------------------------------------------------|-------------------------------|-----------------------------------------------------------------------|
| **MoSCoW**            | Three buckets: Must-have, Should-have, Could-have                                           | MVPs, Fixed-scope execution   | Useful for high-level categorization of a large number of features    |
| **Stack Ranking**     | Unique priority to each task. Start from the top and work on the items one by one           | Deadlines, Fixed-time execution | Useful when you need to make hard decisions about initially equally-prioritized items. |
| **Value/Effort**      | Four buckets: Low Effort High Value, High Effort High Value, Low Effort Low Value, High Effort Low Value | Tech-debt backlog             | When there is uncertainty in the Value vs Effort ratio of tasks       |
| **Severity/Frequency**| Severity Levels: Business Impact of an Incident, Frequency/Exposure: How often it occurs and how many people it is affecting, Priority: S/F pairs | Incident Management           | When there is a need for well-defined objective rules                 |
| **Eisenhower Matrix** | Four buckets: Important Urgent, Important Non-urgent, Not Important Urgent, Not Important Non-urgent | Daily/Weekly Planning/Delegating of Engineering Managers | For daily decision making                                             |

# Conclusion

That’s it, folks! Prioritization doesn’t need to be hard.

We’ve explored different types of prioritization frameworks that serve slightly different purposes. Some of them can be combined to help with both high-level and lower-level prioritization.

One important thing to remember is that prioritization is not static. What you prioritize today as urgent can change to “good to have” tomorrow. It’s a hard pill to swallow since it’s a time-consuming process, but the benefit of an up-to-date ranking is definitely worth it.

Study them, try them, and good luck with your prioritization!

Cheers to a smoother workflow and fewer 'what should I do next?' moments!
