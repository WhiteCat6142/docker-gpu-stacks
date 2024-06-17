# Copyright (c) WhiteCat6142 <whitecat6142+git@gmail.com>
# Fork from Jupyter Development Team.
# Distributed under the terms of the Modified BSD License.
ARG BASE_CONTAINER=datascience-notebook
FROM $BASE_CONTAINER

LABEL maintainer="WhiteCat6142 <whitecat6142+git@gmail.com>"

USER root
RUN --mount=type=cache,target=/var/lib/apt/lists \
    --mount=type=cache,target=/var/cache/apt \
    apt update -y && \
    apt install -y ninja-build cmake gcc-11 g++-11

USER ${NB_UID}
ENV _GLIBCXX_USE_CXX11_ABI 1
ENV CC gcc-11
ENV CXX g++-11
ENV CUDA_DIR /usr/local/cuda-12
ENV CXXFLAGS '-Wno-maybe-uninitialized -Wno-uninitialized -Wno-free-nonheap-object -Wno-nonnull' CFLAGS='-Wno-maybe-uninitialized -Wno-uninitialized -Wno-free-nonheap-object -Wno-nonnull' USE_ROCM=0 TORCH_CUDA_ARCH_LIST=8.9 PATH="$CUDA_DIR/bin:$PATH" LD_LIBRARY_PATH=$CUDA_DIR/lib64 python setup.py develop
# ENV CMAKE_PREFIX_PATH=${CONDA_PREFIX:-"/opt/conda/"}

RUN --mount=type=cache,mode=0755,target=/home/${NB_UID}/.cache/pip \
    curl -OL https://github.com/pytorch/pytorch/releases/download/v2.3.1/pytorch-v2.3.1.tar.gz && \
    tar -zxvf pytorch-v2.3.1.tar.gz && \
    cd pytorch-v2.3.1 && \
    pip3 install -r requirements.txt && \
    python3 setup.py develop && \
    pip install nvcc4jupyter && \
    fix-permissions "${CONDA_DIR}" && \
    fix-permissions "/home/${NB_USER}"
