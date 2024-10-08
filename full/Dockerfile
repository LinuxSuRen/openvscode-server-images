ARG GITPOD=latest
FROM ghcr.io/linuxsuren/hd:v0.0.97 as HD
FROM gitpod/openvscode-server:$GITPOD

ARG GO=1.22.4
ARG GH_VERSION=2.52.0
ENV OPENVSCODE_SERVER_ROOT="/home/.openvscode-server"
ENV OPENVSCODE="${OPENVSCODE_SERVER_ROOT}/bin/openvscode-server"
LABEL org.opencontainers.image.source https://github.com/LinuxSuRen/openvscode-server-images

USER root
SHELL ["/bin/bash", "-c"]
COPY --from=HD /usr/local/bin/hd /usr/local/bin/hd
RUN \
    # install necessary tools
    sudo apt update -y && sudo apt install ca-certificates curl make vim keychain python3 net-tools unzip zip python3-pip openjdk-21-jdk maven gradle zsh --no-install-recommends -y \
    && sudo install -m 0755 -d /etc/apt/keyrings \
    && sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc \
    && sudo chmod a+r /etc/apt/keyrings/docker.asc \
    && echo \
        "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
        $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
        sudo tee /etc/apt/sources.list.d/docker.list > /dev/null \
    && sudo apt-get update \
    && sudo apt install docker-ce-cli docker-buildx-plugin docker-compose-plugin -y \
    && rm -rf /etc/apt/sources.list.d/docker.list \
    && curl -fsSL https://fnm.vercel.app/install | bash -s -- --install-dir /home/workspace/.openvscode-server/fnm \
    && source /home/workspace/.bashrc \
    && fnm use --install-if-missing 20 \
    && curl -s https://get.sdkman.io | bash \
    # Create a tmp dir for downloading
    && tdir=/tmp/exts && mkdir -p "${tdir}" && cd "${tdir}" \
    # Download via wget from $urls array.
    && wget https://go.dev/dl/go${GO}.linux-amd64.tar.gz \
    && tar -C ${OPENVSCODE_SERVER_ROOT} -xzf go${GO}.linux-amd64.tar.gz \
    && rm -rf go${GO}linux-amd64.tar.gz \
    && sudo echo export PATH=$PATH:/home/.openvscode-server/go/bin >> /etc/profile \
    && sudo echo export GOPROXY=https://goproxy.cn,direct >> /etc/profile \
    && sudo echo export GOROOT=/home/.openvscode-server/go >> /etc/profile \
    && sudo echo export MAVEN_OPTS="-Dmaven.repo.local=/home/workspace/.m2" >> /etc/profile \
    && sudo echo source "/home/workspace/.sdkman/bin/sdkman-init.sh" >> /etc/profile \
    && source /etc/profile \
    && echo eval "$(fnm env --use-on-cd)" >> /home/workspace/.zshrc \
    && go install -v github.com/go-delve/delve/cmd/dlv@v1.22.1 \
    && go install golang.org/x/tools/gopls@v0.16.0 \
    && sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)" \
    && curl -L https://github.com/containernetworking/plugins/releases/download/v1.5.1/cni-plugins-linux-amd64-v1.5.1.tgz --output cni.tgz \
    && curl -L https://github.com/moby/buildkit/releases/download/v0.14.1/buildkit-v0.14.1.linux-amd64.tar.gz --output buildkit.tar.gz \
    && curl -L https://github.com/cli/cli/releases/download/v${GH_VERSION}/gh_${GH_VERSION}_linux_amd64.tar.gz --output gh_${GH_VERSION}_linux_amd64.tar.gz \
    && sudo mkdir -p /opt/cni/bin \
    && sudo tar -xzvf cni.tgz -C /opt/cni/bin/ \
    && sudo tar -xzvf buildkit.tar.gz -C /usr/local/bin \
    && sudo tar -C /usr/local/bin -xzvf gh_${GH_VERSION}_linux_amd64.tar.gz gh_${GH_VERSION}_linux_amd64/bin/gh --strip-components 2 \
    && rm -rf cni.tgz \
    && rm -rf buildkit.tar.gz \
    && rm -rf gh_${GH_VERSION}_linux_amd64.tar.gz \
    # List the extensions in this array
    && exts=(\
        # From https://open-vsx.org/ registry directly
        gitpod.gitpod-theme \
        redhat.java \
        vscjava.vscode-java-pack \
        golang.go \
        Vue.volar \
        ms-python.python \
        ms-azuretools.vscode-docker \
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
    && cp /home/workspace/.local /var/data/.local -r \
    && cp /home/workspace/.oh-my-zsh /var/data/.oh-my-zsh -r \
    && cp /home/workspace/.bashrc /var/data/.bashrc \
    && cp /home/workspace/.zshrc /var/data/.zshrc

USER openvscode-server
