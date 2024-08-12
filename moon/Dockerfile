ARG GITPOD=latest
FROM gitpod/openvscode-server:$GITPOD

ENV OPENVSCODE_SERVER_ROOT="/home/.openvscode-server"
ENV OPENVSCODE="${OPENVSCODE_SERVER_ROOT}/bin/openvscode-server"
LABEL org.opencontainers.image.source https://github.com/LinuxSuRen/openvscode-server-images

USER root
SHELL ["/bin/bash", "-c"]
SHELL ["/bin/bash", "-c"]
RUN \
    # install necessary tools
    sudo apt update -y && sudo apt install curl make vim keychain net-tools unzip zip -y \
    && curl -fsSL https://cli.moonbitlang.com/install/unix.sh | bash \
    # List the extensions in this array
    && exts=(\
        # From https://open-vsx.org/ registry directly
        gitpod.gitpod-theme \
        moonbit.moonbit-lang \
        Alibaba-Cloud.tongyi-lingma \
        linuxsuren.webhook \
    )\
    # Install the $exts
    && for ext in "${exts[@]}"; do ${OPENVSCODE} --install-extension "${ext}"; done \
    # Backup data of openvscode-server
    && mkdir /var/data \
    && cp /home/workspace/.openvscode-server /var/data/.openvscode-server -r \
    && cp /home/workspace/.moon /var/data/.moon -r

USER openvscode-server
