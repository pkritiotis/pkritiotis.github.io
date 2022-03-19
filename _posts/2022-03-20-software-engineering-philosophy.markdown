---
layout: single
title:  "Software Engineering Philosophy"
date:   2022-03-20 18:15:52 +0300
tags: software-philosophy
toc: true
# classes: wide
---
This post talks about *software engineering philosophy*; the list and priorities of the *fundamental values* we, software engineers, use to decide how to approach tradeoffs in software engineering problems.

# Introduction
The nature of software engineering involves a continuous need for decision making around the design, development and delivery of software. It also expands in other topics such as how we decide to approach processes and organization.

While there is a general consensus on many fundamental best practices within the software engineering community, there are some topics that are open to arguments, and preferences.  

Disagreement is natural and expected. It's a matter of perspective and experience; Through our experience we reflect on approaches that produce good and bad results from which, through time, we form opinions and define principles that shape the way we work. This set of fundamental principles can be thought as *values* that compose an individual's *"software engineering philosophy"*.

In the next sections I will be sharing my view on the importance of having a software philosophy and the framework with the values around how I approach software egnineering projects.

This will be the first of a series of articles I am planning to write on this topic since software engineering philosophy covers many broad topics. 

# The philosophy analogy
I am an admirer of philosophy with a strong interest in *ethics* and especially practical ethics.

What I admire most about the philosophy of ethics, is that through the study of different concepts on how one should live an *ethical* life as per their own terms, we can form **fundamental values** that are used as a guide in our decision making. 

These fundamental values can significantly increase the quality of life by making it simpler. When these values are engraved through one's character and become their identity, decision making becomes much more straightforward in complicated cases we might be exposed to.

Without the guidance of fundamental values that are previously well-established, it can become very difficult to make *"correct"*, as per our own terms, decisions.

This is why self-reflection and the study of important philosophy concepts is such a useful tool for an in-control and happy life. 

# The importance of software engineering philosophy

The concept of philosophy and fundamental values can be applied in software engineering as well.

The nature of software engineering involves a continuous need for decision making around the design, development and delivery of software. Having a set of fundamental principles/values on the way we approach problems can greatly simplify the decision making process, and in the software engineering world, prioritization too.

As per their universal nature, these principles are **programming language-independent** and **tool-agnostic**.

Similarly to moral values, in software engineering, these values are slowly engraved in our professional identity through our study and experience on applying different methods of work.

Analogously to philosophy, defining, tracking and ***consciously and deliberately*** evaluating these personal-formed values over time is a significant step to personal growth.

It is also very interesting to see from an individual's perspective, how our views change through time by our experiences and advances within the industry.

# My software engineering philosophy

I had the opportunity to work on a various projects since I started working as a software engineer and through this time I have made multiple mistakes, I sometimes focused on the wrong things and also took decisions that proved to be the correct ones in the long run. At the same time I have read, discussed and studied multiple ways of approaching software engineering projects and experimented with different techniques and suggestions. 

Through this time and experience, I have formed my own values, which I continuously revisit and evaluate as I learn new things and gain experience in new situations.

The list of values can expand to multiple different items from development, to application and architecture design, processes, and workflows.

In this first article of this series, I decided to focus on the core decision making factors that touch on the tradeoffs we get to balance during the development and application design of code.

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
2. Make your code readable. For others, but also for your future self.
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

### Prevent errors via Code Reviews and Pair Programming
Error Prevention can be partly achieved by common practices such as *pair programming* and *code reviews*. Both of these practices ensure that more than one developers validate the code before it is submitted so we have more eyes that can identify potential issues at the implementation stages.

These two practices are indeed very useful and offer a large list of benefits expanding well over the correctness of software. For correctness specifically however, code reviews and pair programming are not 100% effective and adequate. They are helpful but they do not cover correctness to a large degree, since as humans we are always somewhat biased and we have limited focus which causes bugs to be missed.

This leads to a more effective and robust way to ensure correctness, which is testing.

### Testing
The most important tool for ensuring correctness is **testing**. Testing has a multitude of benefits and is critical to achieve correctness. Unit testing as well as BDD tests and e2e tests for more complicated flows are essential for validating the behavior of software.

