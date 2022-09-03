---
layout: single
title:  "Hail the Docs! Thoughts on Software Documentation"
date:   2022-08-30 20:30:00 +0300
tags: software-processes
author_profile: true
toc: true
---
This article explores the value of documentation, several misconceptions, some common mistakes when we document software, and some suggestions on how to write, structure, and maintain great documentation. 

# Introduction
Documentation is a hot topic in the software engineering world and has an arguably bad reputation.

Most engineers complain about the quality, the state, and the lack of documentation.

At the same time, most engineers avoid and do not invest time in writing quality documentation. 

Many engineers even hate to write documentation. Some engineers even consider documentation redundant and unnecessary. In contrast, some of them complain when they don't find something when it is most needed. 

In my early years as a junior software engineer, I have to admit that I also hated writing documentation. After all, *"Why would an engineer write boring text to explain code instead of investing that time to write more code?"*
<p align="center">
  <img alt="whattodo" src="/assets/images/write.png" width="200">
  <br>
    <small>Image by <a href="https://github.com/MariaLetta/free-gophers-pack"> Maria Letta @free-gophers-pack</a></small>
</p> 

Well, through the years and my interaction with undocumented systems and codebases, I have become a big proponent of documentation. The pain, tears and time spent to find about undocumented requirements, specs, and designs were convincing enough for me to appreciate the value of good documentation.

In this article, we'll explore three main areas:
1. Common reasons why many engineers don't write documentation
2. The value that documentation can bring
3. Guidelines that have helped me when dealing with writing and maintaining documentation.

# Purpose

Why should we write software documentation? What's the purpose of software documentation?

Software documentation is like any documentation; it's a form of communication. In software projects built by more than one person, communication is a crucial success factor of the software's success.

> Software documentation either explains how the software operates or how to use it, and may mean different things to people in different roles.
> from [^wikipedia]()

Is it essential for everyone who wants to build, use, understand or interact with the software to have access to a knowledge base that can give an understanding of what they seek to find.


# Common Misconceptions

This section, explores common arguments from engineers who refuse to write documentation.
<p align="center">
  <img alt="whattodo" src="/assets/images/scared.png" width="200">
  <br>
    <small>Image by <a href="https://github.com/MariaLetta/free-gophers-pack"> Maria Letta @free-gophers-pack</a></small>
</p> 

## Misconception #1: It's not required; just look at the code
> You don't need documentation! Just look at the code!

This is a common one. *"Why would you write documentation to explain how things work if the absolute source of truth is the code itself? Just invest time in providing better code readability"*.

<p align="center">
  <img alt="whattodo" src="/assets/images/who-needs-documentation-i-can-read-code.jpg" width="200">
</p> 

Code readability is a vast topic and one of the qualities we strive for as software engineers. And it's true, the most accurate description of what a software system does it's the code. 

But **the purpose of code is to define behavior, not describe behavior**.

The major problems with this argument are:
1. Not everyone can read code. There are more roles than software engineers involved in producing a software system
2. Reading code to understand the behavior of a system is not efficient and does not scale well with the increase in complexity and size

### Not everyone can read code
Reading code is an option for software engineers who can read code. But what about the other stakeholders of the system?

A software team interacts with multiple stakeholders. For example, product managers want to clarify specific use cases. Software Engineers of other teams we collaborate with want to know just enough details to integrate with our systems.

<p align="center">
  <img alt="whattodo" src="/assets/images/source-full-code.jpeg" width="300">
</p> 

Without documentation, the communication overhead with go-to experts becomes too big to handle, and the team's efficiency decreases significantly.

### Reading code to understand a system is not efficient

Even if you can read code, software engineers that have not worked with the codebase need some context. Before working with code, when we join new projects, we need an overview of the purpose of the system and the primary use cases it tries to solve.

**Checking the codebase to extract the system's behavior is possible but extremely slow and painful**. It's also possible to result in misconceptions about the behavior. Remember, reading code is much harder than writing code. 

Finally, code readability becomes less and less valuable, especially for troubleshooting, when the complexity and size of a system increase over time. Clarifying the behavior of an extreme use case and "why does this do this instead of that" might become impossible when there is only a codebase to work with.


## Misconception #2: Documentation wastes time that could be spent on development instead
> Why would I waste my time writing about code rather than coding at that time?

Does writing documentation waste time *in the long term*? How can we determine whether it genuinely wastes time? 

Does avoiding documentation in the long term result in a higher throughput? Well, I don't think so.

As mentioned above, as the complexity and size of the software increases, the communication overhead becomes unbearable and will inevitably result in lower throughput. 

If there's no documentation at some point, the development time will be inevitably decreased.

Why? Because stakeholders will always have questions.

And who knows the answer? Developers! The worst problem is that different people repeatedly ask the same questions to the same engineers. #AutomateThis
  

## Misconception #3: We don't need to write this; it's obvious
> "We don't need to document this. Everyone knows that!"

