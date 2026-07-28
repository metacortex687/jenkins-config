FROM jenkins/ssh-agent:debian-jdk21

RUN apt-get update \
    && apt-get install -y --no-install-recommends curl \
    && install -m 0755 -d /etc/apt/keyrings \
    && curl -fsSL https://download.docker.com/linux/debian/gpg \
        -o /etc/apt/keyrings/docker.asc \
    && chmod a+r /etc/apt/keyrings/docker.asc \
    && . /etc/os-release \
    && echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/debian ${VERSION_CODENAME} stable" \
        > /etc/apt/sources.list.d/docker.list \
    && apt-get update \
    && apt-get install -y --no-install-recommends \
        docker-ce-cli \
        docker-buildx-plugin \
        docker-compose-plugin \
    && rm -rf /var/lib/apt/lists/*

ARG DOCKER_GID

RUN set -eu; \
    test -n "${DOCKER_GID}"; \
    docker_group="$(getent group "${DOCKER_GID}" | cut -d: -f1)"; \
    if [ -z "${docker_group}" ]; then \
        docker_group=docker-host; \
        groupadd --gid "${DOCKER_GID}" "${docker_group}"; \
    fi; \
    usermod --append --groups "${docker_group}" jenkins; \
    mkdir -p /home/jenkins/agent; \
    chown jenkins:jenkins /home/jenkins/agent
