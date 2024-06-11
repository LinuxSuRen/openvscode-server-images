ARG GITPOD=latest
FROM ghcr.io/containerd/nerdctl:main AS nerdctl
FROM gitpod/openvscode-server:$GITPOD

ARG GO=1.22.4
ENV OPENVSCODE_SERVER_ROOT="/home/.openvscode-server"
ENV OPENVSCODE="${OPENVSCODE_SERVER_ROOT}/bin/openvscode-server"
LABEL org.opencontainers.image.source https://github.com/LinuxSuRen/openvscode-server-images

SHELL ["/bin/bash", "-c"]
COPY --from=nerdctl /usr/local/bin/nerdctl /usr/local/bin/nerdctl
RUN \
    # install necessary tools
    sudo apt update -y && sudo apt install ca-certificates curl make vim keychain python3 --no-install-recommends  -y \
    && sudo install -m 0755 -d /etc/apt/keyrings \
    # Create a tmp dir for downloading
    && tdir=/tmp/exts && mkdir -p "${tdir}" && cd "${tdir}" \
    # Download via wget from $urls array.
    && wget https://go.dev/dl/go${GO}.linux-amd64.tar.gz \
    && tar -C ${OPENVSCODE_SERVER_ROOT} -xzf go${GO}.linux-amd64.tar.gz \
    && rm -rf go${GO}linux-amd64.tar.gz \
    && echo export PATH=$PATH:/home/.openvscode-server/go/bin > ${OPENVSCODE_SERVER_ROOT}/.bashrc \
    && echo export GOPROXY=https://goproxy.cn,direct >> ${OPENVSCODE_SERVER_ROOT}/.bashrc \
    && echo export GOROOT=/home/.openvscode-server/go >> ${OPENVSCODE_SERVER_ROOT}/.bashrc \
    && echo alias docker=nerdctl >> ${OPENVSCODE_SERVER_ROOT}/.bashrc \
    # List the extensions in this array
    && exts=(\
        # From https://open-vsx.org/ registry directly
        gitpod.gitpod-theme \
        golang.go \
        Alibaba-Cloud.tongyi-lingma \
    )\
    # Install the $exts
    && for ext in "${exts[@]}"; do ${OPENVSCODE} --install-extension "${ext}"; done
