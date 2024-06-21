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
    sudo apt update -y && sudo apt install ca-certificates curl make vim keychain python3 net-tools python3-pip --no-install-recommends  -y \
    && sudo install -m 0755 -d /etc/apt/keyrings \
    # Create a tmp dir for downloading
    && tdir=/tmp/exts && mkdir -p "${tdir}" && cd "${tdir}" \
    # Download via wget from $urls array.
    && wget https://go.dev/dl/go${GO}.linux-amd64.tar.gz \
    && tar -C ${OPENVSCODE_SERVER_ROOT} -xzf go${GO}.linux-amd64.tar.gz \
    && rm -rf go${GO}linux-amd64.tar.gz \
    && sudo echo export PATH=$PATH:/home/.openvscode-server/go/bin >> /etc/profile \
    && sudo echo export GOPROXY=https://goproxy.cn,direct >> /etc/profile \
    && sudo echo export GOROOT=/home/.openvscode-server/go >> /etc/profile \
    && source /etc/profile \
    && go install -v github.com/go-delve/delve/cmd/dlv@v1.22.1 \
    && go install golang.org/x/tools/gopls@v0.16.0 \
    && sudo echo alias docker=nerdctl >> /etc/profile \
    && sudo echo alias docker-compose="nerdctl compose" >> /etc/profile \
    && curl -L https://github.com/containernetworking/plugins/releases/download/v1.5.1/cni-plugins-linux-amd64-v1.5.1.tgz --output cni.tgz \
    && curl -L https://github.com/moby/buildkit/releases/download/v0.14.1/buildkit-v0.14.1.linux-amd64.tar.gz --output buildkit.tar.gz \
    && sudo mkdir -p /opt/cni/bin \
    && sudo tar -xzvf cni.tgz -C /opt/cni/bin/ \
    && sudo tar -xzvf buildkit.tar.gz -C /usr/local/bin \
    && rm -rf cni.tgz \
    && rm -rf buildkit.tar.gz \
    # List the extensions in this array
    && exts=(\
        # From https://open-vsx.org/ registry directly
        gitpod.gitpod-theme \
        golang.go \
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
