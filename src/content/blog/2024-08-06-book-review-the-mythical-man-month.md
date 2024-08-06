---
title: "Book review: The Mythical Man-Month"
pubDatetime: 2024-08-06T12:40:00+08:00
postSlug: book-review-the-mythical-man-month
featured: false
draft: false
tags:
  - book-review
  - tldr
description: After reading "The Mythical Man-Month," I have some reflections.
  This book is the author's exploration of "how to effectively enable an
  organization to develop software programs."
---
After reading "The Mythical Man-Month," I have some reflections. This book is the author's exploration of "how to effectively enable an organization to develop software programs." I currently lack sufficient team development experience, so I often can't validate the author's arguments with my own experiences. However, intuitively, I feel that most of what he says is correct. The book essentially has four parts: the main text of "The Mythical Man-Month," "No Silver Bullet," and the author's later assessments and follow-ups on these two pieces.

## No Silver Bullet

The core argument of "No Silver Bullet" is that software problems are divided into essence and accidents. Essence refers to the inherent complexity in developing a program that cannot be bypassed logically, regardless of the methods used. Accidents are the unnecessary complexities brought by the tools used. New tools can only reduce accidents, at most suppress some essence-related problems. Therefore, unless more than 9/10 of a software project consists of accidents, new tools alone cannot achieve a tenfold increase in efficiency. The author also posits that the essence of software consists of complexity, conformity, changeability, and invisibility. I agree that new tools cannot conquer essence. With the advent of LLMs (Large Language Models) like ChatGPT, there is an expectation that LLMs could replace software engineers. However, after actually using LLMs, I found that they are only good at writing simple, clear-rule programs with few lines. When it comes to programs exceeding dozens of lines, they struggle to produce quality work. I believe this is because the instructions given to LLMs are in natural language, which is highly abstract in everyday life, thus lacking control over execution details. In other words, unless you know exactly what to do and can clearly tell the LLM step-by-step in natural language, it is almost impossible for it to generate the desired content. This is an example of how new tools cannot conquer essence.

## The Mythical Man-Month

"The Mythical Man-Month" covers more and varied content. However, I believe there is still a core idea: the quality of software is determined by conceptual integrity. Therefore, to ensure conceptual integrity, we need a new position called an architect, responsible solely for the project's conceptual integrity. A project only needs one chief architect, but if necessary, a pyramid of architects can be formed. Once the work related to conceptual integrity is separated, the remaining people are implementers. To ensure the architect's ideas are implemented by all implementers, a manager position is added, engineering documents are made available for everyone to consult, and various meetings are held. Fixing bugs like patchwork often leads to a decline in conceptual integrity, resulting in more bugs until the project progresses but also regresses, marking the end of the project's lifespan.

Another core idea is that man-month does not equal man times month because the more people there are, the more management is needed, and the communication requirements grow exponentially. Additionally, implementers should ideally start their work after the architect has completed most of the blueprint to ensure conceptual integrity.

In future collaborations with teams, I will try to apply and validate the ideas from this book.