FROM debian:bullseye-slim

LABEL maintainer="Vlad Popovici <popovici@bioxlab.org>"
LABEL tia_version="1.0.1"

VOLUME [ "/data" ]

SHELL [ "/bin/bash", "--login", "-c" ]

ENV LANG=C.UTF-8 LC_ALL=C.UTF-8

# hadolint ignore=DL3008
RUN apt-get update -q && \
    apt-get install -q -y --no-install-recommends \
        bzip2 \
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

# Create a non-root user
ENV USER tia
ENV HOME /home/$USER
RUN useradd -ms /bin/bash $USER
USER tia
WORKDIR /home/tia

RUN echo ". /opt/conda/etc/profile.d/conda.sh" >> ~/.bashrc && \
    echo "conda activate base" >> ~/.bashrc

CMD [ "/bin/bash" ]

RUN git clone https://github.com/vladpopovici/ComPath.git
ENV PYTHONPATH "/home/tia/ComPath"

ADD tia-reqs.conda.yml tia.yml
RUN conda env create --file tia.yml
RUN conda activate tia
RUN conda install pip
RUN pip install tiatoolbox=1.0.1

RUN echo "#!/bin/bash --login" >> /usr/local/bin/entrypoints.sh && \
    echo "set -e"  >> /usr/local/bin/entrypoints.sh && \
    echo "conda activate tia"  >> /usr/local/bin/entrypoints.sh && \
    echo "exec \"$@\""  >> /usr/local/bin/entrypoints.sh


ENTRYPOINT [ "/usr/local/bin/entrypoint.sh" ]
