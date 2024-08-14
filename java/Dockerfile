ARG GITPOD=latest
FROM ghcr.io/linuxsuren/hd:v0.0.97 as HD
FROM gitpod/openvscode-server:$GITPOD

ENV OPENVSCODE_SERVER_ROOT="/home/.openvscode-server"
ENV OPENVSCODE="${OPENVSCODE_SERVER_ROOT}/bin/openvscode-server"
LABEL org.opencontainers.image.source https://github.com/LinuxSuRen/openvscode-server-images

USER root
SHELL ["/bin/bash", "-c"]
COPY --from=HD /usr/local/bin/hd /usr/local/bin/hd
RUN \
    # install necessary tools
    sudo apt update -y && sudo apt install ca-certificates curl make vim keychain python3 net-tools openjdk-21-jdk maven gradle unzip zip --no-install-recommends  -y \
    && sudo install -m 0755 -d /etc/apt/keyrings \
    && curl -s https://get.sdkman.io | bash \
    && source "/home/workspace/.sdkman/bin/sdkman-init.sh" \
    # Create a tmp dir for downloading
    && tdir=/tmp/exts && mkdir -p "${tdir}" && cd "${tdir}" \
    && curl -L https://github.com/containernetworking/plugins/releases/download/v1.5.1/cni-plugins-linux-amd64-v1.5.1.tgz --output cni.tgz \
    && curl -L https://github.com/moby/buildkit/releases/download/v0.14.1/buildkit-v0.14.1.linux-amd64.tar.gz --output buildkit.tar.gz \
    && sudo mkdir -p /opt/cni/bin \
    && sudo tar -xzvf cni.tgz -C /opt/cni/bin/ \
    && sudo tar -xzvf buildkit.tar.gz -C /usr/local/bin \
    && rm -rf cni.tgz \
    && rm -rf buildkit.tar.gz \
    && sudo echo export GOROOT=/home/.openvscode-server/go >> /etc/profile \
    && sudo echo export MAVEN_OPTS="-Dmaven.repo.local=/home/workspace/.m2" >> /etc/profile \
    && source /etc/profile \
    # List the extensions in this array
    && exts=(\
        # From https://open-vsx.org/ registry directly
        gitpod.gitpod-theme \
        redhat.java \
        vscjava.vscode-java-pack \
        ms-vscode.makefile-tools \
        Alibaba-Cloud.tongyi-lingma \
        linuxsuren.webhook \
    )\
    # Install the $exts
    && for ext in "${exts[@]}"; do ${OPENVSCODE} --install-extension "${ext}"; done \
    # Backup data of openvscode-server
    && mkdir /var/data \
    && cp /home/workspace/.openvscode-server /var/data/.openvscode-server -r \
    && cp /home/workspace/.sdkman /var/data/.sdkman -r \
    && cp /home/workspace/.bashrc /var/data/.bashrc \
    && cp /home/workspace/.zshrc /var/data/.zshrc

USER openvscode-server
