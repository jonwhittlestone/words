---
toc: true
layout: post
description: 2021 Edition
categories: ["year-in-review"]
title: This Year I Learned
image: https://i.imgur.com/AKwcpBi.jpg
hide: false
---

![](https://i.imgur.com/AKwcpBi.jpg "2021: This Year I Learned")

Here's a roundup of my professional learnings from the year.

The sections for 2022 goals are limited in scope on account of recently giving birth to our second child :)

## Professional

### Always be Quitting

Thanks to Julio Merino for the phrase and his excellent [article](https://jmmv.dev/2021/04/always-be-quitting.html).

The phrase describes how we should approach a software project, as participants, owners and stewards. We should be in a permanent state of being able to handover the project.

It does not mean, abdicating responsbility.

It means encouraging transparency by promoting documentation as you develop. So that at any one time, A project and user can be onboarded or the project can be demoed. As well as the software itself, this also includes documenting your long-term plans for the project.

It means, eliminating, what Google refer to as The Bus Factor. The act of sharing knowledge so no one individual is a silo or bottleneck. This is covered extensively in [O'Reilly's excellent book, Software Engineering at Google](https://learning.oreilly.com/library/view/software-engineering-at/9781492082781/).

It means, looking to move on from a project as a positive, rather than a negative move. This can mean sharing your knowledge by training others around you and to identify and train your replacement. When the time is right, this leaves you to focus your efforts on kickstarting new projects.

#### Resources

- Julio Merino - Always Be Quitting
  - [https://jmmv.dev/2021/04/always-be-quitting.html](https://jmmv.dev/2021/04/always-be-quitting.html)

### Using a design document to explain and persuade.

This year I've found using a platform like Confluence to create a design document has helped to explain and persuade an approach or design strategy.

This is especially true when time pressures have meant face-to-face, synchronous communications have been harder to come by.

2021 has been a very active year in the hiring market. A shift to permanent work-from-home has contributed to [The Great Resignation](https://hbr.org/2021/09/who-is-driving-the-great-resignation).

On my own team, we saw quite a radical shift in headcount and time for meetings and proposals has often been cut short as resources are stretched. For a more asynchronous working approach, it's useful to make a write up with the problem statement and the various approaches available.

Let your audience understand the various Bronze, Silver and Gold approaches to solving a problem, with its tradeoffs and costs. Subsequently, a team can make a considered judgement in a way that's been documented for posterity. Further discussion takes place in the page comments rather than taking place on a call, or buried in a ticket.

#### Resources

- Chapter 10 - Design Docs - Sofware Engineering at Google
  - [https://learning.oreilly.com/library/view/software-engineering-at/9781492082781/ch10.html#design_docs](https://learning.oreilly.com/library/view/software-engineering-at/9781492082781/ch10.html#design_docs)

### Development using Static Code Analysis for quicker feedback loops

In 2021 I dabbled with Rust. It's my first dive into high-level, compiled languages since my days of C++ and Java at the start of my career.

It's worth saying, the tooling around Rust development is first-class and it showed how the compiler is able to present the developer with errors at an early stage so that you fail fast.

In a work project, we also used the Typescript template with Create-React-App.

Using both of these opened my eyes to how the compilation step and static code analysis can help the developer find bugs, antipatterns and other issues at the coding stage of the Software Development Lifecycle without running the program.

Subsequently, in my daily Python-land, this led me to use `mypy` and Python's 'opt-in' typing mechanism more thoroughly.

#### Resources

- Rust Book

  - [https://learning.oreilly.com/library/view/programming-rust-2nd/9781492052586/](https://learning.oreilly.com/library/view/programming-rust-2nd/9781492052586/)

- Rustlings

  - [https://github.com/rust-lang/rustlings](https://github.com/rust-lang/rustlings)

- React Typescript cheatsheet

  - [https://react-typescript-cheatsheet.netlify.app/docs/basic/setup/](https://github.com/rust-lang/rustlings)

- Full-Stack React, Typescript and Node
  - [https://learning.oreilly.com/library/view/full-stack-react-typescript/9781839219931/](https://learning.oreilly.com/library/view/full-stack-react-typescript/9781839219931/)

### Efficient learning using a variety of senses

As mentioned above, I was curious about Rust.

From my learnings, it appears that Rust is a high level language that repeatedly appears as a winner in the [Stack Overflow Developer survey](https://insights.stackoverflow.com/survey/2021#most-loved-dreaded-and-wanted-language-love-dread) as the most-loved language. I needed to see what the fuss was all about.

I was curious to see how a high-level systems language compares to Python in terms in memory footprint and performance in general.

Instead of just linearly reading the official Rust book and doing exercies, I used the excellent [Rustlings](https://github.com/rust-lang/rustlings) repository which has the user authoring actual rust code samples, correcting the logic and syntax to pass a unit test, and then running the the test suite to ensure it passes.

By not only reading, but interactively coding with what I'd learnt from the rust book, I was putting knowledge acquired to the test. By using different learning methods, with different media, this helps the brain with retention.

#### Resources

- Rust Book

  - [https://learning.oreilly.com/library/view/programming-rust-2nd/9781492052586/](https://learning.oreilly.com/library/view/programming-rust-2nd/9781492052586/)

- Rustlings

  - [https://github.com/rust-lang/rustlings](https://github.com/rust-lang/rustlings)

### Post-Pandemic remote teamwork: Do not rely on Jira alone for communication

In 2021 I've been involved in consulting for a single software team where we've delivered on a couple of data-driven software projects.

I consider this team highly productive and efficient in meeting its bi-weekly sprint goals. One of its strengths has been how we communicate. That is, to use Jira for ticketing, as a documentation tool, rather than a communication tool.

It's quite easy to fall into the trap of making Jira comments, tagging other team members and then relying on this for inter-team communication.

In a remote-first working world, where an asynchronous communication style takes priority, we cannot rely solely that intended recipients will have digested the Jira notifications. Communications should be reinforced with a quick ping on instant message or voice call.

The sheer barrage of notifications (Jira or other platform) will mean your crafted comment or code review note may get buried in the noise. Use a more immediate communication method if applicable.

### Tools and Libraries: FastAPI, Pydantic, React Context API, Mongo Aggregation Framework

In Python, 2021 I've gotten to grips with FastAPI and Pydantic which has greatly helped with rapid application development.

FastAPI is an async micro web framework that works beautifully with Pydantic and works with the OpenAPI protocol to produce beauiful API documentation out of the box. This is exactly what our team needed in order to move quickly.

<!-- In frontend land, we've reafactored -->

### 2022 Professional Goals

- Write a Rust crate
- Run [`mypy`](http://www.mypy-lang.org/) constantly during development to flag typing errors in Python code as they appear, rather than solely as a [`pre-commit`](https://pre-commit.com/) hook

## Personal

### Lesser Minds, the importance of engaging with strangers

In 2021 I read a very memorable book, [The Power Of Strangers](https://www.goodreads.com/book/show/58377159-the-power-of-strangers?ac=1&from_search=true&qid=RGzexHIQnx&rank=1)

<!--
### Standing desks work well for me

Lorem Ipsum ..

### Parenting: Your childhood experiences and behaviours carried over for you to repeat as a parent

Lorem Ipsum ..

### The role of listening in healthy relationships

Lorem Ipsum ..

### Be a strict gatekeeper for the media you consume.

Lorem Ipsum ..

### Parenting: Importance of apology

Lorem Ipsum ..

-->
