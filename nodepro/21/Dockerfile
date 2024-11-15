FROM        --platform=$TARGETOS/$TARGETARCH node:21-bookworm-slim

LABEL       author="Max Nomad" maintainer="info@sivium.solutions"

RUN         apt update \
            && apt -y install ffmpeg iproute2 git sqlite3 libsqlite3-dev python3 python3-dev ca-certificates dnsutils tzdata zip tar curl build-essential libtool iputils-ping libnss3 tini \
            && useradd -m -d /home/container container

# Install npm, TypeScript, and Node types
RUN         npm install npm@10.x.x typescript ts-node @types/node --location=global

# Directly install pnpm (bypassing corepack for compatibility)
RUN         curl -fsSL https://get.pnpm.io/install.sh | sh - \
            && ln -s /root/.local/share/pnpm/pnpm /usr/local/bin/pnpm

# Install Bun
RUN         curl -fsSL https://bun.sh/install | bash \
            && mv /root/.bun/bin/bun /usr/local/bin/bun

# Install pm2 and Sentry CLI globally
RUN         npm install -g pm2 @sentry/cli

USER        container
ENV         USER=container HOME=/home/container
WORKDIR     /home/container

STOPSIGNAL SIGINT

COPY        --chown=container:container ./../entrypoint.sh /entrypoint.sh
RUN         chmod +x /entrypoint.sh
ENTRYPOINT  ["/usr/bin/tini", "-g", "--"]
CMD         ["/entrypoint.sh"]
