---
layout: single
title:  "Software Engineering Philosophy"
date:   2022-03-13 18:15:52 +0300
tags: software-processes
# classes: wide
---
This post talks about *software engineering philosophy*; the list and priorities of the fundamental *values* we, software engineers, use to decide how to approach tradeoffs in software engineering problems.

## Introduction
The nature of our software engineering involves a continuous need for decision making around the design, development and delivery of software.

While there is a general consensus on many fundamental best practices within the software engineering community, there are some topics that are open to arguments.  

This is natural and expected; Through our experience we reflect on approaches that produce good and bad results from which, through time, we form opinions and define principles that shape the way we work. This set of fundamental principles can be thought as *values* that compose an individual's *"software engineering philosophy"*.

In the next sections I will be sharing my view on the importance of having a software philosophy and the framework with the values around how I approach software egnineering projects.

## The philosophy analogy
I am an admirer of philosophy with a strong interest in *ethics* and especially practical ethics.

What I admire most about the philosophy of ethics, is that through the study of different concepts on how one should live an *ethical* life as per their own terms, we can form **fundamental values** that are used as a guide in our decision making. 

These fundamental values can significantly increase the quality of life by making it simpler. When these values are engraved through one's character and become their identity, decision making becomes much more straightforward in complicated cases we might be exposed to.

Without the guidance of fundamental values that are previously well-established, it can become very difficult to make *"correct"*, as per our own terms, decisions.

This is why self-reflection and the study of important philosophy concepts is such a useful tool for an in-control and happy life. 

## The importance of software engineering philosophy

The concept of philosophy and fundamental values can be applied in software engineering as well.

The nature of software engineering involves a continuous need for decision making around the design, development and delivery of software. Having a set of fundamental principles/values on the way we approach problems can greatly simplify the decision making process, and in the software engineering world, prioritization too.

As per their universal nature, these principles are **programming language-independent** and **tool-agnostic**.

Similarly to moral values, in software engineering, these values are slowly engraved in our professional identity through our study and experience on applying different methods of work.

Analogously to philosophy, defining, tracking and ***consciously*** evaluating these personal-formed values over time is a significant step to personal growth.

It is also very interesting to see from an individual's perspective, how our views change through time by our experiences and advances within the industry.

# My software engineering philosophy

I had the opportunity to work on a various projects since I started working as a software engineer and through this time I have made multiple mistakes, I sometimes focused on the wrong things and also took decisions that proved to be the correct ones in the long run. At the same time I have read, discussed and studied multiple ways of approaching software engineering projects and experimented with different techniques and suggestions. 

Through this time and experience, I have formed my own values, which I continuously revisit and evaluate as I learn new things and gain experience in new situations.

The list of values can expand to multiple different items from development, to application and architecture design, processes, and workflows. In this document I decided to focus on the core decision making factors that touch on the development and application design of code.

## On tradeoffs in Software development

In software engineering, in the decision making process everything is a tradeoff. 

Software can be characterized by many properties; Some of the main core properties that we always look into are:
1. Correctness
2. Maintainability
    - Readability
    - Simplicity
    - Extensibility
3. Performance

In general, my view on the prioritization of the above when I work on a new project is **generally** the following:

**Correctness > Readability > Simplicity > Extensibility > Performance**

My values around tradeoffs are represented by the following simplistic rules:
1. Always strive for correctness at all costs. Meet the software requirements.
2. Make your code readable. If not for others, for your future self.
3. Strive for simplicity. Simplicity promotes clarity, minimizes errors and reduces the effort to maintain code.
4. Write extensible code only if it is guaranteed to be needed.
5. Don't sacrifice any of the above for performance optimizations if not absolutely required.
6. Never apply pre-mature optimizations. Profile, measure and analyze.

## On Correctness

Software applications have a well-defined purpose that is described by their requirements.

Correctness is the only mandatory property of any software. **As long as the behavior of the application does what it needs to do without any errors, the software is useful and it's also deliverable**. For PoC and early experimental MVP stage products this can be the only requirement.

