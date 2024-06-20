ARG GITPOD=latest
FROM ghcr.io/containerd/nerdctl:main AS nerdctl
FROM gitpod/openvscode-server:$GITPOD

ARG GO=1.22.4
ENV OPENVSCODE_SERVER_ROOT="/home/.openvscode-server"
ENV OPENVSCODE="${OPENVSCODE_SERVER_ROOT}/bin/openvscode-server"
LABEL org.opencontainers.image.source https://github.com/LinuxSuRen/openvscode-server-images

USER root
SHELL ["/bin/bash", "-c"]
COPY --from=nerdctl /usr/local/bin/nerdctl /usr/local/bin/nerdctl
RUN \
    # install necessary tools
    sudo apt update -y && sudo apt install ca-certificates curl make vim keychain python3 net-tools openjdk-21-jdk maven --no-install-recommends  -y \
    && sudo install -m 0755 -d /etc/apt/keyrings \
    # Create a tmp dir for downloading
    && tdir=/tmp/exts && mkdir -p "${tdir}" && cd "${tdir}" \
    && sudo echo export GOROOT=/home/.openvscode-server/go >> /etc/profile \
    && sudo echo alias docker=nerdctl >> /etc/profile \
    && source /etc/profile \
    # List the extensions in this array
    && exts=(\
        # From https://open-vsx.org/ registry directly
        gitpod.gitpod-theme \
        redhat.java \
        ms-vscode.makefile-tools \
        Alibaba-Cloud.tongyi-lingma \
        linuxsuren.webhook \
    )\
    # Install the $exts
    && for ext in "${exts[@]}"; do ${OPENVSCODE} --install-extension "${ext}"; done \
    # Backup data of openvscode-server
    && mkdir /var/data \
    && cp /home/workspace/.openvscode-server /var/data/openvscode-server-back -r

USER openvscode-server
