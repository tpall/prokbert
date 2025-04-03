FROM nvidia/cuda:12.1.1-base-ubuntu22.04

# Install necessary packages
RUN apt-get update && apt-get install -y --no-install-recommends \
    wget \
    unzip \
    lbzip2 \
    mc \
    libgtextutils-dev \
    sudo \
    curl \
    gpg \
    git \
    && rm -rf /var/lib/apt/lists/*

# Install Miniconda
# entrypoint.sh is from https://gist.github.com/xkortex/84226629c2a1286120bf139b93bca9bf
COPY envs/entrypoint.sh ./
RUN chmod +x /entrypoint.sh
ENV PATH /opt/miniconda/bin:${PATH}
ARG PATH /opt/miniconda/bin:${PATH}
RUN mkdir -p /opt/conda
RUN wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh -O /opt/conda/miniconda.sh \ 
    && bash /opt/conda/miniconda.sh -b -p /opt/miniconda \
    && rm -f /opt/conda/miniconda.sh \
    && conda init bash \
    && echo 'conda activate "${CONDA_TARGET_ENV:-base}"' >>  ~/.bashrc

# Install some dependencies
COPY envs/environment.yml /tmp
RUN . /opt/miniconda/bin/activate \
    && conda env update --name base -f /tmp/environment.yml \
    && conda clean -afy

# Install prokbert
RUN mkdir /app 
WORKDIR /app
COPY . ./ 
RUN pip install -e .

SHELL ["/entrypoint.sh", "/bin/bash", "-c"]