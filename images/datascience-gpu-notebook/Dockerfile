# Copyright (c) WhiteCat6142 <whitecat6142+git@gmail.com>
# Fork from Jupyter Development Team.
# Distributed under the terms of the Modified BSD License.
ARG BASE_CONTAINER=datascience-notebook
FROM $BASE_CONTAINER

LABEL maintainer="WhiteCat6142 <whitecat6142+git@gmail.com>"

USER ${NB_UID}

RUN --mount=type=cache,mode=0755,target=/home/${NB_UID}/.cache/pip \
    pip3 install torch torchvision torchaudio && \
    fix-permissions "${CONDA_DIR}" && \
    fix-permissions "/home/${NB_USER}"
