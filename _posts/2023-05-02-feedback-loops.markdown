---
layout: single
title:  "Planning, adaptability and feedback loops in software engineering"
date:   2023-05-02 23:50:00 +0300
tags: software-processes
toc: true
# classes: wide
---
In this blog post I write about the power of feedback loops and the factors of forming an ideal feedback loop.

# Introduction

Our goal as software engineers, is to deliver software projects. Personal projects, work projects; projects of any type, size and industry.

To achieve any type of goal, not only in software engineering, one of the most first steps to begin with is some type of ***planning***.
Even for small-size, solo projects, a little bit of planning is often very useful. More significantly, in big projects and especially in projects that require one or more teams to work together, planning is one of the first and most important steps towards a successful project.

While planning is a necessary step for achieving our goals, it's not enough to ensure an efficient delivery of a software project.
It's inevitable that we'll face some hiccups with our initial plan, or missed something or something unexpected happen. For this reason it's important that we **adapt** and get back on the right track.
One of the most powerful tools that we can use in every single stage of a software engineering lifecycle, are ***feedback loops***.

Feedback loops enable us to continuously check whether we are on the right track towards achieving our goals, identify where we can optimize, and then make the following adjustments to improve our process towards achieving our goal.

In software engineering, feedback loops are everywhere, even if we don't notice.
And the efficiency of achieving anything **is all about optimizing the feeback loops**.

In the following sections, we'll see why planning is essential but plans are useless, the need for adaptability in our planning and processes and then we'll see the power and characteristics of a good feeback loop.


# Plans are useless, planning is indispensable
As we set our mind to achieve something a common start is setting up our big goals.
To achieve these goals we need do some planning.
It doesn't have to be a concrete plan, it might be a systematic process that will eventually get us there.
But we still need to have a high-level idea on the path that we need to achieve this.

It's usually a good idea to break down the goal into smaller achievable minigoals. Mini-goals can be a part of the bigger goal or a step towoards the right direction of achieving a goal. Whatever the case, we need some kind of planning to get there.

**Unfortunately concrete plans, especially the long-term ones, are almost certainy doomed to fail**. As the famous quote by Eisenhauer states *Plans are worthless, but planning is everything*.

Life happens. "A task is more complicated than expected", "Someone left the team", "My cat spilled coffee on my laptop".

Before we know it we derail from our initial goal. **And that's expected.** 

\- So how do we manage this?

We first need to ***recognize the issue***, and then **adapt** to the situation.
We address upcoming issues and we work our way to go back on track.

The tricky thing about recognizing the issues, is that sometimes problems are not obvious. Without the required visibility and tools it's easy to miss issues that are inhibiting our progress.

## The effort of adapting is not linear 

We can only adapt once we detect the issue. The the quicker we discover the issue, the quicker we can react.
The quicker we react, the less effort we need to resolve the issues and get back on track. This relation is very critical. Small issues can be solved quickly.

However the effort to solve issues increases dramatically if we don't identify the problem early in the process.
The above statement should not be underestimated.

How do we detect issues in the progress of a project/goal? **Feedback loops of course**
# The power of feedback loops

## What are feedback loops ?
> Feedback occurs when outputs of a system are routed back as inputs as part of a chain of cause-and-effect that forms a circuit or loop.[^feedback loop]

We can't detect a problem unless we evaluate whether our current progress and way of working is on the right track. To do that we need to ensure that our workflow provides:

1. A way to evaluate the progress
2. A continuous/periodic evaluation step in the process

This is what a feeback loop is about:

1. Start working on the task
2. Collect data about the progress of the task
3. Evaluate the data and check if we are in the right direction
4. Make informed adjustments depending on the outcome
5. Repeat

## What are the benefits of using feedback loops?

The most important benefit of feedback loops is **efficiency** and confidence on the quality of the project.

By introducing a feedback loop in a process you have:

1. Early detection of issues -> Quicker response time and time to react
2. An informed more accurate view about the current progress of a project
todo

# Characteristics of an efficient feedback loop 

Having a feedback loop though is not enough. A good feedback loop requires the following characteristics:

1. Frequent
2. Quick
3. Specific
4. Measureable
5. Automated
6. Actionable

Let's explore each one of these characteristics below
## Frequent, timely feedback loops
Is having a feedback loop in our project enough?

Well, a whole project process can have a single feedback loop; What does this mean? 

1. We start the project
2. We finish all of the work
3. We check the outcome in the end
 
