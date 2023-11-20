---
title: "Pivot Trick: names_to -> .value"
summary: "One of my favourite tidyr tips that I've shared over and over again"
date: 2023-11-19
url: /pivot-trick
tags:
 - r
 - tips
---

{{< alert "twitter" >}}
Transcribing [one](https://twitter.com/_tanho/status/1415100126272577536) of my 
more popular Twitter posts into a website post now that I'm mostly off of Twitter. 
{{</ alert >}}

One of my favourite data-wrangling tricks is [tidyr::pivot_longer's](https://tidyr.tidyverse.org/reference/pivot_longer.html) 
`names_to` & `.value` sentinel - use-cases don't come up often, but it's so deeply
:sparkles: **satisfying** :sparkles: when it does!

Let's say you have this starting dataframe:

![A snippet of the raw Scooby Doo data from the TidyTuesday dataset, showing columns caught_fred, captured_fred, unmask_fred, snack_fred, caught_daphnie, captured_daphne, unmask_daphne etc. The goal is to transform this into a dataframe per character (one row for fred, one row for daphne, etc )](start_dataframe.png)

and you want this ending dataframe:

![The end goal dataframe, with one row for each of fred, daphne, velma, shaggy, and scooby per episode and columns for captured, caught, unmask, or snack.](end_dataframe.png)

How do you extract the characters (Fred, Velma, Daphnie, Shaggy, Scooby) from the
column names into a `character` column?

{{< alert "circle-info" >}}
TLDR: Full gist [here](https://gist.github.com/tanho63/50d9b323e29165ad3e027bc3cf1c5926).
{{</ alert >}}

Past me: hmm, well, I can pivot_longer, then separate the name, then pivot_wider again?

```r
three_lines <- x %>% 
  pivot_longer(cols = -c("season","title")) %>% 
  separate(name,into = c("action","character"), sep = "_") %>% 
  pivot_wider(names_from = "action", values_from = "value")
```

returns

![output of previous code example, looks like goal dataframe](three_step_output.png)

This seems to work okay! 

**But what if I told you there's a one-liner for this exact situation?**

pivot_longer has a "names_to" argument that takes a special "sentinel value" 
called `".value"` - it immediately re-pivots the columns so that "character" 
(fred, daphne etc) stays as a column and then the ".value" part becomes colnames
again (caught, captured etc)

```r
one_line <- x %>% 
  pivot_longer(
    cols = -c("season","title"), 
    names_to = c(".value","character"), 
    names_sep = "_"
  )
```

![output of one liner, for confirmation](one_step_output.png)

**Why use many line when one line do trick?**

You can read more about this pivot_longer feature in this [tidyr vignette](https://tidyr.tidyverse.org/articles/pivot.html#multiple-observations-per-row).


