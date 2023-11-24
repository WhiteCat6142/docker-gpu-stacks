# Copyright (c) Jupyter Development Team. WhiteCat6142 <whitecat6142+git@gmail.com>
# Distributed under the terms of the Modified BSD License.
ARG BASE_CONTAINER=minimal-notebook
FROM $BASE_CONTAINER

LABEL maintainer="WhiteCat6142 <whitecat6142+git@gmail.com>"

# Fix: https://github.com/hadolint/hadolint/wiki/DL4006
# Fix: https://github.com/koalaman/shellcheck/wiki/SC3014
SHELL ["/bin/bash", "-o", "pipefail", "-c"]

USER root

RUN --mount=type=cache,target=/var/lib/apt/lists \
    --mount=type=cache,target=/var/cache/apt \
    rm /etc/apt/apt.conf.d/docker-clean && \
    apt-get update --yes && \
    apt-get install --yes --no-install-recommends \
    # for cython: https://cython.readthedocs.io/en/latest/src/quickstart/install.html
    build-essential \
    # for kubernetes plugins
    apt-transport-https gnupg2 \
    # for latex labels
    cm-super \
    dvipng \
    # for matplotlib anim
    ffmpeg

USER ${NB_UID}

# mamba downgrades these packages to previous major versions, which causes issues
RUN echo 'jupyterlab >=4.0.4' >> "${CONDA_DIR}/conda-meta/pinned" && \
    echo 'notebook >=7.0.2' >> "${CONDA_DIR}/conda-meta/pinned"

# Install Python 3 packages
RUN mamba install --yes \
    'altair' \
    'beautifulsoup4' \
    'bokeh' \
    'bottleneck' \
    'cloudpickle' \
    'conda-forge::blas=*=openblas' \
    'cython' \
    'dask' \
    'dill' \
    'h5py' \
    'ipympl'\
    'ipywidgets' \
    'jupyterlab-git' \
    'matplotlib-base' \
    'numba' \
    'numexpr' \
    'openpyxl' \
    'pandas' \
    'patsy' \
    'protobuf' \
    'pytables' \
    'scikit-image' \
    'scikit-learn' \
    'scipy' \
    'seaborn' \
    'sqlalchemy' \
    'statsmodels' \
    'sympy' \
    'widgetsnbextension'\
    'xlrd' \
    'cvxpy' \
    'cvxopt' \
    'pyomo' \
    'tensorflow' \
    'keras' \
    'cupy' \
    'jupyter-server-proxy' \
    'jupyter-vscode-proxy' && \
    mamba clean --all -f -y && \
    fix-permissions "${CONDA_DIR}" && \
    fix-permissions "/home/${NB_USER}"
#    'dccp' \
#    'scikit-glpk' \
#    'sdpa-python' \

# Install facets package which does not have a `pip` or `conda-forge` package at the moment
WORKDIR /tmp
RUN git clone https://github.com/PAIR-code/facets && \
    jupyter nbclassic-extension install facets/facets-dist/ --sys-prefix && \
    rm -rf /tmp/facets && \
    fix-permissions "${CONDA_DIR}" && \
    fix-permissions "/home/${NB_USER}"

RUN MPLBACKEND=Agg python -c "import matplotlib.pyplot" && \
    fix-permissions "/home/${NB_USER}"

USER ${NB_UID}

WORKDIR "${HOME}"
