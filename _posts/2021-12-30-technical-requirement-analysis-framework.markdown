---
layout: single
title:  "A methodical approach on Technical Requirement Analysis"
date:   2021-12-30 21:30:00 +0300
last_modified_at: 2021-12-30 21:30:00 +0300
tags: software-processes
author_profile: true
toc: true
---

This article describes a methodical way of approaching Technical Requirement Analysis that includes a list of steps that help in clarifying ambiguity and prevent unplanned issues before an engineering team jumps into the design phase. 

# Introduction
There is a great amount of literature on approaching different Software Development LifeCycle (SDLCs) frameworks that provide the steps, practices  and guidelines required to achieve an *efficient* software development process.

Most times, software engineers are involved in this whole process after the business requirements definition phase with the responsibility to design, implement, test and deploy applications.

![Technical Requirement Analysis](/assets/diagrams/technical-requirements/TechnicalRequirementAnalysis.drawio.png)

Between the handover of the requirements from the product team to the engineering team and the initiation of the design phase, there is a short period in which engineers work with the product team to clarify any ambiguous requirements, talk about potential issues and agree on specific business specs. This phase which in this article I refer to as **Technical Requirement Analysis**. 

In my view, the Technical Requirement Analysis is one of the most important phases in the lifecycle and in my experience, it is one of the phases that is often rushed and sometimes even overlooked. If done right, it can lead to catching issues early that saves future time and money. During the last years, I have been involved in the analysis and design of a quite a few projects and many features. Finding a perfect solution to approaching the Technical Requirement Analysis is very difficult if not impossible; Different companies, teams, people use different tools and methods to complete the technical analysis.

Recently, I have tried to document the way I approach Technical Analysis in a structured manner that I can use as a template for future features. Personally, I find that following a methodical approach with a set of recommended steps, can prevent consistent errors and provide the necessary confidence to proceed to the design phase.

In the following sections, we will explore the importance and how we can benefit from a detailed Technical Analysis phase, and provide a set of well-defined, structured steps to minimize unclarity and future inconvenience.

# Importance
 Many teams see the technical analysis as a quick phase to read though the requirement definition and clarify a few ambiguous statements. 
 
 However a team can greatly benefit by following a few extra steps before proceeding to the design phase. The goal of an SDLC is efficiency; while iterations help immensely in this aspect, catching potential issues in the technical analysis phase can further increase efficiency by preventing delays from edge-case scenarios and collateral impact that affect the requirements.

The Technical Analysis of Requirements phase is important for many reasons:
1. It **ensures that engineering and product are aligned** on the goal and the problem this feature is trying to solve. While the product team has gathers the requirements from the stakeholders and are aligned with business, it is also important that the defined requirements are well understood by the engineering too. This will ensure that the end-goal is the same from all teams' perspectives. 
2. It **allows engineers to raise concerns and present possible issues** on the requirements. Firstly, some features can be technically infeasible. This is usually prevented by having some technical presence when the product defines the business requirements. However, even for feasible issues, the technical team might raise risks and present technical challenges that are not trivial to catch at the requirement gathering phase.
3. It **allows engineers to give their input and perhaps reshape the requirements**. While the product/business team see the requirements from a customer perspective, engineers can identify collateral technical consequences, future codebase quality issues that should be considered. Depending on the priorities and the long term vision, this input from engineers might change the course of the feature and alter some of the requirements. A small tweak that is not so important for the product can decrease the complexity and consequently the delivery time of the feature.

# A simple, methodical Technical Requirement Analysis approach
As also mentioned in the introduction, in my experience working on new features, I have not seen a standard way to approach Technical Analysis. However, through the years I have personally used a few tools and I have been incrementally optimizing my approach on this. In this sesction I will be sharing this approach.

Please note that this is by no means a phase that has to be strictly defined and may not be applicable for all use-cases, but I believe some of the steps can be useful.

## 1. Read the docs!
Quite obvious uh? Well, yes it is :) 

![Read](/assets/images/read.png)

This first step is begins with the handover of the requirement definition from the product to the engineering.

The technical requirement analysis is ideally a *team activity*. The engineering team should dedicate enough time to read the requirements and make sure that all members understand **what** is the problem, **why** it needs to be addressed, and **how** the feature requirements addresses it.

If there are any unknowns or any missing context, this is the time to ask questions to the product and seek complete understanding on the requirements.

Additionally, reading and clarifying requirements expands the domain knowledge of engineers and also makes the engineers feel more engaged. 

## 2. Edge-Case Analysis - Business Specifications
Once the requirements are well understood, the team should proceed with the in-depth edge-case analysis stage.

![Read](/assets/images/find.png)

Often, the requirements presented from the Product cover the essentials and potential business-related side-effects, but the engineering perspective can bring edge cases that are related to the system behavior and impact on other services that product might be unaware off. 

The purpose of this step is to raise these edge cases, and if it's possible present available options to manage them and even propose a recommended way forward. The edge-case analysis step consists of 3 substeps:
a. Identify Edge Cases
b. List Edge Case Resolution Options
c. Present recommendations (For technical-impacting features)

### a. Identify Edge Cases
The team should look at the requirements with the engineering lens and understand if there are any technical edge cases in the requirements. 

