---
layout: single
title:  "Write the Docs! Thoughts on Software Documentation"
date:   2022-02-05 20:30:00 +0300
tags: software-processes
author_profile: true
toc: true
---
## Introduction
Documentation has a bad reputation in the software engineering world.
Everyone complains about the lack of it and no-one wants to invest time in writing quality documentation. 

Many engineers even hate to write documentation. Some engineers even consider documentation redundant and unecessary; and most of them complain when they don't find something when it is most needed. 

In my early years as a junior engineer I also hated writing documentation. Afterall, "what proper engineer prefers to write text for explaining code instead of investing that time to write more code?"

Well, through the years and my interaction with undocumented systems and codebases, I have grown to be a big proponent of documentation. The pain, tears and time spent to find about undocumented requirements, specs ,and designs were convincing enough for me to appreciate the value of a good documentation.

In this article we'll explore some common reasons why many engineers don't write documentation, the value that documentation can bring and some guidelines that have helped me when dealing with documentation.

## Why not? Misconceptions

- It takes unecessary time
  - Documentation is an investment, not a quick win
  - it has compound returns
  
- It is not needed - just look at the code
  - so. much. more. difficult
  
- it's straightforward
  - nothing is straightforward

- i'll never forget this

- 


## Why bother?

- communication efficiency
  - don't take time from other engineers
- onboarding time for new engineers
- preserve knowledge
  - people leave
- no one remembers everything forever
- enforce correctness

## Common Gotchas

- Set and forget
- Do it for some, don't do it for all
- Spread them, spread them everywhere

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
