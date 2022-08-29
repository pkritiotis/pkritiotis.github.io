---
layout: single
title:  "Hail the Docs! Thoughts on Software Documentation"
date:   2022-08-30 20:30:00 +0300
tags: software-processes
author_profile: true
toc: true
---
This article explores the value of documentation, its state in many software engineering companies, several misconceptions, some common mistakes when we document software and some suggestions on how to write, structure and maintain great documentation. 

# Introduction
Documentation is a hot topic in the software engineering world and has an arguably bad reputation.

Most engineers complain about the quality, the state and the lack of documentation.

At the same time most engineers avoid and do not invest time in writing quality documentation. 

Many engineers even hate to write documentation. Some engineers even consider documentation redundant and unecessary. And at the same time some of them complain when they don't find something when it is most needed. 

In my early years as a junior software engineer I have to admit that I also hated writing documentation. Afterall, *"Why would an engineer spend time to write boring text for explaining code instead of investing that time to write more code?"*

Well, through the years and my interaction with undocumented systems and codebases, I have grown to be a big proponent of documentation. The pain, tears and time spent to find about undocumented requirements, specs ,and designs were convincing enough for me to appreciate the value of a good documentation.

In this article we'll explore three main areas:
1. Common reasons why many engineers don't write documentation
2. The value that documentation can bring
3. Guidelines that have helped me when dealing with writing and maintaining documentation.

# Common Misconceptions

In this section we explore common arguments from engineers that refuse to write documentation.

## Misconception #1: It's not required, just look at the code
> You don't need documentation! Just look at the code!

This is a common one. *"Why would you write documentation to explain how things work if the absolute source of truth is the code itself? Just invest time in providing better code readability"*.

Well, code readability is a huge topic on its own and one of the qualities we strive for as software engineers. And it's true, the most correct description of what a software system does it's the code. 

But unfortunately **the purpose of code is to define behavior, not describe behavior**.

The major problems with this argument are:
1. Not everyone can read code. There are more roles than software engineers involved in producing a software system
2. Reading code to understand the behavior of a system is not efficient and does not scale well with the increase of complexity and size

### Not everyone can read code
Reading code is an option for software engineers who can read code. But what about the other stakeholders of the system?

A software team interacts with multiple stakeholders. Product Managers want to clarify specific use-cases. Software Engineers of other teams we collaborate with want to know just enough details to integrate with our systems.

If there is no documentation, the communication overhead with go-to experts becomes too big to handle and the efficiency of the team decreases significantly.

### Reading code to understand a system is not efficient

Even if you can read code, software engineers that have not worked with the codebase before, need some context. Before working with code when we join new projects we need an overview, the purpose of the system and the main use-cases it tries to solve.

**Checking the codebase to extract the behavior of the system is possible, but extremely slow and painful**. It's also possible to result in misconceptions about the behavior. Remember, reading code is much harder than writing code. 

Finally code readability becomes less and less valuable, especially for troubleshooting, when the complexity and size of a system increases over time. Clarifying the behavior of an extreme use-case and "why does this do this instead of that" might become impossible when there is only a codebase to work with.


## Misconception #2: It wastes time that could be spent in development instead
> Why would I waste my time on writing about code rather than coding at that time?

Does writing documentation really waste time *in the long term*? How can we determine whether it trully wastes time? 

Does avoiding documentation in the long term result in a higher throughput? Well, I don't think so.

As mentioned above, as the complexity and size of the software increases, the communication overhead becomes unbearable and will inevitably result in lower throughput. 

If there's no documentation at some point the development time will be inevitably decreased.

Why? Because stakeholders will always have questions.

And who knows the answer? Developers! The worst problem is that different people will ask the same questions over and over again to the same engineers. #AutomateThis
  

## Misconception #3: We don't need to write this, it's obvious
> "We dont' need to document this. Everyone knows that!"

Well, it might be obvious for **you**, **now** under your area of work. But nothing is obvious for everyone.
Documenting clearly use-cases, architecture, interaction between components avoids misunderstandings that could result in major errors/incidents that can be constly to businesses.

## Misconception #4: No need to write this, we will remember this
> "I'll never forget this"

Famous last words.

Well, there's not really much to comment on this one. People forget and people leave from companies. It's inevitable. We need accept it, and be prepared for it.

How? **Write the docs!**


# The value of documentation

Hopefully it's now clear that avoiding documentation is a dangerous game to play. 

Let's go through the value of documentation.

## Communication efficiency
The purpose of documentation is the communicating the description of a software system to the reader.
The greatest benefit of documentation is the communication efficiency it offers.

A software project has a lifecycle of many steps that spans accross different functions. 
It requires the collaboration of multiple roles such as Business, Product, Design,  Engineering.

A contributor can wear multiple hats, but for teams with more than a single peron, communication is the key to success.

Whether we are talking about business needs, detailed use-cases, edge-cases and software engineering documentation, all play a key role in communicating the purpose, the behavior of the system and how the system is implemented.

Each of the people involved in a software project, have expertise in their function and therefore and the single point of contact for specific areas of the project.

The presence of documentation in all areas of the project increases the efficiency by:
- Preventing consuming time from people who work on their ongoing tasks to get an answer for a question about the system's behavior
- People are not blocked by someone's unavailability. The information is documented so you can read it and proceed accordingly

## Quick Onboarding time for new members
When a new member joins the team, we want them to be productive as soon as possible.

Depending on the existence of good documentation, the onboarding process can be either a painful team-draining process, or a smooth process for both the new joiner and the team. 

I've personally experienced both situations and the difference in moral and frustration and delivery times is outstanding!

This is especially important for medium-big size and high-growth organizationswhere hiring is constant.

## Preserving knowledge

People forget, and people leave companies. No one remembers everything forever, and there is no guaranteed loyalty to a company.

Concentrated knowledge of the systems to a handful of people is a very bad idea. It will lead to an inevitable bad situation.

Imagine what would happen if the people who know the business of the product inside out left the company. It's a huge cost for a company to take.

Thankfully it's preventable.\
How?\
Write the docs! 

## Promotes Alignment, Enforces Correctness

People disagree, and people forget.

Maintaining a good documentation, especially on use-cases, edge-case scenarios and decisions made about specific parts of the project has a huge impact on the success of the project. A few examples:
- Software engineers can ensure correctness via test cases and review code against the written documentation
- New software engineers can understand the decision-making process for "Why was this done this way"
- Product can confirm use-cases and behavior and check the data points they collected to reach their decisions

## Promotes External Collaboration
Lastly, good documentation, especially in large organizations and open source projects can be used by people outside the contribution team to gain knowledge on the area of work of other teams.
This greatly promotes domain knowledge and understanding and can result in cross-domain collaboration and exchange of ideas.

# Common Gotchas
As we've seen the value of documentation is apparent and software projects can benefit greatly from a well written documentation.

However, just writing any documentation is not enough. The documentation should be meet a quality standard and be maintained consistently.

Below we explore some common mistakes you can see in project where the value of documentation is well understood but the execution fails.

## The "written in stone" documentation
This is probably the most commonly found problematic state in documentation.
While a team puts the effort to write documentation, there is no attention to keep the documentation up to date.

This is a highly dangerous situation since it can result in:
1. Either the documentation becomes completely useless since it does not represent the current state of the project
2. Or even worse, people could read the documentation and make decisions based on wrong assumptions that are presented in outdated documentation

Documentation should be updated systematically as the software grows organically. 

## Unreviewed documentation
Often documentation is assigned and written by a team member, published in the knowledgebase tool of choice and that's all. 

Similarly to how code is written and reviewed, the same applies for documentation. People make mistakes, miss important information, have misconceptions about how specific things work. 

Therefore it's essential that documentation is peer-reviewed using a feeback-provisioning process to ensure correctness. 

## Documentation spread in too many locations
Another common problem you can find in the software world is using too many documentation tools/services without a clear approach of what goes where.

Spread the docs, spread the docs everywhere

This devalues the documentation since, if someone wants to search for something, they need to check each documentation source separately without . 

This is of course preventable by either hosting everything together or standardizing the types of documents that exist in each tool and how they should be structured.

# Crucial documents for the software engineer
So far we have explored the misconceptions our documentation, the value of documentation and common problems we see with documentation.

But what should we document?

We will be looking at this from a software engineer perspective and go through the types of documents that I believe are absolutely necessary in modern projects

## Business use cases
Software development is all about implementing business use-cases. The first stage in the lifecycle of software development is gathering the requirements. From the requirements we continue with technical analysis [todo]() and extract the specifications.

Documenting the business use-cases is one of the most important points of documentation, as they define how the system should behave. This is the part of the documentation that team members will visit often to clarify and confirm how the system should behave under specific circumstances.

## Technical Designs
The technical design is one of the first areas software engineers look to gain a generic understanding of the system.
Usually technical designs should contain the following:
- Tech Stack
- System context
- Architecture
- Netwok Diagram
- Infrastructure/Deployment Diagram
- Data Flow Diagram
- Component Diagram

When we think of technical design for a specific project we can think about it in two levels:
- System level technical design
- Service level technical design

For large projects that consist of multiple components, the system technical design should be hosted in a commonly shared location and describe the high level interaction of sub-components

In a service-level, the technical desing can live with the code itself. It's 

- Runbooks
- Onboarding Guides
- Developer's Handbook
- FAQ

# My philosophy on Documentation
Documentation is an investement; it saves communication and investigation time.
At the same time documentation needs care; if it gets outdated it can become useless.

My rules around documentation are described in the below statements:
1. Always document business use cases in detail
2. Always include and maintain a *Developer's Handbook* section for projects with multiple contributors
3. When a project is big enough and support takes considerable time, document a *Troubleshooter's guide* for common issues and FAQ

Looking at documentation from a higher level, documentation should always be well-organized., In my experience, I have seen documentations been spread out to multiple locations which causes confusion and defeats it's purpose, since the stakeholders cannot find easily what they want to.

My approach on organizing this is:
1. Keep all project/service-specific technical documentation within the corresponding code repository
2. Keep all business documentation together in one, single common space
3. Keep technical analysis of features in one, single common space
4. Keep architecture documentation and documents that involve more than one components separate from the repos, within a dedicated shared location 

Again, documentation is a huge subject on its own, but the above generally encapsulate the fundamental values I formed around the developer's need for it. From an organization perspective, there are many more necessary components that are out of scope of this doument, such as onboarding, runbooks, company-wide practices etc.


For those about to doc, I salute you