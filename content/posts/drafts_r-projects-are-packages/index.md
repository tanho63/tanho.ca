---
title: "All R Projects Are Packages"
summary: |
  A brief manifesto on project structures in R and why I think every project 
  should be structured like a package
date: 2024-02-24
url: /all-r-projects-are-packages
draft: true
tags:
 - r
 - r-tips
heroStyle: card
---

R is a functional programming language. Hadley goes deeper into the nuts and bolts 
of what that means [here](https://adv-r.hadley.nz/fp.html), but for most purposes 
I tend to think of it as: in R, logic tends to be best broken down into functions.
Any R code you write involves taking functions (whether that's from base R, 
tidyverse, dplyr, or other packages) and applying it to data. The best way to
abstract this logic is to compile them into their own functions. 

Pop quiz: what do you call two or more related functions? That's right, it's a 
package! It's not a **CRAN** package, nor is it a **published** package, but I
think of a package as any collection of functions written to address a problem
area. 
