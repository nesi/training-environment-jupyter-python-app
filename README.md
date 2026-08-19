# REANNZ training environment Python Jupyter app

JupyterLab app for running Introduction to Python workshops on the NeSI training environment.

## Datasets in the image

`docker/Dockerfile` pins `nesi/intro-python` by release tag (`PYTHON_VERSION`) and clones it to
`/opt/intro-python`. `template/script.sh.erb` then rsyncs that into `~/intro-python`, so
learners find the notebooks and `data/` in their home directory.

Data shipped:

* Portal surveys — `data/surveys.csv`, `data/species.csv`
* Gapminder, tidy long — `data/gapminder.csv`
* Gapminder, wide — `data/gapminder_all.csv`, `data/gapminder_gdp_<continent>.csv`

The gapminder files also have a no-clobber `wget` fallback in the Dockerfile, pinned to the
upstream Carpentries lesson commits, so the image still has them if `PYTHON_VERSION` is rolled
back to a release that predates them. To add or change data, commit it to `nesi/intro-python`
under `data/`, tag a release there, and bump `PYTHON_VERSION` here — pushing to this repo triggers the container build.
