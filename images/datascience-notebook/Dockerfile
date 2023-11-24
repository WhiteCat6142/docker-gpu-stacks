# Copyright (c) Jupyter Development Team.
# Distributed under the terms of the Modified BSD License.
ARG BASE_CONTAINER=scipy-notebook
FROM $BASE_CONTAINER

LABEL maintainer="Jupyter Project <jupyter@googlegroups.com>"

# Fix: https://github.com/hadolint/hadolint/wiki/DL4006
# Fix: https://github.com/koalaman/shellcheck/wiki/SC3014
SHELL ["/bin/bash", "-o", "pipefail", "-c"]

USER root

# R pre-requisites
RUN apt-get update --yes && \
    apt-get install --yes --no-install-recommends \
    fonts-dejavu \
    gfortran \
    gcc && \
    apt-get clean && rm -rf /var/lib/apt/lists/*

# https://qiita.com/hiroki_okuhata_int/items/73b1c866f146a0d639e6
# https://kubernetes.io/ja/docs/setup/production-environment/tools/kubeadm/install-kubeadm/
RUN curl -sL https://dl.k8s.io/apt/doc/apt-key.gpg | apt-key add - && \
    echo "deb https://apt.kubernetes.io/ kubernetes-xenial main" >> /etc/apt/sources.list.d/kubernetes.list && \
    apt-get update && apt-get install -y kubectl && \
    curl https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash && \
    apt-get clean && rm -rf /var/lib/apt/lists/*

# Julia dependencies
# install Julia packages in /opt/julia instead of ${HOME}
ENV JULIA_DEPOT_PATH=/opt/julia \
    JULIA_PKGDIR=/opt/julia

# Setup Julia
RUN /opt/setup-scripts/setup-julia.bash

USER ${NB_UID}

# Setup IJulia kernel & other packages
RUN /opt/setup-scripts/setup-julia-packages.bash

# R packages including IRKernel which gets installed globally.
# r-e1071: dependency of the caret R package
RUN mamba install --yes \
    'r-base' \
    'r-caret' \
    'r-crayon' \
    'r-devtools' \
    'r-e1071' \
    'r-forecast' \
    'r-hexbin' \
    'r-htmltools' \
    'r-htmlwidgets' \
    'r-irkernel' \
    'r-nycflights13' \
    'r-randomforest' \
    'r-rcurl' \
    'r-rmarkdown' \
    'r-rodbc' \
    'r-rsqlite' \
    'r-shiny' \
    'r-tidymodels' \
    'r-tidyverse' \
    'rpy2' \
    'unixodbc' && \
    mamba clean --all -f -y && \
    fix-permissions "${CONDA_DIR}" && \
    fix-permissions "/home/${NB_USER}"

USER root

RUN apt-get update -y && \
    apt-get install -y \
    texlive-lang-japanese texlive-lang-cjk texlive-fonts-recommended texlive-fonts-extra fonts-takao fonts-noto-cjk-extra \
    ghostscript gv xzdec texlive-latex-base texlive-latex-extra texlive-latex-recommended texlive-science texlive-extra-utils texlive-bibtex-extra && \
    apt-get clean && rm -rf /var/lib/apt/lists/*

RUN apt-get update -y && \
    apt-get install -y latexmk biber&& \
    apt-get clean && rm -rf /var/lib/apt/lists/*

RUN curl -fsSL https://www.preining.info/rsa.asc | tlmgr key add -

# https://discourse.jupyter.org/t/how-to-configure-jupyterhub-to-run-code-server/11578/9
ENV CODE_VERSION2=4.18.0
RUN curl -fOL https://github.com/coder/code-server/releases/download/v${CODE_VERSION2}/code-server_${CODE_VERSION2}_amd64.deb \
    && dpkg -i code-server_${CODE_VERSION2}_amd64.deb \
    && rm -f code-server_${CODE_VERSION2}_amd64.deb

USER ${NB_UID}

RUN code-server \
    --install-extension ms-ceintl.vscode-language-pack-ja \
    --install-extension ms-python.python \
    --install-extension ms-toolsai.jupyter \
    --install-extension James-Yu.latex-workshop \
    --install-extension streetsidesoftware.code-spell-checker \
    --install-extension oderwat.indent-rainbow && \
    fix-permissions "/home/${NB_USER}"

# RUN wget https://mirror.ctan.org/systems/texlive/tlnet/update-tlmgr-latest.sh && \
#    chmod +x update-tlmgr-latest.sh && \
RUN fmtutil-user --byfmt uplatex && \
    updmap-user nofont --syncwithtrees && \
    kanji-config-updmap-user ipaex && \
    mktexlsr
#    tlmgr init-usertree --usermode && \
#    tlmgr repository add ftp://ftp.math.utah.edu/pub/tex/historic/systems/texlive/2019/tlnet-final && \
#    tlmgr repository remove http://mirror.ctan.org/systems/texlive/tlnet && \
#    tlmgr option repository ftp://ftp.math.utah.edu/pub/tex/historic/systems/texlive/2019/tlnet-final && \
#    tlmgr install --verify-repo=none --usermode ntheorem inputenc amsthm assymb optidef xr docmute enumerate bm

