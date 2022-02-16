---
layout: single
title:  "Software Engineering Philosophy"
date:   2022-01-29 14:15:52 +0300
tags: software-processes
# classes: wide
---
This post contains my *software engineering philosophy*; the list and priorities of the fundamental *values* I use to decide how to approach software engineering problems.

## Introduction
The nature of our software engineering involves a continuous need for decision making around the design, development and delivery of software.

While there is a general consensus on many fundamental best practices within the software engineering community, there are some topics that are open to arguments.  

This is normal; Through our experience we reflect on approaches that produce good and bad results from which, through time, we form opinions and define principles that shape the way we work. This set of fundamental principles can be thought as *values* that compose an individual's *"software engineering philosophy"*.

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

Since I started studying and then working as a software engineer I have made multiple mistakes, I sometimes focused on the wrong things and had the opportunity to work on a various projects. At the same time I have read, discussed and studied multiple ways of approaching software engineering projects and experimented with different techniques and suggestions. 

Through this time, I have formed my own values, which I continuously revisit and evaluate as I learn new things and gain experience in new situations.

## Prioritizing Tradeoffs in Software development

In software engineering, the decision making is complicated; everything is a tradeoff. 

Software can be characterized by many properties; Three main core properties are:
- Correctness
- Maintainability
    - Readability
    - Simplicity
    - Extensibility
- Performance

In general, my view on the prioritization of the above when I work on a new project is **generally** the following:

Correctness > Readability > Simplicity > Extensibility > Performance

I live by the following simplistic rules:
1. Always strive for correctness at all costs. Meet the software requirements.
2. Make your code readable. If not for others, for your future self.
3. Strive for simplicity. Simplicity promotes clarity, minimizes errors and reduces the effort to maintain the code.
4. Write extensible code only if it is guaranteed to be needed.
5. Don't sacrifice any of the above for performance optimizations if not required.
6. Never apply pre-mature optimizations. Profile, measure and analyze.

### Correctness

Software applications have a well-defined purpose that is described by its requirements.

Correctness is the only mandatory property of any software. As long as the behavior of the application does what it needs to do without any errors, the software is useful and it's also deliverable. For PoC and early experimental MVP stage products this can be the only requirement.

For large projects, ensuring correctness can be challenging. We can increase the correctness confidence of software by introducing processes that:
1. Prevent errors
2. Validate Behavior

Error Prevention can be achieved by practices such as *pair programming* and code reviews; Both of these practices ensure that more than one developers validate the code before it is submitted so we have more eyes that can identify potential issues at the implementation stages.

The most important tool though is **testing**. Testing has a multitude of benefits and is critical to achieve correctness. Unit testing as well as BDD tests and e2e tests for more complicated flows are essential for validating the behavior of software.

### Maintainability

One of the common properties software engineers strive for, is *maintainability*. Maintainability is a term that encapsulates multiple attributes but usually it is associated with code that has the following characteristics:
1. Easy to troubleshoot
2. Easy to extend 

These two characteristics are achievable by writing code that is:
- Readable
- Simple
- Extensibile

Readability and simplicity are two properties that prevent errors when developing and reviewing software.
These two are excellently represented by two of my favorite principles:
- YAGNI - You Ain't Gonna Need it
- KISS - Keep It Stupid Simple

Extensibility is much more complicated. Sometimes extensibility is not needed at all and we should follow the YAGNI(You Aint Gonna Need It) principle. If however we build a system that is by nature going to be extended, then extensibility is necessary.
In this case we opt for **low-coupling** and **high-cohesion**. The principles I almost always follow are:
- SRP - Single Responsibility Principle
- Platform Independability

### Performance
Another common property that describes software is performance.
General performance considerations should always be part of software, but performance optimizations that inhibit readability and simplicity without a solid requirement should be avoided.

Premature optimization is unfortunately applied in many projects unecessarily and introduces uneeded complexity.

## Summary

1. Correctness
   - Unit Testing
   - E2E Testing
   - BDD Testing
   - Integration Testing
2. Maintainability
   - YAGNI
   - KISS
   - SRP
3. Performance


## Documentation
Once upon a time I used to hate documentation and now I am a big proponent of documentation.
Documentation is an investement. At the same time documentation needs care; if it gets outdated it can become useless.

1. Document Business Use Cases in Detail
2. Document Developer's Handbook for projects with multiple contributors
3. Document a Troubleshooter's guide for common issues and FAQ

