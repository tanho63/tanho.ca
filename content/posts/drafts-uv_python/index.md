---
title: "An R Dev's Field Notes For Setting Up Python Stuff (aka 'use uv')"
summary: |
  A few field notes from some recent experimentation with installing/managing Python and Python packages locally and in production Docker. TLDR: praise be to uv, uv is the best.
date: 2024-12-23
tags:
 - production
 - python
 - docker
url: /uv-python
---

{{< alert "circle-info">}} 
TLDR: Use [uv](https://docs.astral.sh/uv) to install everything Python: configuring 
a virtual env for easy interactive development and a direct installation for Docker. 
It's magically painless. Example repository here. LINK TBD
{{</alert>}}

I was recently helping my colleague Javier use tensorflow in one of his R projects, 
and found myself trying to optimize the Python setup flow for both local interactive 
and production Docker usage.

My goals were:
- ***painlessly*** install Python, required packages, and Linux dependencies, all
with compatible and correct versions, *such that the reticulate, tensorflow, and 
keras R packages all work out of the box*.
- have extremely simple setup for local interactive development (so that Javier 
spends time on the fancy spatiotemporal modelling he's world class at, not debugging 
installation issues)
- have a minimal Docker footprint that builds quickly and is compatible with our
existing production docker images, which are built on top of [`rocker/r-ver`](https://rocker-project.org)

Here's some notes on what I ended up with. Yes, I'm notably an R person and consider
myself vaguely literate with Python, so feel free to let me know if I'm doing 
something dumb here.

# part one: local workflows

## use uv

If there is even _one_ takeaway here, it's that uv is the fucking best and 
magically solves all Python installation-related problems. It's basically pak,
if pak also encompassed installing R itself as well as managing renv environments.
(Sidenote: you should be using pak, but I'm mixed on the renv stuff as of now.)

The first order of business is to install uv locally, which can be done by following
these [instructions](https://docs.astral.sh/uv/getting-started/installation/). 

For me it was as simple as running:

```sh
curl -LsSf https://astral.sh/uv/install.sh | sh
```

## initialize uv in the project

uv (and all python projects, really) works best with virtual environments, which
means that the python version and all package versions are installed to a 
project-specific directory. Python installation and compatibility is much more
finicky than R's imo (certainly have never seen CRAN/R packages depend on specific
versions like Python ones tend to), so virtualenvs are great and wonderful and we 
like them a lot.

```sh
uv init 
```
This should create the following files:
```
❯ tree -a ~/uv-example
~/uv-example
├── hello.py
├── pyproject.toml
├── .python-version
└── README.md

0 directories, 4 files
```
For my purposes (getting reticulate/tensorflow working), we can immediately delete
the hello.py and README.md files.

## sort out packages and versions

Now, let's figure out some package versioning: Javier advises that he wants to 
use Keras 2 since he finds that Keras 3 is somewhat poorly supported in the R
package wrapper, so we should go ahead and add that as follows:

```sh
uv add "keras<3.0.0"
```
We also need a compatible version of tensorflow, so we can add that with:
```sh
uv add tensorflow
```
Running these commands creates a virtual environment folder (.venv) and a lockfile
(uv.lock) of the packages that are computed and installed into that venv. 

Notice earlier that it created a `.python-version` file? My system defaulted to
installing 3.10, which came installed on my Ubuntu 22.04 machine. We can experiment
with finding the latest Python version compatible with our requirements by bumping
the version marker in this file, deleting the venv folder, and running `uv sync`
to try installing everything again. 

Let's go ahead and edit that to 3.13 (the latest Python release) and see what happens:
```sh
❯ 3.13 > .python_version
❯ rm -rf .venv
❯ uv sync
Using CPython 3.13.1
Creating virtual environment at: .venv
Resolved 42 packages in 1ms
error: Distribution `tensorflow==2.15.1 @ registry+https://pypi.org/simple` can't 
be installed because it doesn't have a source distribution or wheel for the current
platform
```
Hm. 3.13 is incompatible, not super surprising I guess. Let's delete .venv and 
repeat this until we find the latest compatible python version - this happens to
be 3.11.

```sh
❯ uv sync
Using CPython 3.11.11
Creating virtual environment at: .venv
Resolved 42 packages in 0.73ms
Installed 41 packages in 262ms
```

## install reticulate, tensorflow, and keras R packages

Let's install the reticulate, tensorflow, and keras R packages to make sure we did
things correctly. I like using a DESCRIPTION file for all R projects, so let's make
a minimal one to list out these dependencies:

```
Package: uv.example
Title: example repo for uv + r + python installs
Version: 1.0.0
Authors@R: person("Tan", "Ho", , "tan@tanho.ca", role = c("aut", "cre"))
License: MIT + file LICENSE
Imports: 
    reticulate (>= 1.40.0),
    tensorflow,
    keras
Encoding: UTF-8
```

We need [reticulate >= 1.40.0](https://github.com/rstudio/reticulate/pull/1678)
in order to properly use uv venvs. 

Then, with this DESCRIPTION, we can use pak to install the dependencies:
```r
pak::local_install_deps("~/uv-example")
```
[_vague pakpakpakpakpak noises_]

Now we can validate that everything works happily together locally:

```r
R> reticulate::py_config()
python:         ~/uv-example/.venv/bin/python
libpython:      ~/.local/share/uv/python/cpython-3.11.11-linux-x86_64-gnu/lib/libpython3.11.so
pythonhome:     ~/uv-example/.venv:~/uv-example/.venv
virtualenv:     ~/uv-example/.venv/bin/activate_this.py
version:        3.11.11 (main, Dec 19 2024, 14:33:27) [Clang 18.1.8 ]
numpy:          ~/uv-example/.venv/lib/python3.11/site-packages/numpy
numpy_version:  1.26.4

NOTE: Python version was forced by './.venv' existing in the current working directory
```
```r
R> tensorflow::tf_config()
TensorFlow v2.15.1 ()
Python vError in cat("Python v", x$python_version, " (", aliased(x$python), ")\n",  : 
  argument 2 (type 'list') cannot be handled by 'cat'
```
(ugh, tensorflow pkg has a print error trying to print the package version smh)
```r
R> str(tensorflow::tf_config())
List of 6
 $ available     : logi TRUE
 $ version       :Classes 'package_version', 'numeric_version'  hidden list of 1
  ..$ : int [1:2] 2 15
 $ version_str   : chr "2.15.1"
 $ location      : NULL
 $ python        : chr "~/uv-example/.venv/bin/python"
 $ python_version:Classes 'package_version', 'numeric_version'  hidden list of 1
  ..$ : int [1:2] 3 11
 - attr(*, "class")= chr "tensorflow_config"
```
```r
R> keras::is_keras_available()
[1] TRUE
```
Cool, now I'm satisfied everything is working as it should.

## update gitignore to ignore venv

`.venv` is a hefty folder, since it contains a whole python install as well as 
tensorflow, keras, a whack of other dependencies. 

```sh
❯ du -hd 0 .venv
1.6G    .venv
```
We want to ignore the .venv folder in git, so let's add that to .gitignore:
```sh
echo ".venv" >> .gitignore
```

## recap of local workflow

At this point, I think I'm satisfied with the local interactive workflow: 

- `uv sync` initiates the virtualenv based on pyproject.toml + .python_version files
- `uv add` handles adding a dependency and installing it, making sure it is compatible
with the other dependencies and current python versions. If it fails to install,
it will warn loudly.
- reticulate, tensorflow, and keras R packages all work fine locally.

Let's move on to dockerizing the project.

# dockerizing

First, let's add .venv to a .dockerignore file so that it's not included in the
Docker context - this makes Docker builds much faster:
```sh
echo ".venv"" >> .dockerignore
```
Our base images start with a rocker/r-ver image, e.g.
```Dockerfile
FROM rocker/r-ver:4.4.1
```
The uv docs [recommend](https://docs.astral.sh/uv/guides/integration/docker/#installing-uv) 
installing uv by copying the relevant portions from the official uv docker image:
```Dockerfile
## Copy relevant install portions from official uv docker image
COPY --from=ghcr.io/astral-sh/uv:0.5.7 /uv /uvx /bin/
```
Next, we can copy the relevant install files over to our image and run our install
commands:
```Dockerfile
RUN R -e 'install.packages("pak")'
WORKDIR /app
COPY DESCRIPTION .python-version pyproject.toml /app
RUN uv sync
RUN R -e "pak::local_install_deps('/app')"
```