It might be obvious to **you**, **now** under your area of work. But nothing is obvious for everyone.
Documenting use cases, architecture, and interaction between components prevents misunderstandings that could result in significant errors/incidents that can be costly to businesses.

## Misconception #4: No need to write this; we will remember this
> "I'll never forget this"

Famous last words.

Well, there's not really much to comment on this one. People forget, and people leave companies. It's inevitable. We need to accept it and be prepared for it.

How? **Write the docs!**


# The value of documentation

Hopefully, it's now clear that avoiding documentation is a dangerous game to play. 


This section explores common arguments from engineers who refuse to write documentation.
<p align="center">
  <img alt="whattodo" src="/assets/images/lamp.png" width="200">
  <br>
    <small>Image by <a href="https://github.com/MariaLetta/free-gophers-pack"> Maria Letta @free-gophers-pack</a></small>
</p> 

The value of documentation is quite evident if we think of a scenario in which we don't have documentation:
1. We need to find a person who possesses the knowledge that we seek
   1. Blockers:
      1. This person must be available
      2. This person might have other high-priority items to work on
   2. Inefficiency
      1. This person must answer the same questions over and over again to different people
2. Or we will try to extract the information on our own
   1. If there is existing software, we can look at the code
      1. Inefficiency
         1. This is a slow process
   2. If we seek information about things already discussed but not documented, well, we can't do anything :shrugged:

If we want to encapsulate everything in one sentence, I would say that the value of documentation is that **Documentation is always available for everyone to consume at any time**.


## Communication efficiency
Software documentation aims to communicate what a software system does and how it does it from different perspectives. The details can vary depending on the target audience and what documentation needs to convey. 

Given this purpose, the great benefit of documentation is **communication efficiency**.

A software project has a lifecycle of many steps that span across different functions. 
It requires the collaboration of multiple roles such as Business, Product, Design, and Engineering.
A contributor can wear numerous hats during a project, but communication is the key to success for teams with more than one person.

Whether we are talking about business needs, detailed use cases, edge cases, and software engineering documentation, all play a key role in communicating the purpose, the behavior of the system, and how the system is implemented.

<p align="center">
  <img alt="whattodo" src="/assets/images/agree.png" width="200">
  <br>
    <small>Image by <a href="https://github.com/MariaLetta/free-gophers-pack"> Maria Letta @free-gophers-pack</a></small>
</p> 

Each person involved in a software project has expertise in their function and, therefore is the single point of contact for specific areas of the project.

The presence of documentation in all areas of the project increases the efficiency by:
- **Preventing consuming time from people** who work on their ongoing tasks to get an answer for a question about the system's behavior
- **Someone’s unavailability does not block people**. The information is documented so you can read it and proceed accordingly
- **People who seek information can find what they want easily and quickly**

## Quick Onboarding time for new members
When a new member joins the team, we want them to be productive as soon as possible.

Depending on the existence of good documentation, the onboarding process can be either a painful resource and time-draining process or a smooth process for both the new joiner and the team. 

I've experienced both situations, and the difference in morale and frustration and delivery times is outstanding!

This is especially important for medium-big size and high-growth organizations where hiring is constant.

## Preserving knowledge

People forget, and people leave companies. And unfortunately, no one remembers everything forever, and there is no guaranteed loyalty to a company.

Concentrating knowledge on a handful of people is a horrible idea. It's almost guaranteed to lead to an inevitably bad, bad situation.

Imagine what would happen if the people who know the business of the product inside-out left the company. It's a massive cost for a company to take.

Thankfully it's preventable.\
How?\
Write the docs! 

## Promotes Alignment, Enforces Correctness

People disagree, and people forget.

Arguments and conflict resolution is part of our job. If we talk about preferences and subjective topics with competing tradeoffs, that's expected; and inevitable. 

Disagreements and arguments on already agreed product requirements, specifications, and decisions are completely preventable. This might sound obvious. But these sort of arguments is the reality of some software engineering teams.

Maintaining good documentation, especially on use cases, edge-case scenarios, and decisions made about specific parts of the project, dramatically impacts the project's success. Here are a few examples:
- Software engineers can ensure correctness via test cases and review code against the written documentation
- New software engineers can understand the decision-making process for "Why was this done this way."
- Product can confirm use cases and behavior and check the data points they collected to reach their decisions

## Promotes External Collaboration
Lastly, good documentation, especially in large organizations and open source projects, can be used by people outside the contribution team to gain knowledge on the area of work of other teams.
<p align="center">
  <img alt="whattodo" src="/assets/images/alien.png" width="200">
  <br>
    <small>Image by <a href="https://github.com/MariaLetta/free-gophers-pack"> Maria Letta @free-gophers-pack</a></small>
</p> 

This greatly promotes domain knowledge and understanding and can result in cross-domain collaboration exchanging of ideas.

