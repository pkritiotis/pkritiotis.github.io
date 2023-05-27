---
layout: single
title:  "Embracing the modular monolith"
date:   2023-06-15 12:20:00 +0300
tags: software-processes
toc: true
# classes: wide
---
In this blog post, I write about modular monoliths, what they are, what's their purpose, essential characteristics of a good modular monolith and a framework to build one with success

# Introduction
For the last two decades the hottest software enginerring architecture topic is microservices.
Since the new cool shiny architecture came into our lives, it's been the main topic of dicussion, the cool thing to work with, the silver bullet to solve our architecture problems.

> Developers are drawn to complexity like moths to a flame, frequently with the same result.
> Neal Ford

Naturally, as we engineers like shiny things, we need to apply it. Immediately. No need to dig into the details of it. Otherwise we are not cool, we loose the trend, we are old school.

And, naturally, more times than not, we failed. I, myself, guilty of this. Because, in software engineering there is no panacea when it comes to architecture choices. Everything is a trade-off. There are different approaches for different problems.

During the last few years, after many documented failures, we've come to the realization of the enormous complexity that comes with microservices, the difficulty in getting things right and the fact that microservices are not a miracle drug that applies in all software.

# Wrong reasons to choose microservices

The majority of people who have chosen to go with microservices, even though in hindisight they shouldn't, did that for one of the following reasons:
1. They had a monolith that was terrible. 
   - The existing codebase was just bad to work with: unreadable, coupled, unmaintainable, not scalable and extensible.
   - Let's take advantage of all the benefits of microservices: decoupled, autonomous, clear responsibilities, scalable
2. They had to start a greenfield project that had huge scalability requirements
   - Monoliths are bad for scaling, let's do microservices-first approach

# The case of the modular monolith

- The problem is modularity, not the monolith
  - Monolith = less complex by definition (infra, observability)
  - More performant
- Greenfield vs Brownfield
  - Bounded context is complex in itself, one mission at a time
  - Code Plasticity - Easier to tackle core changing decisions
- Scalable enough
  - database sharding
  - stateless = scalable app logic
- More straightforward transition to microservices


# A Framework for building a modular monolith

- Clear domains (decomposition)
- Module Interfaces and Dependency Management
  - Encapsuled modules

# Implementation Challenges
- Decomposition
- Intra-module architecture
- Centralized coordinator
- Enforcing dependency flow

# Conclusion

# References
- Moths flame quote
- Martin fowler, always start from monolith
- Software Decomposition

# Further reading
- Simon Brown
- Majestic Monolith by DHH
- 
- 