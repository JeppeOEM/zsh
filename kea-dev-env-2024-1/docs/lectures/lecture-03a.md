# Lecture 3a: Requirement Specifications

In this lecture we will look at *requirement specifications* as a tool to help us make better web development projects.


## What is a Requirement Specification?

A requirement specification is a document that records the required features and behaviors of a software system.

There are several standards available for requirement specifications, e.g. [Software requirements specification IEEE 830](https://standards.ieee.org/standard/830-1998.html).
Such standards are very useful in large projects with staff dedicated to manage the requirement specification but are overkill in smaller projects where handling the requirements is done by regular team members.

In this course, we will take a much more pragmatic approach, keeping in mind that the important thing is to work structured and methodically with the requirements.

!!! note inline end

    Any project involving more than one single person should work out a requirement specification.
    It brings clarity and enables you to have an overview of the project.

    Your project report for this course should most definitely contain a requirement specification.

Therefore, the requirement specification essentially boils down to one or more tables of listed requirements, accompanied by longer explanations referencing the requirement specification table as needed.

From a practical perspective, you can use a spreadsheet, a word processing document, or a database to organize the requirements
However, you should be able to generate a complete list, e.g., for putting in a report, and it is also almost necessary that you can filter the list.

You can use requirement specifications not just to collect the requirements for a project, but also to help you decide on the technology, tools, and developer profiles that need to be involved in the project.
As such, the requirement specification can serve to clarify the needs in a project and serve as a decision-making tool.


## Types of Requirement Specifications

Requirements arise at different points during a project, and different stakeholders are involved in different sets of requirements.

For this reason, we consider a *Business Requirement Specification*, a *User Requirement Specifications*, and a *System Requirement Specification*.

The three specifications can be recorded in one or more documents, as long as you have some way of separating items, e.g. via a filter.



### Business Requirement Specification

The Business Requirement Specification consists of overall requirements that are aligned with business processes and overall strategic goals.
For this reason, this is the first part of requirement specification to be worked out.

The primary stakeholder is the management level of the target organization, and very likely, this stakeholder is the *customer*, i.e., the one who will authorize payment.


### User Requirement Specification

The User Requirement Specification is concerned with the daily use of the system.
*Use Cases* and *User Stories* can feed into this specification, and you will likely have interviews with existing users and observe existing business processes.

The User Requirement Specification should focus on the system from a user's perspective; however, it can still be very technical in nature, depending on the project.

The User Requirement Specification often elaborates on requirements from the Business Requirement Specification.


### System Requirement Specification

The System Requirement Specification takes input from the Business Requirement Specification and the User Requirement Specification and translates the requirements into technical requirements that can serve as guidelines for the project.

Further more, we might add requirements based on technical limitations we might identify.


## Types of Requirements

Requirements fall into two categories: functional requirements and non-functional requirements.

!!! warning inline end

    Students often find it difficult to distinguish *functional* and *non-functional* requirements.

    Make sure you have a good understanding of these terms, as they are widely used in the industry, and you are expected to understand these terms.


### Functional Requirements

Functional requirements specify what a system should be able to do, e.g., *display a list of the customer's outstanding bills and the account balance*.
Functional requirements must be *operational* and *specific*.


### Non-Functional Requirements

Non-functional requirements specify *how* or *under what conditions* a requirement must be fulfilled.

The example above can be made into a non-functional requirement: *display a list of the customer's outstanding bills and the account balance in under 1 second under normal load*, given that *normal load* is well-defined.

!!! note inline end

    To simplify requirements and to avoid redundancy, we often have non-functional requirements that refer to functional requirements.


### Practical Use of Functional and Non-Functional Requirements

If we do a good job of specifying functional and non-functional requirements, we will make our job of writing tests much easier, in that we know what to test.

The requirement specification can server as a contract between the stakeholders, and writing tests that specifically tests these requirements make it easy to determine whether a project meets its requirements.


## Requirement Prioritization

There are many ways you can indicate priorities for requirements in a requirement specification.

A straightforward way is to simply use numbers, e.g., 1, 2, and 3.

A better way is to use the [MoSCoW](https://en.wikipedia.org/wiki/MoSCoW_method) method.
This method records requirements into *Must have*, *Should have*, *Could have*, and *Won't have*.
It might seem odd that you would record requirements that you won't implement, but this can serve as a way of indicating that this has already been dealt with and decided upon, and thus save time during the project.

As said, there are many ways of handling priorities in requirement specifications.
The most important thing is that they serve the needs and complexities of the project and that the project participants understand them and agree on their meaning.


## Scheduling

!!! note inline end

    Keep in mind that you have an interest in keeping the requirement specification as *static* as possible.
    When updates are made, there is always a risk of new features creeping into the requirements.
    For this reason, you do not want to constantly update the requirement specification with changing schedules, it is far better just to refer to the requirements from other documents.

It is tempting to use the requirement specification for scheduling information as well.
There is nothing wrong with doing this, but keep in mind the primary purpose of the requirement specification.

One way of using the requirement specification for scheduling is to have a column indicating what version number of the product should implement a particular feature.


## Requirement Specification As A Contract

The requirement specification can be used as a contract between the stakeholders.

If you take this approach, it is important to have formal rules in place as to *who* can make changes, and who must sign off on these changes.

You can use a version control system such as Git to keep track of the project documents, and give access to stakeholders as needed.


## Summary

Requirement specifications should play a central role in any development project for both planning and decision-making purposes.

For this to work, the requirement specifications must be kept up-to-date at all times.

Different stakeholders have requirements for the system at different stages in the project, and you can get input from stakeholders via interviews, surveys, user stories, use cases, etc.

Requirements fall into the functional and non-functional categories.
Functional requirements specify *what* a system should do, while non-functional requirements specify *how* or *under what conditions* (e.g., time) the functionality should work.

Requirements can be prioritized by various methods; the MoSCoW method is often used.

The requirement specification can serve as a simple project contract, and making formal tests to test the individual requirements makes it simple to determine if a project meets its requirements.


## Assignments

> **Assignment 3a.1:** Make a template for a requirement specification. Put some thought into what columns it should have.

> **Assignment 3a.2:** Fill out your requirement specification template for a project you are working on.