If we do that it will be too late before we know that we have a problem.
If we want to gain the advantage of quick identification of issues, we need regular feedback loops.
## Quick feedback loops
Regular feedback loops are also not enough, we need quick feedback loops.
If getting the feedback is slow, then we are delaying the progression from a process step.

If it is very slow, we might loose the value of having the feedback loop since it might be too late.

The feedback loop should be quick. As quick as possible, depending on the use case of course.

## Specific
Ok so we have regular, timely and quick feedback loops. What do we need from the feeback loop?

Feedback loops should have one goal.

Trying to get feedback about too many things, broadens the focus of the feeback loop and decreases the quality of the results.

## Measurable
One more characteristic of feeback loops is that they should be measurable.
If they are not measureable, they cannot function. This is one of the prerequisites of feedback loops and good, measureable indicators often result in good quality feedback loops

## Automate feedback loops
Ok so we have regular, quick feedback loops, do we need anythign else? Of course yes 😄. 
We need easy to execute feedback loops. How do we get easy to execture feeback loops? Automate feedback loops. Enforce automated feedback loops.

## Actionable feedback
Ok so we got regular, quick, automated feedback loops. Do we need anyting else? Of course we do 😄
We know we have a problem, but it's also critical that we find as soon as possible what's cause the problem. This will enable us to fix the problem soon and continue our unblocked workflow.

How do we find the cause of the problem? Observability and monitoring.

This is applicable in the software engineering world, but it's also applicable in all industries and sectors. From sports, to physics experiments, even in art. 


Tracking each one of these sections is a distinct challenge and the combination of an efficiency in all of these areas results in an efficient workflow.

Let's where feedback loops exist in the software engineering world and how we can optimize them.

# Feedback Loops in Software Engineering
In the software engineering world, companies and individuals work on projects. From huge projects such as a software system that will guide a spaceship for a moon landing to very small projects such as a simple calculator.

Let's see some examples of how feedback loops are used in software engineering.
## SDLCs
To deal with software projects we apply software development lifecyle (SDLC) models. SDLC models help us approach a project in a methodical way with a number of steps towards the successful completion of a project. 
All of these SDLC follow the approach of spliting the goal/project into smaller pieces/tasks.
Historically the software industry started with waterfall as a means of completing a project and more recently we see *Agile* being the most prominent model that most teams use.

Agile as a philosophy is proven to be the most successful in adapting to a situation of ever-chaging requirements and conditions and adapting fast to remove blockers and change direction. It also uses the notion of incremental deliverables which further improves the concept of smaller tasks to tasks that are not only part of the greater goal, they are actualy a working version of a subset of the overall goal.

However the most important concept of Agile is **continuous feedback**. In agile a team works in small increments with the ultimate goal of getting quick feedback. This feedback is the most valuable asset in this process as it dictates the direction of the project and ultimately, the project survical based on the results.

Scrum
- SU - feedback on the daily blockers
- Grooming - feedback on what/how we are building
- Planning - feedback on what we are trying to achieve
- Retrospective - feedback on how we are doing
- Demo - feedback on what we built


## Development

Developers usually work on small tasks. They get assigned to a task, and they start writing their code that will fulfill the acceptance criteria of the task in hand. 

The efficiency of the developer, even in that small tasks lies on the development feedback loop.
In development we have multiple feedback loops.
1. Low-level ones - syntax highlighting, compile, lint, build success or error
2. Unit-tests - run test -> success or fail
   1. TDD
3. Integration tests -> success or fail
4. e2e tests
5. CI pipeline

### Deployment
1. Canary releases
2. Blue green deployments
3. Synthetic tests
4. Vulnerability scans

### Experiment
 The power of the discover/spike

### The RFC

## Management
- one o ones
- performance evaluation


# Learning in General
- Learning process
	- formal informal practical
	- another dimenstion - make mistakes - learn improve

# Measure
- Feedback loops feedback loops everywhere
	- Workflow
		- delivery cycles - sprint goal/milestone
		- task blockers - standup
		- process feedback - retrospective
	- Development
		- test - fix - repeat
	- The power of the spike
		- experiment learn repeat
	- Design
		- design, limit, redesign repeat
	- RFCs
- Yagni
- Small tasks
- The importance of measurements
	- measure before you start the work
	- slos and slis
	- don't optimize for measure optimize for the goal
	- difficult
	- it''s just another feedback loop