ARG GITPOD=latest
FROM gitpod/openvscode-server:$GITPOD

ARG GO=1.22.4
ENV OPENVSCODE_SERVER_ROOT="/home/.openvscode-server"
ENV OPENVSCODE="${OPENVSCODE_SERVER_ROOT}/bin/openvscode-server"
LABEL org.opencontainers.image.source https://github.com/LinuxSuRen/openvscode-server-images

SHELL ["/bin/bash", "-c"]
RUN \
    # install necessary tools
    sudo apt update -y && sudo apt install ca-certificates curl make vim keychain --no-install-recommends  -y \
    && sudo install -m 0755 -d /etc/apt/keyrings \
    && sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc \
    && sudo chmod a+r /etc/apt/keyrings/docker.asc \
    && echo \
        "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
        $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null \
    && sudo apt-get update \
    && sudo apt-get install docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin \
    # Create a tmp dir for downloading
    && tdir=/tmp/exts && mkdir -p "${tdir}" && cd "${tdir}" \
    # Download via wget from $urls array.
    && wget https://go.dev/dl/go${GO}.linux-amd64.tar.gz \
    && tar -C ${OPENVSCODE_SERVER_ROOT} -xzf go${GO}.linux-amd64.tar.gz \
    && rm -rf go${GO}linux-amd64.tar.gz \
    && echo export PATH=$PATH:/home/.openvscode-server/go/bin > ${OPENVSCODE_SERVER_ROOT}/.bashrc \
    && echo export GOPROXY=https://goproxy.cn,direct >> ${OPENVSCODE_SERVER_ROOT}/.bashrc \
    && echo export GOROOT=/home/.openvscode-server/go >> ${OPENVSCODE_SERVER_ROOT}/.bashrc \
    # List the extensions in this array
    && exts=(\
        # From https://open-vsx.org/ registry directly
        gitpod.gitpod-theme \
        golang.go \
        Alibaba-Cloud.tongyi-lingma \
    )\
    # Install the $exts
    && for ext in "${exts[@]}"; do ${OPENVSCODE} --install-extension "${ext}"; done
