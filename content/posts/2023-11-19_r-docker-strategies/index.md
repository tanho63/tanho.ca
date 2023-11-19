---
title: R + Docker Strategies
summary: Some personal best practices for writing effective Dockerfiles for R
date: 2023-11-19
draft: true
tags:
  - r
  - devops
  - docker
---

Docker+R best practices for developer happiness and smaller/faster image builds.

- Docker images should live in repos alongside the code used within them. This makes it much easier to iterate on images (i.e. adding/editing code that needs to run within the containerized environment).

- Docker images create one layer for each RUN, COPY, ENV, and ARG command. You can chain shell commands in a single RUN block with && - this is encouraged because everything within that single RUN command gets cached together as one layer. 

- Docker images are saved layer-by-layer, so cleaning up after installing things within the same RUN block is important to keeping image sizes small. apt cleanup code (`&& rm -rf /var/lib/apt/lists/*`) and `pak::pak_cleanup(force = TRUE)` should be added to every layer where a Linux or R package may be installed

- Using \ is handy for splitting things across multiple lines. What this is actually doing under the hood is escaping the invisible \n (newline) character that comes after every line in a file so that the command is actually parsed all as one line - as a result, you should make sure that \ is the last character of the line and that there are no spaces or comments after it

- Try to order COPY commands to the bottom of a given Dockerfile - if the file changes (which is often outside of your control) it invalidates the caching layers and causes a long wait while packages reinstall

- {pak} is the best thing since sliced bread when it comes to installing R packages

  - It uses a solver to automatically calculate minimum (or latest) suitable package versions to install, so can handle more complicated minimum/maximum version logic

  - if you set the PKG_SYSREQS environment variable to true, it will install the correct version of Linux dependencies for you (see pak config)
  
  - Can be used with either/both a lockfile setup (like renv) or the simpler DESCRIPTION file approach

  - If a pak build complains about sh installation, you probably need to add apt-get update && \ to that RUN block (and the appropriate cleanup code at the end && rm -rf /var/lib/apt/lists/*) to make it aware of the Linux libraries available to install

- One useful strategy is to COPY the DESCRIPTION file, install the dependencies with pak::local_install_deps(), and then COPY the rest of the package directory over and installing with pak::local_install()- this saves a lot of time when iterating on new code while your DESCRIPTION file and package dependencies stay the same, since it leverages caching much better. 

- A well-specified DESCRIPTION file makes it much easier to work with dependencies

- Pruning away as many dependencies as possible will make for a smaller image, which leads to a more efficient run process - especially for website app images and batch jobs where size of image matters.

- can use Config/Needs/{x} to specify dependencies required for special use cases, e.g. for a bundled Shiny app within an R package or for training/fitting/validating a model - these help keep things light for ETL versions of the image where performance is at a premium and also easy to adjust for the full app image. 

- a pak-optimized Remotes field (e.g. Remotes: nflreadr=nflverse/nflreadr@*release) automatically parses the latest GitHub release tag for a given internal package, which saves us from having to manually bump it along when there is a new version

- Using two Dockerfiles can be useful when building a bash script that parses this sort of @*release tag - having the internal packages get installed in one Dockerfile with docker build --no-cache -f Dockerfile-fbutils . and the core/common dependencies get built/cached separately makes it easy to make sure we always pull the latest release without it getting cached.

- RSPM provides binary CRAN packages for Ubuntu. This speeds things up a lot! This is already the default repo setting for most of rocker images, but version-tagged rocker images are also date-locked (e.g. rocker/r-ver:4.2.1 is locked to October 2022). If we need a more recent version of something, set options(repos = 'https://packagemanager.rstudio.com/cran/__linux__/focal/latest'); \ within the r -e command in question

- rocker-based images have a nice shell command called install2.r that installs R packages from CRAN, this can sometimes be a useful alternative to pak-based installs

- rocker-based images have a nice lower-case r command that is the same as R but quieter

- .dockerignore is spelled with a lowercase d. This is important if there are large files that you want to ignore within the repo - things will build quicker and your image will be much smaller. Keep an eye on the early message about “Sending build context to Docker daemon” - if it specifies a very large size this is likely a bad sign.

- If things can be written entirely in shell, we might want to consider using a different non-R base image.

## Other Improvement Opportunities

- Alpine-based images (i.e. using `rhub/r-minimal` as the base image) are less than 10% the size of Ubuntu based images (i.e. rocker/r-ver) but take 10-20 times as long to build. This may be worthwhile but investigating and eliminating dependency bloat is generally a better strategy. 

- Avoid COPYing secret files and hardcoding access token ENV variables within the base image. Docker buildkit can handle providing build-time-only secrets like GITHUB_PAT and .ssh keys while mounting volumes at runtime are much more secure. 

- We should look into Docker-compose files that live in /usr/local/bin that we can use to make the runtime commands run smoother (with the correct env + volume mounts etc?)
