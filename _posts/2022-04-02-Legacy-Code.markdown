---
layout: single
title:  "Thoughts and Lessons Learned working with Legacy Code"
date:   2022-04-02 20:30:00 +0300
last_modified_at: 2022-04-02 12:30:00 +0300
tags: software-processes
author_profile: true
toc: true
---
In this blog post I write about some benefits engineers can get when they work on legacy code and some lessons learned.

# Introduction
*Legacy code* is a term often despised by software engineers. Maintaining and enhancing an 10+ year old codebase with old technologies and outdated design approaches doesn't sound very enticing.

In many cases, legacy systems are designed badly. This makes your daily work harder since you cannot easily add that new requested feature. Often you have limited freedom to change foundational things to make your life easier and this becomes quickly frustrating. Phrases such as "Why on earth did people design this system like this. They should have done it like this" and "What were they thinking. This is obviously a wrong approach" are very common, especially in legacy systems.
Legacy code has admittedly a very bad reputation. Even recruiters of new projects and startups try to lure new candidates with phrases like "No Legacy Code, 100% Greenfield projects".

Without a doubt, building something from scratch is amazing for multiple reasons. First of all you use the newest, hot technologies. You also get to try using shiny tools and modern processes. In addition, depending on your seniority, it's a great opportunity to have a considerable impact on the vision of the project. You can even experiment with ideas on how to solve problems found along the way. From a philosophical perspective, working on something from it's infancy and seeing it mature into a full blown usable project is something that most of us would feel a sense of pride.

I've had the luck to work in both greenfield and legacy projects. Mostly, I've worked with legacy projects and my thoughts had been perfectly aligned with "I hate working with Legacy Code". However, contemplating on the previous years and the experience I gained from the legacy projects that I have worked, I have now come to the verdict that these legacy systems have provided the most value in my career.

Along the way, I have learned precious lessons that definitely help when working with legacy code.
In the following sections I describe why working with legacy code can be a blessing and some tips that greatly helped me in addressing legacy code programming challenges.

## The blessing of working with Legacy Code
Working with legacy code a blessing!? Well, yes.
Legacy systems are old systems. This can be a good thing. Our goal as software engineers is to write systems that provide value. And usually valuable software lasts. And if it lasts it's probably successful. Legacy systems have this amazing characteristic that you cannot find in greenfield projects: maturity. Maturity comes with a long list of lessons that you can't otherwise learn. 

### How to write code that works well and is proven to do well for years
While some legacy systems are just badly written, it's almost certain that you will find some gems in most of them. Legacy systems are usually old, and if they are old it's possible that they were successful and some things were designed/developed in the right way. The learnings from such systems are invaluable and are especially important in the beginning of working on something new.  

### How not to write code that is full of bugs
Similarly to the previous point, when working with legacy code you can appreciate whether some things were just bad. This realisation and experience that you gain from such situations are strong tools that will help you take better decisions in future instances that you have to solve a similar problem.

### Adjusting to changes as an application matures
It's very common, especially for junior engineers to learn a good approach for solving a specific problem and then applying this approach for everything. The famous Nail todo.

Legacy systems provide a proof whether an approach was considered good or bad for a specific case. This is invaluable in our industry given the enormous variety of use-cases.

Since codebases grow and an application is implemented differently at the beginning stages and in its maturity there is great opportunity to see and evaluate/appreciate how previous decisions ages. This is a great skillset to acquire. Everything is a tradeoff in software engineering and finding that sweet balance in building for the future vs building quickly for the customer is something tht is constantly trained. Legacy systems can be great case studies for determining whether something that sounded good ended bad and vice verca.

### New and Cool vs Good
A very commonly discussed theme in software engineering forums is premature adoption of cool technologies when not needed.
A cool, new toy isn't the only solution to the problem and sometimes it might not be the correct solution either.

There are countless examples of this. Two of the most popular:
- Moving from server-rendered web applications to Single Page Applications is not always needed and could introduce unecessary overhead.
- Monoliths could perform much better and can be much more maintainable than microservices.

Working with legacy systems can give you perspective and knowledge on a variety of problems that are solved in less modern ways.


## Lessons Learned working with Legacy Code
### Respect the predecessors decisions
I have worked with a number of legacy projects and I had thought of the type **"Why on earth did they do this like X? They could have easily done it like Y instead"**. 

Well, software code is organic. And business often prioritize time-to-market over future extensibility and maintainability. We also learn from our mistakes. All these factors contribute to instances in which we initially design optimal code for Feature A and Feature B in mind. And when Feature C comes and the code is not ready for it we produce quick results by bypassing quality standards. **Been there, done that.**

My philosophy around legacy code is:
1. **Never rush to judge/blame previous code authors.**
2. **The state of the software you inherit does not represent the competency of its developers.**
3. **It's there, it's alive, it works**

### Be Patient, Let it sink - Grace Period
When you start working on a large legacy project it's very easy to get excited and want to change everything that looks wrong.
It's very common that some of the "problems" you identified, are problems that are already known and they are there for a reason.
This is especially true about design decisions.

I remember on one of the first legacy projects I worked on, I started making big structure changes to critical files, only to find out that my new design did not meet a cross-compatibility requirement. My C++ code was running fine on Linux machines but it couldn't run on VMS machines because the VMS c++ compiler did not support directories. Yes!

My current approach when I start looking into a new project is to give some time to let the new information sink. Then, I can make much more confident decisions and much more valuable as well, with limited risk.


### Consistency
No matter how bad you think the design or style or any other attribute of the codebase approach is, it is much better in the long-term to conform to it.

When modifying or introducing new behavior stick to the format and practices of the codebase. This comes back to readability and maintainability.
It's very confusing and frustrating to work on a codebase that contains different approaches solving the same problem. 

Consistency, from naming variables, to structuring files, and coding style are important to follow, even when you don't agree.

