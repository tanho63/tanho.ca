---
title: "The Beginner Data Science Programming Book That Doesn't Exist (Yet?)"
summary: |
  Free idea: the beginner book that doesn't exist yet (but should) is one that \
  introduces programming via language-agnostic, domain-specific case studies \
  that complement existing resources on learning programming.
date: 2024-12-10
aliases: /beginner-book
tags:
 - thoughts
 - learning
 - free-ideas
heroStyle: card
---

{{< alert "lightbulb" >}}
Ideas are [worth nothing unless executed](https://sive.rs/multiply), and I'm 
tired of adding ideas to my backlog only to never get to them. This [series](/tags/free-ideas)
aims to set those ideas free - let me know if you take it on!
{{</ alert >}}

## Hot take: the ideal beginner data science programming book doesn't exist (yet)

The vaunted [R for Data Science](https://r4ds.hadley.nz) is fantastic, I'm sure 
Wes McKinney's [book](https://wesmckinney.com/book/) is also good if you want to learn
Python, and yet I struggled immensely with learning from R4DS. I think it's 
because I can only care so much about flower irises, penguins, and NYC taxis - I 
quit halfway through the third chapter of R4DS on my first attempt in 2018ish ...
because I got bored. 

Since then, I've gotten treatment for ADHD (which probably explains a lot, tbh), 
but I think what really helped the most with learning programming was that I 
started working on projects that I actually cared about - fantasy football and NFL.

I'd work on something for a while, get stuck on something I didn't know, Ctrl-F 
the R4DS book + dplyr/tidyr reference manual, figure out what I needed to know
and how it fit into what I already knew, and then keep going. Reading R4DS cover
to cover was utterly unhelpful until I knew how to fit each concept into a mental
model that I knew how to use. 

My love of all things R and data has since taken off, enough to have made a whole 
career of it, and now I often get asked what books I'd recommend for learning 
NFL/sports analytics, R, data science, programming etc. I supply the easy answer
(you should read R4DS), but I'm a little uncomfortable with it because the book 
I wish existed doesn't quite exist.

## the ideal book for learning programming and NFL analytics

There are a lot of introductory data science and programming books out there, and
even ones that apply the context of NFL: both [Eric Eager](https://www.oreilly.com/library/view/football-analytics-with/9781492099611/), 
and [Brad Congelio](https://bradcongelio.com/nfl-analytics-with-r-book/) have great
iterations of this - and the latter is free online!

I'm so glad these books exist now and they'd have certainly helped a lot when I 
first got started, but I'm not quite sure these are the ones I've been wanting:
  - how do they stay up to date with a fast-evolving programming landscape? 
  - how do they compare with the existing literature that is beloved by the community, 
    like R4DS, and how should beginners proceed if the books disagree on best code
    approach? 
  - what happens if I want to learn another programming language other than R?

I think the book I want to exist would be structured as a _companion_ to books 
like R4DS, but not include _any_ language-specific code whatsoever. Instead, I'd 
focus on a few goals: 

  - create a series of case studies and exercises that challenge learners to write
    their own solutions based on domain-specific research questions
    - provide starter data at the correct cleanliness where appropriate in a 
    data repository
  - organize and cover a full breadth of high-level data science / programming topics
    - kind of like what R4DS covers, e.g. visualize, transform, import, program, communicate
  - maintain up-to-date links to readings for each case study that help learners 
    find best-practice guidance on a given topic in a given programming language
    - each case study should provide links to "prerequisite reading" to solve it, 
      e.g. links to relevant R4DS chapters for R
    - should be easily extended to other languages by linking to equivalent material
      for the same overall concepts - pivoting data, group by summarize etc all
      have equivalents in various languages
  - introduce the essential background knowledge for the domain in question
    - for NFL this should include concepts like EPA, fourth downs, completion 
      probability, expected rushing yards etc - essentially covering the body of
      public research
  - suggest further research questions that can be tackled beyond the controlled
    sandbox of the book/repository
