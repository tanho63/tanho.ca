---
layout: single
title: "blogdown, distill, and personal websites with R"
date:   2022-02-04
summary: "Some thoughts on blogdown, distill, and getting started with your first website in R"
type: rshiny
menu:
  sidebar:
    name: 'Personal websites'
    identifier: 2022personalwebsites
    parent: rshiny
url: /personalwebsites
hero: /images/rshiny/painting.jpg
---

I've recently been [asked three times](https://twitter.com/drob/status/928447584712253440) for my advice on whether to use blogdown or distill when getting started with your first website with R, so in true DRob fashion: here's a blog post!

TLDR: 

- They're both great, and incredibly well supported within the R community.
- It doesn't matter what you start with, you can redo your site later. You probably will want to at some point down the line regardless, it's part of the learning process! Your content **will** transfer with minimal effort. 
- I recommend [starting with blogdown and Hugo Apero](https://hugo-apero-docs.netlify.app/start/) in most cases, EXCEPT
- ...if you know you **dislike** CSS and styling, in which case [Distill is perfect for you](https://themockup.blog/posts/2020-08-01-building-a-blog-with-distill/).
- [Buy a domain name](), serve it with Netlify, and go forth and grow your garden! 

## Why a personal site at all?
{}

## Distill

- Distill is minimalist in that it **doesn't** let you do a lot aesthetically - this means that most distill sites are straightforward and clean and incredibly easy to maintain/use going forward.

- The most you'll do is pick fonts and colours. everything else is pretty much set out for you

## Blogdown

- blogdown is decidedly not minimal - everything is flexible and can be laid out in any number of ways

- this means you can pick out some kickass hugo themes from somewhere and plug them in so that your site doesn't look like other people's, as much

- e.g. https://tanho.ca/ and https://ffverse.com/ are both blogdown sites, but don't have the same feel at all

- the flexibility means you're more likely to break your site somehow, and also increases the temptation that you'll end up nuking your site and rebuilding it from scratch 

- the latest/safest hotness in blogdown is Alison Hill's Hugo Apero theme https://hugo-apero-docs.netlify.app/start/ 

- I spend a buncha time doing front-end with Shiny so I'm not as scared to wade into the guts of css/html crap - mine is primarily made from a theme called Toha, and ffverse from a theme called Osprey

## Which one do I choose?

Whichever one appeals to you, if you've read all the way through to here! Longer answer: in most cases, I think there's a lot of value in learning to use blogdown, and would recommend starting there with Apero...except if a) you don't care about the aesthetics, b) you know you hate CSS and style decisions, or c) you want to get started quickly - in which case Distill is perfect for you!
