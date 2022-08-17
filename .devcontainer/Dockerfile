# FROM nvidia/cuda:11.0-base
FROM --platform=linux/amd64 nvidia/cuda:11.0.3-base-ubuntu18.04

# user args 
ARG USERNAME=vscode
ARG USER_UID=1000
ARG USER_GID=$USER_UID

# conda args 
ARG CONDA_DIR="/home/${USERNAME}/conda"
ARG CONDA_ENV_FILE=".devcontainer/mamba_env.yaml"

# system envs
ENV PATH="${CONDA_DIR}/bin:${PATH}"
RUN echo "export PATH=$PATH" > /etc/environment
RUN echo $PATH

# Create the user
RUN groupadd --gid $USER_GID $USERNAME \
    && useradd --uid $USER_UID --gid $USER_GID -m $USERNAME \
    # [Optional] Add sudo support. Omit if you don't need to install software after connecting.
    && apt-get update \
    && apt-get install -y sudo \
    && echo $USERNAME ALL=\(root\) NOPASSWD:ALL > /etc/sudoers.d/$USERNAME \
    && chmod 0440 /etc/sudoers.d/$USERNAME

# ********************************************************
# * Anything else you want to do like clean up goes here *
# ********************************************************

# install APT stuff
RUN apt-get update --quiet 
RUN apt-get install \
    --yes \
    --no-install-recommends \
    --no-install-suggests \
    ca-certificates \
    git \
    htop \
    tree \
    wget \
    zsh

# [Optional] Set the default user. Omit if you want to keep the default as root.
USER $USERNAME

# Install miniconda

RUN wget  https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh \
    --quiet \
    -O /tmp/miniconda.sh
RUN /bin/bash /tmp/miniconda.sh -b -p ${CONDA_DIR}

# install mamba
RUN conda install --quiet -c conda-forge -y mamba 

# install mamba env
COPY --chown=${USERNAME}:${USERNAME} "${CONDA_ENV_FILE}" /tmp/env.yaml
RUN mamba env update \ 
    # --quiet \
    --verbose \
    --name base \
    --file /tmp/env.yaml
RUN mamba clean --all --yes

ARG MAMBA_DOCKERFILE_ACTIVATE=1  # (otherwise python will not be found)

# install ohmyzsh
RUN wget https://github.com/robbyrussell/oh-my-zsh/raw/master/tools/install.sh -O - | zsh || true
# terminal colors with xterm
ENV TERM xterm
# set the zsh theme 
ENV ZSH_THEME agnoster

# [Optional] If your pip requirements rarely change, uncomment this section to add them to the image.
# COPY requirements.txt /tmp/pip-tmp/
# RUN pip3 --disable-pip-version-check --no-cache-dir install -r /tmp/pip-tmp/requirements.txt \
#    && rm -rf /tmp/pip-tmp

# [Optional] Uncomment this section to install additional OS packages.
# RUN apt-get update && export DEBIAN_FRONTEND=noninteractive \
#     && apt-get -y install --no-install-recommends <your-package-list-here>

# [Optional] Uncomment this line to install global node packages.
# RUN su vscode -c "source /usr/local/share/nvm/nvm.sh && npm install -g <your-package-here>" 2>&1

# use zsh shell by default
RUN chsh -s $(which zsh)

# enable correct path when running docker container
CMD [ "zsh" ]
