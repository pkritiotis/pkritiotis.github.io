---
layout: single
title:  "Planning, adaptability, and feedback loops in software engineering"
date:   2023-05-20 12:20:00 +0300
tags: software-processes
toc: true
# classes: wide
---
In this blog post, I write about the power of feedback loops and the essential characteristics of the ideal feedback loop.

# Introduction
Our goal, as software engineers, is to deliver software projects. Personal projects, work projects, and projects of any type, size, and industry.

<mark>To achieve any goal, one of the first steps, to begin with, is some planning</mark>.
Even for small-size, solo projects, some planning is often handy. But, more significantly, in big projects, especially those requiring one or more teams to work together, planning is one of the first and most essential steps toward a successful project.

While planning is a necessary step for achieving our goals, it's not enough to ensure the efficient delivery of a software project.
<mark>We'll inevitably face some hiccups</mark> with our initial plan or miss something, or something unexpected might happen. For this reason, <mark>it's crucial that we adapt</mark> and get back on the right track.

One of the most powerful tools we can use in every stage of a software engineering lifecycle is ***feedback loops***.
<p align="center">
  <img alt="dependencies-everywhere" src="/assets/images/arrow-right.png" width="200">
  <br>
    <small>Image by <a href="https://github.com/MariaLetta/free-gophers-pack"> Maria Letta @free-gophers-pack</a></small>
</p> 
Feedback loops enable us to continuously check whether we are on the right track toward achieving our goals, identify where we can optimize, and then make the corresponding adjustments to improve our process toward achieving our goal.

Feedback loops are everywhere in software engineering, even if we don't notice.
And the efficiency of achieving anything <mark>is all about optimizing the feedback loops</mark>.

In the following sections, we'll see:

1. Why while planning is essential, plans are useless
2. The need for adaptability in our planning and processes
3. The power and characteristics of a good feedback loop.

# Plans are useless; planning is indispensable
As we set our minds to achieve something, a typical start is setting our big goals.
Then, to achieve these goals, we need to do some planning.
It doesn't have to be a concrete plan; it might be a systematic process eventually getting us there.
But we still need a high-level idea of our path to achieve this.

<mark>Unfortunately, concrete plans, especially the long-term ones, are almost certainly doomed to fail</mark>. 

As the famous quote Eisenhower states *"Plans are worthless, but planning is everything"*[^Plans-are-useless].

Life happens. "*A task is more complicated than expected*", "*Someone left the team*", "*My cat spilled coffee on my laptop*".

Before we know it, we derail from our initial goal. And <mark>that's expected</mark>.

\- *So, how do we manage unexpected incidents that impact our plans?*

1. We first need to <mark>recognize that there is an issue</mark>.
2. Then we need to <mark>identify what's wrong</mark>.
3. And then <mark>adapt to the situation</mark>.
4. Finally, we address upcoming problems and work to get back on track.

## The problem is not always obvious
Of course, this process is not always straightforward.

<p align="center">
  <img alt="dependencies-everywhere" src="/assets/images/find.png" width="200">
  <br>
    <small>Image by <a href="https://github.com/MariaLetta/free-gophers-pack"> Maria Letta @free-gophers-pack</a></small>
</p> 

The tricky thing about recognizing an issue is that sometimes problems are not obvious. Without the required visibility and tooling, it's easy to miss problems inhibiting our progress.

## The effort of adapting is not linear

We can only adapt once we detect the issue. Therefore, the quicker we discover the problem, the faster we can react.
The faster we respond, the less effort we need to resolve the problems and get back on track. This relation is very critical. Small issues can be solved quickly.

However, <mark>the effort to solve issues increases dramatically if we don't identify the problem early in the process.</mark>
The above statement should not be underestimated.

How do we detect issues in the progress of a project/goal? **Feedback loops, of course**
# The power of feedback loops

Feedback loops are a precious tool that allows us to quickly recognize and react to problems at an early stage.

Let's explore what feedback loops are, their main benefits, and some characteristics that make a good feedback loop.
## What are feedback loops?

Let's check the definition of a feedback loop:

> Feedback occurs when outputs of a system are routed back as inputs as part of a chain of cause-and-effect that forms a circuit or loop.[^feedback-loop]

A feedback loop enables us to continuously measure and evaluate our process toward achieving a goal and, based on the results, make adjustments to course-correct our working methods for a more efficient and productive workflow.
<p align="center">
  <img alt="dependencies-everywhere" src="/assets/images/feedback-loops.png" width="500">
  <br>
    <small> Feedback loops</small>
