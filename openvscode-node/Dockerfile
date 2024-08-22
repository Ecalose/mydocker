FROM linuxserver/openvscode-server:latest
# ENV OPENVSCODE="/app/openvscode-server/bin/openvscode-server"
RUN curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash -
RUN apt install nodejs -y
# RUN \
#     # List the extensions in this array
#     exts=(\
#         # From https://open-vsx.org/ registry directly
#         gitpod.gitpod-theme \
#         Vue.volar \
#         Alibaba-Cloud.tongyi-lingma \
#         linuxsuren.webhook \
#     )\
#     # Install the $exts
#     && for ext in "${exts[@]}"; do /app/openvscode-server/bin/openvscode-server --install-extension "${ext}"; done
RUN /app/openvscode-server/bin/openvscode-server --install-extension Vue.volar linuxsuren.webhook
