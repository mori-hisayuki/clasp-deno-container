FROM debian:stable-slim

ARG USER_ID
ARG USER_NAME
ARG GROUP_ID
ARG GROUP_NAME
ARG WORK_DIR
ARG DENO_VERSION

# CA の証明書のパッケージ
RUN set -x \
    && apt-get update \
    && apt-get install -y --no-install-recommends \
        ca-certificates \
    && apt-get clean && rm -rf /var/cache/apt/archives/* /var/lib/apt/lists

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

# common tools
RUN set -x \
    && apt-get -y update \
    && apt-get install -y --no-install-recommends \
    curl \
    git \
    openssh-client \
    jq \
    unzip \
    wget \
 && apt-get clean && rm -rf /var/cache/apt/archives/* /var/lib/apt/lists/*

# Deno install
RUN set -x \
    && curl -fsSL -o /deno.zip "https://github.com/denoland/deno/releases/download/v${DENO_VERSION}/deno-x86_64-unknown-linux-gnu.zip" \
    && unzip -d /usr/local/bin/ /deno.zip \
    && rm -f /deno.zip \
    && /usr/local/bin/deno completions bash > /etc/bash_completion.d/deno

USER ${USER_NAME}
RUN mkdir /home/${USER_NAME}/${WORK_DIR}
WORKDIR /home/${USER_NAME}/${WORK_DIR}

# ENV DENO_INSTALL /home/vscode/.deno
# ENV PATH=$DENO_INSTALL/bin:$PATH

# vscode setting copy
COPY --chown=${GROUP_NAME}:${USER_NAME} ./.vscode /home/${USER_NAME}/${WORK_DIR}/.vscode