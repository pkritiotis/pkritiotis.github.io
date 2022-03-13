---
layout: single
title:  "Write the Docs! Thoughts on Software Documentation"
date:   2022-02-05 20:30:00 +0300
tags: software-processes
author_profile: true
toc: true
---


## My philosophy on Documentation
Many software engineers hate to write documentation and I have to say that I was one of them in the past. Through the years and my interaction with undocumented code I have grown to be a big proponent of documentation.

Documentation is an investement; it saves communication and investigation time.
At the same time documentation needs care; if it gets outdated it can become useless.

My rules around documentation are described in the below statements:
1. Always document business use cases in detail
2. Always include and maintain a *Developer's Handbook* section for projects with multiple contributors
3. When a project is big enough and support takes considerable time, document a *Troubleshooter's guide* for common issues and FAQ

Looking at documentation from a higher level, documentations should always be well-organized., In my experience, I have seen documentations been spread out to multiple locations which causes confusion and defeats it's purpose, since the stakeholders cannot find easily what they want to.

My approach on organizing this is:
1. Keep all technical documentation within the code repository
2. Keep all business documentation together in one, single common space
3. Keep technical analysis of features in one, single common space
4. Keep architecture documentation and documents that involve more than one components separate from the repos, within a dedicated shared location 

Again, documentation is a huge subject on its own, but the above generally encapsulate the fundamental values I formed around the developer's need for it. From an organization perspective, there are many more necessary components that are out of scope of this doument, such as onboarding, runbooks, company-wide practices etc.