My list of most important principles around testing are:
1. Always test your software. Any(meaningful) test is better than no test
2. Isolated edge case testing should be done in unit-tests and component tests. Small scope testing is much simpler, faster and easier to maintain. Use this to your advantage
3. Choose the test types on a case-by-case scenario. Absolute, strict rules/opinions such as *"unit tests are better"*, *"use only black box testing"* might be right for one scenario but not optimal for another.
4. Testing is much easier if you write *testable* code. Invest in a design that allows easy testing of both very specific and very generic modules
  - Overall, while testing's main purpose is Correctness it greatly contribute to Maintainability too. A testable code is also more maintainable.

On top of the above, I have formed some guidelines around when I tend to choose between specific types of testing. This is very closely related to my preference on architecture design philosophy such as the Clean Architecture:
1. Write unit tests for business and domain logic. Mock non business-logic components
2. Write BDD tests in a unit-style fashion (cucumber-like) for flows that expand in multiple business logic components to test impact of an action on different components. These should align with business use-cases
3. Write primarily isolated integration tests for external service integrations. Use unit tests for very specific edge case scenarios
4. Write end to end tests only for critical flows. Use them as sanity tests

For the purpose of this article I'm limiting by guidelines to the importance of testing and some guidelines on choosing the types of tests. Testing is of course a huge topic with multiple dimensions that span from choosing the types of test to testing strategies and how to approach testing from a repo, all the way to an organization level.

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
- YAGNI - You Ain't Gonna Need It TODO:ref
- KISS - Keep It Stupid Simple TODO:ref

On top of these two principles, there are two attributes that greatly contribute to readable code:
1. **Consistency** in coding style and the naming of coding components
2. **Low verbosity** in coding. This is related to simplicity too


### Decoupling, High Cohesion & Extensibility
Decoupling TODO:ref, High Cohesion TODO:ref and Extensibility are the most adored properties by engineers. It's what the cool, smart kids do. It's also what most software engineers consider good quality code.

Some of the principles we use for decoupling and extensibility though, often have a negative effect on readability and simplicity.
The level in which decoupling and extensibility is needed for a software varies; It is very common to over-engineer solutions where instead we should be following the YAGNI principle. 

At the same time, all large, growing systems always change and require a high level of extensibility.

Easiness for change is tackled by promoting two main characteristics: **low-coupling** and **high-cohesion**. These are often achieved by the famous SOLID TODO:ref principles that I always strive to follow
- SRP - Single Responsibility Principle
- Open-Closed Principle
- Liskov's Substitution 
- Interface Segregation
- Dependency Inversion

Extensibility is achieved mainly by a mixtur`e of design patterns and an application architecture. I have worked with multiple architecture patterns and I tend to find that an application architecture philosophies such as **clean architecture** solves greatly the problem of extensibility through the promotion of:
- Separation of Concerns
- Platform independability

### Troubleshooting
Readability, Simplicity and Extensibility make troubleshooting easier but we also have specific tools to greatly enhanve the troubleshootability of our software.

Most software engineers, including me, spend a long time troubleshooting code. Many times the time spent in troubleshooting is much bigger than the time spent in developing new code. The principles I have with regards to troubleshooting are:
- Be prepared - the most important
  - Alerts, alerts alerts. Add alerts that track abnormal behavior. Follow a version of "The Hollywood Principle" from a troubleshooting perspective: Don't call us, we'll call you
  - Log wisely. Especially errors, warning and important flows that contain information that you might required when debugging.
- Observability - smart metrics

## On Performance
Another common property that describes software quality is performance.
General performance considerations should always be part of software, but performance optimizations that inhibit readability and simplicity without a solid requirement should be avoided.

Premature optimization is unfortunately applied in many projects unecessarily and often introduces uneeded complexity and a low cost vs value benefit.

# Conclusion

The notion of Software engineering philosophy is something I believe that every single engineer should try to form and review occasionally and reavaluate as they grow in their carreers.

This article contains the introduction to what I consider as software engineering philosophy and its significance as well as my view on choosing between tradeoffs of software quality attributes.