FROM python:3.13-alpine as builder
ADD https://raw.githubusercontent.com/whyour/qinglong/develop/pnpm-lock.yaml /ql/
ADD https://raw.githubusercontent.com/whyour/qinglong/develop/.npmrc /ql/
ADD https://raw.githubusercontent.com/whyour/qinglong/develop/package.json /ql/
RUN set -x \
    && apk update \
    && apk --no-cache add -f coreutils \
                             moreutils \
                             perl \
                             openssl \
                             nodejs \
                             npm \
                             jq \
                             openssh \
                             make \
                             gcc \
                             g++ \
                             libc-dev \
                             git \
                             netcat-openbsd \
                             procps \
    && npm install -g pnpm@8.3.1 \
    && pip3 install wheel pysocks pycryptodome aiohttp tomli tomli-w rsa httpx --no-cache-dir \
    && python -m pip install requests "urllib3<2" \
    && cd /ql \
    && pnpm install --prod
FROM python:3.13-alpine

ENV PNPM_HOME=/root/.local/share/pnpm \
    PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/root/.local/share/pnpm:/root/.local/share/pnpm/global/5/node_modules:$PNPM_HOME \
    LANG=zh_CN.UTF-8 \
    SHELL=/bin/bash \
    PS1="\u@\h:\w \$ " \
    QL_DIR=/ql

WORKDIR ${QL_DIR}
COPY --from=builder /usr/local/lib/python3.11/site-packages/. /usr/local/lib/python3.11/site-packages/
RUN set -x \
    && sed -i 's/dl-cdn.alpinelinux.org/mirrors.ustc.edu.cn/g' /etc/apk/repositories \
    && apk update -f \
    && apk upgrade \
    && apk --no-cache add -f bash \
                             coreutils \
                             moreutils \
                             git \
                             curl \
                             wget \
                             tzdata \
                             perl \
                             openssl \
                             nginx \
                             nodejs \
                             npm \
                             jq \
                             openssh \
                             fish \
                             nano \
                             netcat-openbsd \
                             unzip \
                             procps \
    && rm -rf /var/cache/apk/* \
    && ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime \
    && echo "Asia/Shanghai" > /etc/timezone \
    && npm install -g pnpm@8.3.1 \
    && pnpm add -g pm2 tsx \
    && crontab -l > cron_bkp \
    && echo "0 */4 * * * /usr/sbin/ntpdate ntp3.aliyun.com >> /dev/null 2>&1" >> cron_bkp \
    && crontab cron_bkp \
    && rm cron_bkp \
    && rm -rf /root/.pnpm-store \
    && rm -rf /root/.local/share/pnpm/store \
    && rm -rf /root/.cache \
    && rm -rf /root/.npm
COPY --from=builder /ql/node_modules/. /tmp/build/node_modules/
