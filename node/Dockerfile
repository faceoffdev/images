ARG NODE_VERSION
FROM node:${NODE_VERSION}

RUN corepack enable && corepack prepare pnpm@latest --activate

WORKDIR /app
