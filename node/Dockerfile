ARG NODE_VERSION
FROM node:${NODE_VERSION}

RUN apt update  \
    && apt install -y git  \
    && apt clean  \
    && rm -rf /var/lib/apt/lists/*

RUN corepack enable && corepack prepare pnpm@latest --activate

USER node
WORKDIR /app