# Common Gotchas
As we've seen, the value of documentation is apparent, and software projects can benefit significantly from well-written documentation.

However, just writing any documentation is not enough. The documentation should meet a quality standard and be maintained consistently.

Below we explore some common mistakes you can see in a project where the value of documentation is well understood, but the execution fails.

## The "written in stone" documentation
This is probably the most commonly found problematic state in documentation.
While a team puts effort into writing documentation, there is no attention to keeping the documentation up to date.


<p align="center">
  <img alt="whattodo" src="/assets/images/bomb.png" width="200">
  <br>
    <small>Image by <a href="https://github.com/MariaLetta/free-gophers-pack"> Maria Letta @free-gophers-pack</a></small>
</p> 


This is a dangerous situation since it can result in the following:
1. Either the documentation becomes utterly useless since it does not represent the current state of the project
2. Or even worse, people could make decisions based on wrong assumptions that are presented in outdated documentation

Documentation should be updated systematically as the software grows organically. 

## Inaccurate documentation
Often documentation is assigned and written by a team member, published in the knowledgebase tool of choice, and that's all. 

Code is written and reviewed before being published. The same applies to documentation. People make mistakes, miss important information, and have misconceptions about how specific things work. 

Therefore it's essential that documentation is peer-reviewed using a feedback-provisioning process to ensure correctness. 

## Documentation spread in too many locations
Another relatively common problem in the software world is using too many documentation approaches and even tools/services without a clear direction of what goes where.

<p align="center">
  <img alt="whattodo" src="/assets/images/docs-docs-everywhere.jpg" width="600">
</p> 

This devalues the documentation since, if someone wants to search for something, they need to check each documentation source separately without a clear indication of what goes where. 

This is preventable by hosting everything together or standardizing the types of documents in each tool and how they should be structured. KISS usually works until a specific inefficiency is identified along the way.

# Crucial documents for the software engineer
So far, we have explored the misconceptions of documentation, the value of documentation and common problems we see with documentation.

But what should we document?

We will look at this from a software engineer perspective, and going through documents I believe are absolutely necessary for modern projects.

## Business Requirements and use cases
Software development is all about implementing business use cases. The first stage in the lifecycle of software development is gathering the requirements. From the requirements, we continue with technical analysis [todo]() and extract the specifications.

Documenting this process and the extracted business use cases is one of the most critical points of documentation, as they define how the system should behave. This is the part of the documentation that team members will often visit to clarify and confirm how the system should act under specific circumstances.

## Technical Documentation
### Technical Designs
Technical design is one of the first areas in that software engineers look to gain a high-level understanding of the system.
Usually, technical designs should contain the following:
- Tech Stack
- System context
- Architecture Diagram
- Network Diagram
- Infrastructure/Deployment Diagram
- Data Flow Diagram
- Component Diagram

When we think of a technical design for a specific project, we can think about it on two levels:
- System-level technical design
- Service-level technical design

For large projects that consist of multiple components, the system technical design should be hosted in a commonly shared location and describe the high-level interaction of sub-components

At a service level, the technical design can live with the code itself. It's easier to have a common place for everything related to a project, including documentation and code. It's also easier to maintain it.

### Developer's Handbook
The Developer's Handbook is one of the sections I have adopted in the last years and is invaluable.

All documentation that a developer should know about standards, processes, tooling, and guides that is non-project specific lives in this space.

Let's review some of the contents that live under the dev's handbook.

#### Guides
Guides can be in the form of runbooks, how-tos, and specific documented steps of achieving a goal that is commonly required and does not have a straightforward approach.

#### Onboarding 
A step-by-step onboarding guide is essential for bringing a new team member up to speed without blocking other team members.

#### Processes
Every team has its practices that can span at a team, domain, or company level. Therefore, documenting the processes is important to align everyone on the team.

# Final thoughts - My philosophy on Documentation
Documentation is an investment; it saves communication and investigation time.
But, at the same time, documentation needs care; if it gets outdated, it can become useless.

## My guidelines
My guidelines around documentation are described in the below statements:
1. Always document business use cases in detail.
2. Always include and maintain a *Developer's Handbook* section for projects with multiple contributors.
3. When a project is big enough, and support takes considerable time, document a *Troubleshooter's guide* for common issues and FAQ.

## Organizing documentation
Documentation should always be well-organized. In my experience, I have seen documention being spread out to multiple locations, which causes confusion and defeats its purpose since the stakeholders cannot easily find what they want.

My approach to organizing this is as follows:
1. Keep all project/service-specific technical documentation within the corresponding code repository.
2. Keep all business documentation together in one single space.
3. Keep technical analysis of features in one single space.
4. Keep architecture documentation and documents that involve more than one component separate from the code repositories, within a dedicated shared location.

Again, documentation is a vast subject on its own, but the above generally encapsulates the fundamental values I formed around the developer's need for it.


For those about to doc, I salute you :metal: