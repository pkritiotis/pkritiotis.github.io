---
layout: single
title:  "Hail the Docs! Thoughts on Software Documentation"
date:   2022-08-30 20:30:00 +0300
tags: software-processes
author_profile: true
toc: true
---
This article explores the value of documentation, its state in many software engineering companies, several misconceptions, some common mistakes when we document software and some suggestions on how to write, structure and maintain great documentation. 

## Introduction
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

## Common Misconceptions

In this section we explore common arguments from engineers that refuse to write documentation.

### Misconception #1: It's not required, just look at the code
> You don't need documentation! Just look at the code!

This is a common one. Why would you write documentation to explain how things work if the absolute source of truth is the code itself? Just invest time in providing better code readability.

Well, code readability is a huge topic on its own and one of the qualities we strive for as software engineers. Well code readability is not a universal solution for everyone that needs to know about the system's behavior. The readability of a codebase is mostly valuable for software engineers that need to modify to the code. 

First of all, software engineers that have not worked with the codebase before, need some context. Before working with code when we join new projects we need an overview, the purpose of the system and the main use-cases it tries to solve. Checking the codebase to extract these details is possible, but extremely slow and painful. It's also possible to result in misconceptions about the behavior. Remember, reading code is much harder than writing code. 

Secondly, what about the other stakeholders of the system? A software team interacts with multiple stakeholders. Product Managers want to clarify specific use-cases. Software Engineers of other teams we collaborate with want to know just enough details to integrate with our systems. If there is no documentation, the communication overhead with go-to experts becomes too big to handle and the efficiency of the team decreases significantly.

Lastly code readability becomes less and less valuable especially for troubleshooting when a system is more complicated and larger in size. Clarifying the behavior of an extreme use-case and "why does this do this instead of that" might become impossible when there is only a codebase to work with.


### Misconception #2: It requires time that could be spent in development instead
> Why would I waste my time on writing about code rather than coding at that time?

- It takes unecessary time
  - Documentation is an investment, not a quick win
  - it has compound returns
  

### We don't need to write this, it's obvious
- it's straightforward
  - It might be straightforward for you now under your area of work. But nothing is straightforward

### We will remember this
- i'll never forget this
  - Famous last words 


## Why bother?

- communication efficiency
  - don't take time from other engineers
- onboarding time for new engineers
- preserve knowledge
  - people leave
- no one remembers everything forever
- enforce correctness
- promote innersourcing

## Common Gotchas

- Set and forget
- Do it for some, don't do it for all
- Spread the docs, spread the docs everywhere

## Crucial documents for the software engineer
- Business use cases
- Technical Designs
- Runbooks
- Onboarding Guides
- Developer's Handbook
- FAQ

## My philosophy on Documentation
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