FROM        --platform=$TARGETOS/$TARGETARCH node:17-bullseye-slim

LABEL       author="Max Nomad" maintainer="info@sivium.solutions"

RUN         apt update \
            && apt -y install ffmpeg iproute2 git sqlite3 libsqlite3-dev python3 python3-dev ca-certificates dnsutils tzdata zip tar curl build-essential libtool iputils-ping libnss3 tini \
            && useradd -m -d /home/container container

# Install npm, TypeScript, and Node types
RUN         npm install npm@8.11.0 typescript ts-node @types/node --location=global

# Install Bun
RUN         curl -fsSL https://bun.sh/install | bash \
            && mv /root/.bun/bin/bun /usr/local/bin/bun

# Install pm2, Sentry CLI, and Wrangler globally
RUN         npm install -g pm2 @sentry/cli wrangler

RUN         mkdir -p /home/container/.acme /home/container/.ssl

# Clone acme.sh and install with custom paths under /home/container
RUN         git clone --depth 1 https://github.com/acmesh-official/acme.sh.git \
            && cd acme.sh \
            && ./acme.sh --install --force \
            --home /home/container/.acme \
            --config-home /home/container/.acme/data \
            --cert-home /home/container/.ssl \
            --accountemail "my@example.com" \
            --accountkey /home/container/.acme/account.key \
            --accountconf /home/container/.acme/account.conf \
            --useragent "this is my client." \
            && chmod -R 755 /home/container/.acme /home/container/.ssl \
            && ln -s /home/container/.acme/acme.sh /usr/local/bin/acme.sh \
            && rm -rf acme.sh

USER        container
ENV         USER=container HOME=/home/container
WORKDIR     /home/container

STOPSIGNAL SIGINT

COPY        --chown=container:container ./../entrypoint.sh /entrypoint.sh
RUN         chmod +x /entrypoint.sh
ENTRYPOINT  ["/usr/bin/tini", "-g", "--"]
CMD         ["/entrypoint.sh"]
