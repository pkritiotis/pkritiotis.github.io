---
layout: single
title:  "Software Engineering Philosophy"
date:   2022-01-29 14:15:52 +0300
tags: software-processes
# classes: wide
---
This post contains my *software engineering philosophy*; a list of the fundamental *values* that I have accumulated over the years and that I use to decide how to work on software engineering problems.

## Introduction
The nature of our software engineering involves a continuous need for decision making around the design, development and delivery of software.

While there is a general consensus on many fundamental best practices within the software engineering community, there are some topics that cause arguments.  

This is normal; Through our experience we reflect on approaches that produce good and bad results from which, through time, we form opinions and define principles that shape the way we work. This set of fundamental principles can be thought as *values* that compose an individual's *"software engineering philosophy"*.

In the next sections I will be sharing my view on the importance of having a software philosophy and the framework with the values around how I approach software egnineering projects.

### The philosophy in Software Engineering

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

Over the years I have read, discussed and studied multiple ways of approaching software engineering projects and experimented with different techniques and suggestions. 

During this time I have formed my own values, which I continuously revisit and evaluate as I learn new things and gain experience in new situations.

## Tradeoffs in Software development

Software can be characterized by three main properties:
- Correctness
- Maintainability
    - Readability
    - Simplicity
    - Extensibility
- Performance

In software engineering, the decision making is complicated; everything is a tradeoff. 
In general, my view on the prioritization of the above when I work on a new project is **generally** the following:

Correctness > Readability > Simplicity > Extensibility > Performance

### Correctness
Each software has a well-defined purpose that is described by its requirements.

Correctness is the only mandatory property of any software. We can increase the correctness confidence of software by introducing processes such as pair programming, code reviews so that more than one developers can identify potential issues at the implementation stages. The most important tool though is testing. Testing has a multitude of benefits and is critical to achieve correctness. 

### Maintainability
One of the common properties we strive for is *maintainability*. Maintainability is a word that can mean a lot of things but usually it is associated with code that has the following characteristics:
1. Easy to troubleshoot
2. Easy to extend 

These two characteristics are achievable by writing code that is:
- Readable
- Simple
- Extensibile

Readability and simplicity are two attributes that prevent errors when developing and reviewing software.

Extensibility is much more complicated. Sometimes extensibility is not needed at all and we should follow the YAGNI(You Aint Gonna Need It) principle. If however we build a system that is by nature going to be extended, then extensibility is necessary.

### Performance
Another common property that describes software is performance.
General performance considerations should always be part of software, but performance optimizations that decrease in readability and simplicity without a solid requirement should be avoided.

Premature optimization is unfortunately applied in many projects unecessarily and introduces uneeded complexity.

