FROM node:alpine AS builder_frontend

ARG FILEBROWSER_VER
ENV NODE_OPTIONS=--openssl-legacy-provider

### install frontend
WORKDIR /frontend-src
RUN apk add --no-cache git; \
    git clone https://github.com/filebrowser/filebrowser.git --branch v${FILEBROWSER_VER} --depth 1 .; \
    cd frontend; \
    npm install; \
    npm run build; \
    npm prune --production; \
    find ./node_modules/* -name 'node_modules' -type d -prune -print -exec rm -rf '{}' \;

#============================================================

FROM golang:alpine AS builder_backend

COPY --from=builder_frontend /frontend-src/ /backend-src/

### install backend
WORKDIR /backend-src
RUN apk add --no-cache git upx; \
    go install github.com/GeertJohan/go.rice/rice@latest; \
    go mod download; \
    cd http; \
    rice embed-go; \
    cd ..; \
    go build -ldflags="-s -w" -o /output/filebrowser/filebrowser; \
    upx --brute /output/filebrowser/filebrowser

COPY *.sh /output/usr/local/bin/
RUN chmod +x /output/usr/local/bin/*.sh

#============================================================

FROM alpine:latest

ARG FILEBROWSER_VER
ENV PS1="\[\e[32m\][\[\e[m\]\[\e[36m\]\u \[\e[m\]\[\e[37m\]@ \[\e[m\]\[\e[34m\]\h\[\e[m\]\[\e[32m\]]\[\e[m\] \[\e[37;35m\]in\[\e[m\] \[\e[33m\]\w\[\e[m\] \[\e[32m\][\[\e[m\]\[\e[37m\]\d\[\e[m\] \[\e[m\]\[\e[37m\]\t\[\e[m\]\[\e[32m\]]\[\e[m\] \n\[\e[1;31m\]$ \[\e[0m\]" \
    PUID=1000 \
    PGID=1000 \
    UMASK=022 \
    TZ=Asia/Shanghai
    
RUN apk add --no-cache \
        ca-certificates \
        mailcap \
        curl \
        s6-overlay \
        bash \
        tzdata

LABEL org.label-schema.name="filebrowser" \
      org.label-schema.description="A Docker image for Web File Browser app" \
      org.label-schema.url="https://filebrowser.org" \
      org.label-schema.version=${FILEBROWSER_VER}

COPY --from=builder_backend /output/ /

WORKDIR /filebrowser

VOLUME ["/data"]

EXPOSE 8080/TCP

HEALTHCHECK --start-period=10s --timeout=5s \
    CMD wget -qO /dev/null "http://127.0.0.1:8080/login"

ENTRYPOINT ["/sbin/tini", "--", "/usr/local/bin/entrypoint.sh"]
CMD ["/filebrowser/filebrowser", "-c", "/config/filebrowser.json"]
