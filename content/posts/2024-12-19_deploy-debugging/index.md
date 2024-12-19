---
title: "DSLC Slack: Debugging Netlify Deploys"
summary: |
  A quick example of reading through automated deploy logs, finding the relevant errors, and triaging a fix - transcribed from the DSLC Slack Community help channels
date: 2024-12-19
aliases: /debugging-netlify-deploys
tags:
 - dslc-slack
 - q-a
 - r-in-production
 - r
---

{{< dslc-qa >}}

## Question

> Hi all! I'm having a hard time understanding why netlify checks on a Pull Request 
> with only a empty line added in the commit are failing for a blogdown website. 
> I created [this PR](https://github.com/MetaDocencia/SitioWeb/pull/226) as a 
> small example, but basically any kind of PR is failing nevermind its content, 
> and the site is published-rendered without problem if we commit directly to main.
> 
> I'm super puzzled by this, does anyone have any tips on how can I debug it? Thanks :pray:
> 
> \- MC Nanton

## Answer

Let's look through some of your Netlify deploy logs. I found these from your GitHub 
repo automated checks:

- [failing deploy logs for your PR](https://app.netlify.com/sites/metadocencia/deploys/6758b3ee5881ee0009315995)
- [successful deploy logs for your main branch](https://app.netlify.com/sites/metadocencia/deploys/67587beefabc760008a03d0b)
- [repo link](https://github.com/MetaDocencia/SitioWeb)

{{< alert "circle-info" >}}
editor's note: archived the deploy logs in question to 
[this gist](https://gist.github.com/tanho63/09ccc1edec99c4b95227cc18b8dc7957)
just in case the deploy logs are deleted at some point
{{</ alert >}}

It seems like your 
[PR deploy logs](https://app.netlify.com/sites/metadocencia/deploys/6758b3ee5881ee0009315995#L100)
show this warning immediately before failing:
```
WARN 2024/12/10 21:35:19 Module "hugo-academic" is not compatible with this Hugo 
version; run "hugo mod graph" for more information.
```
and this definitely doesn't show up on your main deploy logs. Let's try to figure out
where your hugo versions are specified. Here's a relevant section of the deploy logs:
```
4:35:19 PM: ❯ Current directory
4:35:19 PM:   /opt/build/repo
4:35:19 PM: ​
4:35:19 PM: ❯ Config file
4:35:19 PM:   /opt/build/repo/netlify.toml
4:35:19 PM: ​
4:35:19 PM: ❯ Context
4:35:19 PM:   deploy-preview
```

The logs say that the file responsible for specifying hugo version is netlify.toml,
so let's have a look at what that looks like on GitHub 
[right now](https://github.com/MetaDocencia/SitioWeb/blob/74e10134f18b5e08a99059d1cb927a14be3afb60/netlify.toml):
```toml
[context.production.environment]
HUGO_VERSION = "0.64.0"
```
Notice that it specifies `context.production` here, and that the context for the 
failing logs is `deploy-preview`. If we go back into the failing logs, we can confirm
that the hugo-version specified here isn't working properly:
```
4:35:12 PM: Installing Hugo 0.57.2
```
So a solution that should work is to update the netlify.toml to specify the 
HUGO_VERSION for the deploy-preview context:
```toml
[context.production.environment]
HUGO_VERSION = "0.64.0"
[context.deploy-preview.environment]
HUGO_VERSION = "0.64.0"
```
and this should do the trick. You might need to push it to main instead of to a PR
branch though, Netlify usually uses the config from the main repo branch.

(Update: this solution worked!)
