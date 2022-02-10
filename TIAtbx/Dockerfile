FROM --platform=linux/amd64 nvidia/cuda:11.3.0-runtime-ubuntu20.04

LABEL maintainer="Vlad Popovici <popovici@bioxlab.org>"
LABEL tia_version="1.0.1"

VOLUME [ "/data" ]

SHELL [ "/bin/bash", "--login", "-c" ]

ENV LANG=C.UTF-8 LC_ALL=C.UTF-8

# RUN apt-get update -q && apt-get install -q -y apt-utils

RUN apt-get update -q && \
    apt-get install -q -y --no-install-recommends \
        bzip2 \
	nano \
        ca-certificates \
        git \
        libglib2.0-0 \
        libsm6 \
        libxext6 \
        libxrender1 \
        mercurial \
        openssh-client \
        procps \
        subversion \
        wget \
        build-essential \
        libgl1 \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

ENV CONDA_DIR /opt/conda
ENV PATH ${CONDA_DIR}/bin:$PATH


CMD [ "/bin/bash" ]

# MINICONDA
RUN wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh  -O miniconda.sh -q && \
    mkdir -p ${CONDA_DIR}} && \
    sh ./miniconda.sh -b -p ${CONDA_DIR} && \
    rm -f miniconda.sh && \
    ln -s ${CONDA_DIR}/etc/profile.d/conda.sh /etc/profile.d/conda.sh && \
    echo ". /opt/conda/etc/profile.d/conda.sh" >> ~/.bashrc && \
    echo "conda activate base" >> ~/.bashrc && \
    find ${CONDA_DIR} -follow -type f -name '*.a' -delete && \
    find ${CONDA_DIR} -follow -type f -name '*.js.map' -delete && \
    ${CONDA_DIR}/bin/conda clean -afy

RUN conda update --name base --channel defaults conda
RUN conda install -y pip

RUN chmod -R ago+wr /opt/conda

# Create a non-root user
ENV USER tia
ENV HOME /home/$USER
RUN useradd -ms /bin/bash $USER
RUN echo ". /opt/conda/etc/profile.d/conda.sh" >> /home/tia/.bashrc
USER tia
WORKDIR /home/tia


ENV CONDA_DIR /opt/conda
ENV PATH ${CONDA_DIR}/bin:$PATH

CMD [ "/bin/bash" ]

# ADD tia-reqs2.conda.yml tia.yml
RUN conda create -n tia python=3.7

RUN git clone https://github.com/vladpopovici/ComPath.git
ENV PYTHONPATH "/home/tia/ComPath"

RUN conda activate tia && \
    conda install -y pytorch torchvision torchaudio cudatoolkit=11.3 -c pytorch

RUN echo "pytorch" >> ${CONDA_DIR}/envs/tia/conda-meta/pinned && \
    echo "torchvision" >> ${CONDA_DIR}/envs/tia/conda-meta/pinned && \
    echo "torchaudio" >> ${CONDA_DIR}/envs/tia/conda-meta/pinned && \
    echo "cudatoolkit=11.3" >> ${CONDA_DIR}/envs/tia/conda-meta/pinned

RUN conda activate tia && \
    conda config --add channels conda-forge && \
    conda config --add channels pytorch && \
    conda install -y -c conda-forge "opencv>=4.5.4" && \
    conda install -y -c conda-forge "scikit-learn>=0.23.2" "scikit-image>=0.18.3" && \
    conda install -y h5py openslide pyyaml requests && \
    conda install -y shapely tifffile tqdm umap-learn zarr && \
    conda install -y albumentations "black>=21" "Click>=7.0" cython defusedxml && \
    conda install -y flask glymur && \
    conda install -y -c conda-forge openslide-python==1.1.2 && \
    conda install -y -c conda-forge mahotas && \
    pip install tiatoolbox==1.0.1

