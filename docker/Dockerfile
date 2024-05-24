#FROM nvidia/cudagl:11.4.2-devel-ubuntu20.04
#FROM ubuntu:22.04
FROM nvidia/cuda:12.4.1-cudnn-devel-ubuntu22.04

# ============================================================================

# ============================================================================
# install via apt
RUN apt-get update
RUN apt-get install sudo

# continue apt
RUN DEBIAN_FRONTEND=noninteractive apt-get install -y \
    build-essential \
    git \
    curl \
    vim \
    ca-certificates \
    libjpeg-dev \
    libpng-dev \
    libglfw3-dev \
    libglm-dev \
    libx11-dev \
    libomp-dev \
    libegl1-mesa-dev \
    pkg-config \
    libnvidia-gl-535=535.129.03-0ubuntu1 \
    nvidia-utils-535=535.129.03-0ubuntu1 \
    libnvidia-compute-535=535.129.03-0ubuntu1 \
    libnvidia-common-535=535.129.03-0ubuntu1 \
    libglvnd-dev

RUN DEBIAN_FRONTEND=noninteractive apt-get install -y \
    wget \
    zip \
    htop \
    tmux \
    unzip \
    terminator \
    libsm6 \
    libxext6 \
    libxrender-dev \
    gedit \
    openssh-client \
    unzip

RUN DEBIAN_FRONTEND=noninteractive apt-get install -y \
    htop \
    libopenni-dev \
    apt-utils \
    usbutils \
    dialog \
    ffmpeg \
    nvidia-settings \
    libffi-dev \
    flex \
    bison \
    build-essential \
    git \
    wget \
    pciutils \
    xserver-xorg \
    xserver-xorg-video-fbdev \
    xauth
    #module-init-tools \

RUN apt-get install -y  --no-install-recommends mesa-utils && rm -rf /var/lib/apt/lists/*

# Install cmake
RUN wget https://github.com/Kitware/CMake/releases/download/v3.14.0/cmake-3.14.0-Linux-x86_64.sh
RUN mkdir /opt/cmake
RUN sh /cmake-3.14.0-Linux-x86_64.sh --prefix=/opt/cmake --skip-license
RUN ln -s /opt/cmake/bin/cmake /usr/local/bin/cmake
RUN cmake --version

# ============================================================================

# ============================================================================
# miniconda
RUN wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
RUN bash Miniconda3-latest-Linux-x86_64.sh -b
RUN rm Miniconda3-latest-Linux-x86_64.sh

ENV PATH /root/miniconda3/bin:${PATH}
RUN conda init
RUN conda config --add channels conda-forge && \
    conda install -n base conda-libmamba-solver && \
    conda config --set solver libmamba && \
    conda create --name prompter python=3.6 opencv numpy h5py numba "pytorch=1.10" "torchvision" "cudatoolkit=11.3" -c pytorch -c nvidia
ENV PATH /root/miniconda3/envs/prompter/bin/:${PATH}

# Install detectron2
RUN python -m pip install detectron2 -f https://dl.fbaipublicfiles.com/detectron2/wheels/cu113/torch1.10/index.html --trusted-host dl.fbaipublicfiles.com


# requirements.txt
COPY requirements.txt /root/requirements.txt
RUN pip install -r /root/requirements.txt
# ============================================================================

# ============================================================================
# installation-related
# Setup habitat-sim
RUN git clone https://github.com/facebookresearch/habitat-sim.git
RUN /bin/bash -c "cd habitat-sim; git checkout tags/v0.1.5; pip install -r requirements.txt; python setup.py install --headless --with-cuda"

# Install challenge specific habitat-api
RUN git clone https://github.com/facebookresearch/habitat-api.git
RUN /bin/bash -c "cd habitat-api; git checkout tags/v0.1.5; pip install -e ."
RUN /bin/bash -c "cd habitat-api; wget http://dl.fbaipublicfiles.com/habitat/habitat-test-scenes.zip; unzip habitat-test-scenes.zip"

# Silence habitat-sim logs
ENV GLOG_minloglevel=2
ENV MAGNUM_LOG="quiet"

RUN git clone https://github.com/facebookresearch/habitat-lab.git
RUN /bin/bash -c "cd habitat-lab; git checkout tags/v0.1.5; pip install -e ."

# Install project specific packages
RUN apt-get update; apt-get install -y libsm6 libxext6 libxrender-dev
RUN pip install --upgrade cython
RUN pip install matplotlib seaborn==0.9.0 scikit-fmm==2019.1.30 imageio==2.6.0 scikit-learn==0.22.2.post1 ifcfg numpy numba
RUN pip install scikit-image

RUN pip install einops plotly kaleido transformers

# ============================================================================
