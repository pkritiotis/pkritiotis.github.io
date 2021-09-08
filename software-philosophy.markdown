---
layout: single
title: Software Philosophy
permalink: /software-philosophy/
---
  
This is a curated list of quotes and principles on software design and development that I have collected over the years.
Originally inspired by listening to 'Go Time - Software Philosophy', these statements compose my design philosophy; the fundamental values that I use to decide how to work on software engineering problems

# Design 

> Design code so that it is easy to understand, not easy to implement

> YAGNI
> You ain't gonna need it.

Design for today. Don't over-design for tomorrow unless tomorrow is promised. Tomorrow **is not promised**.

## Dependencies
> A little bit of copying is better than a little dependency - Rob Pike
External Dependencies = possible vulnerabilities

# Code Duplication
> DRY
> Do not repeat yourself

Duplication of Code = Double Maintenance = Inconsistency Possibility
// Use in conjuction with dependency principle

# Design patterns
Don't try to add patterns in code, identify patterns in code

# Code complexity
> KISS
> Keep it stupid simple

# On inline comments:
> Add comments to explain why specific code is there. Don't describe what it does

# On logging
Log **detailed** errors
Log **detailed** warnings
Keep info logs to minimum
    - use metrics for analytics
    - use dedicated auditing for data flow tracking

# Working on unknonw areas
