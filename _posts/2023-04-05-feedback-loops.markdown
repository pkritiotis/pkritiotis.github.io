---
layout: single
title:  "It's all about optimizing feedback loops"
date:   2023-04-02 23:50:00 +0300
tags: software-engineering-philosophy
toc: true
# classes: wide
---
In this blog post I write about the need for optimizing feedback loops with a focus in software engineering.

# Introduction
Most of us have some aspirations in life and work. Even for those who don't, all of us in our daily life we wake up and we act to achieve a specific outcome. This can be a small goal at work of getting my assigned task done by the end of day, or study by chapter X so that I can have good chances in passing my exam next week.

To increase the efficiency with how we move for achieving our goals - big or small - every single step in the process of achieving big and small goals should consist of feeback loops.

The efficiency of achieving anything is all about optimizing the feeback loops.


# Plans are useless, planning is indispensable
As we set our mind to achieve something we often start with setting up our big goals. To achieve those goals we need to do some planning. It doesn't have to be a concrete plan, it might be a systematic process that will eventually get us there. But we still need to have a high-level idea on the path that we need to achieve this.

It's usually a good idea to break down the goal into smaller achievable minigoals. Mini-goals can be a part of the bigger goal or a step towoards the right direction of achieving a goal. Whatever the case, we need some kind of planning to get there.

Unfortunately concrete plans, especially the long-term ones, are almost certainy doomed to fail. Life happens, things change and before we know it we derail from our initial goal. And that's expected. After all, as the famous quote by Eisenhauer states *Plans are worthless, but planning is everything*.

So how do we manage this? We *adapt* to the situation. We address upcoming issues and we work our way to go back on track.

### The effort of adapting is not linear 
The challenge here is that we can only adapt once we detect the issue. The the quicker we discover the issue, the quicker we can react.

And the quicker we react, the less effort we need to resolve the issues and get back on track. This relation is very critical. Small issues can be solved quickly. However the effort to solve issues increases dramatically if we don't identify the problem early in the process  

# The power of feedback loops

How do we detect issues in the progress of a project/goal? **Feedback loops of course**

We can't detect a problem unless we evaluate whether our current progress and way of working is on the right track. To do that we need to ensure that our workflow provides:
1. A way to evaluate the progress
2. A continuous/periodic evaluation step in the process

This is what a feeback loop is about:
1. Work on a task
2. Check if we are in the right direction
3. Adjust depending on the outcome
4. Repeat

Having a feedback loop though is not enough. A whole project process can be a single feedback loop.
We can only be efficient if we have regular feedback loops. Otherwise it can be too late before we know that we have a problem.

Regular feedback loops are also not enough, we need quick feedback loops. If getting the feedback is slow, then we are delaying the progression from a process step. The feedback loop should be quick.

Ok so we have regular, quick feedback loops, do we need anythign else? Of course yes 😄. 
We need easy to execute feedback loops. How do we get easy to execture feeback loops? Automate feedback loops. Enforce automated feedback loops.

Ok so we got regular, quick, automated feedback loops. Do we need anyting else? Of course we do 😄
Ok we have a problem, we know that, what else do we need? Well, we need to be able to identify what the problem. Recognizing that we have a problem is the first part, but it's also critical that we find as soon as possible what's cause the problem. This will enable us to fix the problem soon and continue our unblocked workflow.

The answer, observability and monitoring. This is applicable in the software engineering world, but it's also applicable in all industries and sectors. From sports, to physics experiments, even in art. 

Tacking each one of these sections is a distinct challenge and the combination of an efficiency in all of these areas results in an efficient workflow.

Let's where feedback loops exist in the software engineering world and how we can optimize them.

# Feedback Loops in Software Engineering
In the software engineering world, companies and individuals work on projects. From huge projects such as a software system that will guide a spaceship for a moon landing to very small projects such as a simple calculator. 

## SDLCs
To deal with software projects we apply software development lifecyle (SDLC) models. SDLC models helps us approach a project in a methodical way with a number of steps towards the successful completion of a project. 
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
3. Integration tests -> success or fail
4. e2e test
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