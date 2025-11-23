ARG NODE_VERSION
FROM node:${NODE_VERSION}

RUN corepack enable && corepack prepare pnpm@latest --activate

ARG UID=1000
ARG GID=1000

ENV USER=application \
    WORKDIR=/app

RUN groupadd --gid "${GID}" "${USER}" \
    && useradd --uid "${UID}" --gid "${USER}" --shell /bin/bash --create-home "${USER}" \
    && mkdir -p "${WORKDIR}" \
    && chown -R "${USER}:${USER}" \
       "${WORKDIR}" \
       "/home/${USER}"

USER ${USER}
WORKDIR ${WORKDIR}
