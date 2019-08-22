# Set the base image to Ubuntu 16.04
FROM ubuntu:16.04

# File Author / Maintainer
MAINTAINER Yanhai Gong <gongyh@qibebt.ac.cn>

ARG PACKAGE_VERSION=2.3.5
ARG BUILD_PACKAGES="wget apt-transport-https"
ARG DEBIAN_FRONTEND=noninteractive
ARG CONDA_VERSION=4.5.12

ENV LANG=C.UTF-8 LC_ALL=C.UTF-8

# guppy
RUN apt update && apt-get install --yes $BUILD_PACKAGES && \
    apt-get install --yes libzmq5 libhdf5-cpp-11 libcurl4-openssl-dev libssl-dev libhdf5-10 libboost-regex1.58.0 libboost-log1.58.0 libboost-atomic1.58.0 libboost-chrono1.58.0 libboost-date-time1.58.0 libboost-filesystem1.58.0 libboost-program-options1.58.0 libboost-iostreams1.58.0 && \
    cd /tmp && wget -q https://mirror.oxfordnanoportal.com/software/analysis/ont_guppy_cpu_${PACKAGE_VERSION}-1~xenial_amd64.deb && dpkg -i *.deb && rm *.deb && \
    apt-get remove --purge --yes && \
    apt-get autoremove --purge --yes && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

# conda
RUN apt-get update && apt-get install -y git build-essential lbzip2 zlib1g-dev bzip2 libbz2-dev && \
    apt-get remove --purge --yes && apt-get autoremove --purge --yes && apt-get clean && rm -rf /var/lib/apt/lists/*

RUN wget --no-check-certificate https://repo.anaconda.com/miniconda/Miniconda3-${CONDA_VERSION}-Linux-x86_64.sh && \
    bash Miniconda3-${CONDA_VERSION}-Linux-x86_64.sh -b -p /opt/conda && \
    rm -f Miniconda3-${CONDA_VERSION}-Linux-x86_64.sh

ENV PATH /opt/conda/bin:$PATH

RUN conda config --add channels bioconda && \
    conda config --add channels conda-forge && \
    conda config --add channels default && \
    conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/
RUN conda install python=3.6 virtualenv #medaka pomoxis

# katuali
RUN cd /opt && git clone https://github.com/nanoporetech/katuali.git && \
    cd katuali && make install

# pomoxis
RUN cd /opt && virtualenv pomoxis --python=python3 --prompt "(pomoxis) " && \
    . pomoxis/bin/activate && \
    pip install git+https://github.com/rrwick/Porechop && \
    pip install pomoxis

# medaka
RUN cd /opt && virtualenv medaka --python=python3 --prompt "(medaka) " && \
    . medaka/bin/activate && pip install medaka

ENV TINI_VERSION v0.16.1
ADD https://github.com/krallin/tini/releases/download/${TINI_VERSION}/tini /usr/bin/tini
RUN chmod +x /usr/bin/tini

ENTRYPOINT [ "/usr/bin/tini", "--" ]
CMD [ "/bin/bash" ]