</p> 

A feedback loop consists of the following steps:

1. **Act**
   - Start with a plan and work on the task
2. **Measure**
   - Collect data about the progress of the task
3. **Assess**
   - Analyze and evaluate the data and check if we are in the right direction
4. **Adjust**
   - Make informed adjustments depending on the outcome
5. **Repeat**

To create a valuable feedback loop, we have the following prerequisites.
We need:

1. A way to measure progress
2. A way to analyze and assess our measurements
3. A way to extract valuable information from the evaluation and measurements
4. A continuous/periodic measure-assessment-action cycle in the process

## The benefits of feedback loops

The primary benefits of feedback loops are
1. Efficiency
2. Productivity through continuous improvement
3. A better understanding of the current progress

### Efficiency

A good feedback loop enables early detection and reaction to problems.
By quickly responding to issues, we prevent wasting time and resources on tasks and practices not aligned with the end goal.

### Productivity through continuous improvement

When we close a feedback loop and decide to proceed with the corresponding adjustments for the next one, there is a sign of progression and improvement. 

Whether in a team scope or individually, these adjustments cultivate a culture of continuous improvement and growth, resulting in greater overall productivity.

### A better understanding of the current progress
Feedback Loops, by design, require some form of measurement to work. These measurements provide an informed view about the recent progress of a project which would be missing otherwise.

# Characteristics of an efficient feedback loop 

A good feedback loop requires the following characteristics:

1. Frequent
2. Quick
3. Specific
4. Takes high-quality measurements
5. Automated
6. Actionable

Tracking each of these characteristics is a distinct challenge, and the combination of efficiency in these areas results in an efficient workflow.

Let's explore each one of these characteristics below
## Frequent, timely feedback loops
Is having a feedback loop in our project enough?

Well, a whole project process can have a single feedback loop; What does this mean? 

1. We start the project
2. We finish all of the work
3. We check the outcome at the end

If we do that, it will be too late before we know we have a problem. 
So if we want to gain the advantage of quickly identifying issues, we need regular feedback loops ☝️.
## Quick feedback loops

We need _quick_ feedback loops. 
For example, if the collection, analysis, or assessment stages are slow, we delay the progression from a process step.

Even worse, if they are very slow, we might lose the value of having the feedback loop since it might be too late.

A feedback loop should be quick ☝️. 

As quick as possible, depending on the use case, of course.

## Specific
Ok, so we have regular, timely, and quick feedback loops. So what else do we need from the feedback loop?

**Feedback loops should have a specific purpose and only one goal ☝️.**

Trying to get feedback about too many things broadens the feedback loop's focus and decreases the results' quality.

❗A common problem when designing feedback loops is overcomplicating multiple metrics, making them hard to understand and use.

## Quality Measurements
One more characteristic of feedback loops is that they should have high-quality measurements.

High quality in terms of specificity and accuracy.

Wrong indicators might drive us to the wrong conclusions or reduce our efficiency toward our end goal.

For example, Lines of Code or Commits per day is a wrong indicator for measuring a team's productivity.   
On the other hand, metrics such as Service Level Agreements (SLAs) and Service Level Objectives(SLOs) of a specific service can be much better indicators.

Only highly accurate indicators and measurements can result in good-quality feedback loops.

## Automate feedback loops
Ok, so we have regular, quick feedback loops. Do we need anything else? Of course, we do 😄. We need easy-to-execute feedback loops. 

<p align="center">
  <img alt="dependencies-everywhere" src="/assets/images/fix.png" width="200">
  <br>
    <small>Image by <a href="https://github.com/MariaLetta/free-gophers-pack"> Maria Letta @free-gophers-pack</a></small>
</p> 

\- How do we get easy-to-execute feedback loops? 

Automate feedback loops ☝️. Enforce automated feedback loops.

## Actionable feedback
Even if we have recognized a problem, it may not be obvious how to solve it.
An essential characteristic of a good feedback loop is providing as much information as possible on solving the problem as soon as possible.

This will enable us to fix the issue soon and continue our unblocked workflow.

❗A common pitfall in this feedback loop characteristic is overoptimizing against the measurement, not the problem. 

How do we find the cause of the problem? Observability and monitoring to the rescue ☝️.

# Feedback Loops in Software Engineering
In the software engineering world, teams or individuals work on projects, from massive projects, such as a software system that guides a spaceship for a moon landing, to small projects, such as a simple calculator.

