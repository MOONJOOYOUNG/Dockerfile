# Dockerfile

 cuda-command-line-tools-10-0 \
       cuda-cublas-10-0 \
       cuda-cufft-10-0 \
       cuda-curand-10-0 \
       cuda-cusolver-10-0 \
       cuda-cusparse-10-0 \
       libcudnn7=7.4.1.5-1+cuda10.0 \
       libfreetype6-dev \
       libhdf5-serial-dev \
       libzmq3-dev \
       pkg-config \
       software-properties-common \
       unzip \
       zip \
       zlib1g-dev \
       wget \
       git \
       sudo \
       vim \
       zsh \
       curl \
       python3-tk \
       ncurses-dev \
       libsm6 \
       libxext6 \
       eog
RUN apt-get update && \
       apt-get install nvinfer-runtime-trt-repo-ubuntu1804-5.0.2-ga-cuda10.0 \
       && apt-get update \
       && apt-get install -y --no-install-recommends libnvinfer5=5.0.2-1+cuda10.0 \
       && apt-get clean \
       && rm -rf /var/lib/apt/lists/*
# For CUDA profiling, TensorFlow requires CUPTI.
ENV LD_LIBRARY_PATH /usr/local/cuda/extras/CUPTI/lib64:$LD_LIBRARY_PATH
ARG USE_PYTHON_3_NOT_2=1
ARG _PY_SUFFIX=${USE_PYTHON_3_NOT_2:+3}
ARG PYTHON=python${_PY_SUFFIX}
ARG PIP=pip${_PY_SUFFIX}
# See http://bugs.python.org/issue19846
ENV LANG C.UTF-8
RUN apt-get update && apt-get install -y \
   ${PYTHON} \ARG UBUNTU_VERSION=18.04
FROM nvidia/cuda:10.0-base-ubuntu${UBUNTU_VERSION} as base
ENV DEBIAN_FRONTEND=noninteractive
# Pick up some TF dependencies
RUN apt-get update && apt-get install -y --no-install-recommends apt-utils \
       build-essential \
       libpng-dev \
 
   ${PYTHON}-pip
RUN ${PIP} --no-cache-dir install --upgrade \
   pip \
   setuptools \
   ipython \
${PYTHON}-pip
RUN ${PIP} --no-cache-dir install --upgrade \
   pip \
   setuptools \
   ipython \
   ipdb \
   matplotlib \
   opencv-python \
   pandas \
   scipy
# Some TF tools expect a "python" binary
RUN ln -s $(which ${PYTHON}) /usr/local/bin/python
# Options:
#   tensorflow
#   tensorflow-gpu
#   tf-nightly
#   tf-nightly-gpu
#ARG TF_PACKAGE=tensorflow-gpu
#RUN ${PIP} install ${TF_PACKAGE}
ARG UNAME
ARG UID
ARG GID
RUN addgroup --gid ${GID} ${UNAME}
RUN useradd -m -u ${UID} --gid ${GID} -s /bin/bash -p ${UNAME} ${UNAME}
RUN echo "${UNAME}:${UNAME}" | chpasswd
RUN adduser ${UNAME} sudo
#RUN useradd -u $USR_ID $USR && echo "$USR:$USR" | chpasswd && adduser $USR sudo
USER ${UNAME}
WORKDIR /home/${UNAME}
 