Let's look at an over-simplified and open-ended example(usually requirements are defined in high detail). We may have a requirement from the product to '*send an email to a user when their account balance changes*'. The engineering team can bring the edge case of 'What happens when the email provider is not available at the time of the change? or How do we deal with bounced back emails?'. Almost all requirements have such edge-cases. Capturing them in this stage is vital for a smooth progress to the next stages. 

### b. Present Edge Case Options
Once the edge cases are identified, the team can prepare a list of options with pros and cons.
This steps is about finding options, not decisions. Decisions will be taken in the next step.

Example: From the previous example 'Send an email to a user when their account balance changes. What happens when the email provider is not available - our email provider SLAs are 99% availability' the options could be:
- Option 1: Send the email when the provider is available
	- Pros:
		- From a channel perspective, the requirement is met, the custom will eventually receive the email
	- Cons:
		- Technically complicated. Requires retrial policy management that is currently not available
	- Follow-up questions:
		- How quickly should we retry? Do we have a maximum time-window for retrials?
		- Does our provider's SLAs meet our requirements?
- Option 2: Fallback to SMS
	- Pros:
		- Immediate response, good customer experience.
	- Cons:
		- This might fail too
		- The customer might not have a mobile number
- Option 3: Don't send an email automatically. Track errors and send them manually
	- This is not really an option since it doesn't cover the automated requirement
	- Pros:
		- Technically Easy
	- Cons:
		- If this is a marketing promotional email it may be ok. But if this is a financial status related email it wouldn't be acceptable.

### c. Edge case resolution - Recommended Approach
If the requirement is purely technical, and the technical complexity between solutions vary with impact on delivery timelines, introduced tech debt, troubleshooting convenience etc, then the engineering team can propose a recommended approach. This will help drive the edge case resolution in a quicker manner if the product agrees.

This sub-step is not mandatory.

## 3. Finalize revised Business Specs 
Once the requirement clarifications are given with all edge cases documented with possible options and maybe the recommended approach, the product team can review the new information.
In this phase the product team must understand the edge cases, the concerns from the engineering side, and based on that, take an informed decisions taking into account the pros and cons of the selected approach on how to move forward.

## 4. Data Flow Analysis
Having the well-defined requirements with all edge cases the engineering team can proceed with the technical data flow analysis. This stage is often grouped under the technical design stage but I find that doing this at a high level earlier in the process, offers great initial feedback on the data requirements of the requested feature.

In this stage I like to think in terms of input and output. All features require an input and produce an output and this can be the base of this phase.

Based on the input requirements, the team needs to identify
a. If the input is already available
	- For example will the input provided by the client or already exists within an internal component
b. If it is not available, determine where the information is
	- For example, if the input required is missing, determine which external sources are the owners of the data we are looking for.

The output should reflect the requirements as well. If the output is a data object, then an initial version of the schema can be defined in this stage. This is particularly useful when working on features for which the output is a bloker to another feature implemented by an external team. 

Since this is still the analysis stage, 

## 5. Extract Use Cases
The next step is to identify the use cases.

We can think of use cases as individual operations that should be served by the application to meet the requirements.
A useful tool we can use in this phase is thinking of use-cases in terms of **Commands** and **Queries**. If a requirement refers to an operation that provides data it can be though of as a 'Query'. If the operation modifies data, it can be considered as a 'Command'.
This separation of use-cases is particularly useful when working with code bases using [Clean Architecture]().

At the end of this step, the engineering team should have a clear picture of the requirements in very high detail and they can start visualizing how the technical design would look like.
# TL;DR;
Here is an overview of the Technical Requirement Analysis Steps

|    | Step                    | Goal                                                                                                                           | Outcome                                                               |
|----|-------------------------|--------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------|
| #1 | Read the docs           | Understand what, why and how                                                                                                   | Requirements are clear                                                |
| #2 | Edge-case analysis      | 1. Identify technical edge cases <br>2. Present possible options to deal with technical edge cases <br>3. Propose recommended approach | Documented edge cases with options and approach                       |
|  #3  | Finalize business specs | Based on the edge case analysis, finalize business specs                                                                       | Documented final business specs                                       |
| #4 | Data flow analysis      | Identify required data Find required input data Define output data                                                             | Input Data Requirements with their owners <br>Defined output model/schema |
| #5 | Extract Use Cases       | Defind the use-cases of the requested feature                                                                                  | Documented use-cases in commands and queries                          |

## Sample Template
```markdown
# Requirement Clarifications
## What need/problem do we need to solve? 
## Why need/problem do we need to solve it?
## How do we address the problem?

# Edge Cases
## Edge Case 1
### Detailed Description
### Options
#### Option 1
##### Pros
##### Cons
### Final Decision

# Data Analysis
## Input
### Dataset 1
- Owner
- Fields/Schema
## Output
- Schema/Model

# Use Cases
## Commands
## Queries
```

# Conclusion
That's all. We have gone through  a structured approach that I have been following during the last years and I have seen it producing great results in terms of common expectations/alignment between product and engineering, and preventing delayed capturing of edge cases.

This is a process, and naturally some of its steps might be applicable in one context but not in another. I find structure in this phase very beneficial as it gives a sense of progress and also a set of objectives to look for before proceeding with the design.