<p align="center">
  <img alt="dependencies-everywhere" src="/assets/images/read.png" width="200">
  <br>
    <small>Image by <a href="https://github.com/MariaLetta/free-gophers-pack"> Maria Letta @free-gophers-pack</a></small>
</p> 

Let's see some examples of using feedback loops in many areas of software engineering.
## Software Development Life Cycles (SDLCs)
We apply software development life cycle (SDLC)[^sdlc] models to deal with the complexity of software projects.

SDLC models help us methodically approach a project with several steps toward the successful completion of a project. 
All of these SDLC follows the approach of splitting the goal/project into smaller pieces/tasks.
Historically the software industry started with waterfall as a means of completing a project, and more recently, we see *Agile*[^agile] being the most prominent model that most teams use.

<p align="center">
  <img alt="dependencies-everywhere" src="/assets/images/feedback-loops-everywhere.png" width="300">
  <br>
    <small>Image by <a href="https://github.com/MariaLetta/free-gophers-pack"> Maria Letta @free-gophers-pack</a></small>
</p> 

Agile as a philosophy is proven to be the most successful in adapting to ever-changing requirements and conditions and adapting fast to remove blockers and change direction. It also uses the notion of incremental deliverables, which further improves the concept of smaller tasks to tasks that are not only part of the greater goal but are a working version.

However, the essential concept of Agile is centered around **continuous feedback**. In agile, a team works in small increments with the ultimate goal of getting quick feedback. This feedback is the most valuable asset in this process as it dictates the project's direction and, ultimately, the project's survival based on the results.

Excerpt from the Agile Manifesto[^agile-manifesto]:

> Responding to change over following a plan


Every single meeting in agile methodologies is a feedback meeting which is part of a loop
- Stand Up - daily feedback on the daily blockers
- Grooming - feedback on what/how we are building
- Planning - feedback on what we are trying to achieve
- Retrospective - feedback on how we are doing
- Demo - feedback on what we built. 
  
Depending on the feedback we receive in each one of these meetings, we make the necessary adjustments and repeat the process. As a result, we have bigger loops and smaller loops depending on the type of task.

## Software Development
The efficiency of developers, even in small tasks, lies in the development feedback loop.

<p align="center">
  <img alt="dependencies-everywhere" src="/assets/images/build.png" width="200">
  <br>
    <small>Image by <a href="https://github.com/MariaLetta/free-gophers-pack"> Maria Letta @free-gophers-pack</a></small>
</p> 

In development, we have multiple feedback loops:
1. While writing code
   - Get feedback about the code you are writing in real-time, fix, repeat
      - Editor syntax error highlighting, compilation errors, linting warnings/errors, building results
2. Code Reviews
   - Propose changes, peer review, get feedback, improve repeat
3. Testing
   1. Unit tests
      - Using TDD[^tdd], we can get feedback about the correctness of the current function/class we are implementing
      - Run test -> success or fail -> fix -> repeat
   2. Integration tests
      - Get as early feedback about how the current changes affect external components/systems
   3. e2e tests
      - Get early feedback about how the current changes affect the whole system
4. CI/CD pipeline
   - See how my changes affect the version of the code my team works on, along with other changes
   - Get feedback about the deployment of the application and fix it immediately if there is a problem
5. Release Management
   - Continuously monitor the release, collect feedback about the smooth operations and identify quickly and resolve issues or revert immediately


# Conclusion

We have seen that planning alone is insufficient when working on any project.
Unexpected things will occur while moving through a plan, and we need to be able to adapt.
Feedback loops can be a potent tool that helps us quickly identify issues and share information about how to resolve them to stay on the right track.

Designing a good feedback loop is not an easy process. It requires careful design of which measurements are needed and the frequency of their execution, and they have to be specific and actionable.

We use feedback loops in multiple processes in software engineering and should incorporate them whenever possible to achieve efficiency and continuous improvement.

# References
[^feedback-loop]: [Feedback loops](https://en.wikipedia.org/wiki/Feedback)
[^Plans-are-useless]: [Plans Are Worthless, But Planning Is Everything](https://quoteinvestigator.com/2017/11/18/planning/#f+17261+1+1)
[^sdlc]: [Software Development Life Cycle](https://en.wikipedia.org/wiki/Systems_development_life_cycle)
[^agile]: [Agile software development](https://en.wikipedia.org/wiki/Agile_software_development)
[^agile-manifesto]: [Agile Manifesto](https://agilemanifesto.org/)
[^tdd]: [Test Driven Development](https://en.wikipedia.org/wiki/Test-driven_development)