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
    sudo apt update -y && sudo apt install curl make vim keychain net-tools unzip zip build-essential libssl-dev pkg-config -y \
    && curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs > rust.sh \
    && chmod u+x rust.sh && ./rust.sh -y \
    && . "$HOME/.cargo/env" \
    && curl https://rustwasm.github.io/wasm-pack/installer/init.sh -sSf | sh \
    && curl -fsSL https://fnm.vercel.app/install | bash -s -- --install-dir /home/workspace/.openvscode-server/fnm \
    && source /home/workspace/.bashrc \
    && fnm use --install-if-missing 20 \
    # List the extensions in this array
    && exts=(\
        # From https://open-vsx.org/ registry directly
        gitpod.gitpod-theme \
        rust-lang.rust-analyzer \
        Alibaba-Cloud.tongyi-lingma \
        linuxsuren.webhook \
    )\
    # Install the $exts
    && for ext in "${exts[@]}"; do ${OPENVSCODE} --install-extension "${ext}"; done \
    # Backup data of openvscode-server
    && mkdir /var/data \
    && cp /home/workspace/.openvscode-server /var/data/.openvscode-server -r \
    && cp /home/workspace/.cargo /var/data/.cargo -r \
    && cp /home/workspace/.rustup /var/data/.rustup -r \
    && cp /home/workspace/.profile /var/data/.profile \
    && cp /home/workspace/.bashrc /var/data/.bashrc

USER openvscode-server