For large projects, ensuring correctness can be challenging. We can increase the correctness confidence of software by introducing processes that:
1. Prevent errors
2. Validate Expected Behavior

### Code Reviews and Pair Programming
Error Prevention can be partly achieved by common practices such as *pair programming* and *code reviews*. Both of these practices ensure that more than one developers validate the code before it is submitted so we have more eyes that can identify potential issues at the implementation stages.

### Testing
The most important tool for ensuring correctness is **testing**. Testing has a multitude of benefits and is critical to achieve correctness. Unit testing as well as BDD tests and e2e tests for more complicated flows are essential for validating the behavior of software.

My approach on testing is:
1. Always test your software. Any test is better than no test
2. Isolated edge case testing should be done in unit-tests and component tests. Small scope testing is much simpler, faster and easier to maintain. Use this to your advantage
3. Choose the test types on a case-by-case scenario. Absolute, strict opinions such as "unit tests are better", "use only black box" testing might be right for one scenario but not optimal for another.

Testing is of course a huge topic with multiple dimensions that span from choosing the types of test to testing strategies and how to approach testing from a repo, all the way to an organization level; The purpose of this article is not to analyze the how, but the importance of it.

## On maintainability

One of the most common properties software engineers strive for, is *maintainability*. Maintainability is a term that encapsulates multiple attributes but usually it is associated with code that has the following characteristics:
1. Easy to troubleshoot
2. Easy to extend 

These two characteristics are achievable by writing code that is:
- Readable
- Simple (Low complexity)
- Easy/Safe to change - Decoupling and High Cohesion
- Extensible

### Readability and Simplicity
Readability and simplicity are two properties that prevent errors when developing and reviewing software.
These two are excellently represented by the following principles:
- YAGNI - You Ain't Gonna Need It
- KISS - Keep It Stupid Simple

On top of this for readability we should always strive for **consistency** in coding style and the naming of coding components

Comments:
Comment why - don't comment what

### Decoupling, High Cohesion & Extensibility
Decoupling, High Cohesion and Extensibility are the most adored properties by engineers. It's what the cool, smart kids do. It's also what most software engineers consider good quality code.

Some of the principles we use for decoupling and extensibility though, often have a negative effect on readability and simplicity.
The level in which decoupling and extensibility is needed for a software varies; It is very common to over-engineer solutions where instead we should be following the YAGNI(You Aint Gonna Need It) principle. 

At the same time all large, growing systems always change and require a high level of extensibility.

Easiness for change is tackled by promoting two main characteristics: **low-coupling** and **high-cohesion**. These are often achieved by the famous SOLID principles that I always strive to follow
- SRP - Single Responsibility Principle
- Open-Closed Principle
- Liskov's Substitution 
- Interface Segregation
- Dependency Inversion

Extensibility is achieved mainly by a mixture of design patterns and an application architecture. I have worked with multiple architecture patterns and I tend to find that an application architecture such as **clean architecture** solves greatly the problem of extensibility through the promotion of:
- Separation of Concerns
- Platform independability


## On Performance
Another common property that describes software quality is performance.
General performance considerations should always be part of software, but performance optimizations that inhibit readability and simplicity without a solid requirement should be avoided.

Premature optimization is unfortunately applied in many projects unecessarily and often introduces uneeded complexity and a low cost vs value benefit.

## On troubleshooting
- Be prepared - the most important
- Alerts, alerts alerts A version of the The Hollywood Principle - Don't call us, we'll call you
- Observability - smart metrics

- Logging
Log wisely


## On Technical Requirement Analysis
Technical analysis should be always done before design and development. The biggest issues I faced in my career were issues on unclear understanding of requirements and a gap of alignment between business, product and engineering.
All these can be avoided by following a methodical workflow.

## On SDLCs
Frequent, incremental cycles offers invaluable early feedback that is impossible to achieve in a waterfall-like lifecycle. Strict frameworks are difficult to follow by the book and that's fine. Adaption within the team

# My philosophy on Software Development
* Logging
* Verbosity