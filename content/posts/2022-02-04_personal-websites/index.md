---
title: "blogdown, distill, and personal websites with R"
date:   2022-02-04
summary: "Some thoughts on blogdown, distill, and getting started with your first website in R"
tags: 
  - r
url: /personal-websites-with-r
heroStyle: big
---

I've recently been [asked three times](https://twitter.com/drob/status/928447584712253440) 
for my advice on whether to use **blogdown** or **distill** when getting started with your 
first website with R, so in true DRob fashion: here's a blog post!

{{< alert "circle-info">}}
**2023 note** This post never actually got properly fleshed out beyond a set of 
bullet notes, but I figure it's still worth sharing 😅 Have added a couple of 2023 notes
as well.
{{< /alert >}}

## TLDR

- They're both great, and incredibly well supported within the R community.
- It doesn't matter what you start with, you can redo your site later. You probably 
will want to at some point down the line regardless, it's part of the learning process! 
Your content **will** transfer with minimal effort. 
- I recommend [starting with blogdown and Hugo Apero](https://hugo-apero-docs.netlify.app/start/) 
in most cases, EXCEPT
- ...if you know you **dislike** CSS and styling, in which case
[Distill](https://themockup.blog/posts/2020-08-01-building-a-blog-with-distill/) 
is perfect for you
- [Buy a domain name](), serve it with Netlify or GitHub Pages, and go forth and grow your garden!

## Distill

- Distill is minimalist in that it **doesn't** let you do a lot aesthetically - 
this means that most distill sites are straightforward and clean and incredibly 
easy to maintain/use going forward.
- The most you'll do is pick fonts and colours - everything else is pretty much set out for you.

{{< alert "circle-info">}}
**2023 note** RStudio/Posit folks are promoting Quarto websites for this minimal/simple 
aesthetic now.

Distill sites will still work (Distill simplicity at it's finest!), but if you 
haven't started your site yet you might be interested in starting from the "latest hotness". 

Another compelling reason to use to Quarto is the ability to use Python and Observable JS 
alongside with R chunks, so if that's of interest I'd look into switching. 
{{< /alert >}}

## Blogdown

- blogdown is decidedly not minimal - everything is flexible and can be laid out 
in any number of ways
- this means you can pick out some kickass hugo themes from somewhere and plug them in 
so that your site doesn't look like other people's sites
  - e.g. https://tanho.ca/ and https://ffverse.com/ are both blogdown sites, 
    but don't have the same feel at all
- the flexibility means you're more likely to break your site somehow, and also 
increases the temptation that you'll end up nuking your site and rebuilding it 
from scratch.
- the latest/safest hotness in blogdown is Alison Hill's [Hugo Apero theme](https://hugo-apero-docs.netlify.app/start/)
- I spend a bunch of time doing front-end with Shiny so I'm not as scared to wade 
into the guts of css/html crap - this site is primarily made from a theme called Toha, 
and [ffverse.com](https://ffverse.com) from a theme called Osprey

{{< alert "circle-info">}}
**2023 note**
I've since ported tanho.ca to a new theme, [Blowfish](https://blowfish.page).

I found that I was overriding a lot of the Toha theme components manually and realized
I wanted something that was more flexible.
{{< /alert >}}


## Which one do I choose?

Whichever one appeals to you, if you've read all the way through to here! 
In most cases, I think there's a lot of value in learning to use blogdown, and 
would recommend starting there with Apero...except if:

- you don't care about the aesthetics, 
- you know you hate CSS and style decisions, or 
- you want to get started quickly 

in which case Distill is perfect for you!
