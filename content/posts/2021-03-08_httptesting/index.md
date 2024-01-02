---
layout: single
title: "Adventures in HTTP Testing for R"
date:   2021-03-08
summary: "Thoughts on httptest, not skipping CRAN where possible, GitHub Actions, and dealing with large packages of mock data."
tags: 
  - r
  - r-pkg-dev
url: /httptesting
---

> ***Embrace unit testing karma***
> 
> Karma says:
> 
> - Do good things and good things will happen to you.
> - Do them the way you know.
> - Do them the way you like.
> 
> Karma is flexible. Testing needs flexibility.
> 
> Karma thrives on creativity. Testing needs creativity. - [Testivus](https://www.artima.com/weblogs/viewpost.jsp?thread=203994)

While developing [ffscrapr](https://ffscrapr.dynastyprocess.com), I've taken a 
deeper dive into HTTP testing for R packages. I've settled on using httptest and 
find it very convenient path to building and using mock data, especially since I'm 
using [httr](https://httr.r-lib.org/) extensively within the package.  I wish 
Scott Chamberlain & Maelle Salmon's new book [HTTP testing in R](https://books.ropensci.org/http-testing/) 
had been written before I started - that would have been incredibly helpful as a 
guiderail for my experimentations! Here are some thoughts on what I've discovered 
while testing `ffscrapr`, mostly trying to capture parts of my experiences that 
differ from what's already in the HTTP testing book. 

## Why test?

Testing is about making sure that the package (and functions) work as intended, 
**in spite of** external factors. With API packages, sources of errors can include:

- internet connectivity (both from the user and from the API)
- API response changes (format changes, variable naming, etc)
- dependency changes (this could include base R versions, operating systems, 
as well as the more obvious package dependencies)
- users doing unexpected things with your package (finding corner cases)

A good test setup should test at least the first three of these (user-side corner 
cases being difficult to foresee at time of function definition), and ideally should 
test each area individually while holding the other two parts constant. For example, 
you want to test how the package handles internet connectivity **without** worrying 
about whether the API response changes, or whether the dependencies have changed. 
You also want to test the package every time the dependencies change, without 
worrying about internet connectivity or API responses changing. And lastly, you 
want to test and make sure that the real API response has not changed, because 
it might return unexpected results. In addition to these concerns, another really 
important element to consider is making sure that it is easy to maintain the testing 
setup.  

For `ffscrapr`, I think the most important of these are testing the code for 
dependency changes and testing the code for API response changes. Internet 
connectivity seems fairly straightforward to write code for, and is worth doing 
at least once, but especially in a heavier-weight package like ffscrapr (which 
imports quite liberally from tidyverse packages like dplyr, purrr, tidyr etc), 
dependency testing is crucial to making sure the package continues to clean and 
process the data in the intended fashion. Also, fantasy platform APIs do change 
on a pretty frequent basis, adding and removing features nearly every year. It's 
worth testing against the live APIs to make sure that `ffscrapr` continues to 
operate as intended and without errors.

## Why not skip CRAN testing? 

Many API packages skip all testing on CRAN - it's a hassle to maintain, and the 
risk of being booted from CRAN can outweigh the effort of making sure tests don't 
fail. I'm here to suggest otherwise! I think developers are missing out on one of 
CRAN's most important benefits, which is being a massive dependency testing suite. 
Packages are tested on many different combinations of operating systems and R 
versions on a regular basis, and CRAN will also require your dependency packages 
to make sure that changes they make don't break your package (and/or if they do, 
to give you advanced notice that it will break your package). This gives you time 
to make the appropriate changes to the package to accommodate the change! 

Skipping these tests on CRAN is doing yourself a disservice, in my opinion, 
especially with the development of packages like [httptest](https://enpiar.com/r/httptest/) 
(which I use) and alternatives like vcr/webmockr - I think the extra work in making 
the package CRAN-testable (at least, to make sure that your R code processes the 
responses properly) is worth doing. These packages essentially create local copies 
of the data that the API would return from your http query, and redirect your 
request to read from the local files instead of letting your request go through 
to the API - allowing you to test while holding the API responses constant.

## Storing mock data in a separate repository

In an ideal world, tests and files are small and can be stored alongside the test 
data, such that you don't overrun CRAN's limitations on compressed or installed 
package sizes (5MB, generally speaking). With `ffscrapr`, I was finding that 
responses from certain sites (cough, Fleaflicker, ESPN, cough) were enormous - 
they package a whole whack of player data within each call, such that their 
responses were coming back at 5-10MB each!

In consulting the available documentation, I came across [this GitHub issue](https://github.com/nealrichardson/httptest/issues/11/) in the httptest 
repository, in which Neal Richardson describes some of his suggested approaches 
to solving this problem:

- Creating a function to redact the responses on capture
- Manually pruning the captured responses
- Refactor larger functions so that they can be tested on smaller chunks of data
- Lastly (and least-optimally, in Neal's opinion) - creating a separate data 
package that holds the response cache

I took a stab at each of these approaches, but one by one I had to discard them:

- API responses for fantasy football platforms are subject to change on a pretty 
regular basis - I've had to rebuild the captured responses several times since 
I started developing this package in August 2020, and corner cases are unlikely 
to appear in the first 5-10 responses of data
- Manually pruning the captured responses is unfeasible for similar reasons as 
above, and is also infinitely more time-consuming!
- I did try to refactor some of the larger/longer-running functions into 
smaller/more-testable units, especially since APIs frequently have options to 
limit and paginate. However, some of these requests were very difficult to 
configure in this fashion, and other functions that I wanted to test needed to 
aggregate large amounts of data. I wanted the tests to be easily evaluated via 
the naked eye. 

Thus, I landed on creating a separate package of test data files as my best route 
forward. I briefly considered the idea of pushing the data package to CRAN - this 
would ensure that it would always be "accessible" to the CRAN package wherever 
accessed - but discarded the prospects of both getting an exemption on package 
size AND maintaining regular updates to the test data. Instead, I opted for a 
separate GitHub repository of test files, which I considered to be a relatively 
reliable place to store the data. 

Of course, a few concerns popped up with this option, which needed handling:

- I would need to check that I could download the data at the time of 
test/vignette-build, and then skip testing/evaluating if GitHub or the data 
were unavailable. 
- I would need to find a way to version-control and make sure that I didn't 
accidentally delete test data that would cause CRAN testing to break - that 
would defeat the whole purpose of going through all the trouble of mocking the data!

I managed the latter concern with [a GitHub release](https://github.com/dynastyprocess/ffscrapr-tests/releases/tag/1.3.0), 
which allowed me to version-lock the test-data to match the CRAN version. I'll 
need to make sure to update the links within the package at every CRAN release, 
but I think this should be feasible!

{{< alert icon="circle-info" >}}
**2023 note** I didn't know of the existence of [piggyback](https://github.com/ropensci/piggyback)
at the time of this writing but if I did I would have used it instead so as to
not have to track another repository. It's been buried on my project list, but I've 
wanted to make a PR to add a `piggyback` option to `httptest` for a while now.
{{< /alert >}}

## Accessing the mock data

This needed some finagling, but I eventually settled on the following code in my [testthat setup file](https://github.com/dynastyprocess/ffscrapr/blob/main/tests/testthat/setup.R):

```R
suppressPackageStartupMessages({
  library(testthat)
  library(httptest)
  library(checkmate)
})
```

(Packages)

```R
if (identical(Sys.getenv("MOCK_BYPASS"), "true")) with_mock_api <- force 
```

If a system environment variable named MOCK_BYPASS has value of true, override the definition of "with_mock_api" to be "force", which stops the mocking behaviour and replaces it with normal evaluation.

``` R
download_mock <- !identical(Sys.getenv("MOCK_BYPASS"), "true") & !is.null(curl::nslookup("github.com", error = FALSE))
```

If not mock_bypass AND github.com is available (via a curl check), set download_mock to TRUE, otherwise set to FALSE. This checks as to whether the mock data should be downloaded at all. 

```R
skip <- FALSE

if (download_mock) {
  tryCatch(
    expr = {
      download.file("https://github.com/dynastyprocess/ffscrapr-tests/archive/1.3.0.zip", "f.zip")
      unzip("f.zip", exdir = ".")

      httptest::.mockPaths(new = "ffscrapr-tests-1.3.0")

      withr::defer(
        unlink(c("ffscrapr-tests-1.3.0", "f.zip"), recursive = TRUE, force = TRUE),
        testthat::teardown_env()
      )
    },
    warning = function(e) skip <<- TRUE,
    error = function(e) skip <<- TRUE
  )
}
```

With "skip" as default FALSE, try to download the data from the archive link (version controlled) and then unzip it. Then, set the .mockPaths to read this unzipped directory. When done, delete the zipped file and the test data. If anything generates a warning or error, set skip to TRUE. 

```
skippy <- function() NULL
if (skip) skippy <- function() testthat::skip(message = "Unable to download test data")
```

With skippy defined by default as a function that does nothing, if skip is TRUE (because of the previous step), change the definition of skippy to skip all tests.

I think this addresses my concerns - if running in a non-live-situation AND mock data can be downloaded, do so, otherwise skip all tests! A similar setup and teardown happens in all the [vignette RMDs as well](https://github.com/dynastyprocess/ffscrapr/blob/main/vignettes/espn_basics.Rmd#L21). 

## Capturing the mock data

Capturing the mock data turned out to be a fairly straightforward process. I made sure to have the test-data folder in the same top-level directory as the package itself, and then capturing the mock data was comfortably done with:

```R
library(httptest)
start_capturing("../ffscrapr-tests")
{run the tests}
stop_capturing()
{commit and push the test data}
```

I've had some problems with my PC's locale settings encoding the JSON oddly in the past, but I usually get around that by jumping onto an available RStudio server instance on a Linux server and re-recording the tests there when necessary. (This is probably overkill - if you're reading this and have better suggestions, let me know!)

## Testing the real APIs with GitHub Actions

CRAN obviously cannot test the real APIs, because it cannot handle failures gracefully. Instead, I have [a GitHub Action that does this](https://github.com/dynastyprocess/ffscrapr/blob/main/.github/workflows/test-apis.yml) by running the package tests with the MOCK_BYPASS environment variable set to "true". Paired with a cron schedule of once a week, this successfully catches API changes and issues before users can find them, which helps me anticipate and start patching bugs more effectively.



