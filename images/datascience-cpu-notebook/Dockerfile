# Copyright (c) WhiteCat6142 <whitecat6142+git@gmail.com>
# Fork from Jupyter Development Team.
# Distributed under the terms of the Modified BSD License.
ARG BASE_CONTAINER=datascience-notebook
FROM $BASE_CONTAINER

LABEL maintainer="WhiteCat6142 <whitecat6142+git@gmail.com>"

USER ${NB_UID}

RUN pip3 install --no-cache-dir torch torchvision torchaudio --index-url https://download.pytorch.org/whl/cpu && \
    fix-permissions "${CONDA_DIR}" && \
    fix-permissions "/home/${NB_USER}"
