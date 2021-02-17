FROM debian:stable-slim

ARG USER_ID
ARG USER_NAME
ARG GROUP_ID
ARG GROUP_NAME
ARG DENO_VERSION

# Install sudo
RUN set -x \
    && apt-get -y update \
    && apt-get install -y --no-install-recommends \
    sudo \
    && apt-get clean && rm -rf /var/cache/apt/archives/* /var/lib/apt/lists/*

# create group
RUN set -x \
    && groupadd --gid ${GROUP_ID} ${GROUP_NAME}

# create vscode user
RUN set - x \
    && echo "$USER_NAME ALL=(ALL) NOPASSWD: ALL" >> /etc/sudoers.d/${USER_NAME} \
    && chmod 0440 /etc/sudoers.d/${USER_NAME} \
    && useradd \
        --uid ${USER_ID} \
        --gid ${GROUP_ID} \
        --home-dir /home/${USER_NAME} \
        --create-home \
        --shell /bin/bash \
        ${USER_NAME}

# vscode extensions cache (extensionsの再インストールを防ぐ)
# https://code.visualstudio.com/docs/remote/containers-advanced#_avoiding-extension-reinstalls-on-container-rebuild
RUN set -x \
    && mkdir -p /home/vscode/.vscode-server/extensions \
    && chown -R vscode:vscode /home/vscode/.vscode-server

COPY --chown=${GROUP_NAME}:${USER_NAME} ./ssh /home/${USER_NAME}/.ssh

RUN apt-get -y update \
    && apt-get -y upgrade \
    && apt-get install -y git curl openssh-client sudo jq unzip wget

USER ${USER_NAME}
# Deno install
RUN curl -fsSL https://deno.land/x/install/install.sh | sh

# vscode setting copy
COPY --chown=${GROUP_NAME}:${USER_NAME} ./.vscode /home/${USER_NAME}/${WORK_DIR}/.vscode