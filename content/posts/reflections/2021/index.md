---
layout: single
title:  "Reflections on 2021"
date:   2021-12-29
tags: 
  - reflections
summary: "Reflecting on a year of streaming, fasting, pandemic isolation, and more."
showComments: false
---

Another year in the books. 2021 seemed to have it all: ups and downs, twists and turns, successes and failures, and plenty of frustration. In that spirit, some reflections on both the good and the bad:

-   I [started streaming](https://twitch.tv/tanho_) my programming projects on Twitch. The consistency has been incredible for my project productivity (more on that later) and for anchoring my weekly schedule through good and bad times - a haven where I could disconnect from the regular grind and code for myself with a schedule. I've also made great friends in the R and streaming communities, and am grateful and inspired to help champion data science streaming going forward.

-   I burnt out to rock bottom several times and slumped into deep and empty depressions, unable to get out of bed. 

-   I took up a (rather strict) extended [fasting schedule](https://tanho.ca/now-archive/2021-07-16-now/) (two 36 hour fasts each week) and went from 285 pounds to 235 pounds. It was hard and lonely at times, but effective and felt maintainable. I'd like to get into the 180s still, and would be very pleased with continuing that pattern next year.

-   I [stopped rowing and coaching](https://tanho.ca/now-archive/2021-07-16-now/#no) this year. I felt unappreciated and found myself dreading the start of the season, which eventually (along with being depressed at the time) pushed me to finally let it go. The relief has been welcome. I'm not sure I'm back next year - might pick up some other sport instead - frisbee? soccer? rugby? martial arts? crossfit? I'll come back to rowing eventually, once I've really cleared my head and can learn to enjoy the sport for the sport itself.

-   I [travelled to London](https://tanho.ca/now-archive/2021-11-15-now/) for my birthday and enjoyed my first trip in a few years. Getting to see Liverpool play at Anfield was an incredible experience, as was seeing Les Miserables in theatres - but I also really enjoyed eating my way through London's restaurants and food markets!

-   I picked up freelance work and bit off a lot more than I could chew, burning out quickly. I do like the variety and think I might try to do more of it next year, if I can better manage my time and energy.

-   I've become a lot closer to many of my online communities and friends (TDM, Mafia, nflverse, streaming, R4DS) and I was especially grateful to have finally gotten the opportunity to [hang out with Joe](https://twitter.com/_TanHo/status/1462120175185149958?s=20) in person. A life-changing friendship.

-   I've fallen out of touch with my local friends, between choosing to fast (and skipping socials) and letting go of rowing. It was lonely and I miss them greatly.

-   I managed to get three doses of COVID vaccine, and worked hard at maintaining health, masking, and distancing. 

Overall, I'm pleased with my efforts to control what is within my control and respond to what I could respond to this year. I'm still working on trying to let go of the things that are out of my control - to varying degrees of success.

And some thoughts on projects:

-   I expanded [{ffscrapr}](https://ffscrapr.ffverse.com) to cover ESPN and to connect with nflverse data. I'm now covering API features for four of the largest platforms (MFL, Sleeper, ESPN, and Fleaflicker) and pleased with its featureset and overall performance. I've been asked to expand it to Yahoo but without having a league there (nor experience with the OAuth), I haven't really had the drive to do anything on that front.

-   I dreamed up and then built out [{ffsimulator}](https://ffsimulator.ffverse.com), getting it published on CRAN. A project that literally walked out of my head into code over the course of 3-4 months, and I built it mostly on-stream! I think it gives a really interesting take on fantasy football research - and certainly a lot more data to work with. It's probably not the most robust way to do a simulation but it's one of the first projects of its kind and we haven't really scratched the surface about how to use that data yet, I think.

-   I used ffsimulator to [simulate all of Scott Fish Bowl](https://sfbprojections.dynastyprocess.com/)! It had a pretty decent correlation to points scored and overall success (about 0.30 R^2, which is well in line with most NFL predictions) especially with how volatile this year's fantasy football season turned out to be.

-   I wrote [{ffpros}](https://ffpros.ffverse.com), a package for scraping FantasyPros.com, but have been leery of publishing it to CRAN or taking it much further than the current state.

-   I moved my fantasy football R packages to the ffverse [GitHub organization](https://github.com/ffverse) and [domain](https://ffverse.com), hoping to make it easier to collaborate and share work with the community. Thus far it's still mostly me and Joe. Definitely happy to champion it until it gets more traction.

-   I mostly watched passively as the DynastyProcess [trade calculator](https://apps.dynastyprocess.com/calculator) continued to grow in popularity- it's astounding that it more than doubled in usage (to a peak of 80,000 monthly users this season!) despite me not spending any advertising dollars, promotion time, or even writing a single line of code for it this year. I kind of feel like I've grown a bit away from the project, and am not super sure of what direction it should go. I'm also not sure I want to monetize it, even though I know there's a market there if I want it. I'm learning that I'm not very money driven!

-   I learned how to use GitHub Actions and ported all of my [DynastyProcess automation](https://github.com/dynastyprocess/data) there.

-   I managed to get Ben Baldwin, Sebastian Carl, Lee Sharpe and more to unite under the nflverse [GitHub organization](https://github.com/nflverse) and domain, synchronizing our efforts to advance open source NFL analytics and research.

-   I built out [{nflreadr}](https://nflreadr.nflverse.com) as a lower-dependency package that only downloads precomputed data from nflverse repositories. It's lighter weight and faster than the original nflfastR equivalent - and it's been really good to collaborate with Seb on it. Among other things, Seb is a remarkably good code reviewer!

-   I maintained a lot of the nflverse GitHub Actions data automations and I'm proud of how well it works now - not to say that there aren't problems but that they're resolved efficiently and effectively.

-   I wrote up a mini package called [{fivestars}](https://tanho63.github.io/fivestars) that adds CSS star ratings to rmarkdown and Shiny pages. It does a tiny thing but I enjoy it!

-   I compiled some other fun helper functions into my personal package [{tantastic}](https://github.com/tanho63/tantastic) and it even includes my own personal ggplot theme!

-   I recruited a whole bunch of the rstats community into doing [Advent of Code](https://rstats-aoc.netlify.app)! I didn't finish it this year after taking some time for family stuff, but plan to pick up the last few puzzles eventually.

-   I made a neat [helper package](https://github.com/tanho63/aoc.elf) for Advent of Code that initiates a template, downloads inputs, and applies caching! 

-   I continued to help R users by answering help questions across a wide range of platforms - R4DS, Twitter, nflverse, and (a little bit) the R discoRd. 

-   I'm much more confident in live-coding, data visualisation, package development, and overall R skills.
