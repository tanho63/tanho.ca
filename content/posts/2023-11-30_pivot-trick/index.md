---
title: "tidyr pivot trick: names_to -> .value"
summary: |
  A tidyr tip for extracting data from column names that I've used and shared a 
  lot, now transcribed from Twitter
date: 2023-11-30
url: /pivot-trick
tags:
 - r
 - r-tips
 - tweet-highlights
heroStyle: card
---

{{< alert "twitter" >}}
I originally shared this tip for extracting data from column names as a
[tweet](https://twitter.com/_tanho/status/1415100126272577536), and I've found 
myself sharing it over and over again since. Here's a brief transcription now that 
I'm no longer on Twitter.
{{</ alert >}}

One of my favourite data-wrangling tricks is [tidyr::pivot_longer's](https://tidyr.tidyverse.org/reference/pivot_longer.html) 
`names_to` & `.value` sentinel - use-cases don't come up often, but it's so deeply
:sparkles: **satisfying** :sparkles: when it does!

## Scenario

Let's say you have this starting dataframe:

```
# A tibble: 601 × 22
   season title       caught_fred caught_daphnie caught_velma caught_shaggy
   <chr>  <chr>       <chr>       <chr>          <chr>        <chr>        
 1 1      What a Nig… FALSE       FALSE          FALSE        TRUE         
 2 1      A Clue for… FALSE       FALSE          FALSE        TRUE         
 3 1      Hassle in … FALSE       FALSE          FALSE        FALSE        
 4 1      Mine Your … TRUE        FALSE          FALSE        FALSE        
 5 1      Decoy for … FALSE       FALSE          FALSE        FALSE        
 6 1      What the H… TRUE        FALSE          FALSE        FALSE        
 7 1      Never Ape … TRUE        FALSE          FALSE        FALSE        
 8 1      Foul Play … FALSE       FALSE          FALSE        FALSE        
 9 1      The Backst… FALSE       FALSE          FALSE        TRUE         
10 1      Bedlam in … FALSE       FALSE          FALSE        TRUE         
# ℹ 591 more rows
# ℹ 16 more variables: caught_scooby <chr>, captured_fred <chr>,
#   captured_daphnie <chr>, captured_velma <chr>, captured_shaggy <chr>,
#   captured_scooby <chr>, unmask_fred <chr>, unmask_daphnie <chr>,
#   unmask_velma <chr>, unmask_shaggy <chr>, unmask_scooby <chr>,
#   snack_fred <chr>, snack_daphnie <chr>, snack_velma <chr>,
#   snack_shaggy <chr>, snack_scooby <chr>
# ℹ Use `print(n = ...)` to see more rows
```
and you want this ending dataframe:
```
# A tibble: 3,005 × 7
   season title                     character caught captured unmask snack
   <chr>  <chr>                     <chr>     <chr>  <chr>    <chr>  <chr>
 1 1      What a Night for a Knight fred      FALSE  FALSE    FALSE  TRUE 
 2 1      What a Night for a Knight daphnie   FALSE  FALSE    FALSE  FALSE
 3 1      What a Night for a Knight velma     FALSE  FALSE    FALSE  FALSE
 4 1      What a Night for a Knight shaggy    TRUE   FALSE    FALSE  FALSE
 5 1      What a Night for a Knight scooby    TRUE   FALSE    TRUE   FALSE
 6 1      A Clue for Scooby Doo     fred      FALSE  TRUE     TRUE   FALSE
 7 1      A Clue for Scooby Doo     daphnie   FALSE  TRUE     FALSE  FALSE
 8 1      A Clue for Scooby Doo     velma     FALSE  TRUE     FALSE  TRUE 
 9 1      A Clue for Scooby Doo     shaggy    TRUE   FALSE    FALSE  FALSE
10 1      A Clue for Scooby Doo     scooby    FALSE  FALSE    FALSE  FALSE
# ℹ 2,995 more rows
# ℹ Use `print(n = ...)` to see more rows
```

How do you extract the characters (Fred, Velma, Daphnie, Shaggy, Scooby) from the
column names into a `character` column?

## Past me
Hmm, well, I can pivot_longer, then separate the name, then pivot_wider again?

```r
three_lines <- x %>% 
  pivot_longer(cols = -c("season","title")) %>% 
  separate(name,into = c("action","character"), sep = "_") %>% 
  pivot_wider(names_from = "action", values_from = "value")
```
```
# A tibble: 3,005 × 7
   season title                     character caught captured unmask snack
   <chr>  <chr>                     <chr>     <chr>  <chr>    <chr>  <chr>
 1 1      What a Night for a Knight fred      FALSE  FALSE    FALSE  TRUE 
 2 1      What a Night for a Knight daphnie   FALSE  FALSE    FALSE  FALSE
 3 1      What a Night for a Knight velma     FALSE  FALSE    FALSE  FALSE
 4 1      What a Night for a Knight shaggy    TRUE   FALSE    FALSE  FALSE
 5 1      What a Night for a Knight scooby    TRUE   FALSE    TRUE   FALSE
 6 1      A Clue for Scooby Doo     fred      FALSE  TRUE     TRUE   FALSE
 7 1      A Clue for Scooby Doo     daphnie   FALSE  TRUE     FALSE  FALSE
 8 1      A Clue for Scooby Doo     velma     FALSE  TRUE     FALSE  TRUE 
 9 1      A Clue for Scooby Doo     shaggy    TRUE   FALSE    FALSE  FALSE
10 1      A Clue for Scooby Doo     scooby    FALSE  FALSE    FALSE  FALSE
# ℹ 2,995 more rows
# ℹ Use `print(n = ...)` to see more rows
```

This seems to work okay! 

## But what if I told you there's a one-liner for this exact situation?

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

```
# A tibble: 3,005 × 7
   season title                     character caught captured unmask snack
   <chr>  <chr>                     <chr>     <chr>  <chr>    <chr>  <chr>
 1 1      What a Night for a Knight fred      FALSE  FALSE    FALSE  TRUE 
 2 1      What a Night for a Knight daphnie   FALSE  FALSE    FALSE  FALSE
 3 1      What a Night for a Knight velma     FALSE  FALSE    FALSE  FALSE
 4 1      What a Night for a Knight shaggy    TRUE   FALSE    FALSE  FALSE
 5 1      What a Night for a Knight scooby    TRUE   FALSE    TRUE   FALSE
 6 1      A Clue for Scooby Doo     fred      FALSE  TRUE     TRUE   FALSE
 7 1      A Clue for Scooby Doo     daphnie   FALSE  TRUE     FALSE  FALSE
 8 1      A Clue for Scooby Doo     velma     FALSE  TRUE     FALSE  TRUE 
 9 1      A Clue for Scooby Doo     shaggy    TRUE   FALSE    FALSE  FALSE
10 1      A Clue for Scooby Doo     scooby    FALSE  FALSE    FALSE  FALSE
# ℹ 2,995 more rows
# ℹ Use `print(n = ...)` to see more rows
```

**Why use many line when one line do trick?**

You can read more about this pivot_longer feature in this [tidyr vignette](https://tidyr.tidyverse.org/articles/pivot.html#multiple-observations-per-row).

## Bonus: data.table equivalent

<!-- {{< tweet user="hadleywickham" id="1500244970845974532">}} -->

I learned later (from Hadley quote-tweeting my thread) that this feature was
inspired by a similar feature in `data.table`, so I wanted to track down how it
would be done there. As it turns out, kind of difficult because the feature isn't
in the CRAN version: it was in development as of October 2020, merged to main in
May 2021, and still hasn't made it onto CRAN because of some data.table governance
issues which seem to finally have been resolved

In any case, **with the main branch of rdatatable/data.table 
[as of today](https://github.com/Rdatatable/data.table/tree/6b9d559606767562f7f7dd4c7842a9e4a9fb597c)**, 
here's how you could do the same pivot trick:

```r
# requires development version of data.table and/or 1.15.0+ 
rlang::check_installed("data.table (>= 1.14.99)")
data.table::data.table(x) |> 
  data.table::melt(
    id.vars = c("season", "title"),
    # measure() is not actually exported but is silently parsed under the hood by melt()
    # - uses `sep = "_"` to identify & process the column names (i.e. split with sep "_")
    # - new column names (i.e. character) are provided unquoted
    # - value.name is equivalent to tidyr's `.value`
    measure.vars = measure(value.name, character, sep = "_")
  )
```
```
      season                                title character caught captured unmask  snack
      <char>                               <char>    <char> <char>   <char> <char> <char>
   1:      1            What a Night for a Knight      fred  FALSE    FALSE  FALSE   TRUE
   2:      1                A Clue for Scooby Doo      fred  FALSE     TRUE   TRUE  FALSE
   3:      1                 Hassle in the Castle      fred  FALSE    FALSE   TRUE   TRUE
   4:      1               Mine Your Own Business      fred   TRUE    FALSE   TRUE  FALSE
   5:      1                Decoy for a Dognapper      fred  FALSE    FALSE  FALSE  FALSE
  ---                                                                                    
3001:      2 The Dreaded Remake of Jekyll & Hyde!    scooby  FALSE    FALSE  FALSE  FALSE
3002:  Movie         Happy Halloween, Scooby-Doo!    scooby  FALSE    FALSE  FALSE  FALSE
3003:  Movie  Scooby-Doo! The Sword and the Scoob    scooby  FALSE    FALSE  FALSE  FALSE
3004:      2              Dark Diner of Route 66!    scooby   TRUE    FALSE  FALSE  FALSE
3005:      2                      Total Jeopardy!    scooby  FALSE    FALSE  FALSE  FALSE
```


## Full Code

```r
library(tidyverse)

scooby_data <- read.csv("https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2021/2021-07-13/scoobydoo.csv")

x <- 
  scooby_data %>% 
  select(season, title, 
    starts_with("caught"),
    starts_with("captured"),
    starts_with("unmask"),
    starts_with("snack"),
    -contains("other"),
    -contains("not")) %>% 
  filter(title!= "Wrestle Maniacs")

three_lines <- x %>% 
  pivot_longer(cols = -c("season","title")) %>% 
  separate(name,into = c("action","character"), sep = "_") %>% 
  pivot_wider(names_from = "action", values_from = "value")

one_line <- x %>% 
  pivot_longer(cols = -c("season","title"), names_to = c(".value","character"), names_sep = "_")
  
rlang::check_installed("data.table (>= 1.14.9)")
data.table::data.table(x) |> 
  data.table::melt(
    id.vars = c("season", "title"),
    # measure() is not actually exported but is silently parsed under the hood by melt()
    # - uses `sep = "_"` to identify & process the column names (i.e. split with sep "_")
    # - new column names (i.e. character) are provided unquoted/as bare symbols
    # - value.name is equivalent to tidyr's `.value`
    measure.vars = measure(value.name, character, sep = "_")
  )
```

[Original gist](https://gist.github.com/tanho63/50d9b323e29165ad3e027bc3cf1c5926) for reference.
