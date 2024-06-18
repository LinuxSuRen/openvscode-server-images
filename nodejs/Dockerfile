ARG GITPOD=latest
FROM gitpod/openvscode-server:$GITPOD

ENV OPENVSCODE_SERVER_ROOT="/home/.openvscode-server"
ENV OPENVSCODE="${OPENVSCODE_SERVER_ROOT}/bin/openvscode-server"
LABEL org.opencontainers.image.source https://github.com/LinuxSuRen/openvscode-server-images

SHELL ["/bin/bash", "-c"]
RUN \
    # install necessary tools
    sudo apt update -y && sudo apt install curl make vim keychain unzip -y \
    && curl -fsSL https://fnm.vercel.app/install | bash \
    && source /home/workspace/.bashrc \
    && fnm use --install-if-missing 20 \
    # List the extensions in this array
    && exts=(\
        # From https://open-vsx.org/ registry directly
        gitpod.gitpod-theme \
        Vue.volar \
        Alibaba-Cloud.tongyi-lingma \
        linuxsuren.webhook \
    )\
    # Install the $exts
    && for ext in "${exts[@]}"; do ${OPENVSCODE} --install-extension "${ext}"; done \
    # Backup data of openvscode-server
    && mkdir /var/data \
    && cp /home/workspace/.openvscode-server /var/data/openvscode-server-back -r
