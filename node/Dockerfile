ARG NODE_VERSION
FROM node:${NODE_VERSION}

RUN corepack enable && corepack prepare pnpm@latest --activate

ARG UID=1000
ARG GID=1000
ENV USER='application' \
    WORKDIR='/app'

RUN addgroup --gid ${GID} --system ${USER} \
    && adduser --uid ${UID} --system --ingroup ${USER} ${USER} \
    && mkdir -p ${WORKDIR} \
    && chown -R ${USER}:${USER} \
      ${WORKDIR}  \
      /home/${USER}

USER ${USER}
WORKDIR ${WORKDIR}
