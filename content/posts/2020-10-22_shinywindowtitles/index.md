---
layout: single
title: "Q & A: Changing Window Titles in Shiny"
date:   2020-10-20
summary: "Pairing a snippet of JavaScript with Shiny reactivity to dynamically change the window title"
tags: 
  - dslc-slack
  - shiny
  - r
aliases: /shinywindowtitles
heroStyle: background
---

I greatly enjoy the R4DS Slack Community (found at [r4ds.io/join](r4ds.io/join)!) - and spend a fair bit of time answering questions, especially about Shiny! This is another in a series of posts where I curate some of the more interesting questions and answers I've found (and sometimes answered).

<hr>

`Dhiraj` asks:

> I have a shiny application which uses the navbarPage() layout. The app has multiple tabPanels with their own titles. Is it possible to have the windowTitle of the navbarPage dynamic to show the titles of the tabPanels ?

Here's a minimal example of the Shiny and JS code you'll need:

```r
library(shiny)
library(tidyverse)

custom_js <- 'Shiny.addCustomMessageHandler("handler_windowtitle", switch_window_title );
function switch_window_title(message){
  document.title = message;
}'

ui <- navbarPage(
  title = 'navbarpage',
  id = 'displayed_tab',
  tags$head(tags$script(custom_js)),
  tabPanel(title = "StrangePlanet"),
  tabPanel(title = "DarthVader")
)

server <- function(input, output, session) {
  observeEvent(
    input$displayed_tab,
    session$sendCustomMessage("handler_windowtitle",input$displayed_tab)
  )
}

shinyApp(ui, server)
```


If you give navbarPage an ID, it'll create an `input` variable that can be accessed as `input${id}`, returning the value argument of the tabPanel. In this case, tabPanel returns the `title` argument as the default value - you can specify something else if you prefer. 

In a production app, I'd also move the JS into a separate JS file (stored in www) and use `includeJS()` instead of using the `tags$script` method. 
