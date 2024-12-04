---
layout: single
title: "Q & A: Explaining Shiny to Your IT Team"
date:   2020-10-15
aliases: /explainingshiny
summary: "Helping present the business architecture for Shiny in production to a relatively technical (but not R-based) crowd"
published: true
tags: 
  - r
  - shiny
  - r4ds-slack
heroStyle: background
---

I greatly enjoy the R4DS Slack Community (found at [r4ds.io/join](r4ds.io/join)!) - and spend a fair bit of time answering questions, especially about Shiny! This is the first in a series of posts where I curate some of the more interesting questions and answers I've found (and sometimes answered).

<hr>

`Piranha` asks:

> Hello,\
> This might be bit of a stretch.... I could use a bit of help in prepping for some upcoming conversations with the head of my company's IT/Security. 
> 
>I am trying to get IT support for Shiny (currently IT supports Tableau).
>
> **Question**: Is there any kind of high level summary of this Admin Guide document?
> <https://docs.rstudio.com/shiny-server/> 
> 
> More specifically, I want to summarize the official document into about a 3-5 slide presentation. Topics that I want to include:
>
> -   What is Shiny? (3-5 bullet points, in language that a "traditional" IT person will understand)
>
> -   How to think about security and authentication for both the Shiny Server free and Pro.
>
> -   How to think about Dockerizing an application.
>
> I attempted to summarize the official docs into a 3-5 slide PowerPoint (ugh, yes, PowerPoint!), but am really struggling. This is a massive document and I am not an IT person!\
> Any pointers would be really helpful.\
> Thanks!

Answer: (mine)

I don't know if I have a document to point you at, but I would suggest a mental model where you separate Shiny the app (the app.R file and all of the things included in it) from the ways you deploy Shiny. Writing out some of my personal thoughts on it:

Shiny (the app):

-   is an R package that allows you to write UI (client-side) and server-side logic in R, which can be implemented quickly (from a code-writing perspective) and powerfully (leverages all of the modelling/data-wrangling packages and abilities of R).
-   has an architecture consisting of a client (the browser) and a server (an R process). The two are connected by a [websocket](https://developer.mozilla.org/en-US/docs/Web/API/WebSockets_API) that receives state changes from the client, such as new values for input controls, and distributes state changes from the server, such as new output values.

Shiny is not self-contained because it relies on R and other libraries (Shiny, as well as whatever else you use in the app) that are installed onto the server. Docker is one of the best ways to handle this, because it creates a container that holds an installation of R and all of the relevant packages, and can be version-locked to ensure stability. Once a Shiny app has been dockerized, it can be turned over to a devops team to deploy, and the devops team does not have to know R or Shiny in order to use the app.

This allows for a division of expertise: 

- the data scientist makes the app, and makes sure the docker container builds and works as intended 
- the devops engineer can take care of the details with deploying and securing the apps, by controlling/restricting access to the container itself rather than forcing the data scientist to worry about it inside the app. 
- Both the data scientist and the devops engineer should responsible for performance - data scientist by improving the code for efficiency, engineer by supplying the containers with the right amount of processing power/memory/load-balancing A dedicated devops person can pretty easily deploy a docker container into an existing app/microservices system, if such already exists. 

At the other end of the spectrum, [shinyapps.io](http://shinyapps.io/) and RS Connect are solutions that RStudio provides to help data teams who want this sort of devops process but don't want to have the work done in house. They'll manage the servers for you and make it very easy for R users to just upload all of the app data and configs, and they'll manage deployment, performance and authentication for you.

I'm a data generalist for a small team, and manage the full stack from data import/wrangle/model to app dev to app deploy. I wouldn't necessarily recommend that path if your team is large enough - let devops be devops and data scientists be data scientists where possible!

> Some follow-up thoughts/questions.
>
> In a discussion earlier this week, a person from IT asked me "I dont know R, can you tell me how Shiny connects to the user's browser?" I didn't know much about WebSocket technology at the time, so my response was: "Shiny comes with its own webserver, but a common practice is to use nginx webserver in front of the shiny app".
>
> Now that I know a bit more...would it be accurate to some something along these lines: "Shiny uses WebSocket technology to create a 2-way communication between the server and the browser. This allows the data scientist to leverage the full data-processing and analytical capabilities of R. In our current process, we need to do processing/analysis in one tool (e.g. SAS, R) and visualization/sharing in Tableau/Excel - creating lots of inefficiencies. Using Shiny, we can fine-tune and scale our analysis to exactly what is needed by the client and serve that via a Shiny dashboard."
>
> Furthermore, "From the data science team's perspective, this is our **ask**. We need the ability to deliver interactive analysis to our clients at scale. Can you help us do that in a secure way? We'll build the Shiny app and dockerize it. We need help from IT to provide secure access by clients".

> `Shiny uses WebSocket technology to create a 2-way communication between the server and the browser`

I cribbed that chunk from here: <https://community.rstudio.com/t/is-there-any-equivalent-of-web-sockets-in-shiny-world/17908>. If you're hoping to communicate how Shiny works to an IT person, I would use the wording as described rather than paraphrasing it too much - receives state changes from client and distributes state changes from server is high-level enough for most IT people to understand - and you lose something by abstracting beyond that.

> Using Shiny, we can fine-tune and scale our analysis to exactly what is needed by the client and serve that via a Shiny dashboard

I'd change this to say that it allows you to use the same R code that you use to process and generate the data right in the app, which allows you to package the "business logic" right into the app for immediate interactive analyses. I'm not familiar with your team and the ask - but identifying the docker container as the handoff point where IT helps deploy the app and serve it securely and effectively to clients is a good start. That lets IT do the security bit.
