FROM linuxserver/openvscode-server:latest
# ENV OPENVSCODE="/app/openvscode-server/bin/openvscode-server"
RUN curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash -
RUN apt install nodejs -y
SHELL ["/bin/bash", "-c"]
RUN \
    # List the extensions in this array
    exts=(\
        # From https://open-vsx.org/ registry directly
        gitpod.gitpod-theme \
        Vue.volar \
        Alibaba-Cloud.tongyi-lingma \
        linuxsuren.webhook \
        stylelint.vscode-stylelint \
        esbenp.prettier-vscode \
        dbaeumer.vscode-eslint \
        antfu.unocss \
        antfu.iconify \
        evils.uniapp-vscode \
        uni-helper.uni-helper-vscode \
        uni-helper.uni-app-schemas-vscode \
        uni-helper.uni-highlight-vscode \
        uni-helper.uni-ui-snippets-vscode \
        uni-helper.uni-app-snippets-vscode \
        mrmlnc.vscode-json5 \
    )\
    # Install the $exts
    && for ext in "${exts[@]}"; do /app/openvscode-server/bin/openvscode-server --install-extension "${ext}"; done
# RUN /app/openvscode-server/bin/openvscode-server --install-extension Vue.volar linuxsuren.webhook
