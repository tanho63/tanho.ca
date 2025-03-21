---
title: "DSLC Slack: Things I Learned From Advanced R Book Club"
date: 2020-09-22
summary: A non-exhaustive list of things I learned from each of the sections of Advanced R while participating in R4DS Slack's Advanced R Book Club.
tags: 
  - dslc-slack
  - r
aliases: /advr-learnings
---

I set out reading this book hoping to understand the metaprogramming chapter, and ended up learning a bunch of other stuff along the way!

Here's a non-exhaustive list of things I learned from each of the sections of Advanced R:

## I - Foundations: 

- I notice that I use the switch function a ton now, where previously I used if-else nests.
- I use condition-handling to pass errors back to the user of my Shiny apps now.
- I now know what an Environment is and where and why R would look for something. (This helps both in function-writing and debugging!)
- I write a heck of a lot of functions (this ties into II: Functional Programming, of course)

## II - Functional Programming

- I map the crap out of everything now (if I didn't before, it's way worse now!), and have learned to use functions in a ton of different ways.
- I now understand what a function factory is, and what to do if a function somehow returns me a function instead (as opposed to panicking about it!)
- I now know about a series of modifiers to help adjust function behaviours (memoise, quietly, safely, possibly) - although I use tryCatch as much as I do these.

## III - Object Oriented Programming

- I wrote a [package](https://github.com/ffverse/ffscrapr) that uses S3! It's cool to minimize the user interface for multiple platforms and the mental load accompanying. 
- I still lean to using the explicit "switch" function a lot instead of writing out a useMethod call. May be a habit/pattern worth breaking in the future.
- I'm now comfortable USING an R6 package - I don't know that I'd reach for an R6 system now but the whole "function$method" thing doesn't scare me anymore
- S4? wot that

## IV Metaprogramming

- I now know how to pass expressions to tidyverse stuff!
- I now know where to look in the Metaprogramming and how to intepret the text.

## V Techniques

- Debugging: I now know how to use `browser()`, and love it! Also `debug()`!
- Performance measure/improve: still don't do a ton of this, even though I'm a shiny dev. I feel like I should care more but I mostly work with smaller data (sub 1,000,000 rows) right now. 
- I ... still don't know C++ or Rcpp. ¯\\_(ツ)_/¯

## Toy Code
```r
library(dplyr)
library(rlang)

grouping_variables <- switch(
  params$group_type,
  "S" = exprs(CraftCode, starts_with("Product"), ModelCode, Elevation),
  "B" = exprs(starts_with("Rate")),
  "P" = exprs(starts_with("Part"))
)
  
null_bids <- bids %>%
  group_by(SupplierID, ProjectID, !!!grouping_variables) %>%
  slice_max(LastModDate) %>%
  ungroup() %>%
  select(!!!grouping_variables, ProjectID, SupplierAID, SupplierName, UnitPrice) %>%
  left_join(lowest_bids, by = as.character(grouping_variables))

```

Metaprogramming - how to pass expressions into select statements. Also example of switch. 
