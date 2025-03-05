FROM        --platform=$TARGETOS/$TARGETARCH node:21-bookworm-slim

LABEL       author="Max Nomad" maintainer="info@sivium.solutions"

RUN         apt update \
            && apt -y install ffmpeg jq iproute2 git sqlite3 libsqlite3-dev python3 python3-dev ca-certificates dnsutils tzdata zip tar curl build-essential libtool iputils-ping libnss3 tini \
            && useradd -m -d /home/container container

# Install npm, TypeScript, and Node types
RUN         npm install npm@10.x.x typescript ts-node @types/node --location=global

# Install Bun
RUN         curl -fsSL https://bun.sh/install | bash \
            && mv /root/.bun/bin/bun /usr/local/bin/bun

# Install pm2, Sentry CLI, and Wrangler globally
RUN         npm install -g pm2 @sentry/cli wrangler

# install pnpm
ENV COREPACK_INTEGRITY_KEYS=0
RUN         npm install -g corepack
RUN         corepack enable
RUN         corepack prepare pnpm@latest --activate

USER        container
ENV         USER=container HOME=/home/container
WORKDIR     /home/container

STOPSIGNAL SIGINT

COPY        --chown=container:container ./../entrypoint.sh /entrypoint.sh
RUN         chmod +x /entrypoint.sh
ENTRYPOINT  ["/usr/bin/tini", "-g", "--"]
CMD         ["/entrypoint.sh"]
