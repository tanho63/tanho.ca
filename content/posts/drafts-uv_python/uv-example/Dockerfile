FROM rocker/r-ver:4.4.1
WORKDIR /app

## Copy relevant install portions from official uv docker image
COPY --from=ghcr.io/astral-sh/uv:0.5.7 /uv /uvx /bin/
## Copy the python install files over first
COPY .python-version pyproject.toml uv.lock ./
RUN uv sync \
  && R -e "\
  options(repos = 'https://p3m.dev/cran/__linux__/jammy/latest'); \
  install.packages('pak');\
  pak::pkg_install(c('reticulate', 'tensorflow', 'keras'));\
  pak::sysreqs_fix_installed();\
  pak::pak_cleanup(force = TRUE);\
  " && uv cache prune --ci
## Now copy over the DESCRIPTION file and install remaining deps
COPY DESCRIPTION ./
RUN R -e '\
  pak::local_install_deps(upgrade = FALSE); \
  pak::sysreqs_fix_installed();\
  pak::pak_cleanup(force = TRUE);\
  '

ENTRYPOINT ["R"]
CMD ["-e", "reticulate::py_config();str(tensorflow::tf_config());"]
